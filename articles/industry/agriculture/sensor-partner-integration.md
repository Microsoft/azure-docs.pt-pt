---
title: Integração de parceiros de sensor
description: Descreve a integração de parceiros de sensor
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e90284ce2f8ea37eb9249822e38cef04e1356f59
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889587"
---
# <a name="sensor-partner-integration"></a>Integração de parceiros de sensor

Este artigo fornece informações sobre o componente Azure FarmBeats **Translator** , que habilita a integração de parceiros de sensor.

Usando esse componente, os parceiros podem desenvolver sensores que se integram ao FarmBeats, aproveitando nossa API e enviando dados de dispositivo do cliente e telemetria para o Hub de dados FarmBeats. Os dados são visualizados usando o acelerador FarmBeats. Os dados podem ser usados para a fusão de dados e para a criação de modelos de inteligência artificial/idioma da máquina.

## <a name="link-farmbeats-account"></a>Vincular conta do FarmBeats

Depois que os clientes tiverem comprado e implantado dispositivos/sensores, eles poderão acessar os dados do dispositivo e a telemetria no portal de SaaS dos parceiros de dispositivo (software como serviço). Os parceiros de dispositivo precisam permitir que os clientes vinculem suas contas à instância do FarmBeats no Azure. As seguintes credenciais são necessárias para preencher pelo integrador de cliente/sistema:

   - Nome de exibição (um campo opcional para o usuário definir um nome para essa integração)
   - Ponto de extremidade de API
   - ID do inquilino
   - ID de Cliente
   - Segredo do cliente
   - Cadeia de conexão do EventHub
   - Data de início

   > [!NOTE]
   > A data de início habilita o feed de dados históricos, ou seja, dados da data especificada pelo usuário.

## <a name="unlink-farmbeats"></a>Desvincular FarmBeats

Os clientes têm a capacidade de desvincular uma integração existente do FarmBeats. A desvinculação de FarmBeats não deve excluir nenhum metadado de dispositivo/sensor que foi criado no Hub de dados do cliente. A desvinculação faz o seguinte:

   - Interrompe o fluxo de telemetria.
   - Exclui e apaga as credenciais de integração no parceiro de dispositivo.

## <a name="edit-farmbeats-integration"></a>Editar integração do FarmBeats

O cliente pode editar as configurações de integração do FarmBeats, se a cadeia de conexão ou o segredo do cliente for alterado. Nesse caso, o cliente pode editar apenas os seguintes campos:

   - Nome de exibição (se aplicável)
   - Segredo do cliente (deve ser exibido no formato "2x8 * * * * * * * * * * *" ou mostrar/ocultar recurso em vez de texto não criptografado)
   - Cadeia de conexão (deve ser exibida no formato "2x8 * * * * * * * * * * *" ou mostrar/ocultar recurso em vez de texto não criptografado)

## <a name="view-last-telemetry-sent"></a>Exibir a última telemetria enviada

Você pode exibir o carimbo de data/hora da última **telemetria enviada**. Esta é a hora em que a telemetria mais recente foi enviada com êxito para FarmBeats.

## <a name="translator-development"></a>Desenvolvimento de Tradutor

**Integração baseada na API REST**

Os recursos de integração de dados de sensor do FarmBeats são expostos por meio da API REST. Os recursos incluem definição de metadados, provisionamento de dispositivo/sensor, dispositivo e gerenciamento de sensor.

**Ingestão de telemetria**

Os dados de telemetria são mapeados para uma mensagem canônica que é publicada no Hub de eventos do Azure para processamento. O Azure EventHub é um serviço que habilita a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados.

**Desenvolvimento de API**

As APIs contêm a documentação técnica do Swagger. Para obter mais informações sobre as APIs e suas solicitações/respostas correspondentes, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)

**Autenticação**

O FarmBeats aproveita a autenticação Active Directory do Microsoft Azure. O serviço de Azure App fornece autenticação interna e suporte de autorização.

