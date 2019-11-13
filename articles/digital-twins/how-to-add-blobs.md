---
title: Como adicionar BLOBs a objetos – Azure digital gêmeos | Microsoft Docs
description: Saiba como adicionar BLOBs a usuários, dispositivos e espaços no gêmeos digital do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/01/2019
ms.custom: seodec18
ms.openlocfilehash: 0a6629db50d0cec13d866d28a0823ccd3ed43f42
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014200"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Adicionar BLOBs a objetos no Azure digital gêmeos

BLOBs são representações não estruturadas de tipos de arquivos comuns, como imagens e logs. Os BLOBs controlam o tipo de dados que eles representam usando um tipo MIME (por exemplo: "image/jpeg") e metadados (nome, descrição, tipo e assim por diante).

O Azure digital gêmeos dá suporte à anexação de BLOBs a dispositivos, espaços e usuários. Os BLOBs podem representar uma imagem de perfil para um usuário, uma foto de dispositivo, um vídeo, um mapa, um zip de firmware, dados JSON, um log, etc.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Visão geral do carregamento de BLOBs

Você pode usar solicitações com várias partes para carregar BLOBs para pontos de extremidade específicos e suas respectivas funcionalidades.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadados de BLOB

Além de **tipo de conteúdo** e **disposição de conteúdo**, as solicitações de várias partes de blob do Azure digital gêmeos devem especificar o corpo JSON correto. O corpo JSON a ser enviado depende do tipo de operação de solicitação HTTP que está sendo executada.

Os quatro principais esquemas JSON são:

