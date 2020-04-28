---
title: Como adicionar bolhas a objetos - Azure Digital Twins [ Azure Digital Twins ] Microsoft Docs
description: Saiba como adicionar bolhas a utilizadores, dispositivos e espaços em Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: c85db05e6feeea43023c2391998f837348caed4e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75929689"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Adicione bolhas a objetos em Gémeos Digitais Azure

As bolhas são representações não estruturadas de tipos comuns de ficheiros, como imagens e registos. As bolhas rastreiam o tipo de dados que representam utilizando um tipo MIME (por exemplo: "imagem/jpeg") e metadados (nome, descrição, tipo, e assim por diante).

A Azure Digital Twins suporta a fixação de bolhas a dispositivos, espaços e utilizadores. As bolhas podem representar uma imagem de perfil para um utilizador, uma foto de dispositivo, um vídeo, um mapa, um zip firmware, dados JSON, um registo, etc.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Visão geral de bolhas de upload

Pode utilizar pedidos multipartes para fazer upload de bolhas para pontos finais específicos e respetivas funcionalidades.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadados blob

Para além do **Content-Type** e da **Content-Disposition,** os pedidos multipart da Azure Digital Twins devem especificar o corpo jSON correto. Que corpo da JSON submeter depende do tipo de operação de pedido http que está sendo realizada.

Os quatro principais schemas jSON são:

