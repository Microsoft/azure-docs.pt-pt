---
title: Integração de parceiros de sensores
description: Este artigo descreve a integração de parceiros de sensores.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e4b2e7c40295d134fe24def0f140bc8097c21250
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132838"
---
# <a name="sensor-partner-integration"></a>Integração de parceiros de sensores

Este artigo fornece informações sobre a **componente** Tradutor Azure FarmBeats, que permite a integração de parceiros de sensores.

Utilizando este componente, os parceiros podem integrar-se com farmBeats usando a FarmBeats Data hub APIs e enviar dados do dispositivo do cliente e telemetria para o centro de dados FarmBeats. Uma vez que os dados estão disponíveis no FarmBeats, é visualizado usando o Acelerador FarmBeats e pode ser usado para a fusão de dados e para a construção de modelos de aprendizagem automática/inteligência artificial.

## <a name="before-you-start"></a>Antes de começar

Para desenvolver a componente Tradutor, necessitará das seguintes credenciais que permitirão o acesso às APIs FarmBeats.

- Ponto Final de API
- ID do inquilino
- ID de Cliente
- Segredo do Cliente
- String de conexão EventHub

Consulte esta secção para obter as credenciais acima: Ativar a [integração do dispositivo](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Desenvolvimento de tradutores

**Integração baseada em REST API**

As capacidades de integração de dados de sensores da FarmBeats são expostas através da API REST. As capacidades incluem definição de metadados, fornecimento de dispositivos e sensores, e gestão de dispositivos e sensores.

**Ingestão de telemetria**

Os dados da telemetria estão mapeados para uma mensagem canónica publicada no Azure Event Hubs para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) de dispositivos e aplicações conectados.

**Desenvolvimento da API**

As APIs contêm documentação técnica da Swagger. Para obter mais informações sobre as APIs e os seus pedidos ou respostas correspondentes, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**Autenticação**

FarmBeats usa autenticação do Microsoft Azure Ative Directory. O Azure App Service fornece autenticação incorporada e suporte de autorização.

Para mais informações, consulte [o Diretório Ativo azure.](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)

O centro de dados FarmBeats utiliza a autenticação ao portador, que necessita das seguintes credenciais:
   - ID de Cliente
   - Segredo do cliente
   - ID do inquilino

Usando estas credenciais, o chamador pode solicitar um sinal de acesso. O símbolo deve ser enviado nos pedidos subsequentes da API, na secção cabeçalho, da seguinte forma:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

A amostra seguinte O código Python dá o sinal de acesso, que pode ser usado para chamadas subsequentes da API para FarmBeats.

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


**HTTP solicitar cabeçalhos**

Aqui estão os cabeçalhos de pedido mais comuns que precisam de ser especificados quando você faz uma chamada API para farmBeats Data hub.


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo | O formato de pedido (Tipo de conteúdo: aplicação/<format>). Para a FarmBeats Data hub APIs, o formato é JSON. Tipo de conteúdo: aplicação/json
Autorização | Especifica o sinal de acesso necessário para fazer uma chamada aPi. Autorização: Bearer <Access-Token>
Aceitar | O formato de resposta. Para a FarmBeats Data hub APIs, o formato é JSON. Aceitar: aplicação/json

**Pedidos da API**

Para fazer um pedido de API REST, combina o método HTTP (GET, POST ou PUT), o URL ao serviço API, o Uniform Resource Identifier (URI) a um recurso para consultar, submeter dados para, atualizar ou eliminar, e um ou mais cabeçalhos de pedido http. O URL do serviço API é o ponto final da API que fornece. Aqui está uma amostra: https://\<o seu datahub-website-name>.azurewebsites.net

Opcionalmente, pode incluir parâmetros de consulta nas chamadas GET para filtrar, limitar o tamanho e classificar os dados nas respostas.

O seguinte pedido de amostra é obter a lista de dispositivos.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
A maioria das chamadas GET, POST e PUT requerem um corpo de pedido da JSON.