Para obter mais informações, consulte [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

O FarmBeats data Hub usa a autenticação de portador, que precisa das seguintes credenciais:
   - ID de Cliente
   - Segredo do cliente
   - ID do inquilino

Usando as credenciais acima, o chamador pode solicitar um token de acesso, que precisa ser enviado nas solicitações de API subsequentes na seção de cabeçalho da seguinte maneira:

```json
headers = {"Authorization": "Bearer " + access_token, …} 
```

Veja abaixo um código Python de exemplo que fornece o token de acesso, que pode ser usado para chamadas de API subsequentes para FarmBeats: 

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

Aqui estão os cabeçalhos de solicitação mais comuns que precisam ser especificados ao fazer uma chamada à API para o Hub de dados FarmBeats:


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo | O formato de solicitação (Content-Type: Application/<format>) para o formato de APIs de Hub de dados FarmBeats é JSON. Tipo de conteúdo: aplicativo/JSON
Autorização | Especifica o token de acesso necessário para fazer uma chamada à API autorização: portador acesso < Access-token >
aceitar | O formato de resposta. Para APIs de Hub de dados FarmBeats, o formato é JSON Accept: aplicativo/JSON

**Solicitações de API**

Para fazer uma solicitação de API REST (transferência de estado de reapresentação), você combina o método HTTP (GET, POST ou PUT), a URL para o serviço de API, o URI (Uniform Resource Identifier) a um recurso para consultar, enviar dados para, atualizar ou excluir e uma ou mais solicitações HTTP conector. A URL para o serviço de API é o ponto de extremidade de API fornecido pelo cliente. Aqui está o exemplo: https://\<yourdatahub-site-Name >. azurewebsites. net

Opcionalmente, você pode incluir parâmetros de consulta em GET calls para filtrar, limitar o tamanho e classificar os dados nas respostas.

A solicitação de exemplo abaixo é obter a lista de dispositivos:

```azurepowershell-interactive
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```
A maioria das chamadas GET, POST e PUT exigem um corpo de solicitação JSON.

A solicitação de exemplo abaixo é criar um dispositivo (Este exemplo tem um JSON de entrada com o corpo da solicitação).

```azurepowershell-interactive
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato de dados

O JSON (JavaScript Object Notation) é um formato de dados comum e independente de linguagem que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para obter mais informações, consulte [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Especificações de metadados

O FarmBeats data Hub tem as seguintes APIs que permitem que os parceiros de dispositivo criem e gerenciem metadados de dispositivo/sensor.  

- /modelo de dispositivo **DeviceModel** corresponde aos metadados do dispositivo, como o fabricante, tipo de gateway ou nó.  
- /**dispositivo-dispositivo corresponde** a um dispositivo físico presente no farm.
- /modelo de sensor de **SensorModel** corresponde aos metadados do sensor, como o fabricante, tipo de sensor analógico ou digital, medida de sensor como temperatura ambiente, pressão etc.,
- /**sensor de sensor corresponde** a um sensor físico que registra valores. Um sensor normalmente é conectado a um dispositivo com uma ID de dispositivo.

  Modelo de dispositivo| DeviceModel corresponde aos metadados do dispositivo, como o fabricante, o tipo do dispositivo ou o gateway ou o nó.
  --- | ---
  Tipo (nó, gateway)  | 1 Estrela |
  Fabricante  | 2 Estrelas |
  ProductCode  | Código do produto do dispositivo ou nome/número do modelo. Por exemplo, EnviroMonitor # 6800 |
  Portas  | Nome da porta e tipo (digital/analógico)  |
  Nome  | Nome para identificar o recurso. Por exemplo, nome do modelo/nome do produto |
  Descrição  | Forneça uma descrição significativa do modelo |
  Propriedades  | Propriedades adicionais do fabricante |
  **Vice** | **O dispositivo corresponde a um dispositivo físico presente no farm. Cada dispositivo tem uma ID de dispositivo exclusiva** |
DeviceModelId  |ID do modelo de dispositivo associado. |
HardwareID   |ID exclusiva para o dispositivo, como endereço MAC, etc.,  |
reportingInterval |Intervalo de relatórios em segundos |
Localização    |Dispositivo latitude (-90 a + 90)/longitude (-180 a 180)/Elevation (em metros) |
ParentDeviceId | ID do dispositivo pai ao qual este dispositivo está conectado. Por exemplo,. Um nó conectado a um gateway; o nó terá parentDeviceID como o gateway |
  Nome  | Nome para identificar o recurso.  Os parceiros de dispositivo precisarão enviar um nome consistente com o nome do dispositivo no lado do parceiro do dispositivo. Se o nome do dispositivo for definido pelo usuário no lado do parceiro do dispositivo, o mesmo nome definido pelo usuário deverá ser propagado para FarmBeats  |
  Descrição  | Forneça uma descrição significativa  |
  Propriedades  |Propriedades adicionais do fabricante  |
  **Modelo de sensor** | SensorModel corresponde aos metadados do sensor, como o fabricante, tipo de sensor analógico ou digital, medida de sensor como temperatura ambiente, pressão etc. |
  Tipo (analógico, digital)  |Mencione o sensor analógico ou digital|
  manufacturer  | nome do fabricante |
  ProductCode  | Código do produto ou nome do modelo/número. Por exemplo, RS-CO2-N01  |
  Nome > SensorMeasures  | Nome da medida do sensor. Somente letras minúsculas têm suporte. Para medir de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm esse nome deve ser consistente com os dados de telemetria. |
  sensorMeasures > DataType  | Tipo de dados Telemétrico. Há suporte para o Double no momento  |
  Tipo de > SensorMeasures  | Tipo de medição dos dados de telemetria do sensor. A seguir estão os tipos definidos pelo sistema: AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, comprimento, LiquidLevel, nitrate, O2, PH, phosphate, PointInTime, Potassium, pressão, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, estado, tempo de duração, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Para adicionar mais, consulte API do/ExtendedType
  Unidade de > SensorMeasures | Unidade de dados de telemetria do sensor. A seguir estão as unidades definidas pelo sistema: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, mercúrio, PSI, milímetro, centímetro, medidor, polegada, pés, milhar, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, grau, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, porcentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litro, MilliLiter, segundos, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour para adicionar mais, consulte/ API estendida
  Agregatype de > SensorMeasures  | Nenhum, médio, máximo, mínimo, i
  Profundidade de > de SensorMeasures  | A profundidade do sensor em centímetros (por exemplo, medida de umidade de 10 cm sob o início)
  Descrição de > sensorMeasures  | Forneça uma descrição significativa da medida
  nome  | Nome para identificar o recurso. Por exemplo, nome do modelo/nome do produto
  descrição  | Forneça uma descrição significativa do modelo
  propriedades  | Propriedades adicionais do fabricante
  **Sensores**  |
  HardwareID  | ID exclusiva para o sensor definido pelo fabricante
  sensorModelId  | ID do modelo de sensor associado.
  localização  | Sensor latitude (-90 a + 90)/longitude (-180 a 180)/Elevation (em metros)
  Nome da > de porta  |Nome e tipo da porta à qual o sensor está conectado no dispositivo. Deve ser o mesmo nome definido no modelo do dispositivo
  deviceId  | ID do dispositivo ao qual o sensor está conectado
  nome  | Nome para identificar o recurso. Por exemplo, nome do sensor/nome do produto e número do modelo/código do produto.
  descrição  | Forneça uma descrição significativa
  propriedades  | Propriedades adicionais do fabricante

 Para obter informações sobre cada um dos objetos e suas propriedades, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > As APIs retornam IDs exclusivas para cada instância criada. Essa ID precisa ser retida pelo Tradutor para o gerenciamento de dispositivos e a sincronização de metadados.


**Sincronização de metadados**

O tradutor deve enviar atualizações nos metadados. Por exemplo, os cenários de atualização são: alteração do nome do dispositivo/sensor, alteração do local do dispositivo/sensor.

O tradutor deve ter a capacidade de adicionar novos dispositivos e/ou sensores que foram instalados pelo usuário após a vinculação de FarmBeats. Da mesma forma, se um dispositivo/sensor tiver sido atualizado pelo usuário, o mesmo deverá ser atualizado em FarmBeats para o dispositivo/sensor correspondente. Cenários típicos de atualização de dispositivo/sensor podem ser: alteração do local do dispositivo, adição de sensores em um nó etc.


> [!NOTE]
> A exclusão não tem suporte para metadados de dispositivo/sensor.
>
> Para atualizar os metadados, é obrigatório chamar/Get/{ID} no dispositivo/sensor, atualizar as propriedades alteradas e, em seguida, fazer uma/put/{ID} para que todas as propriedades definidas pelo usuário não sejam perdidas

### <a name="adding-new-typesunit"></a>Adicionando novos tipos/unidade

O FarmBeats dá suporte à adição de novas unidades e tipos de medidas de sensor. Para obter mais informações sobre a API do/ExtendedType, o [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Especificações de telemetria

Os dados de telemetria são mapeados para uma mensagem canônica que é publicada no Hub de eventos do Azure para processamento. O Azure EventHub é um serviço que habilita a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados.

## <a name="send-telemetry-data-to-farmbeats"></a>Enviar dados de telemetria para o FarmBeats

Para enviar dados de telemetria para o FarmBeats, você precisará criar um cliente que envia mensagens para um hub de eventos no FarmBeats. Para obter mais informações sobre dados de telemetria, consulte [enviando telemetria para o Hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Aqui está um código Python de exemplo que envia telemetria como um cliente para um hub de eventos especificado:

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

O formato de mensagem canônica é o seguinte:

```json
{
“deviceid”: “<id of the Device created>”,
 "timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>”
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": value
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": value
        }
      ]
    }
}

