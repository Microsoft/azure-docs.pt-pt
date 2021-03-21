---
title: Operações de Serviços de Mídia REST API visão geral | Microsoft Docs
description: A API de Operações de Serviços de Mídia É utilizada para a criação de Empregos, Ativos, Canais Ao Vivo e outros recursos numa conta dos Serviços de Comunicação Social. Este artigo fornece uma visão geral da Azure Media Services v2 REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.openlocfilehash: 9f147e333e4d1b95a14dd3121d7ab304b6166248
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010053"
---
# <a name="media-services-operations-rest-api-overview"></a>Operações de Serviços de Mídia REST Visão geral da API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

A **API de Operações de Serviços de Mídia** É utilizada para a criação de Empregos, Ativos, Canais Ao Vivo e outros recursos numa conta dos Serviços de Comunicação Social. Para mais informações, consulte a [referência API de Operações de Serviços de Mídia](/rest/api/media/operations/azure-media-services-rest-api-reference)REST .

Os Media Services fornecem uma API REST que aceita o formato JSON ou atom+pub XML. A API dos Serviços de Mídia REQUER cabeçalhos HTTP específicos que cada cliente deve enviar ao ligar-se aos Serviços de Comunicação Social, bem como um conjunto de cabeçalhos opcionais. As seguintes secções descrevem os cabeçalhos e verbos HTTP que pode utilizar ao criar pedidos e receber respostas dos Serviços de Comunicação Social.

Autenticação para os Serviços de Mídia REST A API é feita através da autenticação do Azure Ative Directory, que está delineada no artigo [Autenticação Azure AD para aceder à Azure Media Services API com REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Considerações

Aplicam-se as seguintes considerações ao utilizar o REST.

* Ao consultar entidades, há um limite de 1000 entidades devolvidas de uma só vez porque o REST v2 público limita os resultados da consulta a 1000 resultados. É necessário utilizar **Skip** and **Take** (.NET)/ **top** (REST) conforme descrito neste [exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo de API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Ao utilizar o JSON e especificar a utilização da palavra-chave **__metadata** no pedido (por exemplo, para referência a um objeto ligado) deve definir o cabeçalho **Aceitar** para [o formato JSON Verbose](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (ver o seguinte exemplo). A Odata não entende a **propriedade __metadata** no pedido, a não ser que o deslome para verbose.  

    ```console
    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    {
        "Name" : "NewTestJob", 
        "InputMediaAssets" : 
            [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
    . . . 
   ```

## <a name="standard-http-request-headers-supported-by-media-services"></a>Cabeçalhos de pedido padrão HTTP suportados pelos Media Services
Para cada chamada que fizer nos Serviços de Comunicação Social, há um conjunto de cabeçalhos necessários que deve incluir no seu pedido e também um conjunto de cabeçalhos opcionais que poderá querer incluir. A tabela abaixo lista os cabeçalhos necessários:

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| Autorização |Portador |O portador é o único mecanismo de autorização aceite. O valor deve também incluir o token de acesso fornecido pela Azure Ative Directory. |
| x-ms-versão |Decimal |2.17 (ou versão mais recente)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Como os Serviços de Comunicação Social utilizam o OData para expor as suas APIs REST, os cabeçalhos DataServiceVersion e MaxDataServiceVersion devem ser incluídos em todos os pedidos; no entanto, se não forem, atualmente os Serviços de Mídia assumem que o valor de DataServiceVersion em uso é 3.0.
> 
> 

Segue-se um conjunto de cabeçalhos opcionais:

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| Data |Data RFC 1123 |Tempotam do pedido |
| Aceitar |Tipo do conteúdo |O tipo de conteúdo solicitado para a resposta, tais como:<p> -aplicação/json;odata=verbose<p> - aplicação/átomo+xml<p> As respostas podem ter um tipo de conteúdo diferente, como uma busca de bolhas, onde uma resposta bem sucedida contém o fluxo de bolhas como a carga útil. |
| Accept-Encoding |Gzip, esvaziar |Codificação GZIP e DEFLATE, quando aplicável. Nota: Para grandes recursos, os Serviços de Comunicação Social podem ignorar este cabeçalho e devolver dados não comprimidos. |
| Accept-Language |"en", "es", e assim por diante. |Especifica a língua preferida para a resposta. |
| Accept-Charset |Tipo de charset como "UTF-8" |O padrão é UTF-8. |
| Método X-HTTP |Método HTTP |Permite que clientes ou firewalls que não suportem métodos HTTP como PUT ou DELETE utilizem estes métodos, escavados através de uma chamada GET. |
| Content-Type |Tipo do conteúdo |Tipo de conteúdo do organismo de pedido em pedidos PUT ou POST. |
| cliente-pedido id |String |Um valor definido pelo chamador que identifica o pedido dado. Se especificado, este valor será incluído na mensagem de resposta como forma de mapear o pedido. <p><p>**Importante**<p>Os valores devem ser limitados a 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Cabeçalhos de resposta HTTP padrão suportados pelos Media Services
Segue-se um conjunto de cabeçalhos que lhe podem ser devolvidos, dependendo do recurso que solicitou e da ação que pretendia realizar.

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| pedido id |String |Um identificador único para a operação atual, serviço gerado. |
| cliente-pedido id |String |Um identificador especificado pelo autor da chamada no pedido original, se estiver presente. |
| Data |Data RFC 1123 |A data/hora em que o pedido foi processado. |
| Content-Type |Varia |O tipo de conteúdo do corpo de resposta. |
| Codificação de conteúdos |Varia |Gzip ou esvazie, conforme apropriado. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos HTTP Standard suportados pelos Media Services
Segue-se uma lista completa de verbos HTTP que podem ser utilizados ao esboçar pedidos HTTP:

| Verbo | Descrição |
| --- | --- |
| GET |Devolve o valor atual de um objeto. |
| POST |Cria um objeto com base nos dados fornecidos ou submete um comando. |
| PUT |Substitui um objeto ou cria um objeto nomeado (quando aplicável). |
| DELETE |Elimina um objeto. |
| FUSÃO |Atualiza um objeto existente com alterações de propriedade nomeadas. |
| HEAD |Retorna metadados de um objeto para uma resposta GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Descubra e navegue no modelo da entidade media Services
Para tornar as entidades dos Serviços de Media mais detetáveis, a operação $metadata pode ser utilizada. Permite-lhe recuperar todos os tipos de entidades válidas, propriedades de entidades, associações, funções, ações, e assim por diante. Ao adicionar a $metadata operação ao fim do seu ponto final rest API dos Media Services, pode aceder a este serviço de descoberta.

 /api/$metadata.

Deve anexar "?api-versão=2.x" ao final do URI se quiser ver os metadados num browser, ou não incluir o cabeçalho da versão x-ms no seu pedido.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Autenticar com serviços de mídia REST usando Azure Ative Directory

A autenticação na API REST é feita através do Azure Ative Directory (AAD).
Para mais informações sobre como obter os dados de autenticação necessários para a sua conta de Serviços de Mídia a partir do Portal Azure, consulte [Aceda à API dos Serviços de Media Azure com autenticação Ad Azure.](media-services-use-aad-auth-to-access-ams-api.md)

Para obter mais informações sobre o código de escrita que se liga à API REST utilizando a autenticação AZure AD, consulte o artigo [Utilize a autenticação Azure AD para aceder à AZure Media Services API com REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Passos seguintes
Para aprender a utilizar a autenticação AZure AD com serviços de mídia REST API, consulte [a autenticação AD do Azure para aceder à Azure Media Services API com REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
