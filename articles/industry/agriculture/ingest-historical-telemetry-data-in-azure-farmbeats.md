---
title: Ingerir dados telemétricos do histórico
description: Este artigo descreve como ingerir dados históricos de telemetria.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.custom: has-adal-ref
ms.openlocfilehash: 3833b27e9f90cbffa2320c84877d4eb5bb6520f7
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613273"
---
# <a name="ingest-historical-telemetry-data"></a>Ingerir dados telemétricos do histórico

Este artigo descreve como ingerir dados de sensores históricos em Azure FarmBeats.

A ingestão de dados históricos a partir de recursos da Internet of Things (IoT), como dispositivos e sensores, é um cenário comum nas FarmBeats. Cria metadados para dispositivos e sensores e, em seguida, ingere os dados históricos para farmBeats num formato canónico.

## <a name="before-you-begin"></a>Antes de começar

Antes de avançar com este artigo, certifique-se de que instalou FarmBeats e recolheu dados históricos dos seus dispositivos IoT. Também precisa de permitir o acesso ao parceiro, conforme mencionado nos seguintes passos.

## <a name="enable-partner-access"></a>Ativar o acesso ao parceiro

Tem de permitir a integração do parceiro na sua instância Azure FarmBeats. Este passo cria um cliente que tem acesso à sua instância Azure FarmBeats como parceiro de dispositivo e fornece-lhe os seguintes valores que são exigidos nos passos seguintes:

- Ponto final da API: Este é o\<URL datahub, por exemplo, https:// datahub>.azurewebsites.net
- ID do inquilino
- ID de Cliente
- Segredo do cliente
- Cadeia de ligação EventHub

Siga estes passos.

> [!NOTE]
> Deve ser administrador para fazer os seguintes passos.

1. Inicie sessão em https://portal.azure.com/.

2. **Se estiver na versão 1.2.7 ou posterior do FarmBeats, salte os passos a, b e c, e vá para o passo 3.** Pode verificar a versão FarmBeats selecionando o ícone **Definições** no canto superior direito do UI FarmBeats.

      a.  Ir a Registos de > **Aplicações** de **Diretório Ativo azure**

      b. Selecione o Registo de **Aplicações** que foi criado como parte da sua implementação FarmBeats. Terá o mesmo nome que o seu centro de dados FarmBeats.

      c. Selecione **Expor um API** > selecione **Adicionar uma aplicação de cliente** e insira **04b07795-8ddb-461a-bbee-02f9e1bf7b46** e verifique **Autorizar o Scope**. Isto dará acesso ao Azure CLI (Cloud Shell) para realizar os passos abaixo:

3. Abra o Cloud Shell. Esta opção está disponível na barra de ferramentas no canto superior direito do portal Azure.

    ![Barra de ferramentas do portal Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Certifique-se de que o ambiente está definido para **powerShell**. Por defeito, está definido para Bash.

    ![Definição da barra de ferramentas PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Vá ao seu diretório de casa.

    ```azurepowershell-interactive 
    cd
    ```

6. Execute o seguinte comando. Isto irá transferir um guião para o seu diretório em casa.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. Execute o seguinte script. O script pede o ID do Inquilino, que pode ser obtido a partir da página de > **visão geral** do **Diretório Ativo Azure.**

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1

    ```

8. Siga as instruções no ecrã para capturar os valores para **API Endpoint,** **ID do inquilino,** **ID do cliente,** segredo do **cliente**e String **de Conexão EventHub**.


## <a name="create-device-or-sensor-metadata"></a>Criar metadados de dispositivos ou sensores

 Agora que tem as credenciais necessárias, pode definir o dispositivo e os sensores. Para isso, crie os metadados chamando a FarmBeats APIs. Certifique-se de que chama as APIs como a aplicação do cliente que criou na secção acima.

 FarmBeats Datahub tem as seguintes APIs que permitem a criação e gestão de metadados de dispositivos ou sensores.

 > [!NOTE]
 > Como parceiro tem acesso apenas para ler, criar e atualizar os metadados; excluir a **opção é restrita ao parceiro.**

- /**DeviceModel**: DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é um portal ou um nó.
- /**Dispositivo**: O dispositivo corresponde a um dispositivo físico presente na exploração.
- /**SensorModel**: SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medição do sensor, como temperatura ambiente e pressão.
- /**Sensor**: Sensor corresponde a um sensor físico que regista valores. Um sensor é normalmente ligado a um dispositivo com um ID do dispositivo.


|        DeviceModel   |  Sugestões   |
| ------- | -------             |
|     Tipo (nó, porta de entrada)        |          Tipo do Dispositivo - Nó ou Gateway      |
|          Fabricante            |         Nome do fabricante    |
|  Código de Produto                    |  Código do produto do dispositivo ou nome ou número do modelo. Por exemplo, EnviroMonitor#6800.  |
|            Portas          |     Nome e tipo de porta, que é digital ou analógico.
|     Name                 |  Nome para identificar o recurso. Por exemplo, o nome do modelo ou o nome do produto.
      Descrição     | Forneça uma descrição significativa do modelo.
|    Propriedades          |    Propriedades adicionais do fabricante.   |
|    **Dispositivo**             |                      |
|   DeviceModelId     |     IDENTIFICAÇÃO do modelo de dispositivo associado.  |
|  HardwareId          | Identificação única para o dispositivo, como o endereço MAC.
|  Intervalo de Reporte        |   Intervalo de reporte em segundos.
|  Localização            |  Latitude do dispositivo (-90 a +90), longitude (-180 a 180) e elevação (em metros).
|ParentDeviceid       |    Identificação do dispositivo-mãe ao qual este dispositivo está ligado. Por exemplo, um nó que está ligado a um portal. Um nó tem o paiDeviceId como porta de entrada.  |
|    Name            | Um nome para identificar o recurso. Os parceiros do dispositivo devem enviar um nome consistente com o nome do dispositivo no lado do parceiro. Se o nome do dispositivo parceiro for definido pelo utilizador, o mesmo nome definido pelo utilizador deve ser propagado ao FarmBeats.|
|     Descrição       |      Forneça uma descrição significativa. |
|     Propriedades    |  Propriedades adicionais do fabricante.
|     **Modelo de Sensores**        |          |
|       Tipo (analógico, digital)          |      O tipo de sensor, seja analógico ou digital.       |
|          Fabricante            |       O fabricante do sensor.     |
|     Código de Produto| Código do produto ou nome ou número do modelo. Por exemplo, RS-CO2-N01. |
|       SensorMeasures > Nome       | Nome da medida do sensor. Só a minúscula é suportada. Para medições de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Este nome deve ser consistente com os dados da telemetria.  |
|          SensorMes > DataType       |Tipo de dados de telemetria. Atualmente, o dobro é suportado.|
|    SensorMedidas > tipo    |Tipo de medição dos dados de telemetria do sensor. Os tipos definidos pelo sistema são AmbientTemperature, CO2, Profundidade, ElectricConductivity, LeafWetness, Length, LiquidLevel, Nitrato, O2, PH, Phosfato, PointInTime, Potássio, Pressão, RainGauge, Humidade Relativa, Salinidade, Humidade do Solo, SoilTemperature, Radiação Solar, Estado, Duração do Tempo, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapiration, Para adicionar mais, consulte a API /ExtendedType.|
|        SensorMeasures > Unit              | Unidade de dados de telemetria de sensores. As unidades definidas pelo sistema são NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercúrio, PSI, Millimeter, Centimeter, Meter, Polegada, Pés, Milha, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerSquaredPerSecond, InchesPerHour|
|    SensorMeasures > AgregaçãoType    |  Os valores não podem ser, médios, máximos, mínimos ou StandardDeviation.  |
|          Name            | Nome para identificar um recurso. Por exemplo, o nome do modelo ou o nome do produto.  |
|    Descrição        | Forneça uma descrição significativa do modelo.|
|   Propriedades       |  Propriedades adicionais do fabricante.|
|    **Sensor**      |          |
| HardwareId          |   ID único para o sensor definido pelo fabricante.|
|  SensorModelid     |    Identificação do modelo de sensor associado.|
| Localização          |  Latitude do sensor (-90 a +90), longitude (-180 a 180) e elevação (em metros).|
|   Nome > do Porto        |  Nome e tipo da porta a que o sensor está ligado no dispositivo. Este tem de ter o mesmo nome definido no modelo do dispositivo.|
|    DispositivoID  |    Identificação do dispositivo a que o sensor está ligado. |
| Name            |   Nome para identificar recurso. Por exemplo, nome do sensor ou nome do produto e número de modelo ou código do produto.|
|    Descrição      | Forneça uma descrição significativa.|
|    Propriedades        |Propriedades adicionais do fabricante.|

Para mais informações sobre objetos, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Pedido da API para criar metadados

Para efetuar um pedido de API, combina o método HTTP (POST), o URL ao serviço API e o URI a um recurso para consultar, submeter dados, criar ou apagar um pedido. Em seguida, adicione um ou mais cabeçalhos de pedido HTTP. O URL do serviço API é o ponto final da API, isto é, o URL datahub (https://\<o seu datahub>.azurewebsites.net).

### <a name="authentication"></a>Autenticação

FarmBeats Datahub utiliza a autenticação ao portador, que necessita das seguintes credenciais geradas na secção anterior:

- ID de Cliente
- Segredo do cliente
- ID do inquilino

Usando estas credenciais, o chamador pode solicitar um sinal de acesso. O símbolo deve ser enviado nos pedidos subsequentes da API, na secção cabeçalho, da seguinte forma:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

A seguinte amostra O código Python dá o sinal de acesso, que pode ser usado para chamadas subsequentes da API para FarmBeats: 

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```

**HTTP solicitar cabeçalhos**

Aqui estão os cabeçalhos de pedido mais comuns que devem ser especificados quando você faz uma chamada API para FarmBeats Datahub:

- **Tipo de conteúdo**: aplicação/json
- **Autorização**: Porta<> de acesso a fichas
- **Aceitar**: aplicação/json

### <a name="input-payload-to-create-metadata"></a>Carga útil de entrada para criar metadados

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

Modelo de Sensores

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
Sensor

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

O seguinte pedido de amostra cria um dispositivo. Este pedido tem a entrada JSON como carga útil com o organismo de pedido.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",
\"reportingInterval\": 900,  \"name\": \"Device123\",
\"description\": \"Test Device 123\"}" *
```

Abaixo está um código de amostra em Python. O símbolo de acesso utilizado nesta amostra é o mesmo que é recebido durante a autenticação.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> As APIs devolvem iDs únicos para cada instância criada. Deve reter as identificações para enviar as mensagens de telemetria correspondentes.

### <a name="send-telemetry"></a>Enviar telemetria

Agora que criou os dispositivos e sensores em FarmBeats, pode enviar as mensagens de telemetria associadas.

### <a name="create-a-telemetry-client"></a>Criar um cliente de telemetria

Você deve enviar a telemetria para Azure Event Hubs para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) de dispositivos e aplicações conectados. Para enviar dados de telemetria para farmBeats, crie um cliente que envie mensagens para um centro de eventos em FarmBeats. Para mais informações sobre o envio de telemetria, consulte O Hubs de [Eventos Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Envie uma mensagem de telemetria como cliente

Depois de ter uma ligação estabelecida como cliente do Event Hubs, pode enviar mensagens para o centro do evento como JSON.

Aqui está a amostra do código Python que envia telemetria como cliente para um centro de eventos especificado:

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

Converta o formato histórico de dados de sensores num formato canónico que o Azure FarmBeats entende. O formato de mensagem canónica é o seguinte:

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

Depois de adicionar os dispositivos e sensores correspondentes, obtenha o ID do dispositivo e o ID do sensor na mensagem de telemetria, conforme descrito na secção anterior.

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

**Ação corretiva:**

1. Certifique-se de que fez o registo adequado do parceiro - pode verificar isso indo ao seu datahub swagger, navegar para /Partner API, Domar um Get e verificar se o parceiro está registado. Caso contrário, siga os [passos aqui](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar parceiro.

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

Para mais informações sobre detalhes de integração baseados em REST API, consulte [REST API](rest-api-in-azure-farmbeats.md).
