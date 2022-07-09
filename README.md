# AWS IoT SiteWise

여기서는 IoT SiteWise에 대해 설명합니다. 

AWS IoT SiteWise는 industrial 장비에서 수집된 데이터를 쉽게 모으고, 저장하고, 정리하여 모니터링하는 managed service입니다. 트래픽이 늘어날 경우에 자동으로 scale up하고 유지보수등의 수고를 AWS에서 해주므로 비지니스 로직 개발에 집중할 수 있습니다. 


## Ingest Data

아래와 같이 3가지 방식으로 데이터를 수집할 수 있습니다.

- Device에서 MQTT로 IoT Core로 데이터를 전송할때, Rule을 등록하여 SiteWise로 전송

- [OPC-UA](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/opcua-data-acquisition.html) 서버에서 IoT SiteWise Edge Gateway로 데이터를 전달한후 IoT SiteWise에서 수집

- 디바이스에서 AWS SDK를 이용해 IoT SiteWise로 데이터를 전송 

![image](https://user-images.githubusercontent.com/52392004/178109818-dec45d94-8165-4601-93a4-d4ec0d516f99.png)


## Asset

- Asset은 디바이스, 장비의 한 부분 또는 프로세스로서 SiteWise로 Data Stream을 전송합니다.

- Assets represent industrial devices and processes that send data streams to SiteWise. 

- An asset represents a device, a piece of equipment, or a process that uploads one or more data streams to the AWS Cloud.

## Model

- 모델은 Asset들의 구조를 4가지 asset properties로 표현합니다. Attribute, Measurement, Transform, Metric으로 구성됩니다.




- Models are structures that enforce a specific model of properties and hierarchies for all instances of each asset. You must create every asset from a model.


```java
Model name: Machine
Property {
  Attribute { Name: string }
  Measurement{ Widget Output: INTEGER }
  Measurement{ Temperature_in_C: FLOAT } 
  Transform { 
    Temperature_in_F: FLOAT;
        value = 9/5 * tempC + 32 
    }
  Metric { 
    Efficiency: FLOAT;
    value = Widget Output/Capacity
    window = 1 hour
  }
}
CompositeModel {
  Name : Temperature_Alarm;
  Type : AWS/ALARM
}
```

- 아래 그림은 Wind turbine이 하나의 Asset이 되어서, 온도(Temperature), RPM(프로펠러 회전 속도), 전원(Power)와 같은 Asset properties를 측정합니다. 각각의 stream은 아래와 같은 고유한 alias를 가지고 있습니다. 예) 3번 Wind Farm의 7번 터빈. 

- 이러한 데이터 stream은 "Temperature_C", "RPM", "Power"와 같은 SiteWise Asset으로 configure하는데, 온도와 같은 경우에 섭씨(Celsius)와 화씨(Fahrenheit)와 같이 수학적인 표현으로 변환됩니다. 

![image](https://user-images.githubusercontent.com/52392004/178095017-1f00f14f-4126-4692-8fe0-69e216870159.png)


## Price

![image](https://user-images.githubusercontent.com/52392004/178087905-0ae2fc25-050f-465f-bdb4-433a50516f71.png)

## Workshop

[AWS IOT SITEWISE WORKSHOP](https://iot-sitewise.workshop.aws/en/)
