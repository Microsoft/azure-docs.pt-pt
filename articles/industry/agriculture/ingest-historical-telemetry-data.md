---
title: Dados de telemetria históricos de ingestão
description: Descreve como obter os dados de telemetria históricos de ingestão
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 5ae64371bd114a898ddca874e23b499bc4a2b8a3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128773"
---
# <a name="ingest-historical-telemetry-data"></a>Ingerir dados telemétricos do histórico

Este artigo descreve como ingerir dados de sensor históricos no Azure FarmBeats.

A ingestão de dados históricos de Internet das Coisas (IoT) para recursos como dispositivos e sensores é um cenário comum no FarmBeats. Você cria metadados para dispositivos e sensores e, em seguida, ingeri os dados históricos para FarmBeats em um formato canônico.

## <a name="before-you-begin"></a>Antes de começar

Antes de prosseguir com este artigo, verifique se você instalou o FarmBeats e coletou os dados históricos do IoT.

## <a name="enable-partner-access"></a>Habilitar o acesso do parceiro

Você precisa habilitar a integração de parceiros à instância do FarmBeats do Azure. Esta etapa cria um cliente que terá acesso ao seu FarmBeats do Azure como seu parceiro de dispositivo e fornecerá os seguintes valores necessários nas etapas subsequentes.

- Ponto de extremidade de API – é a URL do hub de dados, por exemplo, https://<datahub>. azurewebsites.net
- ID do inquilino
- ID de Cliente
- Segredo do Cliente
- Cadeia de conexão do EventHub

Siga as etapas abaixo para gerá-los:

>[!NOTE]
> Você deve ser um administrador para executar as etapas a seguir.