[![Squemas JSON](media/how-to-add-blobs/blob-models-swagger-img.png)](media/how-to-add-blobs/blob-models-swagger-img.png#lightbox)

Os metadados blob JSON estão em conformidade com o seguinte modelo:

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
| **parentId** | String | A entidade-mãe para associar a bolha com (espaços, dispositivos ou utilizadores) |
| **nome** |String | Um nome amigo do homem para a bolha |
| **tipo** | String | O tipo de blob - não pode usar *tipo* e *typeid*  |
| **tipoid** | Número inteiro | O ID do tipo blob - não pode usar *o tipo* e a *digitada* |
| **subtipo** | String | O subtipo blob - não pode usar *subtipo* e *subtipoid* |
| **subtipoId** | Número inteiro | O id subtipo para a bolha - não pode usar *subtipo* e *subtipoid* |
| **descrição** | String | Descrição personalizada da bolha |
| **partilha** | String | Se a bolha pode ser partilhada`None`- `Tree` `Global`enum [ , ] |

Os metadados blob são sempre fornecidos como o `.json` primeiro pedaço com **Conteúdo-Tipo** `application/json` ou como um ficheiro. Os dados de ficheiros são fornecidos no segundo pedaço e podem ser de qualquer tipo MIME suportado.

A documentação da Swagger descreve estes modelos schemas em detalhes.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Saiba utilizar a documentação de referência lendo [Como utilizar swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>Dados de resposta blobs

As bolhas devolvidas individualmente estão em conformidade com o seguinte esquema JSON:

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
| **ID** | String | O identificador único para a bolha |
| **nome** |String | Um nome amigo do homem para a bolha |
| **parentId** | String | A entidade-mãe para associar a bolha com (espaços, dispositivos ou utilizadores) |
| **tipo** | String | O tipo de blob - não pode usar *tipo* e *typeid*  |
| **tipoid** | Número inteiro | O ID do tipo blob - não pode usar *o tipo* e a *digitada* |
| **subtipo** | String | O subtipo blob - não pode usar *subtipo* e *subtipoid* |
| **subtipoId** | Número inteiro | O id subtipo para a bolha - não pode usar *subtipo* e *subtipoid* |
| **partilha** | String | Se a bolha pode ser partilhada`None`- `Tree` `Global`enum [ , ] |
| **descrição** | String | Descrição personalizada da bolha |
| **contentInfos** | Matriz | Especifica informações de metadados não estruturadas, incluindo versão |
| **nome completo** | String | O nome completo da bolha |
| **spacePaths** | String | O caminho espacial |

Os metadados blob são sempre fornecidos como o `.json` primeiro pedaço com **Conteúdo-Tipo** `application/json` ou como um ficheiro. Os dados de ficheiros são fornecidos no segundo pedaço e podem ser de qualquer tipo MIME suportado.

### <a name="blob-multipart-request-examples"></a>Exemplos de pedidos multipart blob

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Para fazer upload de um ficheiro de texto como uma bolha e associá-lo a um espaço, faça um pedido de HTTP POST autenticado para:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Com o seguinte corpo:

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
| USER_DEFINED_BOUNDARY | Um nome de limite de conteúdo multiparte |

O código seguinte é uma implementação .NET do mesmo upload blob, utilizando a classe [MultipartFormDataContent:](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent)

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

Por último, os utilizadores [de cURL](https://curl.haxx.se/) podem fazer pedidos de formulários multipartes da mesma forma:

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
| YOUR_TOKEN | O seu token OAuth 2.0 válido |
| YOUR_SPACE_ID | A identificação do espaço para associar a bolha com |
| PATH_TO_FILE | O caminho para o seu ficheiro de texto |

[![exemplo cURL](media/how-to-add-blobs/http-blob-post-through-curl-img.png)](media/how-to-add-blobs/http-blob-post-through-curl-img.png#lightbox)

Um POST de sucesso devolve a identificação do novo blob.

## <a name="api-endpoints"></a>Pontos finais da API

As seguintes secções descrevem os pontos finais de API relacionados com a bolha e as suas funcionalidades.

### <a name="devices"></a>Dispositivos

Pode anexar bolhas aos dispositivos. A imagem que se segue mostra a documentação de referência swagger para as suas APIs de Gestão. Especifica pontos finais de API relacionados com o dispositivo para consumo de bolhas e quaisquer parâmetros de percurso necessários para passar neles.

[![Bolhas de dispositivo](media/how-to-add-blobs/blobs-device-api-swagger-img.png)](media/how-to-add-blobs/blobs-device-api-swagger-img.png#lightbox)

Por exemplo, para atualizar ou criar uma bolha e fixar a bolha a um dispositivo, faça um pedido de HTTP PATCH autenticado para:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_BLOB_ID* | O ID blob desejado |

Pedidos bem sucedidos devolvem um objeto JSON como [descrito anteriormente](#blobs-response-data).

### <a name="spaces"></a>Espaços

Também pode anexar bolhas aos espaços. A imagem seguinte lista todos os pontos finais da API do espaço responsáveis pelo manuseamento de bolhas. Também enumera quaisquer parâmetros de caminho para passar para esses pontos finais.

[![Bolhas espaciais](media/how-to-add-blobs/blobs-space-api-swagger-img.png)](media/how-to-add-blobs/blobs-space-api-swagger-img.png#lightbox)

Por exemplo, para devolver uma bolha anexa a um espaço, faça um pedido autenido HTTP GET para:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_BLOB_ID* | O ID blob desejado |

Pedidos bem sucedidos devolvem um objeto JSON como [descrito anteriormente](#blobs-response-data).

Um pedido de PATCH para o mesmo ponto final atualiza descrições de metadados e cria versões da bolha. O pedido HTTP é feito através do método PATCH, juntamente com quaisquer metas necessários, e dados de formulários multipartes.

### <a name="users"></a>Utilizadores

Pode anexar bolhas aos modelos de utilizador (por exemplo, para associar uma imagem de perfil). A imagem que se segue mostra pontos finais aPI relevantes do utilizador e quaisquer parâmetros de percurso necessários, tais como: `id`

[![Bolhas de utilizador](media/how-to-add-blobs/blobs-users-api-swagger-img.png)](media/how-to-add-blobs/blobs-users-api-swagger-img.png#lightbox)

Por exemplo, para obter uma bolha anexaa a um utilizador, faça um pedido http GET autenticado com quaisquer dados de formulário necessários para:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parâmetro | Substituir |
| --- | --- |
| *YOUR_BLOB_ID* | O ID blob desejado |

Pedidos bem sucedidos devolvem um objeto JSON como [descrito anteriormente](#blobs-response-data).

## <a name="common-errors"></a>Erros comuns

* Um erro comum envolve não fornecer a informação correta do cabeçalho:

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "Invalid media type in first section."
      }
  }
  ```

  Para resolver este erro, verifique se o pedido geral tem um cabeçalho de **tipo conteúdo** apropriado:

     * `multipart/mixed`
     * `multipart/form-data`

  Além disso, verifique se cada *pedaço multiparte* tem um **tipo de conteúdo**correspondente adequado .

* Um segundo erro comum surge quando várias bolhas são atribuídas ao mesmo recurso no seu gráfico de [inteligência espacial:](concepts-objectmodel-spatialgraph.md)

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "SpaceBlobMetadata already exists."
      }
  }
  ```

  > [!NOTE]
  > O atributo da **mensagem** variará em função do recurso. 

  Apenas uma bolha (de cada tipo) pode ser anexada a cada recurso dentro do seu gráfico espacial. 

  Para resolver este erro, atualize a bolha existente utilizando a operação Adequada API HTTP PATCH. Ao fazê-lo, substituirá os dados de bolha existentes com os dados pretendidos.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a documentação de referência da Swagger para as Gémeas Digitais Azure, leia [Use Azure Digital Twins Swagger.](how-to-use-swagger.md)

- Para fazer upload de bolhas através do Carteiro, leia [Como configurar](./how-to-configure-postman.md)o Carteiro .