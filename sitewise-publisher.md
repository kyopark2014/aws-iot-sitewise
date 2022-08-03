# IoT SiteWise publisher

Greengrass의 Component인 IoT Sitewise publisher(aws.iot.SiteWisePublisher)를 이용하여 edge에서 데이터를 처리하여 AWS Cloud로 전송할 수 있습니다. 

## 설치

1) [[AWS IoT] - [Manage] - [Greengrass devices] - [Components] - [Public components]](https://ap-northeast-2.console.aws.amazon.com/iot/home?region=ap-northeast-2#/greengrass/v2/components/public)에서 aws.iot.SiteWiseEdgePublisher을 선택합니다. 

2) 상단의 [Deploy]를 선택하고 Deployment를 선택하여 설치를 진행합니다.

3) 설치후 아래 명령어로 "com.amazon.iot.sitewise.component.Publisher"가 잘 설치되었는지 확인합니다. 

```c
$ ps aux |grep green
root      3232  0.0  0.0  70356  4360 ?        S    22:05   0:00 sudo -n -E -H -u ggc_user -g ggc_group -- sh -c java -cp /greengrass/v2/packages/artifacts/aws.iot.SiteWiseEdgePublisher/2.2.0/IotSiteWisePublisher-1.0-super.jar com.amazon.iot.sitewise.component.Publisher 
ggc_user  3235  0.0  0.0   4632   780 ?        S    22:05   0:00 sh -c java -cp /greengrass/v2/packages/artifacts/aws.iot.SiteWiseEdgePublisher/2.2.0/IotSiteWisePublisher-1.0-super.jar com.amazon.iot.sitewise.component.Publisher 
ggc_user  3236  0.9  2.6 4719344 207632 ?      Sl   22:05   0:06 java -cp /greengrass/v2/packages/artifacts/aws.iot.SiteWiseEdgePublisher/2.2.0/IotSiteWisePublisher-1.0-super.jar com.amazon.iot.sitewise.component.Publisher
```

## Input data

