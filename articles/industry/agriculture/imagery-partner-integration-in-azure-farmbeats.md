---
title: Integração de parceiros de imagens
description: Este artigo descreve a integração de parceiros de imagens.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.custom: has-adal-ref
ms.openlocfilehash: 430907f43fb40f0ee24505bdc366a98a49f23b47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82613290"
---
# <a name="imagery-partner-integration"></a>Integração de parceiros de imagens

Este artigo descreve como usar o componente Azure FarmBeats Tradutor para enviar dados de imagens para FarmBeats. Os dados de imagens agrícolas podem ser gerados a partir de várias fontes, tais como câmaras multiespectrais, satélites e drones. Os parceiros de imagens agrícolas podem integrar-se com os FarmBeats para fornecer aos clientes mapas gerados à medida para as suas quintas.

Os dados, uma vez disponíveis, podem ser visualizados através do Acelerador FarmBeats e potencialmente utilizados para a fusão de dados e aprendizagem automática/inteligência artificial (ML/AI) de construção de modelos por empresas agrícolas ou integradores de sistemas de clientes.

FarmBeats fornece a capacidade de:

- Defina tipos de imagem personalizados, origem e formato de ficheiro utilizando APIs /ExtendedType.
- Ingerir dados de imagens de várias fontes através das APIs /Scene e /SceneFile.

As seguintes informações focam-se em colocar qualquer forma de imagem no sistema FarmBeats.

Ao selecionar a secção **Imagery Drone,** abre-se um pop-up para mostrar uma imagem de alta resolução do drone ortomosaico. Você pode aceder ao software parceiro, o que ajuda a planear voos de drones e obter dados brutos. Continuará a utilizar o software do parceiro para planeamento de caminhos e costura de imagem ortomosa.

Os parceiros de drones precisam de permitir que os clientes liguem a sua conta de clientes com a sua instância FarmBeats no Azure.

Deve utilizar as seguintes credenciais no software do parceiro de drones para ligar farmbeats:

- Ponto final de API
- ID do inquilino
- ID de Cliente
- Segredo do cliente

## <a name="api-development"></a>Desenvolvimento da API

As APIs contêm documentação técnica da Swagger. Para obter informações sobre as APIs e os correspondentes pedidos ou respostas, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="authentication"></a>Autenticação

FarmBeats usa o Microsoft Azure [Ative Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) (Azure AD). O Azure App Service fornece suporte de autenticação e autorização incorporado. 

