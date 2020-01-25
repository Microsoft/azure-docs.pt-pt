---
title: Ingerir dados telemétricos do histórico
description: Este artigo descreve como ingerir dados de telemetria históricos.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 32eb8e71cfb978fac5b4d6d05af4da4fdc9f67b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715514"
---
# <a name="ingest-historical-telemetry-data"></a>Ingerir dados telemétricos do histórico

Este artigo descreve como ingerir dados de sensor históricos no Azure FarmBeats.

A ingestão de dados históricos de recursos de Internet das Coisas (IoT), como dispositivos e sensores, é um cenário comum no FarmBeats. Você cria metadados para dispositivos e sensores e, em seguida, ingeri os dados históricos para FarmBeats em um formato canônico.

## <a name="before-you-begin"></a>Antes de começar

Antes de prosseguir com este artigo, verifique se você instalou o FarmBeats e coletou os dados históricos de seus dispositivos IoT.
Você também precisa habilitar o acesso do parceiro, conforme mencionado nas etapas a seguir.

## <a name="enable-partner-access"></a>Habilitar o acesso do parceiro

Você precisa habilitar a integração de parceiros à instância do FarmBeats do Azure. Esta etapa cria um cliente que tem acesso à instância do Azure FarmBeats como seu parceiro de dispositivo e fornece os seguintes valores que são necessários nas etapas subsequentes:

- Ponto de extremidade de API: essa é a URL Datahub, por exemplo, https://\<Datahub >. azurewebsites. net.
- ID do inquilino
- ID de Cliente
- Segredo do cliente
- Cadeia de conexão do EventHub

Siga estes passos.

>[!NOTE]
> Você deve ser um administrador para executar as etapas a seguir.

