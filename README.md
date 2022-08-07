# AWS IoT SiteWise

여기서는 IoT SiteWise에 대해 설명합니다. AWS IoT SiteWise는 industrial 장비에서 수집된 데이터를 쉽게 모으고, 저장하고, 정리하여 모니터링하는 managed service입니다. 트래픽이 늘어날 경우에 자동으로 scale up하고 유지보수 등의 수고를 AWS에서 해주므로 비지니스 로직 개발에 집중할 수 있습니다. 

AWS IoT SiteWise asset을 사용하여 산업 운영의 가상 표현을 생성할 수 있습니다. 

- Asset은 디바이스, 장비 또는 하나 이상의 데이터 스트림들을 AWS 클라우드에 업로드하는 프로세스를 나타냅니다. Asset은 asset model로 부터 생성되어야 합니다. 

- Asset model은 asset의 형식을 표준화하는 선언적 구조입니다. Asset model은 동일한 유형의 여러 자산에 대해 일관된 정보를 적용하므로 장치 그룹을 나타내는 자산의 데이터를 처리할 수 있습니다.


![image](https://user-images.githubusercontent.com/52392004/182048958-602e34e9-64e1-4f5c-8b64-51c596bacf94.png)




### Edge Gateway

- SiteWisesms는 on-premise gateway로 local 데이터 서버로부터 데이터를 모으고, 선택된 데이터를 IoT Core와 SiteWise API를 통해 업로드 할 수 있습니다. 

- 들어온 데이터중에 필요한 데이터만 업로드 할 수 있습니다. 예로써 들어온 데이터가 초당 10개인데 필요한 데이터가 분당 1개일라면 SitieWise가 데이터를 필터링하여 필요한 분당 1개의 데이터를 전송합니다.

- 인터넷 연결이 원활하지 않거나, Bandwidth가 충분하지 않은 경우에 데이터 전송을 최적화 할 수 있습니다. 이 경우에 Cloud에 저장되는 데이터의 양도 최적화 할 수 있습니다. 

- 최소사양: 4 Cores, 16GB RAM, 256MB Storage


#### Benefits

- 여러 장치들로부터 데이터를 안정적으로 데이터를 모으고, 구조화 하며, 다른 소프트웨어 없이도 SiteWise를 이용하여 데이터를 분석할 수 있습니다. 

- 원격 모니터링을 통해 빠르게 문제를 확인 할 수 있습니다. 

- Improve cross-facility processes with a central data source 

- Process and monitor data on-premises for shop floor applications


## Ingest Data

아래와 같이 3가지 방식으로 데이터를 수집할 수 있습니다.

- Device에서 MQTT로 IoT Core로 데이터를 전송할때, Rule을 등록하여 SiteWise로 전송

- [OPC-UA](https://docs.aws.amazon.com/iot-sitewise/latest/userguide/opcua-data-acquisition.html) 서버에서 IoT SiteWise Edge Gateway로 데이터를 전달한 후 IoT SiteWise에서 수집

- 디바이스에서 AWS SDK를 이용해 IoT SiteWise로 데이터를 전송 

![image](https://user-images.githubusercontent.com/52392004/178109818-dec45d94-8165-4601-93a4-d4ec0d516f99.png)


## Asset

- Asset은 디바이스, 장비의 한 부분 또는 프로세스로서 SiteWise로 Data Stream을 전송합니다.

- Assets represent industrial devices and processes that send data streams to SiteWise. 

- An asset represents a device, a piece of equipment, or a process that uploads one or more data streams to the AWS Cloud.

## Model

- 모델은 Asset들의 구조를 4가지 asset properties로 표현합니다. Attribute (Static Descriptions), Measurement (측정 데이터), Transform, Metric 으로 구성됩니다.

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

## Retrieve data

![image](https://user-images.githubusercontent.com/52392004/178110715-941a0adb-64c0-43fc-9c7f-2eb8e2036b3a.png)



## Price

![image](https://user-images.githubusercontent.com/52392004/178087905-0ae2fc25-050f-465f-bdb4-433a50516f71.png)

## Workshop

[AWS IOT SITEWISE WORKSHOP](https://iot-sitewise.workshop.aws/en/)

[AWS IoT SiteWise workshop for beginners (Korean)](https://catalog.us-east-1.prod.workshops.aws/workshops/b74efd43-8535-47fa-b88c-f463f032e3b9/ko-KR)

[AWS on Air 2020: AWS What’s Next ft. AWS IoT SiteWise Edge](https://www.youtube.com/watch?v=SetcWz3-ezA)