1. Baixe esse [script](https://aka.ms/farmbeatspartnerscript) e extraia-o em em sua unidade local. Você encontrará dois arquivos dentro do arquivo ZIP.
2. Entre em [portal do Azure](https://portal.azure.com/) e Abra Cloud Shell (essa opção está disponível na barra superior direita do Portal)  

    ![Batidas no farm de projetos](./media/for-tutorials/navigation-bar-1.png)

3. Verifique se o ambiente está definido como **PowerShell**.

    ![Batidas no farm de projetos](./media/for-tutorials/power-shell-new-1.png)

4. Carregue os dois arquivos que você baixou (da etapa 1 acima) em seu Cloud Shell.  

    ![Batidas no farm de projetos](./media/for-tutorials/power-shell-two-1.png)

5. Vá para o diretório onde os arquivos foram carregados

   >[!NOTE]
   > Por padrão, o arquivo é carregado para o diretório base/home/username/.
6. Execute o script usando o comando:  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. Siga as instruções na tela para concluir o procedimento.

## <a name="create-devicesensor-metadata"></a>Criar metadados de dispositivo/sensor

 Agora que você tem as credenciais necessárias, você pode definir o dispositivo e os sensores criando os metadados usando APIs FarmBeats.

 O FarmBeats data Hub tem as seguintes APIs que habilitam a criação e o gerenciamento de metadados do dispositivo/sensor.   

- /modelo de dispositivo **DeviceModel** corresponde aos metadados do dispositivo, como o fabricante, tipo de gateway ou nó.  
- /**dispositivo-dispositivo corresponde** a um dispositivo físico presente no farm.  
- /modelo de sensor de **SensorModel** corresponde aos metadados do sensor, como o fabricante, tipo de sensor analógico ou digital, medida de sensor como temperatura ambiente ou pressão.
- /**sensor de sensor corresponde** a um sensor físico que registra valores. Um sensor normalmente é conectado a um dispositivo com uma ID de dispositivo.  


|        Modelo de dispositivo   |  Sugestões   |
| ------- | -------             |
|     Tipo (nó, gateway)        |          1 Estrela      |
|          Fabricante            |         2 Estrelas     |
|  ProductCode                    |  Código do produto do dispositivo ou nome/número do modelo. Por exemplo, EnviroMonitor # 6800.  |
|            Portas          |     Nome da porta e tipo (digital/analógico)
|     Nome                 |  Nome para identificar o recurso. Por exemplo, nome do modelo/nome do produto.
      Descrição     | Forneça uma descrição significativa do modelo
|    Propriedades          |    Propriedades adicionais do fabricante   |
|    **Vice**             |                      |
|   DeviceModelId     |     ID do modelo de dispositivo associado  |
|  HardwareID          | ID exclusiva para o dispositivo, como endereço MAC, etc.,
|  reportingInterval        |   Intervalo de relatórios em segundos
|  Localização            |  Dispositivo latitude (-90 a + 90)/longitude (-180 a 180)/Elevation (em metros)   
|ParentDeviceId       |    ID do dispositivo pai ao qual este dispositivo está conectado. Por exemplo, um nó conectado a um gateway. Um nó terá parentDeviceId como o gateway.  |
|    Nome            | Um nome para identificar o recurso. Os parceiros de dispositivo devem enviar um nome consistente com o nome do dispositivo no lado do parceiro. Se o nome do dispositivo do parceiro for definido pelo usuário, o mesmo nome definido pelo usuário deverá ser propagado para FarmBeats.|
|     Descrição       |      Forneça uma descrição significativa  |
|     Propriedades    |  Propriedades adicionais do fabricante
|     **Modelo de sensor**        |          |
|       Tipo (analógico, digital)          |      tipo de sensor, seja analógico ou digital       |
|          Fabricante            |       o fabricante do sensor     |
|     ProductCode| Código do produto ou nome do modelo/número. Por exemplo, RS-CO2-N01. |
|       Nome > SensorMeasures       | Nome da medida do sensor. Somente letras minúsculas têm suporte. Para medir de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Esse nome deve ser consistente com os dados de telemetria  |
|          sensorMeasures > DataType       |Tipo de dados Telemétrico. Há suporte para o Double no momento|
|    Tipo de > SensorMeasures    |Tipo de medição dos dados de telemetria do sensor. A seguir estão os tipos definidos pelo sistema: AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, comprimento, LiquidLevel, nitrate, O2, PH, phosphate, PointInTime, Potassium, pressão, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, estado, tempo de duração, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Para adicionar mais, consulte API do/ExtendedType.|
|        Unidade de > SensorMeasures              | Unidade de dados de telemetria do sensor. A seguir estão as unidades definidas pelo sistema: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, mercúrio, PSI, milímetro, centímetro, medidor, polegada, pés, milhar, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, grau, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, porcentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litro, MilliLiter, segundos, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour para adicionar mais, consulte/ API estendida.|
|    Agregatype de > SensorMeasures    |  Os valores podem ser nenhum, média, máximo, mínimo ou i  |
|          Nome            | Nome para identificar o recurso. Por exemplo, nome do modelo/nome do produto.  |
|    Descrição        | Forneça uma descrição significativa do modelo  |
|   Propriedades       |  Propriedades adicionais do fabricante  |
|    **Sensores**      |          |
| HardwareID          |   ID exclusiva para o sensor definido pelo fabricante |
|  sensorModelId     |    ID do modelo de sensor associado   |
| localização          |  Sensor latitude (-90 a + 90)/longitude (-180 a 180)/Elevation (em metros)|
|   Nome da > de porta        |  Nome e tipo da porta à qual o sensor está conectado no dispositivo. Isso precisa ser o mesmo nome definido no modelo do dispositivo. |
|    DeviceID  |    ID do dispositivo ao qual o sensor está conectado     |
| Nome            |   Nome para identificar o recurso. Por exemplo, nome do sensor/nome do produto e número do modelo/código do produto.|
|    Descrição      | Forneça uma descrição significativa |
|    Propriedades        |Propriedades adicionais do fabricante |

Para obter mais informações sobre objetos, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**Solicitação de API para criar metadados**

Para fazer uma solicitação de API, você combina o método HTTP (POST), a URL para o serviço de API, o URI para um recurso a ser consultado, envia dados para criar ou excluir uma solicitação e adicionar um ou mais cabeçalhos de solicitação HTTP. A URL para o serviço de API é o ponto de extremidade da API, ou seja, a URL do hub de dados (https://<yourdatahub>. azurewebsites.net)  

**Autenticação**:

O FarmBeats data Hub usa a autenticação de portador, que precisa das seguintes credenciais que geramos na seção acima.

- ID de Cliente
- Segredo do Cliente
- ID do inquilino  

Usando as credenciais acima, o chamador pode solicitar um token de acesso, que precisa ser enviado nas solicitações de API subsequentes na seção de cabeçalho da seguinte maneira:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**Cabeçalhos de solicitação HTTP**:

Aqui estão os cabeçalhos de solicitação mais comuns que precisam ser especificados ao fazer uma chamada à API para o Hub de dados FarmBeats:

- Tipo de conteúdo: aplicativo/JSON
- Autorização: portador de acesso < token >
- Aceitar: aplicativo/JSON

**Carga de entrada para criar metadados**:

**DeviceModel**


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Dispositivo

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Sensores

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
A solicitação de exemplo abaixo é criar um dispositivo (isso tem um JSON de entrada como carga com o corpo da solicitação).  

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> As APIs retornam IDs exclusivas para cada instância criada. Você deve manter as IDs para enviar as mensagens de telemetria correspondentes.

**Enviar telemetria**

Agora que você criou os dispositivos e sensores no FarmBeats, você pode enviar as mensagens de telemetria associadas.  

**Criar cliente de telemetria**

Você deve enviar a telemetria para o Hub de eventos do Azure para processamento. O Azure EventHub é um serviço que habilita a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados. Para enviar dados de telemetria para o FarmBeats, você precisa criar um cliente que envia mensagens para um hub de eventos no FarmBeats. Para obter mais informações sobre como enviar telemetria, consulte [hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

**Enviar mensagem de telemetria como o cliente**

Depois de estabelecer uma conexão como um cliente do EventHub, você poderá enviar mensagens para o EventHub como um JSON.  
Converta o formato de dados do sensor histórico em um formato canônico que o FarmBeats do Azure entenda. O formato de mensagem canônica é o seguinte:  

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

Depois de adicionar os dispositivos e sensores correspondentes, obtenha a DeviceID e o sensorid na mensagem de telemetria, conforme descrito na seção anterior

Mensagem de telemetria de exemplo:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre detalhes de integração baseados na API REST, consulte [REST API](references-for-farmbeats.md#rest-api).
