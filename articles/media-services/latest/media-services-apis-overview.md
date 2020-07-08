---
title: Desenvolver com V3 APIs
titleSuffix: Azure Media Services
description: Conheça as regras aplicáveis às entidades e APIs no desenvolvimento com os Serviços de Comunicação Social v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ea74c85af062ce00dbccf8a486ce39cbd524bb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515060"
---
# <a name="develop-with-media-services-v3-apis"></a>Desenvolver com Serviços de Mídia v3 APIs

Como desenvolvedor, pode utilizar [a API](https://docs.microsoft.com/rest/api/media/) dos Serviços de Media OU bibliotecas de clientes que lhe permitem interagir com a API REST para criar, gerir e manter fluxos de trabalho personalizados para os meios de comunicação. O [Serviço de Mídia v3](https://aka.ms/ams-v3-rest-sdk) API baseia-se na especificação OpenAPI (anteriormente conhecida como Swagger).

Este artigo discute regras que se aplicam a entidades e APIs quando se desenvolve com os Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Acesso à Azure Media Services API

Para ser autorizado a aceder aos recursos dos Serviços de Comunicação social e à API dos Serviços de Comunicação Social, tem primeiro de ser autenticado. Os Serviços de Comunicação Social suportam a autenticação [baseada no Azure Ative Directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) Duas opções comuns de autenticação são:
 
* **Autenticação principal do serviço**: Usado para autenticar um serviço (por exemplo: aplicações web, aplicações de função, aplicações lógicas, API e microserviços). As aplicações que normalmente utilizam este método de autenticação são aplicações que executam serviços daemon, serviços de nível médio ou trabalhos programados. Por exemplo, para aplicações web deve haver sempre um nível médio que se conecta aos Serviços de Mídia com um Principal de Serviço.
* **Autenticação do utilizador**: Usado para autenticar uma pessoa que está a usar a app para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador as credenciais do utilizador. Um exemplo é uma aplicação de consola de gestão usada por utilizadores autorizados para monitorizar trabalhos de codificação ou streaming ao vivo.

A API dos Serviços de Mídia exige que o utilizador ou app que faz os pedidos de API REST tenha acesso ao recurso da conta dos Serviços de Mídia e utilize uma função **de Contribuinte** ou **Proprietário.** A API pode ser acedida com a função **Reader,** mas apenas as operações **Get** ou **List** estarão disponíveis.Para obter mais informações, consulte [o controlo de acesso baseado em funções para contas de Serviços de Mídia.](rbac-overview.md)

Em vez de criar um principal de serviço, considere usar identidades geridas para os recursos da Azure acederem à API dos Serviços de Comunicação Através do Azure Resource Manager. Para saber mais sobre identidades geridas para recursos Azure, veja [o que é gerida identidades para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Diretor de serviço AZure AD

Se está a criar uma aplicação AD Azure e um diretor de serviço, a aplicação tem de estar no seu próprio inquilino. Depois de criar a app, dê à app o acesso da função **Contribuinte** ou **Proprietário** à conta dos Serviços de Media.

Se não tem a certeza se tem permissões para criar uma aplicação AD Azure, consulte [permissões necessárias.](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)

No seguinte número, os números representam o fluxo dos pedidos por ordem cronológica:

![Autenticação de aplicativos de nível médio com AAD a partir de uma API web](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Uma aplicação de nível médio solicita um token de acesso AZure AD que tem os seguintes parâmetros:  

   * Azure AD inquilino ponto final.
   * Recursos de mídia URI.
   * URI de recurso para rest media Services.
   * Valores de aplicação AD AZure: o ID do cliente e o segredo do cliente.

   Para obter todos os valores necessários, consulte [a Access Azure Media Services API](access-api-cli-how-to.md).

2. O sinal de acesso Azure AD é enviado para o nível médio.
4. O nível médio envia pedido à Azure Media REST API com o token AD Azure.
5. O nível médio recebe de volta os dados dos Serviços de Comunicação Social.

### <a name="samples"></a>Amostras

Veja as seguintes amostras que mostram como se conectar com o principal do serviço Azure AD:

* [Conecte-se com REST](media-rest-apis-with-postman.md)  
* [Ligar com Java](configure-connect-java-howto.md)
* [Ligar com .NET](configure-connect-dotnet-howto.md)
* [Ligar com Node.js](configure-connect-nodejs-howto.md)
* [Ligar com Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenções de nomenclatura

Os nomes de recursos dos Serviços de Multimédia do Azure v3 (por exemplo, Ativos, Tarefas, Transformações) estão sujeitos às restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes de recursos são sempre exclusivos. Desta forma, pode utilizar todas as cadeias de identificador exclusivo (por exemplo, GUIDs) para os nomes de recursos.

Os nomes dos recursos dos Serviços de Comunicação Social não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', '.', o personagem de citação única, ou quaisquer personagens de controlo. Todos os outros carateres são permitidos. O comprimento máximo de um nome de recurso é superior a 260 carateres.

Para obter mais informações sobre o nome do Azure Resource Manager, consulte [os requisitos de nomeação](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [as convenções de nomeação.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

### <a name="names-of-filesblobs-within-an-asset"></a>Nomes de ficheiros/bolhas dentro de um ativo

Os nomes de ficheiros/bolhas dentro de um ativo devem seguir tanto os requisitos de [nome blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) como os requisitos de [nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). A razão para estes requisitos é que os ficheiros podem ser copiados do armazenamento de bolhas para um disco NTFS local para processamento.

## <a name="long-running-operations"></a>Operações de longa duração

As operações `x-ms-long-running-operation` marcadas nos [ficheiros](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) swagger da Azure Media Services são operações de longa duração. 

Para mais detalhes sobre como rastrear as operações assíncronas do Azure, consulte [as operações da Async](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Os Serviços de Comunicação Social têm as seguintes operações de longo prazo:

* [Criar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Atualizar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Excluir evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Início ao evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Parar LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Utilize o `removeOutputsOnStop` parâmetro para eliminar todas as saídas ao vivo associadas ao interromper o evento.  
* [Reset LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Criar LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Excluir LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Criar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Atualização StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Excluir streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Iniciar streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Parar streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Escala streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Após a submissão bem sucedida de uma longa operação, recebe um '202 Accepted' e deve sondar para conclusão da operação utilizando o ID de operação devolvido.

O artigo [de operações assíncronas da Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) explica em profundidade como acompanhar o estado das operações assíncronas do Azure através de valores devolvidos na resposta.

Apenas uma operação de longa duração é suportada para um determinado Evento Ao Vivo ou qualquer uma das suas saídas ao vivo associadas. Uma vez iniciado, uma operação de longa duração deve ser concluída antes de iniciar uma operação de longo prazo subsequente no mesmo LiveEvent ou em quaisquer Saídas Ao Vivo associadas. Para eventos ao vivo com várias Saídas Ao Vivo, deve aguardar a conclusão de uma longa operação em execução numa Saída Ao Vivo antes de desencadear uma longa operação em execução em outra Saída Ao Vivo. 

## <a name="sdks"></a>SDKs

> [!NOTE]
> Os Azure Media Services v3 SDKs não são garantidos como seguros. Ao desenvolver uma aplicação multi-roscada, deve adicionar a sua própria lógica de sincronização de fios para proteger o cliente ou utilizar um novo objeto AzureMediaServicesClient por fio. Deve também ter cuidado com as questões de múltiplos fios introduzidas por objetos opcionais fornecidos pelo seu código ao cliente (como uma instância HttpClient em .NET).

|SDK|Referência|
|---|---|
|[SDK do .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referência a .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[SDK Java](https://aka.ms/ams-v3-java-sdk)|[Referência a Java](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Referência a Python](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Referência a Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[SDK Go](https://aka.ms/ams-v3-go-sdk) |[Referência a Go](https://aka.ms/ams-v3-go-ref)|
|[SDK Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Veja também

- [EventGrid .NET SDK que inclui eventos de Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Explorador dos Serviços de Multimédia do Azure

[O Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) é uma ferramenta disponível para clientes windows que querem aprender sobre os Media Services. AMSE é uma aplicação Winforms/C# que faz upload, download, codificação, transmissão de VOD e conteúdo ao vivo com serviços de media. A ferramenta AMSE destina-se a clientes que pretendam testar serviços de mídia sem escrever qualquer código. O código AMSE é fornecido como um recurso para clientes que queiram desenvolver-se com os Media Services.

A AMSE é um projeto Open Source, o apoio é fornecido pela comunidade (as questões podem ser reportadas a https://github.com/Azure/Azure-Media-Services-Explorer/issues) . Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Para mais informações, consulte o [Código de Conduta faQ](https://opensource.microsoft.com/codeofconduct/faq/) ou contacte opencode@microsoft.com com quaisquer outras questões ou comentários.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, encomenda, paging de entidades de Serviços de Comunicação Social

Consulte [a filtragem, encomenda, paging de entidades da Azure Media Services](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="see-also"></a>Veja também

Para obter todos os valores necessários, consulte [a Access Azure Media Services API](access-api-cli-how-to.md).

## <a name="next-steps"></a>Próximos passos

* [Ligue-se aos Serviços de Comunicação Social com a Java](configure-connect-java-howto.md)
* [Ligar aos Serviços de Mídia com .NET](configure-connect-dotnet-howto.md)
* [Ligue-se aos Serviços de Comunicação social com Node.js](configure-connect-nodejs-howto.md)
* [Ligue-se aos serviços de mídia com a Python](configure-connect-python-howto.md)