[![esquemas JSON](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

Os metadados de blob JSON estão em conformidade com o seguinte modelo:

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

| Atributo | Tipo | Descrição |
| --- | --- | --- |
| **parentId** | Cadeia | A entidade pai para associar o blob (espaços, dispositivos ou usuários) |
| **name** |Cadeia | Um nome amigável para o blob |
| **tipo** | Cadeia | O tipo de BLOB-não é possível usar *Type* e *typeId*  |
| **typeId** | Número inteiro | A ID do tipo de BLOB-não pode usar *Type* e *typeId* |
| **subtype** | Cadeia | O subtipo de BLOB-não *pode usar subtipo e* *subtipoid* |
| **subtypeId** | Número inteiro | A ID de subtipo do BLOB-não pode *usar subtipo e* *subtipoid* |
| **description** | Cadeia | Descrição personalizada do blob |
| **sharing** | Cadeia | Se o blob pode ser compartilhado-enum [`None`, `Tree`, `Global`] |

Os metadados de blob são sempre fornecidos como a primeira parte com o **tipo de conteúdo** `application/json` ou como um arquivo de `.json`. Os dados do arquivo são fornecidos na segunda parte e podem ser de qualquer tipo MIME com suporte.

A documentação do Swagger descreve esses esquemas de modelo com detalhes completos.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Saiba mais sobre como usar a documentação de referência lendo [como usar o Swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>Dados de resposta de BLOBs

BLOBs retornados individualmente estão em conformidade com o seguinte esquema JSON:

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

| Atributo | Tipo | Descrição |
| --- | --- | --- |
| **id** | Cadeia | O identificador exclusivo para o blob |
| **name** |Cadeia | Um nome amigável para o blob |
| **parentId** | Cadeia | A entidade pai para associar o blob (espaços, dispositivos ou usuários) |
| **tipo** | Cadeia | O tipo de BLOB-não é possível usar *Type* e *typeId*  |
| **typeId** | Número inteiro | A ID do tipo de BLOB-não pode usar *Type* e *typeId* |
| **subtype** | Cadeia | O subtipo de BLOB-não *pode usar subtipo e* *subtipoid* |
| **subtypeId** | Número inteiro | A ID de subtipo do BLOB-não pode *usar subtipo e* *subtipoid* |
| **sharing** | Cadeia | Se o blob pode ser compartilhado-enum [`None`, `Tree`, `Global`] |
| **description** | Cadeia | Descrição personalizada do blob |
| **contentInfos** | Matriz | Especifica informações de metadados não estruturados, incluindo versão |
| **fullName** | Cadeia | O nome completo do blob |
| **spacePaths** | Cadeia | O caminho do espaço |

Os metadados de blob são sempre fornecidos como a primeira parte com o **tipo de conteúdo** `application/json` ou como um arquivo de `.json`. Os dados do arquivo são fornecidos na segunda parte e podem ser de qualquer tipo MIME com suporte.

### <a name="blob-multipart-request-examples"></a>Exemplos de solicitação de várias partes do blob

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Para carregar um arquivo de texto como um blob e associá-lo a um espaço, faça uma solicitação HTTP POST autenticada para:

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

| Valor | Substituir |
| --- | --- |
| USER_DEFINED_BOUNDARY | Um nome de limite de conteúdo com várias partes |

O código a seguir é uma implementação .NET do mesmo upload de BLOB, usando a classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

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

Por fim, a [ondulação](https://curl.haxx.se/) de usuários pode fazer solicitações de formulário de várias partes da mesma maneira:

[exemplo de ondulação de ![](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Valor | Substituir |
| --- | --- |
| YOUR_TOKEN | Seu token 2,0 do OAuth válido |
| YOUR_SPACE_ID | A ID do espaço com o qual associar o blob |
| PATH_TO_FILE | O caminho para o arquivo de texto |

Um POST bem-sucedido retorna a ID do novo BLOB (realçado em vermelho anteriormente).

## <a name="api-endpoints"></a>Pontos de extremidade de API

As seções a seguir descrevem os principais pontos de extremidade de API relacionados ao blob e suas funcionalidades.

### <a name="devices"></a>Dispositivos

Você pode anexar BLOBs a dispositivos. A imagem a seguir mostra a documentação de referência do Swagger para suas APIs de gerenciamento. Ele especifica pontos de extremidade de API relacionados ao dispositivo para consumo de BLOB e quaisquer parâmetros de caminho necessários para passá-los.

[![blobs de dispositivo](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

Por exemplo, para atualizar ou criar um blob e anexar o blob a um dispositivo, faça uma solicitação de PATCH HTTP autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_BLOB_ID* | A ID de blob desejada |

As solicitações bem-sucedidas retornam um objeto JSON conforme [descrito anteriormente](#blobs-response-data).

### <a name="spaces"></a>Tablespace

Você também pode anexar BLOBs a espaços. A imagem a seguir lista todos os pontos de extremidade de API de espaço responsáveis por manipular BLOBs. Também lista os parâmetros de caminho a serem passados para esses pontos de extremidade.

[blobs de ![espaço](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

Por exemplo, para retornar um blob anexado a um espaço, faça uma solicitação HTTP GET autenticada para:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_BLOB_ID* | A ID de blob desejada |

As solicitações bem-sucedidas retornam um objeto JSON conforme [descrito anteriormente](#blobs-response-data).

Uma solicitação de PATCH para o mesmo ponto de extremidade atualiza as descrições de metadados e cria versões do blob. A solicitação HTTP é feita por meio do método PATCH, juntamente com qualquer meta e dados de formulário com várias partes necessários.

### <a name="users"></a>Utilizadores

Você pode anexar BLOBs a modelos de usuário (por exemplo, para associar uma imagem de perfil). A imagem a seguir mostra os pontos de extremidade da API do usuário relevantes e os parâmetros de caminho necessários, como `id`:

[![blobs de usuário](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

Por exemplo, para buscar um blob anexado a um usuário, faça uma solicitação HTTP GET autenticada com qualquer dado de formulário necessário para:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_BLOB_ID* | A ID de blob desejada |

As solicitações bem-sucedidas retornam um objeto JSON conforme [descrito anteriormente](#blobs-response-data).

## <a name="common-errors"></a>Erros comuns

Um erro comum envolve não fornecer as informações corretas do cabeçalho:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Para resolver esse erro, verifique se a solicitação geral tem um cabeçalho **Content-Type** apropriado:

* `multipart/mixed`
* `multipart/form-data`

Além disso, verifique se cada parte com várias partes tem um **tipo de conteúdo** correspondente, conforme necessário.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a documentação de referência do Swagger para o gêmeos digital do Azure, leia [usar o Azure digital gêmeos Swagger](how-to-use-swagger.md).

- Para carregar blobs por meio do postmaster, leia [como configurar o postmaster](./how-to-configure-postman.md).