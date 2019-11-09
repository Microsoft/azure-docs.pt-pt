---
title: Integração de parceiros com imagens
description: Descreve sobre a integração de parceiros com imagens
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 2b15cc56cb4f804fac0b047819ccf166950e1cf3
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890977"
---
# <a name="imagery-partner-integration"></a>Integração de parceiros com imagens

Este artigo descreve como usar o componente Azure FarmBeats Translator para enviar dados de imagens para o FarmBeats. Os dados de imagens agricultural podem ser de várias fontes, incluindo câmeras multispectrals, satélites e drones. Os parceiros de imagens agricultural podem se integrar ao FarmBeats para fornecer aos clientes mapas gerados personalizados para seus farms.

Os dados, uma vez disponíveis, podem ser visualizados por meio do acelerador de FarmBeats e potencialmente usados para a fusão de dados e a compilação de modelo ML/ia (inteligência Machine Learning/artificial) por agricultural empresas ou integradores de sistemas de clientes.

O FarmBeats fornece a capacidade de:

- Definir tipos de imagem personalizados, origem, formato de arquivo usando APIs de tipo estendido
- Ingerir dados de imagens de várias fontes por meio da cena & as APIs Scenefile.

As informações abaixo se concentram em obter qualquer forma de imagens no sistema FarmBeats.

Quando você seleciona a seção imagens de drone, um pop-up é aberto para mostrar uma imagem de alta resolução do drone orthomosaic. Você pode acessar o software de parceiro, que ajuda a planejar vôos de drone e obter dados brutos. Você continuará a usar o software do parceiro para o planejamento de caminho e a junção de imagem orthomosaic.

Os parceiros drone precisam habilitar os clientes para vincular sua conta de cliente à sua instância do FarmBeats no Azure.

Você deve usar as seguintes credenciais no software de parceiro drone para vincular o FarmBeats:

- Ponto Final de API
- ID do inquilino
- ID de Cliente
- Segredo do cliente

## <a name="api-development"></a>Desenvolvimento de API

As APIs contêm a documentação técnica do Swagger. Examine o [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) para obter informações sobre as APIs e as solicitações/respostas correspondentes.

## <a name="authentication"></a>Autenticação

