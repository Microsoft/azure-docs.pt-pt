---
title: Operações de Media Services REST API visão geral / Microsoft Docs
description: A API "Media Services Operations REST" é utilizada para criar Empregos, Ativos, Canais Ao Vivo e outros recursos numa conta de Media Services. Este artigo fornece uma visão geral da API do Azure Media Services v2 REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 597839f633ed2b925b86c5f859a0fb2d3b64dd59
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773667"
---
# <a name="media-services-operations-rest-api-overview"></a>Media Services operações REST API visão geral 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

A **Media Services Operations REST** API é utilizada para criar Empregos, Ativos, Canais Ao Vivo e outros recursos numa conta de Media Services. Para mais informações, consulte [Media Services Operations REST API reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

A Media Services fornece uma API REST que aceita tanto o formato JSON como o atom+pub XML. A API dos Serviços de Media Rest requer cabeçalhos HTTP específicos que cada cliente deve enviar quando ligar aos Serviços de Media, bem como um conjunto de cabeçalhos opcionais. As seguintes secções descrevem os cabeçalhos e os verbos HTTP que pode utilizar ao criar pedidos e receber respostas dos Media Services.

Autenticação para o Media Services REST API é feita através da autenticação do Diretório Ativo Azure que está delineada no artigo [Utiliza a autenticação Azure AD para aceder à API azure Media Services com REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Considerações

Aplicam-se as seguintes considerações ao utilizar o REST.

* Quando consultam entidades, há um limite de 1000 entidades devolvidas ao mesmo tempo porque o REST v2 público limita os resultados da consulta a 1000 resultados. Tem de utilizar **skip** and **take** (.NET)/ **top** (REST) conforme descrito neste [exemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Ao utilizar o JSON e especificar utilizar a palavra-chave **__metadata** no pedido (por exemplo, para fazer referência a um objeto ligado) deve definir o cabeçalho **Aceitar** o [formato JSON Verbose](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (ver o seguinte exemplo). O Odata não entende a **propriedade __metadata** no pedido, a menos que o delicie.  
  
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

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standard HTTP solicita cabeçalhos suportados por Media Services
Para cada chamada que fizer nos Serviços de Media, há um conjunto de cabeçalhos necessários que deve incluir no seu pedido e também um conjunto de cabeçalhos opcionais que você pode querer incluir. A tabela abaixo enumera os cabeçalhos necessários:

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| Autorização |Portador |O portador é o único mecanismo de autorização aceite. O valor deve incluir também o sinal de acesso fornecido pelo Azure Ative Directory. |
| x-ms-versão |Decimal |2.17 (ou versão mais recente)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Dado que os Serviços de Media utilizam o OData para expor as suas APIs REST, os cabeçalhos DataServiceVersion e MaxDataServiceVersion devem ser incluídos em todos os pedidos; no entanto, se não forem, então atualmente os Serviços de Media assumem que o valor dataServiceVersion em uso é de 3.0.
> 
> 

Segue-se um conjunto de cabeçalhos opcionais:

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| Date |Data RFC 1123 |Carimbo de tempo do pedido |
| Aceitar |Tipo de conteúdo |O tipo de conteúdo solicitado para a resposta, tais como:<p> -application/json;odata=verbose<p> - aplicação/átomo+xml<p> As respostas podem ter um tipo de conteúdo diferente, como uma busca blob, onde uma resposta bem sucedida contém o fluxo de bolhas como a carga útil. |
| Aceitação-Codificação |Gzip, esvaziar |Codificação GZIP e DEFLATE, quando aplicável. Nota: Para grandes recursos, os Serviços de Media podem ignorar este cabeçalho e devolver dados não comprimidos. |
| Linguagem aceitação |"en", "es", e assim por diante. |Especifica a linguagem preferida para a resposta. |
| Aceitar-Charset |Tipo charset como "UTF-8" |O padrão é UTF-8. |
| Método X-HTTP |Método HTTP |Permite que clientes ou firewalls que não suportem métodos HTTP como PUT ou DELETE utilizem estes métodos, túneis através de uma chamada GET. |
| Tipo de conteúdo |Tipo de conteúdo |Tipo de conteúdo do organismo de pedido em pedidos PUT ou POST. |
| cliente-request-id |Cadeia |Um valor definido pelo chamador que identifica o pedido dado. Se especificado, este valor será incluído na mensagem de resposta como forma de mapear o pedido. <p><p>**Importante**<p>Os valores devem ser limitados a 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Cabeçalhos de resposta STANDARD HTTP suportados por Media Services
Segue-se um conjunto de cabeçalhos que lhe podem ser devolvidos dependendo do recurso que solicitava e da ação que pretendia realizar.

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| pedido-id |Cadeia |Um identificador único para a operação atual, serviço gerado. |
| cliente-request-id |Cadeia |Um identificador especificado pelo chamador no pedido original, se presente. |
| Date |Data RFC 1123 |A data/hora em que o pedido foi processado. |
| Tipo de conteúdo |Varia |O tipo de conteúdo do corpo de resposta. |
| Codificação de conteúdos |Varia |Gzip ou esvaziar, conforme apropriado. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos STANDARD HTTP suportados por Media Services
Segue-se uma lista completa de verbos HTTP que podem ser utilizados ao fazer pedidos http:

| Verbo | Descrição |
| --- | --- |
| GET |Devolve o valor atual de um objeto. |
| POST |Cria um objeto com base nos dados fornecidos ou submete um comando. |
| PUT |Substitui um objeto ou cria um objeto nomeado (quando aplicável). |
| DELETE |Elimina um objeto. |
| FUSÃO |Atualiza um objeto existente com alterações de propriedade nomeadas. |
| CABEÇA |Devolve metadados de um objeto para uma resposta GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Descubra e navegue no modelo de entidade dos Media Services
Para tornar as entidades dos Serviços de Media mais detetáveis, a operação $metadata pode ser utilizada. Permite-lhe recuperar todos os tipos de entidades válidas, propriedades de entidades, associações, funções, ações, e assim por diante. Ao adicionar a $metadata operação ao fim do seu ponto final da Media Services REST API, pode aceder a este serviço de descoberta.

 /api/$metadata.

Deve anexar "?api-version=2.x" até ao final do URI se quiser ver os metadados num browser, ou não incluir o cabeçalho da versão x-ms no seu pedido.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Autenticar com Media Services REST usando o Diretório Ativo Azure

A autenticação na API REST é feita através do Diretório Ativo Azure (AAD).
Para mais informações sobre como obter os dados de autenticação necessários para a sua conta de Media Services a partir do Portal Azure, consulte [Aceda à API dos Serviços de Comunicação Social Azure com autenticação Azure AD.](media-services-use-aad-auth-to-access-ams-api.md)

Para mais detalhes sobre o código de escrita que se conecta à API REST utilizando a autenticação Azure AD, consulte o artigo Utilização da [autenticação Azure AD para aceder à API azure Media Services com REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Passos seguintes
Para saber como utilizar a autenticação Azure AD com Media Services REST API, consulte [a autenticação AD Azure para aceder à API azure Media Services com REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

