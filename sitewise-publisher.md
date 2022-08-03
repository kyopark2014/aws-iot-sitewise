# IoT SiteWise publisher

Greengrass의 Component인 IoT Sitewise publisher(aws.iot.SiteWisePublisher)를 이용하여 edge에서 데이터를 처리하여 AWS Cloud로 전송할 수 있습니다. 


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
