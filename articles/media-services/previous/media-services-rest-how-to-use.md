---
title: Descrição geral de REST API de operações de serviços de multimédia | Documentos da Microsoft
description: Descrição geral da API de REST de serviços de multimédia
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
ms.author: juliako;johndeu
ms.openlocfilehash: 549554521570d1d2f27b2da2b36ca1dfde25562f
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293622"
---
# <a name="media-services-operations-rest-api-overview"></a>Descrição geral de REST API de operações de serviços de multimédia 
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

O **REST de operações de serviços de multimédia** API é utilizada para a criação de tarefas, recursos, canais em direto e outros recursos numa conta de Media Services. Para obter mais informações, consulte [referência da API de REST de operações de serviços de multimédia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Os Media Services fornecem uma API REST que aceita JSON ou atom + pub formato XML. API de REST dos serviços de suporte de dados requer específicos cabeçalhos HTTP que cada cliente tem de enviar ao ligar a serviços de multimédia, bem como um conjunto de cabeçalhos opcionais. As secções seguintes descrevem os cabeçalhos e verbos HTTP, pode utilizar quando criar pedidos e receber respostas dos serviços de multimédia.

Autenticação para a API de REST de serviços de suporte de dados é feita através da autenticação do Azure Active Directory que é descrita no artigo [autenticação de utilização do Azure AD para aceder à API de serviços de multimédia do Azure com REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Considerações

As seguintes considerações aplicam-se ao utilizar o REST.

* Ao consultar entidades, existe um limite de 1000 entidades retornadas, ao mesmo tempo, porque a v2 REST pública limita os resultados da consulta para resultados de 1000. Precisa usar **ignorar** e **tirar** (.NET) / **superior** (REST), conforme descrito em [neste exemplo de .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [esta API de REST exemplo](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Quando usar o JSON e especificando a utilizar o **__metadata** palavra-chave no pedido (por exemplo, para referência um objeto vinculado) tem de definir o **Accept** cabeçalho para [formato JSON verboso](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(consulte o exemplo a seguir). OData não compreende o **__metadata** propriedade na solicitação, a menos que defina-o como verboso.  
  
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

## <a name="standard-http-request-headers-supported-by-media-services"></a>Cabeçalhos de pedido HTTP padrão suportados pelos Media Services
Para todas as chamadas feitas para os serviços de multimédia, existe um conjunto de cabeçalhos necessários, que tem de incluir no seu pedido e também um conjunto de cabeçalhos opcionais pode querer incluir. A tabela abaixo lista os cabeçalhos necessários:

| Cabeçalho | Type | Value |
| --- | --- | --- |
| Autorização |Portador |Portador é o mecanismo de autorização aceites apenas. O valor também tem de incluir o token de acesso fornecido pelo Azure Active Directory. |
| x-ms-version |Decimal |2.17 (ou versão mais recente)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Como os serviços de multimédia utiliza OData para expor as suas APIs REST, os cabeçalhos dataserviceversion s e MaxDataServiceVersion devem ser incluídos em todos os pedidos; No entanto, se não forem, em seguida, atualmente os serviços de multimédia assume que o valor de dataserviceversion s em utilização é 3.0.
> 
> 

Segue-se um conjunto de cabeçalhos opcionais:

| Cabeçalho | Type | Value |
| --- | --- | --- |
| Date |Data de RFC 1123 |Timestamp do pedido |
| Aceitar |Tipo de conteúdo |O tipo de conteúdo solicitado para a resposta, como o seguinte:<p> -application/json;odata=verbose<p> -application/atom + xml<p> As respostas podem ter um tipo de conteúdo diferente, como a obtenção de BLOBs, em que uma resposta com êxito contém o fluxo de BLOBs como o payload. |
| Aceite-Encoding |Gzip, deflate |GZIP e DEFLATE codificação, quando aplicável. Nota: Para recursos grandes, os serviços de multimédia pode ignorar este cabeçalho e retornar dados noncompressed. |
| Accept-Language |"pt", "es" e assim por diante. |Especifica o idioma preferido para a resposta. |
| Accept-Charset |Tipo de conjunto de carateres como "UTF-8" |A predefinição é UTF-8. |
| X-HTTP-Method |Método HTTP |Permite que os clientes ou firewalls que não suportam métodos HTTP, como PUT ou DELETE para usar esses métodos, túnel através de uma chamada GET. |
| Content-Type |Tipo de conteúdo |Pedidos de tipo de conteúdo do corpo do pedido PUT ou POST. |
| client-request-id |String |Um valor definido pelo autor da chamada que identifica o pedido indicado. Se for especificado, este valor será incluído na mensagem de resposta como uma maneira de mapear a solicitação. <p><p>**Importante**<p>Valores devem ser limitados a 2096b (k de 2). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Cabeçalhos de resposta HTTP padrão suportados pelos Media Services
Segue-se um conjunto de cabeçalhos que podem ser devolvidos para si, dependendo do recurso que foram solicitar e a ação que pretende executar.

| Cabeçalho | Type | Value |
| --- | --- | --- |
| id do pedido |String |Um identificador exclusivo para a operação atual, o serviço gerado. |
| client-request-id |String |Um identificador especificado pelo chamador na solicitação original, se estiver presente. |
| Date |Data de RFC 1123 |A data/hora que o pedido foi processado. |
| Content-Type |Varia |O tipo de conteúdo do corpo da resposta. |
| Codificação de conteúdo |Varia |Gzip ou deflate, conforme adequado. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Verbos HTTP padrão suportados pelos Media Services
Segue-se uma lista completa dos verbos HTTP que podem ser utilizados quando os pedidos de HTTP de fazer:

| Verbo | Descrição |
| --- | --- |
| GET |Devolve o valor atual de um objeto. |
| POST |Cria um objeto com base em dados fornecidos, ou envia um comando. |
| PUT |Substitui um objeto ou cria um objeto nomeado (quando aplicável). |
| DELETE |Exclui um objeto. |
| INTERCALAR |Atualiza um objeto existente com as alterações de propriedade nomeada. |
| CABEÇALHO |Devolve os metadados de um objeto para uma resposta GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Detetar e procurar o modelo de entidades de serviços de multimédia
Para tornar as entidades de serviços de multimédia mais detetável, a operação de $metadata pode ser utilizada. Ele permite-lhe obter todos os tipos de entidade válido, propriedades de entidade, associações, funções, ações e assim por diante. Ao adicionar a operação de $metadata ao final do ponto final da API de REST dos serviços de multimédia, pode aceder a este serviço de deteção.

 /api/$metadata.

Deve anexar "? api-version=2.x" ao final do URI se quiser ver os metadados num navegador ou, não inclua o cabeçalho x-ms-version no seu pedido.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Autenticar com o REST de serviços de multimédia com o Azure Active Directory

Autenticação na REST API é feita por meio de Directory(AAD) Active Directory do Azure.
Para obter detalhes sobre como obter os detalhes de autenticação obrigatórios para a sua conta de serviços de multimédia no Portal do Azure, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Para obter detalhes sobre como escrever código que liga à API do REST através da autenticação do Azure AD, consulte o artigo [autenticação de utilização do Azure AD para aceder à API de serviços de multimédia do Azure com REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Passos Seguintes
Para saber como utilizar a autenticação do Azure AD com a API de REST de serviços de multimédia, veja [autenticação de utilização do Azure AD para aceder à API de serviços de multimédia do Azure com REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

