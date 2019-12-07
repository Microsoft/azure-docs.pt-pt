---
title: Visão geral da API REST de operações dos serviços de mídia | Microsoft Docs
description: A API "REST de operações de serviços de mídia" é usada para criar trabalhos, ativos, canais ao vivo e outros recursos em uma conta de serviços de mídia. Este artigo fornece uma visão geral da API REST dos serviços de mídia do Azure v2.
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
ms.openlocfilehash: 7df1651be01b4bed533c1173cc37bddda58f0aa3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895820"
---
# <a name="media-services-operations-rest-api-overview"></a>Visão geral da API REST de operações dos serviços de mídia 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

A API **REST de operações dos serviços de mídia** é usada para criar trabalhos, ativos, canais ao vivo e outros recursos em uma conta dos serviços de mídia. Para obter mais informações, consulte [referência da API REST de operações dos serviços de mídia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Os serviços de mídia fornecem uma API REST que aceita o formato XML JSON ou Atom + pub. A API REST dos serviços de mídia requer cabeçalhos HTTP específicos que cada cliente deve enviar ao se conectar aos serviços de mídia, bem como um conjunto de cabeçalhos opcionais. As seções a seguir descrevem os cabeçalhos e os verbos HTTP que você pode usar ao criar solicitações e receber respostas dos serviços de mídia.

A autenticação para a API REST dos serviços de mídia é feita por meio de Azure Active Directory autenticação que é descrita no artigo [usar a autenticação do Azure ad para acessar a API dos serviços de mídia do Azure com REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Considerações

As considerações a seguir se aplicam ao usar REST.

* Ao consultar entidades, há um limite de 1000 entidades retornadas ao mesmo tempo porque o REST público v2 limita os resultados da consulta a 1000 resultados. Você precisa usar **Skip** e **Take** (.net)/ **Top** (REST), conforme descrito neste [exemplo de .net](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo de API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Ao usar o JSON e especificar para usar a palavra-chave **__metadata** na solicitação (por exemplo, para fazer referência a um objeto vinculado), você deve definir o cabeçalho **Accept** para o [formato JSON detalhado](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (consulte o exemplo a seguir). O OData não entende a propriedade **__metadata** na solicitação, a menos que você a defina como Verbose.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Cabeçalhos de solicitação HTTP padrão com suporte dos serviços de mídia
Para cada chamada feita nos serviços de mídia, há um conjunto de cabeçalhos necessários que você deve incluir em sua solicitação e também um conjunto de cabeçalhos opcionais que talvez você queira incluir. A tabela a seguir lista os cabeçalhos necessários:

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| Autorização |Portador |O portador é o único mecanismo de autorização aceito. O valor também deve incluir o token de acesso fornecido pelo Azure Active Directory. |
| x-MS-Version |Decimal |2,17 (ou versão mais recente)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Como os serviços de mídia usam OData para expor suas APIs REST, os cabeçalhos DataServiceVersion e MaxDataServiceVersion devem ser incluídos em todas as solicitações; no entanto, se não forem, os serviços de mídia no momento consideram que o valor de DataServiceVersion em uso é 3,0.
> 
> 

Este é um conjunto de cabeçalhos opcionais:

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| Date |Data da RFC 1123 |Carimbo de data/hora da solicitação |
| Aceitar |Tipo de conteúdo |O tipo de conteúdo solicitado para a resposta, como o seguinte:<p> -application/json;odata=verbose<p> -Application/Atom + XML<p> As respostas podem ter um tipo de conteúdo diferente, como uma busca de BLOB, em que uma resposta bem-sucedida contém o fluxo de blob como a carga. |
| Accept-Encoding |Gzip, deflate |Codificação GZIP e deflate, quando aplicável. Observação: para recursos grandes, os serviços de mídia podem ignorar esse cabeçalho e retornar dados não compactados. |
| Aceitar idioma |"en", "es" e assim por diante. |Especifica o idioma preferencial para a resposta. |
| Accept-charset |Tipo de conjunto de caracteres como "UTF-8" |O padrão é UTF-8. |
| Método X-HTTP |Método HTTP |Permite que clientes ou firewalls que não dão suporte a métodos HTTP como PUT ou DELETE usem esses métodos, encapsulados por meio de uma chamada GET. |
| Content-Type |Tipo de conteúdo |Tipo de conteúdo do corpo da solicitação em solicitações PUT ou POST. |
| ID do cliente-solicitação |String |Um valor definido pelo chamador que identifica a solicitação especificada. Se especificado, esse valor será incluído na mensagem de resposta como uma maneira de mapear a solicitação. <p><p>**Importante**<p>Os valores devem ser limitados em 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Cabeçalhos de resposta HTTP padrão com suporte dos serviços de mídia
Veja a seguir um conjunto de cabeçalhos que podem ser retornados para você dependendo do recurso que você estava solicitando e da ação que você pretende executar.

| Cabeçalho | Tipo | Valor |
| --- | --- | --- |
| ID da solicitação |String |Um identificador exclusivo para a operação atual, gerado pelo serviço. |
| ID do cliente-solicitação |String |Um identificador especificado pelo chamador na solicitação original, se presente. |
| Date |Data da RFC 1123 |A data/hora em que a solicitação foi processada. |
| Content-Type |Varia |O tipo de conteúdo do corpo da resposta. |
| Codificação de conteúdo |Varia |Gzip ou deflate, conforme apropriado. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos HTTP padrão com suporte dos serviços de mídia
A seguir está uma lista completa de verbos HTTP que podem ser usados ao fazer solicitações HTTP:

| Verbo | Descrição |
| --- | --- |
| GET |Retorna o valor atual de um objeto. |
| POST |Cria um objeto com base nos dados fornecidos ou envia um comando. |
| PUT |Substitui um objeto ou cria um objeto nomeado (quando aplicável). |
| DELETE |Exclui um objeto. |
| MESCLE |Atualiza um objeto existente com alterações de propriedade nomeadas. |
| HEAD |Retorna os metadados de um objeto para uma resposta GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Descobrir e procurar o modelo de entidade dos serviços de mídia
Para tornar as entidades dos serviços de mídia mais detectáveis, a operação de $metadata pode ser usada. Ele permite que você recupere todos os tipos de entidade válidos, propriedades de entidade, associações, funções, ações e assim por diante. Ao adicionar a operação de $metadata ao final do ponto de extremidade da API REST dos serviços de mídia, você pode acessar esse serviço de descoberta.

 /api/$metadata.

Você deve acrescentar "? API-Version = 2. x" ao final do URI se desejar exibir os metadados em um navegador ou não incluir o cabeçalho x-MS-Version em sua solicitação.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Autenticar com o REST dos serviços de mídia usando Azure Active Directory

A autenticação na API REST é feita por meio do Azure Active Directory (AAD).
Para obter detalhes sobre como obter os detalhes de autenticação necessários para sua conta de serviços de mídia no portal do Azure, consulte [acessar a API dos serviços de mídia do Azure com a autenticação do Azure ad](media-services-use-aad-auth-to-access-ams-api.md).

Para obter detalhes sobre como escrever código que se conecta à API REST usando a autenticação do Azure AD, consulte o artigo [usar a autenticação do Azure ad para acessar a API dos serviços de mídia do Azure com REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Passos seguintes
Para saber como usar a autenticação do Azure AD com a API REST dos serviços de mídia, confira [usar a autenticação do Azure ad para acessar a API dos serviços de mídia do Azure com REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