O FarmBeats aproveita [Active Directory]((https://docs.microsoft.com/azure/app-service/overview-authentication-authorization))de Microsoft Azure. O serviço de Azure App de autenticação fornece suporte interno de autenticação e autorização. 

Para obter mais informações sobre o, consulte [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

O FarmBeats data Hub usa a autenticação de portador, que precisa das seguintes credenciais:

- ID de Cliente
- Segredo do cliente
- ID do inquilino

Usando as credenciais acima, o chamador pode solicitar um token de acesso, que precisa ser enviado nas solicitações de API subsequentes, na seção de cabeçalho da seguinte maneira:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Veja abaixo um exemplo de código do Python que recupera o token de acesso. Em seguida, você pode usar o token para chamadas de API subsequentes para FarmBeat:   
importar o Azure 

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
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

## <a name="http-request-headers"></a>Cabeçalhos de solicitação HTTP

Aqui estão os cabeçalhos de solicitação mais comuns que precisam ser especificados ao fazer uma chamada à API para o Hub de dados FarmBeats:

**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo  | O formato de solicitação (Content-Type: Application/<format>) para o formato da API do hub de dados FarmBeats é JSON. Tipo de conteúdo: aplicativo/JSON
Autorização | Especifica o token de acesso necessário para fazer uma chamada à API. Autorização: portador de acesso < token >
aceitar  | O formato de resposta. Para APIs de Hub de dados FarmBeats, o formato é JSON Accept: aplicativo/JSON


## <a name="api-requests"></a>Solicitações de API

Para fazer uma solicitação de API REST, você combina o método HTTP (GET/POST/PUT), a URL para o serviço de API, o URI de recurso (para consultar, enviar dados, atualizar ou excluir) e um ou mais cabeçalhos de solicitação HTTP.

Opcionalmente, você pode incluir parâmetros de consulta em GET calls para filtrar, limitar o tamanho e classificar os dados nas respostas.

A solicitação de exemplo abaixo é obter a lista de dispositivos:

```azurepowershell-interactive
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

A maioria das chamadas GET, POST e PUT exigem um corpo de solicitação JSON.

A solicitação de exemplo abaixo é criar um dispositivo (isso tem um JSON de entrada com o corpo da solicitação).


```azurepowershell-interactive
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato de dados

O JSON (JavaScript Object Notation) é um formato de dados comum e independente de linguagem que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para obter mais informações, consulte [JSON org](https://JSON.org).

## <a name="ingesting-imagery-into-farmbeats"></a>Ingestão de imagens no FarmBeats

Depois que o parceiro tiver credenciais para se conectar ao Hub de dados do FarmBeats, o parceiro fará o seguinte no componente do Tradutor:

1.  Crie um novo tipo estendido para os seguintes campos, de acordo com o tipo de imagens que serão carregadas:

    - Origem da cena: por exemplo, < drone_partner_name >
    - Tipo de cena: por exemplo, <drone>
    - Tipo de arquivo de cena: por exemplo, <chlorophyll index>
    - Tipo de conteúdo do arquivo de cena: por exemplo, < imagem/TIFF >

2.  Chame a API farms para obter a lista de farms no sistema FarmBeats do Azure.
3.  Forneça ao cliente a capacidade de escolher um único farm na lista de farms.

    O sistema de parceiros deve mostrar o farm no software do parceiro para fazer o planejamento de caminho e a coleta de imagens e drone de voo.

4.  Chame a API de cena e forneça os detalhes necessários para criar uma nova cena com uma Sceneid exclusiva.
5.  Receba uma URL SAS do blob para carregar as imagens necessárias no Hub de dados FarmBeats, no contexto do farm escolhido, no sistema FarmBeats.

Aqui está um fluxo detalhado nas chamadas à API:

### <a name="step-1-extendedtype"></a>Etapa 1: Extended

Faça check-in da API estendida, se o tipo e a origem do arquivo estiverem disponíveis em FarmBeats. Você pode fazer isso chamando um GET na API/ExtendedType.

A seguir estão os valores definidos pelo sistema:

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
Essa será uma configuração única e o escopo desse novo scenetype será limitado à assinatura na qual o projeto FarmBeats é implantado.

Exemplo: para adicionar o Scenery: "SlantRange", coloque a ID do/ExtendedType com a chave: "Scenery" da carga de entrada:

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

O campo verde é a nova adição aos valores de origem da cena definida pelo sistema.

### <a name="step-2-get-farmdetails"></a>Etapa 2: obter FarmDetails

Os bastidores (arquivos TIFF ou. csv) estarão no contexto de um farm. Você precisa obter os detalhes do farm fazendo uma API Get on/farm. A API retornará a lista de farms disponíveis em FarmBeats, e você poderá selecionar o farm para o qual deseja ingerir os dados.

Obter resposta do/farm:

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

### <a name="step-3-create-ascene-id-post-call"></a>Etapa 3: criar uma ID/cena (chamada post)

Crie uma nova cena (arquivo TIFF ou. csv) com as informações fornecidas, fornecendo a data, a sequência e a ID do farm com os quais a cena será associada. Os metadados associados à cena podem ser definidos em Propriedades, incluindo a duração e o tipo de medida.

Isso cria uma nova Sceneid, que será associada ao farm. Depois que a Sceneid é criada, o usuário pode usar o mesmo para criar um novo arquivo (TIFF ou. csv) & armazenar o conteúdo do arquivo.

Exemplo de carga de entrada para a API/cena de chamada post

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

**Criar/Cenafile**

A ID de cena retornada na etapa três é a entrada para o Scenefile. O Scenefile retorna um token de URL SAS, que é válido por 24 horas.

Se o usuário exigir uma maneira programática de carregar um fluxo de imagens, o SDK do armazenamento de BLOBs poderá ser usado para definir um método usando a ID do Scenefile, o local & URL.

Exemplo de carga de entrada para a chamada post na API/Scenefile:

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

A chamada post para a API/SceneFile retorna uma URL de carregamento SAS, que pode ser usada para carregar o arquivo CSV ou TIFF usando o cliente/biblioteca de armazenamento de BLOBs do Azure.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os detalhes de integração baseada na API REST, consulte [REST API](references-for-farmbeats.md#rest-api).