O seguinte pedido de amostra é criar um dispositivo. (Esta amostra tem uma entrada JSON com o organismo de pedido.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato de dados

JSON é um formato de dados independente da linguagem comum que fornece uma representação simples de texto de estruturas de dados arbitrárias. Para mais informações, consulte [json.org](http://json.org).

## <a name="metadata-specifications"></a>Especificações de metadados

O centro de dados FarmBeats tem as seguintes APIs que permitem aos parceiros do dispositivo criar e gerir metadados de dispositivos ou sensores.

- /**DeviceModel**: DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é gateway ou nó.
- **dispositivo**/: O dispositivo corresponde a um dispositivo físico presente na exploração.
- /**SensorModel**: SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medida do sensor, como temperatura ambiente e pressão.
- **sensor**/: Sensor corresponde a um sensor físico que regista valores. Um sensor é normalmente ligado a um dispositivo com um ID do dispositivo.

  **Modelo de Dispositivos** |  |
  --- | ---
  Tipo (nó, porta de entrada)  | Tipo do dispositivo - Nó ou Gateway |
  Fabricante  | Nome do fabricante |
  Código de Produto  | Código do produto do dispositivo ou nome ou número do modelo. Por exemplo, EnviroMonitor#6800. |
  Portas  | Nome e tipo de porta, que é digital ou analógico.  |
  Nome  | Nome para identificar recurso. Por exemplo, nome do modelo ou nome do produto. |
  Descrição  | Forneça uma descrição significativa do modelo. |
  Propriedades  | Propriedades adicionais do fabricante. |
  **Dispositivo** |  |
  DeviceModelId  |IDENTIFICAÇÃO do modelo de dispositivo associado. |
  HardwareId   |Identificação única para o dispositivo, como um endereço MAC.  |
  Intervalo de Reporte |Intervalo de reporte em segundos. |
  Localização    |Latitude do dispositivo (-90 a +90), longitude (-180 a 180) e elevação (em metros). |
  ParentDeviceid | Identificação do dispositivo-mãe ao qual este dispositivo está ligado. Por exemplo, se um nó estiver ligado a um portal, o nó tem o paiDeviceID como porta de entrada. |
  Nome  | Nome para identificar o recurso. Os parceiros do dispositivo precisam de enviar um nome consistente com o nome do dispositivo no lado do parceiro do dispositivo. Se o nome do dispositivo for definido pelo utilizador no lado do parceiro do dispositivo, o mesmo nome definido pelo utilizador deve ser propagado ao FarmBeats.  |
  Descrição  | Forneça uma descrição significativa.  |
  Propriedades  |Propriedades adicionais do fabricante.  |
  **Modelo de Sensores** |  |
  Tipo (analógico, digital)  |Mencione o sensor analógico ou digital.|
  Fabricante  | Nome do fabricante. |
  Código de Produto  | Código do produto ou nome ou número do modelo. Por exemplo, RS-CO2-N01.  |
  SensorMeasures > Nome  | Nome da medida do sensor. Só a minúscula é suportada. Para medições de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Este nome tem de ser consistente com os dados da telemetria. |
  SensorMeasures > DataType  | Tipo de dados de telemetria. Atualmente, o dobro é suportado. |
  SensorMeasures > Tipo  | Tipo de medição dos dados de telemetria do sensor. Seguem-se os tipos definidos pelo sistema: AmbienteTemperatura, CO2, Profundidade, Condutividade Elétrica, LeafWetness, Comprimento, Nível Líquido, Nitrato, O2, PH, Fosfato, PointInTime, Potássio, Pressão, RainGauge, Humidade Relativa, Salinidade, Humidade do Solo, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Para adicionar mais, consulte a API /ExtendedType.
  SensorMeasures > Unidade | Unidade de dados de telemetria de sensores. Seguem-se as unidades definidas pelo sistema: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercúrio, PSI, MilliMeter, Centímetro, Meter, Polegada, Pés, Milha, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliwattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond e InchesPerHour. Para adicionar mais, consulte a API /ExtendedType.
  SensorMeasures > AgregaçãoType  | Ou nenhuma, média, máxima, mínima ou StandardDeviation.
  SensorMeasures > Profundidade  | A profundidade do sensor em centímetros. Por exemplo, a medição da humidade 10 cm debaixo do solo.
  SensorMeasures > Descrição  | Forneça uma descrição significativa da medição.
  Nome  | Nome para identificar recurso. Por exemplo, o nome do modelo ou o nome do produto.
  Descrição  | Forneça uma descrição significativa do modelo.
  Propriedades  | Propriedades adicionais do fabricante.
  **Sensor**  |  |
  HardwareId  | ID único para o sensor definido pelo fabricante.
  SensorModelid  | Identificação do modelo de sensor associado.
  Localização  | Latitude do sensor (-90 a +90), longitude (-180 a 180) e elevação (em metros).
  Porto > Nome  |Nome e tipo da porta a que o sensor está ligado no dispositivo. Este deve ser o mesmo nome definido no modelo do dispositivo.
  DeviceId  | Identificação do dispositivo a que o sensor está ligado.
  Nome  | Nome para identificar o recurso. Por exemplo, o nome do sensor ou nome do produto e o número do modelo ou código do produto.
  Descrição  | Forneça uma descrição significativa.
  Propriedades  | Propriedades adicionais do fabricante.

 Para obter informações sobre cada um dos objetos e suas propriedades, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > As APIs devolvem iDs únicos para cada instância criada. Este ID precisa de ser conservado pelo Tradutor para a gestão de dispositivos e sincronização de metadados.


**Sincronização de metadados**

O Tradutor deve enviar atualizações sobre os metadados. Por exemplo, os cenários de atualização são alterações do nome do dispositivo ou do sensor e mudança de dispositivo ou localização do sensor.

O Tradutor deve ter a capacidade de adicionar novos dispositivos ou sensores que foram instalados pela ligação do post do utilizador do FarmBeats. Da mesma forma, se um dispositivo ou sensor foi atualizado pelo utilizador, o mesmo deve ser atualizado no FarmBeats para o dispositivo ou sensor correspondente. Cenários típicos que requerem a atualização de um dispositivo ou sensor são uma alteração na localização de um dispositivo ou a adição de sensores num nó.


> [!NOTE]
> A eliminação não é suportada para metadados de dispositivos ou sensores.
>
> Para atualizar os metadados, é obrigatório ligar para /Get/{id} no dispositivo ou sensor, atualizar as propriedades alteradas e, em seguida, fazer um /Put/{id} para que quaisquer propriedades definidas pelo utilizador não se percam.

### <a name="add-new-types-and-units"></a>Adicione novos tipos e unidades

FarmBeats suporta a adição de novos tipos e unidades de medida de sensor. Para mais informações sobre a API /ExtendedType, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Especificações de telemetria

Os dados da telemetria estão mapeados para uma mensagem canónica publicada no Azure Event Hubs para processamento. O Azure Event Hubs é um serviço que permite a ingestão de dados em tempo real (telemetria) de dispositivos e aplicações conectados.

## <a name="send-telemetry-data-to-farmbeats"></a>Envie dados de telemetria para FarmBeats

Para enviar dados de telemetria para farmBeats, crie um cliente que envie mensagens para um centro de eventos em FarmBeats. Para obter mais informações sobre dados de telemetria, consulte [Enviar telemetria para um centro de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

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
Todos os nomes-chave da telemetria JSON devem ser minúsculos. Exemplos são dados de dispositivos e sensores.

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
> As seguintes secções estão relacionadas com outras alterações (por exemplo. UI, gestão de erros, etc.) que o parceiro de sensor pode referir-se no desenvolvimento do componente Tradutor.


## <a name="link-a-farmbeats-account"></a>Link a FarmBeats conta

Depois de os clientes terem adquirido e implementado dispositivos ou sensores, podem aceder aos dados do dispositivo e telemetria no software dos parceiros do dispositivo como portal de serviço (SaaS). Os parceiros de dispositivos podem permitir que os clientes liguem a sua conta à sua instância FarmBeats no Azure, fornecendo uma forma de inserir as seguintes credenciais:

   - Nome de exibição (um campo opcional para os utilizadores definirem um nome para esta integração)
   - Ponto final da API
   - ID do inquilino
   - ID de Cliente
   - Segredo do cliente
   - Cadeia de ligação EventHub
   - Data de início

   > [!NOTE]
   > A data de início permite o feed de dados históricos, ou seja, os dados da data especificada pelo utilizador.

## <a name="unlink-farmbeats"></a>Unlink FarmBeats

Os parceiros de dispositivos podem permitir que os clientes desliguem uma integração farmBeats existente. Desvincular farmBeats não deve eliminar qualquer dispositivo ou sensor de metadados que tenha sido criado no centro de dados FarmBeats. Desvinculação faz o seguinte:

   - Para o fluxo de telemetria.
   - Elimina e apaga as credenciais de integração no parceiro do dispositivo.

## <a name="edit-farmbeats-integration"></a>Editar integração FarmBeats

Os parceiros do dispositivo podem permitir que os clientes editem as definições de integração FarmBeats se o segredo do cliente ou a cadeia de ligação mudar. Neste caso, apenas são editáveis os seguintes campos:

   - Nome de exibição (se aplicável)
   - Segredo do cliente (deve ser exibido em formato "2x8***************
   - Cadeia de ligação (deve ser exibida em formato "2x8*************** em vez de texto claro)

## <a name="view-the-last-telemetry-sent"></a>Veja a última telemetria enviada

Os parceiros de dispositivos podem permitir que os clientes vejam o carimbo temporal da última telemetria que foi enviada, que é encontrada no âmbito da **Telemettry Sent**. Este é o momento em que a última telemetria foi enviada com sucesso para farmBeats.

## <a name="troubleshooting-and-error-management"></a>Resolução de problemas e gestão de erros

**Opção de resolução de problemas ou suporte**

Se o cliente não conseguir receber dados do dispositivo ou telemetria na instância FarmBeats especificada, o parceiro do dispositivo deve fornecer suporte e um mecanismo de resolução de problemas.

**Retenção de dados de telemetria**

Os dados da telemetria também devem ser conservados por um período de tempo predefinido, de modo a que possa ser útil para depurar ou reenviar a telemetria se ocorrer um erro ou perda de dados.

**Gestão de erros ou notificação de erro**

Se um erro afetar os metadados do dispositivo ou do sensor ou a integração de dados ou o fluxo de dados de telemetria no sistema parceiro do dispositivo, o cliente deve receber uma notificação. Deve também ser concebido e implementado um mecanismo para resolver eventuais erros.

**Lista de verificação de ligação**

Os fabricantes ou parceiros de dispositivos podem utilizar a seguinte lista de verificação para garantir que as credenciais fornecidas pelo cliente são precisas:

   - Verifique se é recebida uma ficha de acesso com as credenciais fornecidas.
   - Verifique se uma chamada da API é bem sucedida com o sinal de acesso que foi recebido.
   - Verifique se a ligação ao cliente EventHub está estabelecida.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a API REST, consulte [rest API](rest-api-in-azure-farmbeats.md).
