---
title: Integração de parceiros de sensores
description: Este artigo descreve a integração do parceiro sensor.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ef74c4b799c3a24636f88a8e704bf726104b034f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001597"
---
# <a name="sensor-partner-integration"></a>Integração de parceiros de sensores

Este artigo fornece informações sobre o componente Azure FarmBeats **Tradutor,** que permite a integração de parceiros sensores.

Utilizando este componente, os parceiros podem integrar-se com farmbeats usando As APIs do FarmBeats Datahub e enviar dados de dispositivos de clientes e telemetria para FarmBeats Datahub. Uma vez que os dados estão disponíveis em FarmBeats, é visualizado usando o Acelerador FarmBeats e pode ser usado para fusão de dados e para construir modelos de aprendizagem automática/inteligência artificial.

## <a name="before-you-start"></a>Antes de começar

Para desenvolver o componente Tradutor, necessitará das seguintes credenciais que permitirão o acesso às APIs FarmBeats.

- Ponto Final de API
- ID do inquilino
- ID de Cliente
- Segredo do Cliente
- Cadeia de conexão EventHub

Consulte esta secção para obter as credenciais acima: [Ative a integração do dispositivo](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Desenvolvimento de tradutores

**REST Integração baseada em API**

As capacidades de integração de dados dos Sensores de FarmBeats são expostas através da API REST. As capacidades incluem definição de metadados, provisão de dispositivos e sensores, e gestão de dispositivos e sensores.

**Ingestão de telemetria**

Os dados da telemetria são mapeados para uma mensagem canónica publicada no Azure Event Hubs para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) a partir de dispositivos e aplicações conectados.

**Desenvolvimento da API**