1. Baixe o [arquivo zip](https://aka.ms/farmbeatspartnerscriptv2)e extraia-o para a unidade local. Haverá um arquivo dentro do arquivo zip.
2. Entre para https://portal.azure.com/ e vá para registros do aplicativo Azure Active Directory->

3. Clique no registro do aplicativo que foi criado como parte de sua implantação do FarmBeats. Ele terá o mesmo nome que o FarmBeats Datahub.

4. Clique em "expor uma API"-> clique em "adicionar um aplicativo cliente" e insira **04b07795-8ddb-461A-bbee-02f9e1bf7b46** e marque "autorizar escopo". Isso dará acesso ao CLI do Azure (Cloud Shell) para executar as etapas a seguir.

5. Abra o Cloud Shell. Essa opção está disponível na barra de ferramentas no canto superior direito do portal do Azure.

    ![Barra de ferramentas portal do Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Verifique se o ambiente está definido como **PowerShell**. Por padrão, ele é definido como bash.

    ![Configuração da barra de ferramentas do PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Carregue o arquivo da etapa 1 em sua instância de Cloud Shell.

    ![Botão da barra de ferramentas carregar](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Vá para o diretório onde o arquivo foi carregado. Por padrão, os arquivos são carregados para o diretório base sob o nome de usuário.

9. Execute o seguinte script. O script solicita a ID do locatário que pode ser obtida na página de visão geral de Azure Active Directory >.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. Siga as instruções na tela para capturar os valores para **o ponto de extremidade da API**, ID do **locatário**, **ID do cliente**, segredo do **cliente**e cadeia de **conexão do EventHub**.
## <a name="create-device-or-sensor-metadata"></a>Criar metadados do dispositivo ou do sensor

 Agora que você tem as credenciais necessárias, você pode definir o dispositivo e os sensores. Para fazer isso, crie os metadados chamando APIs FarmBeats. Observe que você precisará chamar as APIs como o aplicativo cliente que você criou na seção acima

 FarmBeats Datahub tem as seguintes APIs que permitem a criação e o gerenciamento de metadados do dispositivo ou do sensor. Por favor, note que como parceiro tem acesso apenas ler, criar e atualizar os metadados; **A eliminação não é permitida por um parceiro.**

- /**DeviceModel**: DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é um gateway ou um nó.
- **dispositivo**/: o dispositivo corresponde a um dispositivo físico presente no farm.
- /**SensorModel**: SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medição do sensor, como temperatura ambiente e pressão.
- **sensor**de /: o sensor corresponde a um sensor físico que registra valores. Um sensor normalmente é conectado a um dispositivo com uma ID de dispositivo.  


|        DeviceModel   |  Sugestões   |
| ------- | -------             |
|     Tipo (nó, gateway)        |          Tipo do dispositivo-nó ou gateway      |
|          Fabricante            |         Nome do fabricante    |
|  ProductCode                    |  Código do produto do dispositivo ou nome ou número do modelo. Por exemplo, EnviroMonitor # 6800.  |
|            Portas          |     Nome da porta e tipo, que é digital ou analógica.
|     Nome                 |  Nome para identificar o recurso. Por exemplo, o nome do modelo ou o nome do produto.
      Descrição     | Forneça uma descrição significativa do modelo.
|    Propriedades          |    Propriedades adicionais do fabricante.   |
|    **Dispositivo**             |                      |
|   DeviceModelId     |     ID do modelo de dispositivo associado.  |
|  HardwareID          | ID exclusiva para o dispositivo, como o endereço MAC.
|  ReportingInterval        |   Intervalo de relatórios em segundos.
|  Localização            |  Dispositivo latitude (-90 a + 90), longitude (-180 a 180) e elevação (em metros).   
|ParentDeviceId       |    ID do dispositivo pai ao qual este dispositivo está conectado. Por exemplo, um nó que está conectado a um gateway. Um nó tem parentDeviceId como o gateway.  |
|    Nome            | Um nome para identificar o recurso. Os parceiros de dispositivo devem enviar um nome consistente com o nome do dispositivo no lado do parceiro. Se o nome do dispositivo do parceiro for definido pelo usuário, o mesmo nome definido pelo usuário deverá ser propagado para FarmBeats.|
|     Descrição       |      Forneça uma descrição significativa. |
|     Propriedades    |  Propriedades adicionais do fabricante.
|     **SensorModel**        |          |
|       Tipo (analógico, digital)          |      O tipo de sensor, se ele é analógico ou digital.       |
|          Fabricante            |       O fabricante do sensor.     |
|     ProductCode| Código do produto ou nome do modelo ou número. Por exemplo, RS-CO2-N01. |
|       Nome > SensorMeasures       | Nome da medida do sensor. Somente letras minúsculas têm suporte. Para medições de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Esse nome deve ser consistente com os dados de telemetria.  |
|          SensorMeasures > DataType       |Tipo de dados Telemétrico. Atualmente, há suporte para Double.|
|    Tipo de > SensorMeasures    |Tipo de medição dos dados de telemetria do sensor. Os tipos definidos pelo sistema são AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, comprimento, LiquidLevel, nitrate, O2, PH, phosphate, PointInTime, Potassium, pressão, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, estado, tempo de duração, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Para adicionar mais, consulte a API do/ExtendedType.|
|        Unidade de > SensorMeasures              | Unidade de dados de telemetria do sensor. As unidades definidas pelo sistema são nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, mercúrio, PSI, milímetro, centímetro, medidor, polegada, pés, milhar, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, grau, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, porcentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litro, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour para adicionar mais, consulte a API/ExtendedType.|
|    Agregatype de > SensorMeasures    |  Os valores podem ser nenhum, média, máximo, mínimo ou i.  |
|          Nome            | Nome para identificar um recurso. Por exemplo, o nome do modelo ou o nome do produto.  |
|    Descrição        | Forneça uma descrição significativa do modelo.  |
|   Propriedades       |  Propriedades adicionais do fabricante.  |
|    **Sensores**      |          |
| HardwareID          |   ID exclusiva do sensor definido pelo fabricante. |
|  SensorModelId     |    ID do modelo de sensor associado.   |
| Localização          |  Sensor latitude (-90 a + 90), longitude (-180 a 180) e elevação (em metros).|
|   Nome da > de porta        |  Nome e tipo da porta à qual o sensor está conectado no dispositivo. Isso precisa ter o mesmo nome definido no modelo do dispositivo. |
|    DispositivoID  |    ID do dispositivo ao qual o sensor está conectado.     |
| Nome            |   Nome para identificar o recurso. Por exemplo, nome do sensor ou nome do produto e número do modelo ou código do produto.|
|    Descrição      | Forneça uma descrição significativa. |
|    Propriedades        |Propriedades adicionais do fabricante. |

Para obter mais informações sobre objetos, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Solicitação de API para criar metadados

Para fazer uma solicitação de API, você combina o método HTTP (POST), a URL para o serviço de API e o URI para um recurso para consultar, enviar dados para, criar ou excluir uma solicitação. Em seguida, você adiciona um ou mais cabeçalhos de solicitação HTTP. A URL para o serviço de API é o ponto de extremidade de API, ou seja, a URL Datahub (https://\<yourdatahub >. azurewebsites. net).  

### <a name="authentication"></a>Autenticação

FarmBeats Datahub usa a autenticação de portador, que precisa das seguintes credenciais que foram geradas na seção anterior:

- ID de Cliente
- Segredo do cliente
- ID do inquilino

Usando essas credenciais, o chamador pode solicitar um token de acesso. O token deve ser enviado nas solicitações de API subsequentes, na seção de cabeçalho, da seguinte maneira:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

O código Python de exemplo a seguir fornece o token de acesso, que pode ser usado para chamadas de API subsequentes para FarmBeats: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**Cabeçalhos de solicitação HTTP**

Aqui estão os cabeçalhos de solicitação mais comuns que devem ser especificados quando você faz uma chamada à API para FarmBeats Datahub:

- **Tipo de conteúdo**: aplicativo/JSON
- **Autorização**: portador de acesso < token >
- **Aceitar**: aplicativo/JSON

### <a name="input-payload-to-create-metadata"></a>Carga de entrada para criar metadados

DeviceModel


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
A solicitação de exemplo a seguir cria um dispositivo. Esta solicitação tem um JSON de entrada como carga com o corpo da solicitação.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> As APIs retornam IDs exclusivas para cada instância criada. Você deve manter as IDs para enviar as mensagens de telemetria correspondentes.

### <a name="send-telemetry"></a>Enviar telemetria

Agora que você criou os dispositivos e sensores no FarmBeats, você pode enviar as mensagens de telemetria associadas.

### <a name="create-a-telemetry-client"></a>Criar um cliente de telemetria

Você deve enviar a telemetria para os hubs de eventos do Azure para processamento. Os hubs de eventos do Azure são um serviço que habilita a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados. Para enviar dados de telemetria para o FarmBeats, crie um cliente que envie mensagens para um hub de eventos no FarmBeats. Para obter mais informações sobre como enviar telemetria, consulte [hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Enviar uma mensagem de telemetria como o cliente

Depois de ter uma conexão estabelecida como um cliente de hubs de eventos, você pode enviar mensagens para o Hub de eventos como JSON.

Aqui está o código Python de exemplo que envia a telemetria como um cliente para um hub de eventos especificado:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

Depois de adicionar os dispositivos e sensores correspondentes, obtenha a ID do dispositivo e a ID do sensor na mensagem de telemetria, conforme descrito na seção anterior.

Aqui está um exemplo de uma mensagem de telemetria:


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

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível visualizar dados de telemetria depois de ingerir dados históricos/de streaming dos seus sensores

**Sintoma**: Dispositivos ou sensores são implantados e você criou os dispositivos/sensores em FarmBeats e ingerido telemetria para o EventHub, mas você não pode obter ou ver dados de telemetria em FarmBeats.

**Ação corretiva**:

1. Certifique-se de que fez o registo do parceiro corretamente - pode verificar isso indo para o seu datahub swagger, navegar para /Partner API, Fazer um Get e verificar se o parceiro está registado. Caso contrário, siga os [passos aqui](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar o parceiro.
2. Certifique-se de que criou os metadados (DeviceModel, Device, SensorModel, Sensor) utilizando as credenciais do cliente parceiro.
3. Certifique-se de que utilizou o formato de mensagem telemetria correto (conforme especificado abaixo):

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre detalhes de integração baseados na API REST, consulte [REST API](references-for-azure-farmbeats.md#rest-api).
