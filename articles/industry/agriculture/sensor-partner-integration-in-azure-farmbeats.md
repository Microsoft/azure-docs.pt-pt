---
title: Integração de parceiros de sensores
description: Este artigo descreve a integração de parceiros de sensor.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ef84c5f1d1c3f3b9e7cd76d7fab327426ea6e313
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530651"
---
# <a name="sensor-partner-integration"></a>Integração de parceiros de sensores

Este artigo fornece informações sobre o componente Azure FarmBeats **Translator** , que habilita a integração de parceiros de sensor.

Usando esse componente, os parceiros podem se integrar com o FarmBeats usando APIs de Hub de dados do FarmBeats e enviar dados de dispositivo do cliente e telemetria para o Hub de dados do FarmBeats. Depois que os dados estiverem disponíveis no FarmBeats, eles serão visualizados usando o acelerador FarmBeats e poderão ser usados para a fusão de dados e para a criação de modelos de inteligência artificial/aprendizado de máquina.

## <a name="before-you-start"></a>Antes de começar

Para desenvolver o componente do tradutor, você precisará das seguintes credenciais que permitirão o acesso às APIs do FarmBeats.

- Ponto Final de API
- ID do inquilino
- ID de Cliente
- Segredo do Cliente
- Cadeia de conexão do EventHub