As APIs contêm documentação técnica da Swagger. Para obter mais informações sobre as APIs e os respetivos pedidos ou respostas, consulte [Swagger](https://aka.ms/FarmBeatsSwagger).

**Autenticação**

FarmBeats utiliza a autenticação do Microsoft Azure Ative Directory.O Azure App Service fornece suporte de autenticação e autorização incorporado.

Para mais informações, consulte [o Diretório Ativo Azure.](../../app-service/overview-authentication-authorization.md)

FarmBeats Datahub utiliza autenticação ao portador, que necessita das seguintes credenciais:
   - ID de Cliente
   - Segredo do cliente
   - ID do inquilino

Usando estas credenciais, o chamador pode solicitar um token de acesso. O token deve ser enviado nos pedidos subsequentes da API, na secção de cabeçalho, da seguinte forma:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

A seguinte amostra do código Python dá o token de acesso, que pode ser usado para chamadas subsequentes da API para FarmBeats.

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


**HTTP solicitam cabeçalhos**

Aqui estão os cabeçalhos de pedido mais comuns que precisam de ser especificados quando fizer uma chamada da API para o FarmBeats Datahub.


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Content-Type | O formato de pedido (Tipo de Conteúdo: aplicação/ <format> ). Para as APIs do FarmBeats Datahub, o formato é JSON. Tipo de conteúdo: aplicação/json
Autorização | Especifica o token de acesso necessário para fazer uma chamada da API. Autorização: <Access-Token> portador
Aceitar | O formato de resposta. Para as APIs do FarmBeats Datahub, o formato é JSON. Aceitar: candidatura/json

**Pedidos de API**

Para esclar o pedido de API REST, combina o método HTTP (GET, POST ou PUT), o URL ao serviço API, o Identificador de Recursos Uniformes (URI) a um recurso para consultar, submeter dados para, atualizar ou apagar, e um ou mais cabeçalhos de pedido HTTP. O URL para o serviço API é o ponto final da API que fornece. Aqui está uma amostra: https:// \<yourdatahub-website-name> .azurewebsites.net

Opcionalmente, pode incluir parâmetros de consulta em chamadas GET para filtrar, limitar o tamanho e ordenar os dados nas respostas.

O seguinte pedido de amostra é obter a lista de dispositivos.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
A maioria das chamadas GET, POST e PUT requerem um corpo de pedido JSON.

O seguinte pedido de amostra é a criação de um dispositivo. (Esta amostra tem uma entrada JSON com o corpo de pedido.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato de dados

JSON é um formato comum de dados independente da linguagem que fornece uma simples representação de texto de estruturas de dados arbitrárias. Para mais informações, consulte [json.org.](http://json.org)

## <a name="metadata-specifications"></a>Especificações de metadados

O FarmBeats Datahub tem as seguintes APIs que permitem aos parceiros de dispositivos criar e gerir metadados de dispositivos ou sensores.

- /**DeviceModel**: DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é um gateway ou nó.
- /**Dispositivo**: O dispositivo corresponde a um dispositivo físico presente na exploração.
- /**SensorModel**: SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medida do sensor, como a temperatura e a pressão ambiente.
- /**Sensor**: O sensor corresponde a um sensor físico que regista valores. Um sensor é normalmente ligado a um dispositivo com um ID do dispositivo.

  DeviceModel | Descrição |
  --- | ---
  Tipo (nó, porta de entrada)  | Tipo do dispositivo - Nó ou Gateway |
  Fabricante  | Nome do fabricante |
  Código de Produto  | Código do produto do dispositivo ou nome ou número do modelo. Por exemplo, EnviroMonitor#6800. |
  Portas  | Nome e tipo portuário, que é digital ou analógico.  |
  Nome  | Nome para identificar recurso. Por exemplo, nome do modelo ou nome do produto. |
  Descrição  | Forneça uma descrição significativa do modelo. |
  Propriedades  | Propriedades adicionais do fabricante. |
  **Dispositivo** | **Descrição** |
  DeviceModelId  |Identificação do modelo do dispositivo associado. |
  HardwareId   |ID único para o dispositivo, como um endereço MAC.  |
  ReporteInterval |Intervalo de reportagem em segundos. |
  Localização    |Latitude do dispositivo (-90 a +90), longitude (-180 a 180) e elevação (em metros). |
  ParentDeviceId | ID do dispositivo-mãe ao qual este dispositivo está ligado. Por exemplo, se um nó estiver ligado a um gateway, o nó tem o paiDeviceID como porta de entrada. |
  Nome  | Nome para identificar o recurso. Os parceiros do dispositivo precisam de enviar um nome consistente com o nome do dispositivo no lado do parceiro do dispositivo. Se o nome do dispositivo for definido pelo utilizador no lado do parceiro do dispositivo, o mesmo nome definido pelo utilizador deve ser propagado ao FarmBeats.  |
  Descrição  | Forneça uma descrição significativa.  |
  Propriedades  |Propriedades adicionais do fabricante.  |
  **SensorModel** | **Descrição** |
  Tipo (analógico, digital)  |Mencione sensor analógico ou digital.|
  Fabricante  | Nome do fabricante. |
  Código de Produto  | Código do produto ou nome ou número do modelo. Por exemplo, RS-CO2-N01.  |
  SensorMeasures > Nome  | Nome da medida do sensor. Apenas a minúscula é suportada. Para medições de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Este nome tem de ser consistente com os dados da telemetria. |
  SensorMeasures > DataType  | Tipo de dados de telemetria. Atualmente, o dobro é suportado. |
  Tipo de > sensores  | Tipo de medição dos dados de telemetria do sensor. Seguem-se os tipos definidos pelo sistema: AmbientTemperature, CO2, Depth, ElectricConductivity, LeafWetness, Comprimento, LiquidLevel, Nitrato, O2, PH, Fosfato, PointInTime, Potássio, Pressão, RainGauge, RelativaHumidity, Salinidade, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapopiration, PAR. Para adicionar mais, consulte a API /ExtendedType.
  Unidade de > sensores | Unidade de dados de telemetria sensor. Seguem-se as unidades definidas pelo sistema: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercúrio, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPercentiMeter, Centibar, Deci SiemensPerMeter, KiloPascal, VolumetricIonContent, Litro, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond e InchesPerHour. Para adicionar mais, consulte a API /ExtendedType.
  SensorMeasures > A agregaçãoType  | Ou nenhuma, média, máxima, mínima ou StandardDeviation.
  SensorMeasures > Profundidade  | A profundidade do sensor em centímetros. Por exemplo, a medição da humidade a 10 cm debaixo do solo.
  SensorMeasures > Descrição  | Fornecer uma descrição significativa da medição.
  Nome  | Nome para identificar recurso. Por exemplo, o nome do modelo ou o nome do produto.
  Descrição  | Forneça uma descrição significativa do modelo.
  Propriedades  | Propriedades adicionais do fabricante.
  **Sensor**  | **Descrição** |
  HardwareId  | ID único para o sensor definido pelo fabricante.
  SensorModelId  | Identificação do modelo de sensor associado.
  Localização  | Latitude sensora (-90 a +90), longitude (-180 a 180) e elevação (em metros).
  Nome > Do Porto  |Nome e tipo da porta à qual o sensor está ligado ao dispositivo. Este deve ter o mesmo nome definido no modelo do dispositivo.
  DeviceId  | Identificação do dispositivo a que o sensor está ligado.
  Nome  | Nome para identificar o recurso. Por exemplo, o nome do sensor ou nome do produto e o número do modelo ou código do produto.
  Descrição  | Forneça uma descrição significativa.
  Propriedades  | Propriedades adicionais do fabricante.

 Para obter informações sobre cada um dos objetos e suas propriedades, consulte [Swagger.](https://aka.ms/FarmBeatsDatahubSwagger)

 > [!NOTE]
 > As APIs devolvem IDs únicos para cada instância criada. Este ID precisa de ser retido pelo Tradutor para a gestão do dispositivo e sincronização de metadados.


**Sincronização de metadados**

O Tradutor deve enviar atualizações sobre os metadados. Por exemplo, os cenários de atualização são a alteração do nome do dispositivo ou do sensor e a alteração da localização do dispositivo ou do sensor.

O Tradutor deverá ter a capacidade de adicionar novos dispositivos ou sensores que tenham sido instalados pela ligação do post de utilizador do FarmBeats. Da mesma forma, se um dispositivo ou sensor foi atualizado pelo utilizador, o mesmo deve ser atualizado em FarmBeats para o dispositivo ou sensor correspondente. Os cenários típicos que requerem a atualização de um dispositivo ou sensor são uma alteração na localização do dispositivo ou a adição de sensores num nó.


> [!NOTE]
> A eliminação não é suportada por metadados de dispositivos ou sensores.
>
> Para atualizar metadados, é obrigatório ligar /Get/{id} no dispositivo ou sensor, atualizar as propriedades alteradas e, em seguida, fazer um /Put/{id} para que quaisquer propriedades definidas pelo utilizador não se percam.

### <a name="add-new-types-and-units"></a>Adicionar novos tipos e unidades

O FarmBeats suporta a adição de novos tipos e unidades de medida de sensor. Para obter mais informações sobre a API /ExtendedType, consulte [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Especificações de telemetria

Os dados da telemetria são mapeados para uma mensagem canónica publicada no Azure Event Hubs para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) a partir de dispositivos e aplicações conectados.

## <a name="send-telemetry-data-to-farmbeats"></a>Enviar dados de telemetria para FarmBeats

Para enviar dados de telemetria para FarmBeats, crie um cliente que envie mensagens para um centro de eventos em FarmBeats. Para obter mais informações sobre dados de telemetria, consulte [enviar telemetria para um centro de eventos.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Aqui está uma amostra do código Python que envia telemetria como cliente para um centro de eventos especificado.

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

O formato de mensagem canónica é o seguinte:

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
Todos os nomes-chave da telemetria JSON devem ser minúsculos. Exemplos são deviceid e sensores.

Por exemplo, aqui está uma mensagem de telemetria:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

> [!NOTE]
> As seguintes secções estão relacionadas com outras alterações (por exemplo. UI, gestão de erros, etc.) que o parceiro sensor pode se referir no desenvolvimento do componente Tradutor.


## <a name="link-a-farmbeats-account"></a>Ligue uma conta FarmBeats

Depois de os clientes terem adquirido e implantado dispositivos ou sensores, podem aceder aos dados do dispositivo e telemetria no software dos parceiros do dispositivo como portal de serviço (SaaS). Os parceiros de dispositivos podem permitir que os clientes liguem a sua conta à sua instância FarmBeats no Azure, fornecendo uma forma de inserir as seguintes credenciais:

   - Nome de exibição (um campo opcional para os utilizadores definirem um nome para esta integração)
   - Ponto final de API
   - ID do inquilino
   - ID de Cliente
   - Segredo do cliente
   - Cadeia de conexão EventHub
   - Data de início

   > [!NOTE]
   > A data de início permite o feed de dados históricos, ou seja, os dados a partir da data especificada pelo utilizador.

## <a name="unlink-farmbeats"></a>Unlink FarmBeats

Os parceiros de dispositivos podem permitir que os clientes desvinculam uma integração FarmBeats existente. Desvincular os FarmBeats não deve eliminar nenhum dispositivo ou metadados de sensores criados no Datahub farmbeats. Desvinculação faz o seguinte:

   - Para o fluxo de telemetria.
   - Elimina e apaga as credenciais de integração no parceiro do dispositivo.

## <a name="edit-farmbeats-integration"></a>Editar a integração farmbeats

Os parceiros de dispositivos podem permitir que os clientes editem as definições de integração farmBeats se o segredo do cliente ou a cadeia de ligação mudar. Neste caso, apenas os seguintes campos são editáveis:

   - Nome do visor (se aplicável)
   - Segredo do cliente (deve ser exibido no formato "2x8****************** "ou na funcionalidade Show/Hide em vez de texto claro)
   - Cadeia de ligação (deve ser exibida no formato "2x8********************** ou recurso Show/Hide em vez de texto claro)

## <a name="view-the-last-telemetry-sent"></a>Ver a última telemetria enviada

Os parceiros de dispositivos podem permitir que os clientes vejam o tempotagrama da última telemetria que foi enviada, que é encontrada no âmbito da **Telemetria Enviada**. Este é o momento em que a mais recente telemetria foi enviada com sucesso para a FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Resolução de problemas e gestão de erros

**Opção de resolução de problemas ou suporte**

Se o cliente não conseguir receber dados do dispositivo ou telemetria na instância FarmBeats especificada, o parceiro do dispositivo deve fornecer suporte e um mecanismo para a resolução de problemas.

**Retenção de dados de telemetria**

Os dados da telemetria também devem ser conservados por um período de tempo predefinido, de modo a que possa ser útil para depurar ou reenressar a telemetria se ocorrer um erro ou perda de dados.

**Gestão de erros ou notificação de erro**

Se um erro afetar o dispositivo ou metadados do sensor ou o fluxo de dados de integração de dados ou telemetria no sistema de parceiros do dispositivo, o cliente deve receber uma notificação. Deve também ser concebido e implementado um mecanismo para resolver eventuais erros.

**Lista de verificação de ligação**

Os fabricantes ou parceiros do dispositivo podem utilizar a seguinte lista de verificação para garantir que as credenciais fornecidas pelo cliente são precisas:

   - Verifique se um token de acesso é recebido com as credenciais que foram fornecidas.
   - Verifique se uma chamada da API tem sucesso com o token de acesso que foi recebido.
   - Verifique se a ligação ao cliente do EventHub está estabelecida.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a API REST, consulte [REST API.](rest-api-in-azure-farmbeats.md)