AWS IoT Greengrass stream manager로부터 [BatchPutAssetPropertyValue](https://docs.aws.amazon.com/iot-sitewise/latest/APIReference/API_BatchPutAssetPropertyValue.html) 메시지를 읽습니다. 


## Log 확인

```java
$ sudo tail /greengrass/v2/logs/aws.iot.SiteWiseEdgePublisher.log
2022-08-03T22:33:01.552Z [INFO] (Copier) aws.iot.SiteWiseEdgePublisher: stdout. [INFO ] 2022-08-03 22:33:01.552 [MetricService-0] CapabilityMetricService - {"message":"Emitting metric (IoTSiteWisePublisher.Heartbeat) to local MQTT topic: IoTSiteWisePublisher/diagnostics"}. {scriptName=services.aws.iot.SiteWiseEdgePublisher.lifecycle.Startup.Script, serviceName=aws.iot.SiteWiseEdgePublisher, currentState=RUNNING}
2022-08-03T22:33:01.553Z [INFO] (Copier) aws.iot.SiteWiseEdgePublisher: stdout. [INFO ] 2022-08-03 22:33:01.553 [MetricService-0] CapabilityMetricService - {"message":"Emitting diagnostic name=IoTSiteWisePublisher.DroppedCount, value=0, timestamp=2022-08-03T22:32:02.882870Z, dimensions=[CapabilityMetricDimension{name=ThingName, value=GreengrassQuickStartCore-18163f7ac3e}]"}. {scriptName=services.aws.iot.SiteWiseEdgePublisher.lifecycle.Startup.Script, serviceName=aws.iot.SiteWiseEdgePublisher, currentState=RUNNING}
2022-08-03T22:33:01.554Z [INFO] (Copier) aws.iot.SiteWiseEdgePublisher: stdout. [INFO ] 2022-08-03 22:33:01.554 [MetricService-0] CapabilityMetricService - {"message":"Emitting metric (IoTSiteWisePublisher.DroppedCount) to local MQTT topic: IoTSiteWisePublisher/diagnostics"}. {scriptName=services.aws.iot.SiteWiseEdgePublisher.lifecycle.Startup.Script, serviceName=aws.iot.SiteWiseEdgePublisher, currentState=RUNNING}
2022-08-03T22:33:01.555Z [INFO] (Copier) aws.iot.SiteWiseEdgePublisher: stdout. [INFO ] 2022-08-03 22:33:01.555 [MetricService-0] CapabilityMetricService - {"message":"Emitting diagnostic name=IoTSiteWisePublisher.PublisherTrailingCheckpoint, value=0, timestamp=2022-08-03T22:32:02.882870Z, dimensions=[CapabilityMetricDimension{name=ThingName, value=GreengrassQuickStartCore-18163f7ac3e}]"}. {scriptName=services.aws.iot.SiteWiseEdgePublisher.lifecycle.Startup.Script, serviceName=aws.iot.SiteWiseEdgePublisher, currentState=RUNNING}
```

## Recipe

```java
{
  "RecipeFormatVersion": "2020-01-25",
  "ComponentName": "aws.iot.SiteWiseEdgePublisher",
  "ComponentVersion": "2.2.0",
  "ComponentType": "aws.greengrass.generic",
  "ComponentDescription": "AWS IoT SiteWise Publisher component.",
  "ComponentPublisher": "Amazon",
  "ComponentConfiguration": {
    "DefaultConfiguration": {
      "accessControl": {
        "aws.greengrass.ipc.mqttproxy": {
          "aws.iot.SiteWiseEdgePublisher:sub:1": {
            "policyDescription": "Allows access to publish to topics.",
            "operations": [
              "aws.greengrass#PublishToIoTCore"
            ],
            "resources": [
              "$aws/sitewise/gateways/+/diagnostics",
              "$aws/+/things/+/connectors/+/configuration/+"
            ]
          },
          "aws.iot.SiteWiseEdgePublisher:pub:1": {
            "policyDescription": "Allows access to publish to topics.",
            "operations": [
              "aws.greengrass#SubscribeToIoTCore"
            ],
            "resources": [
              "$aws/sitewise/things/+/connectors/+/configuration/update"
            ]
          }
        },
        "aws.greengrass.ipc.pubsub": {
          "aws.iot.SiteWiseEdgePublisher:pubsub:1": {
            "policyDescription": "Allows access to publish to topics.",
            "operations": [
              "aws.greengrass#PublishToTopic"
            ],
            "resources": [
              "IoTSiteWisePublisher/diagnostics"
            ]
          }
        }
      }
    }
  },
  "ComponentDependencies": {
    "aws.greengrass.StreamManager": {
      "VersionRequirement": ">=2.0.10 <3.0.0",
      "DependencyType": "HARD"
    },
    "aws.greengrass.Nucleus": {
      "VersionRequirement": ">=2.3.0 <3.0.0",
      "DependencyType": "HARD"
    }
  },
  "Manifests": [
    {
      "Platform": {
        "os": "/windows|linux/",
        "architecture": "amd64"
      },
      "Lifecycle": {
        "Startup": {
          "Script": "java -cp {artifacts:path}/IotSiteWisePublisher-1.0-super.jar com.amazon.iot.sitewise.component.Publisher\n",
          "Timeout": "100"
        }
      },
      "Artifacts": [
        {
          "Uri": "greengrass:sampleEpNTbwMLCOVlrqUKYoibf_OvatGmQYN3R2Oo=/IotSiteWisePublisher-1.0-super.jar",
          "Digest": "samplepCzIbe1VNbqUFMm+K4rqBokBfVuckAEIOvD6o=",
          "Algorithm": "SHA-256",
          "Unarchive": "NONE",
          "Permission": {
            "Read": "OWNER",
            "Execute": "NONE"
          }
        }
      ]
    }
  ],
  "Lifecycle": {}
}
```


## Reference

[IoT SiteWise publisher](https://docs.aws.amazon.com/greengrass/v2/developerguide/iotsitewise-publisher-component.html)

[CONFIGURE ASSET MODEL](https://iot-sitewise-edge.workshop.aws/130_prepare_for_edge_30/10_configure_asset_model.html)