Consulte esta seção para obter as credenciais acima: [habilitar a integração do dispositivo](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Desenvolvimento de Tradutor

**Integração baseada na API REST**

Os recursos de integração de dados de sensor do FarmBeats são expostos por meio da API REST. Os recursos incluem definição de metadados, provisionamento de dispositivo e sensor e gerenciamento de dispositivo e sensor.

**Ingestão de telemetria**

Os dados de telemetria são mapeados para uma mensagem canônica que é publicada nos hubs de eventos do Azure para processamento. Os hubs de eventos do Azure são um serviço que habilita a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados.

**Desenvolvimento de API**

As APIs contêm a documentação técnica do Swagger. Para obter mais informações sobre as APIs e suas solicitações ou respostas correspondentes, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**Autenticação**

O FarmBeats usa a autenticação Microsoft Azure Active Directory. O serviço de Azure App fornece autenticação interna e suporte de autorização.

Para obter mais informações, consulte [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

O FarmBeats data Hub usa a autenticação de portador, que precisa das seguintes credenciais:
   - ID de Cliente
   - Segredo do cliente
   - ID do inquilino

Usando essas credenciais, o chamador pode solicitar um token de acesso. O token precisa ser enviado nas solicitações de API subsequentes, na seção de cabeçalho, da seguinte maneira:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

O código Python de exemplo a seguir fornece o token de acesso, que pode ser usado para chamadas de API subsequentes para FarmBeats.

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

Aqui estão os cabeçalhos de solicitação mais comuns que precisam ser especificados quando você faz uma chamada à API para o Hub de dados FarmBeats.


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Content-Type | O formato da solicitação (Content-Type: Application/<format>). Para APIs de Hub de dados FarmBeats, o formato é JSON. Tipo de conteúdo: aplicativo/JSON
Autorização | Especifica o token de acesso necessário para fazer uma chamada à API. Autorização: portador de acesso < token >
Aceitar | O formato de resposta. Para APIs de Hub de dados FarmBeats, o formato é JSON. Aceitar: aplicativo/JSON

**Solicitações de API**

Para fazer uma solicitação de API REST, você combina o método HTTP (GET, POST ou PUT), a URL para o serviço de API, a Uniform Resource Identifier (URI) a um recurso para consultar, enviar dados para, atualizar ou excluir e um ou mais cabeçalhos de solicitação HTTP. A URL para o serviço de API é o ponto de extremidade de API que você fornece. Aqui está um exemplo: https://\<yourdatahub-site-Name >. azurewebsites. net

Opcionalmente, você pode incluir parâmetros de consulta em GET calls para filtrar, limitar o tamanho e classificar os dados nas respostas.

A seguinte solicitação de exemplo é obter a lista de dispositivos.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
A maioria das chamadas GET, POST e PUT exigem um corpo de solicitação JSON.

A seguinte solicitação de exemplo é criar um dispositivo. (Este exemplo tem um JSON de entrada com o corpo da solicitação.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato de dados

JSON é um formato de dados comum independente de linguagem que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para obter mais informações, consulte [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Especificações de metadados

O FarmBeats data Hub tem as seguintes APIs que permitem que os parceiros de dispositivo criem e gerenciem metadados de dispositivo ou sensor.

- /**DeviceModel**: DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é o gateway ou o nó.
- **dispositivo**/: o dispositivo corresponde a um dispositivo físico presente no farm.
- /**SensorModel**: SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medida do sensor, como temperatura ambiente e pressão.
- **sensor**de /: o sensor corresponde a um sensor físico que registra valores. Um sensor normalmente é conectado a um dispositivo com uma ID de dispositivo.

  **DeviceModel** |  |
  --- | ---
  Tipo (nó, gateway)  | Tipo do dispositivo-nó ou gateway |
  Fabricante  | Nome do fabricante |
  ProductCode  | Código do produto do dispositivo ou nome ou número do modelo. Por exemplo, EnviroMonitor # 6800. |
  Portas  | Nome da porta e tipo, que é digital ou analógica.  |
  Nome  | Nome para identificar o recurso. Por exemplo, nome do modelo ou nome do produto. |
  Descrição  | Forneça uma descrição significativa do modelo. |
  Propriedades  | Propriedades adicionais do fabricante. |
  **Dispositivo** |  |
  DeviceModelId  |ID do modelo de dispositivo associado. |
  HardwareID   |ID exclusiva para o dispositivo, como um endereço MAC.  |
  ReportingInterval |Intervalo de relatórios em segundos. |
  Localização    |Dispositivo latitude (-90 a + 90), longitude (-180 a 180) e elevação (em metros). |
  ParentDeviceId | ID do dispositivo pai ao qual este dispositivo está conectado. Por exemplo, se um nó estiver conectado a um gateway, o nó terá parentDeviceID como o gateway. |
  Nome  | Nome para identificar o recurso. Os parceiros de dispositivo precisam enviar um nome consistente com o nome do dispositivo no lado do parceiro do dispositivo. Se o nome do dispositivo for definido pelo usuário no lado do parceiro do dispositivo, o mesmo nome definido pelo usuário deverá ser propagado para FarmBeats.  |
  Descrição  | Forneça uma descrição significativa.  |
  Propriedades  |Propriedades adicionais do fabricante.  |
  **SensorModel** |  |
  Tipo (analógico, digital)  |Mencione o sensor analógico ou digital.|
  Fabricante  | Nome do fabricante. |
  ProductCode  | Código do produto ou nome do modelo ou número. Por exemplo, RS-CO2-N01.  |
  Nome > SensorMeasures  | Nome da medida do sensor. Somente letras minúsculas têm suporte. Para medições de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Esse nome deve ser consistente com os dados de telemetria. |
  SensorMeasures > DataType  | Tipo de dados Telemétrico. Atualmente, há suporte para Double. |
  Tipo de > SensorMeasures  | Tipo de medição dos dados de telemetria do sensor. A seguir estão os tipos definidos pelo sistema: AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, comprimento, LiquidLevel, nitrate, O2, PH, phosphate, PointInTime, Potassium, pressão, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, estado, tempo de duração, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Para adicionar mais, consulte a API do/ExtendedType.
  Unidade de > SensorMeasures | Unidade de dados de telemetria do sensor. A seguir estão as unidades definidas pelo sistema: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, mercúrio, PSI, milímetro, centímetro, medidor, polegada, pés, milhar, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, grau, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, porcentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litro, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond e InchesPerHour. Para adicionar mais, consulte a API do/ExtendedType.
  Agregatype de > SensorMeasures  | Nenhum, médio, máximo, mínimo ou i.
  Profundidade de > de SensorMeasures  | A profundidade do sensor em centímetros. Por exemplo, a medida de umidade de 10 cm sob a base.
  Descrição de > SensorMeasures  | Forneça uma descrição significativa da medida.
  Nome  | Nome para identificar o recurso. Por exemplo, o nome do modelo ou o nome do produto.
  Descrição  | Forneça uma descrição significativa do modelo.
  Propriedades  | Propriedades adicionais do fabricante.
  **Sensores**  |  |
  HardwareID  | ID exclusiva do sensor definido pelo fabricante.
  SensorModelId  | ID do modelo de sensor associado.
  Localização  | Sensor latitude (-90 a + 90), longitude (-180 a 180) e elevação (em metros).
  Nome da > de porta  |Nome e tipo da porta à qual o sensor está conectado no dispositivo. Deve ser o mesmo nome definido no modelo do dispositivo.
  DeviceId  | ID do dispositivo ao qual o sensor está conectado.
  Nome  | Nome para identificar o recurso. Por exemplo, o nome do sensor ou o nome do produto e o número do modelo ou o código do produto.
  Descrição  | Forneça uma descrição significativa.
  Propriedades  | Propriedades adicionais do fabricante.

 Para obter informações sobre cada um dos objetos e suas propriedades, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > As APIs retornam IDs exclusivas para cada instância criada. Essa ID precisa ser retida pelo Tradutor para o gerenciamento de dispositivos e a sincronização de metadados.


**Sincronização de metadados**

O tradutor deve enviar atualizações nos metadados. Por exemplo, cenários de atualização são alteração do nome do dispositivo ou do sensor e alteração do local do dispositivo ou do sensor.

O tradutor deve ter a capacidade de adicionar novos dispositivos ou sensores que foram instalados pelo usuário postando a vinculação de FarmBeats. Da mesma forma, se um dispositivo ou sensor tiver sido atualizado pelo usuário, o mesmo deverá ser atualizado em FarmBeats para o dispositivo ou sensor correspondente. Cenários típicos que exigem a atualização de um dispositivo ou sensor são uma alteração em um local do dispositivo ou na adição de sensores em um nó.


> [!NOTE]
> A exclusão não tem suporte para os metadados do dispositivo ou do sensor.
>
> Para atualizar os metadados, é obrigatório chamar/Get/{ID} no dispositivo ou sensor, atualizar as propriedades alteradas e, em seguida, fazer uma/put/{ID} para que todas as propriedades definidas pelo usuário não sejam perdidas.

### <a name="add-new-types-and-units"></a>Adicionar novos tipos e unidades

O FarmBeats dá suporte à adição de novas unidades e tipos de medidas de sensor. Para obter mais informações sobre a API do/ExtendedType, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Especificações de telemetria

Os dados de telemetria são mapeados para uma mensagem canônica que é publicada nos hubs de eventos do Azure para processamento. Os hubs de eventos do Azure são um serviço que habilita a ingestão de dados em tempo real (telemetria) de dispositivos e aplicativos conectados.

## <a name="send-telemetry-data-to-farmbeats"></a>Enviar dados de telemetria para o FarmBeats

Para enviar dados de telemetria para o FarmBeats, crie um cliente que envie mensagens para um hub de eventos no FarmBeats. Para obter mais informações sobre dados de telemetria, consulte [enviando telemetria para um hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Aqui está um código Python de exemplo que envia telemetria como um cliente para um hub de eventos especificado.

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
Todos os nomes de chave no JSON de telemetria devem estar em minúsculas. Os exemplos são DeviceID e SensorData.

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
> As seções a seguir estão relacionadas a outras alterações (por exemplo, Interface do usuário, gerenciamento de erros etc.) o parceiro do sensor pode se referir ao desenvolvimento do componente tradutor.


## <a name="link-a-farmbeats-account"></a>Vincular uma conta do FarmBeats

Depois que os clientes compraram e implantaram dispositivos ou sensores, eles podem acessar os dados do dispositivo e a telemetria no portal de SaaS (software como serviço) dos parceiros do dispositivo. Os parceiros de dispositivo podem permitir que os clientes vinculem suas contas à instância do FarmBeats no Azure, fornecendo uma maneira de inserir as seguintes credenciais:

   - Nome de exibição (um campo opcional para os usuários definirem um nome para essa integração)
   - Ponto de extremidade de API
   - ID do inquilino
   - ID de Cliente
   - Segredo do cliente
   - Cadeia de conexão do EventHub
   - Data de início

   > [!NOTE]
   > A data de início habilita o feed de dados histórico, ou seja, os dados da data especificada pelo usuário.

## <a name="unlink-farmbeats"></a>Desvincular FarmBeats

Os parceiros de dispositivo podem permitir que os clientes desvinculem uma integração existente do FarmBeats. A desvinculação de FarmBeats não deve excluir nenhum metadado de dispositivo ou sensor criado no Hub de dados FarmBeats. A desvinculação faz o seguinte:

   - Interrompe o fluxo de telemetria.
   - Exclui e apaga as credenciais de integração no parceiro do dispositivo.

## <a name="edit-farmbeats-integration"></a>Editar integração do FarmBeats

Os parceiros de dispositivo podem permitir que os clientes editem as configurações de integração do FarmBeats se a cadeia de conexão ou o segredo do cliente for alterado. Nesse caso, somente os campos a seguir são editáveis:

   - Nome de exibição (se aplicável)
   - Segredo do cliente (deve ser exibido no formato "2x8 * * * * * * * * * * *" ou no recurso mostrar/ocultar em vez de texto não criptografado)
   - Cadeia de conexão (deve ser exibida no formato "2x8 * * * * * * * * * * *" ou mostrar/ocultar recurso em vez de texto não criptografado)

## <a name="view-the-last-telemetry-sent"></a>Exibir a última telemetria enviada

Os parceiros de dispositivo podem permitir que os clientes exibam o carimbo de data/hora da última telemetria enviada, que é encontrada sob **telemetria enviada**. Esta é a hora em que a telemetria mais recente foi enviada com êxito para FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Solução de problemas e gerenciamento de erros

**Solução de problemas ou suporte**

Se o cliente não puder receber dados de dispositivo ou telemetria na instância FarmBeats especificada, o parceiro de dispositivo deverá fornecer suporte e um mecanismo para solução de problemas.

**Retenção de dados de telemetria**

Os dados de telemetria também devem ser retidos por um período de tempo predefinido para que possam ser úteis na depuração ou reenvio da telemetria se ocorrer um erro ou perda de dados.

**Gerenciamento de erros ou notificação de erro**

Se um erro afetar os metadados do dispositivo ou do sensor ou a integração de dados ou o fluxo de dados de telemetria no sistema de parceiro de dispositivo, o cliente deverá receber uma notificação. Um mecanismo para resolver quaisquer erros também deve ser projetado e implementado.

**Lista de verificação de conexão**

Fabricantes de dispositivos ou parceiros podem usar a seguinte lista de verificação para garantir que as credenciais fornecidas pelo cliente sejam precisas:

   - Verifique se um token de acesso é recebido com as credenciais que foram fornecidas.
   - Verifique se uma chamada à API é realizada com êxito com o token de acesso recebido.
   - Verifique se a conexão do cliente EventHub foi estabelecida.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a API REST, consulte [REST API](references-for-azure-farmbeats.md#rest-api).