```

Todos os nomes de chave no JSON de telemetria devem estar em letras minúsculas, por exemplo, DeviceID, SensorData, etc.

Por exemplo, mensagem de telemetria:


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

## <a name="troubleshooterror-management"></a>Solução de problemas/gerenciamento de erros

**Solução de problemas/suporte**

Caso o cliente não possa receber dados do dispositivo e/ou telemetria na instância do FarmBeats especificada, o parceiro do dispositivo deve fornecer suporte e um mecanismo para solucionar os mesmos problemas.

**Retenção de dados de telemetria**

Os dados de telemetria também devem ser retidos por um período de tempo predefinido para que o mesmo possa ser útil na depuração ou reenvio da telemetria em caso de erro ou perda de dados.

**Notificação de erro/gerenciamento de erro**

No caso de um erro que afeta os metadados de dispositivo/sensor ou a integração de dados ou o fluxo de dados de telemetria no sistema de parceiro de dispositivo, o mesmo deve ser notificado ao cliente. Um mecanismo para resolver os erros também deve ser projetado e implementado.

**Lista de verificação de conexão**

Os fabricantes/parceiros de dispositivos podem ter a seguinte lista de verificação/teste de sanidade para garantir que as credenciais fornecidas pelo cliente sejam precisas.

   - Verificar se um token de acesso é recebido com as credenciais fornecidas
   - Verificar se uma chamada à API é realizada com sucesso com o token de acesso recebido
   - Verifique se a conexão do cliente EventHub foi estabelecida

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a API REST, consulte [REST API](references-for-farmbeats.md#rest-api).