Para mais informações sobre a Azure AD, consulte [o Azure Ative Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

FarmBeats Datahub utiliza autenticação ao portador, que necessita das seguintes credenciais:

- ID de Cliente
- Segredo do cliente
- ID do inquilino

Utilizando as credenciais anteriores, o chamador pode solicitar um token de acesso, que precisa de ser enviado nos pedidos subsequentes da API, na secção de cabeçalho, da seguinte forma:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

A seguinte amostra de código Python recupera o token de acesso. Em seguida, pode utilizar o token para chamadas subsequentes da API para FarmBeats.

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

## <a name="http-request-headers"></a>HTTP solicitam cabeçalhos

Aqui estão os cabeçalhos de pedido mais comuns que precisam de ser especificados quando fizer uma chamada da API para o FarmBeats Datahub.

**Cabeçalho** | **Descrição e exemplo**
--- | ---
Content-Type  | O formato de pedido (Tipo de Conteúdo: aplicação/ <format> ). Para as APIs do FarmBeats Datahub, o formato é JSON. Tipo de conteúdo: aplicação/json
Autorização | Especifica o token de acesso necessário para fazer uma chamada da API. Autorização: <Access-Token> portador
Aceitar  | O formato de resposta. Para as APIs do FarmBeats Datahub, o formato é JSON. Aceitar: candidatura/json


## <a name="api-requests"></a>Pedidos de API

Para fazer um pedido de API REST, você combina:

- O método HTTP (GET, POST e PUT).
- O URL para o serviço API.
- O recurso URI (para consultar, submeter dados, atualizar ou eliminar).
- Um ou mais cabeçalhos de pedido HTTP.

Opcionalmente, pode incluir parâmetros de consulta em chamadas GET para filtrar, limitar o tamanho e ordenar os dados nas respostas.

O seguinte pedido de amostra é obter a lista de dispositivos:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

A maioria das chamadas GET, POST e PUT requerem um corpo de pedido JSON.

O seguinte pedido de amostra é a criação de um dispositivo. Esta amostra tem uma entrada JSON com o corpo de pedido.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"accept: application/json" -H
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato de dados

JSON é um formato comum de dados independente da linguagem que fornece uma simples representação de texto de estruturas de dados arbitrárias. Para mais informações, consulte [json org.](https://JSON.org)

## <a name="ingest-imagery-into-farmbeats"></a>Ingerir imagens em FarmBeats

Depois de o parceiro ter credenciais para ligar ao FarmBeats Datahub, o parceiro toma as seguintes medidas no componente Tradutor.

1.  Criar um novo tipo estendido para os seguintes campos, de acordo com o tipo de imagens a carregar:

    - **Fonte de cena**: Por exemplo, drone_partner_name
    - **Tipo de cena**: Por exemplo, drone
    - **Tipo de arquivo de**cena : Por exemplo, índice de clorofila
    - **Tipo de conteúdo de ficheiro de**cena : Por exemplo, imagem/tiff

2.  Ligue para a API /Farms para obter a lista de fazendas dentro do sistema Azure FarmBeats.
3.  Forneça ao cliente a capacidade de escolher uma única quinta da lista de quintas.

    O sistema parceiro deve mostrar a exploração dentro do software parceiro para fazer o planeamento do percurso e a recolha de voos e imagens de drones.

4.  Ligue para a API /Cena e forneça os detalhes necessários para criar uma nova cena com um ID de cena único.
5.  Receba um URL BLOB SAS para enviar as imagens necessárias para o FarmBeats Datahub, no contexto da quinta escolhida, no sistema FarmBeats.

Aqui está um fluxo detalhado das chamadas da API.

### <a name="step-1-extendedtype"></a>Passo 1:Type estendido

Consulte a API /ExtendedType para ver se o tipo e a fonte de ficheiro estão disponíveis no FarmBeats. Para tal, ligue para um GET na API /ExtendedType.

Aqui estão os valores definidos pelo sistema:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Este passo é uma configuração única. O âmbito deste novo tipo de cena limita-se à subscrição em que os Azure FarmBeats estão instalados.

Por exemplo, para adicionar SceneSource: "SlantRange", faça um PUT no ID da API /ExtendedType com a carga útil de entrada "SceneSource" da chave.

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

O campo verde é a nova adição aos valores de origem de cena definidos pelo sistema.

### <a name="step-2-get-farm-details"></a>Passo 2: Obter detalhes da fazenda

As cenas (.tiff ou .csv files) estão no contexto de uma fazenda. Você precisa obter os detalhes da fazenda fazendo um GET on the /Farm API. A API devolve a lista de quintas que estão disponíveis na FarmBeats. Pode selecionar a quinta para a quais pretende ingerir os dados.

RESPOSTA GET /Farm:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>Passo 3: Criar um ID de cena (CHAMADA POST)

Crie uma nova cena (.tiff ou .csv file) com a informação dada, que fornece a data, sequência e iD da fazenda com que a cena está associada. Os metadados associados à cena podem ser definidos em propriedades, que incluem a duração e o tipo de medida.

Criar uma nova cena cria uma nova identificação de cena, que está associada à quinta. Após a criação do ID da cena, o utilizador pode usar o mesmo para criar um novo ficheiro (.tiff ou .csv) e armazenar o conteúdo do ficheiro.

Carga útil de entrada de exemplo para a chamada POST na API /Cena:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Resposta da API:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Criar um arquivo de cena**

A identificação da cena devolvida no passo 3 é a entrada para o ficheiro da cena. O ficheiro de cena devolve um token URL SAS, que é válido por 24 horas.

Se o utilizador necessitar de uma forma programática de carregar um fluxo de imagens, o SDK de armazenamento de bolhas pode ser usado para definir um método utilizando o ID, localização e URL do ficheiro de cena.

Carga útil de entrada de exemplo para a chamada POST na API /SceneFile:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Resposta da API:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

A chamada POST para a API /SceneFile devolve um URL de upload SAS, que pode ser usado para carregar o ficheiro .csv ou .tiff utilizando o cliente ou biblioteca de armazenamento Azure Blob.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre detalhes de integração baseados em API REST, consulte [REST API](rest-api-in-azure-farmbeats.md).
