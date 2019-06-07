---
title: Como adicionar blobs a objetos duplos Digital do Azure | Documentos da Microsoft
description: Saiba como adicionar blobs a objetos duplos Digital do Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 9490772226ecdb90cdd2e0b98fe8336b91db6044
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754479"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Adicionar blobs a objetos duplos Digital do Azure

Os BLOBs são representações de dados não estruturadas de tipos de ficheiros comuns, como imagens e registos. BLOBs de controlam o tipo de dados que eles representam, utilizando um tipo de MIME (por exemplo: "imagem/jpeg") e metadados (nome, descrição, tipo e assim por diante).

Duplos Digital do Azure suporta anexar blobs para dispositivos, espaços e os utilizadores. BLOBs podem representar uma foto de perfil para um utilizador, uma fotografia de dispositivo, um vídeo, um mapa, um zip de firmware, dados JSON, um registo, etc.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Descrição geral de blobs a carregar

Pode usar várias partes pedidos para carregar blobs para os pontos finais e suas respectivas funcionalidades.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadados do blob

Para além **Content-Type** e **Content-Disposition**, pedidos de várias partes de blob de duplos Digital do Azure tem de especificar o corpo JSON correto. O corpo JSON para submeter depende do tipo de operação de pedido HTTP que está a ser efetuada.

Os esquemas JSON quatro principais são:

[![Esquemas JSON](media/how-to-add-blobs/blob-models.PNG)](media/how-to-add-blobs/blob-models.PNG#lightbox)

Metadados do blob JSON está em conformidade com o modelo seguinte:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Atributo | Type | Descrição |
| --- | --- | --- |
| **parentId** | String | A entidade principal para associar o blob (espaços, dispositivos ou utilizadores) |
| **name** |String | Um nome amigável a humanos para o blob |
| **type** | String | O tipo de blob - não é possível utilizar *tipo* e *typeId*  |
| **typeId** | Número inteiro | O ID de tipo de blob - não é possível utilizar *tipo* e *typeId* |
| **subtype** | String | Não é possível utilizar o subtipo de BLOBs - *subtipo* e *subtypeId* |
| **subtypeId** | Número inteiro | O ID de subtipo para o blob - não é possível utilizar *subtipo* e *subtypeId* |
| **description** | String | Descrição personalizada do blob |
| **sharing** | String | Se o blob pode ser partilhado - enum [`None`, `Tree`, `Global`] |

Metadados do blob é sempre fornecido como o primeiro segmento com **Content-Type** `application/json` ou como um `.json` ficheiro. Os dados de ficheiro são fornecidos no segundo segmento e podem ser de qualquer tipo MIME suportado.

A documentação de Swagger descreve esses esquemas de modelo detalhes.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Saiba como utilizar a documentação de referência, lendo [como utilizar o Swagger](./how-to-use-swagger.md).

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Dados de resposta de BLOBs

Blobs individualmente retornados está em conformidade com o esquema JSON do seguinte:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Atributo | Type | Descrição |
| --- | --- | --- |
| **id** | String | O identificador exclusivo para o blob |
| **name** |String | Um nome amigável a humanos para o blob |
| **parentId** | String | A entidade principal para associar o blob (espaços, dispositivos ou utilizadores) |
| **type** | String | O tipo de blob - não é possível utilizar *tipo* e *typeId*  |
| **typeId** | Número inteiro | O ID de tipo de blob - não é possível utilizar *tipo* e *typeId* |
| **subtype** | String | Não é possível utilizar o subtipo de BLOBs - *subtipo* e *subtypeId* |
| **subtypeId** | Número inteiro | O ID de subtipo para o blob - não é possível utilizar *subtipo* e *subtypeId* |
| **sharing** | String | Se o blob pode ser partilhado - enum [`None`, `Tree`, `Global`] |
| **description** | String | Descrição personalizada do blob |
| **contentInfos** | Array | Especifica informações de metadados não estruturados, incluindo a versão |
| **fullName** | String | O nome completo do blob |
| **spacePaths** | String | O caminho de espaço |

Metadados do blob é sempre fornecido como o primeiro segmento com **Content-Type** `application/json` ou como um `.json` ficheiro. Os dados de ficheiro são fornecidos no segundo segmento e podem ser de qualquer tipo MIME suportado.

### <a name="blob-multipart-request-examples"></a>Exemplos de pedido com várias partes de blob

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Carregar um ficheiro de texto como um blob e associá-lo com um espaço, fazer um pedido de HTTP POST autenticado para:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Com o corpo do seguinte:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Value | Substituir |
| --- | --- |
| USER_DEFINED_BOUNDARY | Um nome de limites com várias partes de conteúdo |

O código a seguir é uma implementação de .NET do carregamento de BLOBs mesmo, usando a classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Por último, [cURL](https://curl.haxx.se/) os utilizadores podem executar pedidos de formulário com várias partes da mesma forma:

[![Blobs de dispositivo](media/how-to-add-blobs/curl.PNG)](media/how-to-add-blobs/curl.PNG#lightbox)

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\": \"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\": \"A well chosen description\", \"Sharing\": \"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Value | Substituir |
| --- | --- |
| YOUR_TOKEN | O token de OAuth 2.0 válido |
| YOUR_SPACE_ID | O ID do espaço para associar o blob com |
| PATH_TO_FILE | O caminho para o ficheiro de texto |

Uma publicação com êxito retorna a ID do novo blob (realçado em vermelho anteriormente).

## <a name="api-endpoints"></a>Pontos finais da API

As secções seguintes descrevem os principais relacionados com BLOBs pontos finais da API e suas funcionalidades.

### <a name="devices"></a>Dispositivos

Pode anexar blobs a dispositivos. A imagem seguinte mostra a documentação de referência do Swagger para as APIs de gestão. Especifica relacionados com o dispositivo pontos finais da API para consumo de blob e quaisquer parâmetros de caminho necessário passar neles.

[![Blobs de dispositivo](media/how-to-add-blobs/blobs-device-api.PNG)](media/how-to-add-blobs/blobs-device-api.PNG#lightbox)

Por exemplo, para atualizar ou criar um blob e anexar o blob para um dispositivo, fazer um pedido de HTTP PATCH autenticado para:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_BLOB_ID* | O ID de blob pretendido |

Pedidos com êxito de devolvem um objeto JSON como [descrito anteriormente](#blobModel).

### <a name="spaces"></a>Espaços

Também pode anexar blobs aos espaços. A imagem seguinte apresenta uma lista de todos os espaço pontos finais da API responsáveis pelo tratamento de blobs. Ele também lista quaisquer parâmetros de caminho para passar para esses pontos de extremidade.

[![Blobs de espaço](media/how-to-add-blobs/blobs-space-api.PNG)](media/how-to-add-blobs/blobs-space-api.PNG#lightbox)

Por exemplo, para devolver um blob anexado a um espaço, fazer um pedido HTTP GET autenticado para:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_BLOB_ID* | O ID de blob pretendido |

Pedidos com êxito de devolvem um objeto JSON como [descrito anteriormente](#blobModel).

Um pedido de PATCH para o mesmo ponto final descrições de metadados de atualizações e cria as versões do blob. O pedido HTTP é feito através do método PATCH, juntamente com todos os metadados necessários e dados de formulário com várias partes.

### <a name="users"></a>Utilizadores

Pode anexar blobs a modelos de utilizador (por exemplo, para associar uma foto de perfil). A imagem seguinte mostra os pontos finais de API de utilizadores relevantes e quaisquer parâmetros de caminho necessário, como `id`:

[![Blobs de utilizador](media/how-to-add-blobs/blobs-users-api.PNG)](media/how-to-add-blobs/blobs-users-api.PNG#lightbox)

Por exemplo, para obter um blob anexado a um utilizador, efetuar um pedido HTTP GET autenticado com quaisquer dados de formulário necessária para:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_BLOB_ID* | O ID de blob pretendido |

Pedidos com êxito de devolvem um objeto JSON como [descrito anteriormente](#blobModel).

## <a name="common-errors"></a>Erros comuns

Um erro comum envolve não fornecer as informações de cabeçalho correto:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Para resolver este erro, verifique se o pedido geral tem apropriadas **Content-Type** cabeçalho:

* `multipart/mixed`
* `multipart/form-data`

Além disso, certifique-se de que cada segmento de várias partes tem um correspondente **Content-Type** conforme necessário.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre a documentação de referência do Swagger duplos Digital do Azure, leia [Use Azure Digital duplos Swagger](how-to-use-swagger.md).

- Para carregar blobs através do Postman, leia [como configurar o Postman](./how-to-configure-postman.md).