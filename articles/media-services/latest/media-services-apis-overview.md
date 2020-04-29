---
title: Desenvolver com v3 APIs
titleSuffix: Azure Media Services
description: Conheça as regras aplicáveis a entidades e APIs no desenvolvimento com o Media Services v3.
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
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79472089"
---
# <a name="develop-with-media-services-v3-apis"></a>Desenvolver com Media Services v3 APIs

Como desenvolvedor, pode utilizar a API do Media Services [REST API](https://docs.microsoft.com/rest/api/media/) ou bibliotecas de clientes que lhe permitem interagir com a API REST para criar, gerir e manter fluxos de trabalho personalizados dos media. O [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API baseia-se na especificação OpenAPI (anteriormente conhecida como Swagger).

Este artigo discute regras aplicáveis a entidades e APIs quando se desenvolve com o Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Acesso à API dos Serviços de Mídia Azure

Para ser autorizado a aceder aos recursos dos Serviços de Media e à API dos Serviços de Media, tem primeiro de ser autenticado. A Media Services suporta a autenticação [baseada em Azure Ative Directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) Duas opções comuns de autenticação são:
 
* **Autenticação principal**do serviço : Usado para autenticar um serviço (por exemplo: aplicações web, aplicações de função, aplicações lógicas, API e microserviços). Aplicações que geralmente utilizam este método de autenticação são aplicações que executam serviços de daemon, serviços de nível médio ou empregos regulares. Por exemplo, para aplicações web deve haver sempre um nível médio que se conecta aos Serviços de Media com um Diretor de Serviço.
* **Autenticação do utilizador**: Usado para autenticar uma pessoa que está a usar a app para interagir com os recursos dos Media Services. A aplicação interativa deve primeiro solicitar ao utilizador as credenciais do utilizador. Um exemplo é uma aplicação de consola de gestão usada por utilizadores autorizados para monitorizar trabalhos de codificação ou streaming ao vivo.

A API dos Serviços de Media exige que o utilizador ou app que efevie os pedidos rest API tenha acesso ao recurso da conta media Services e utilize uma função **de Contribuinte** ou **Proprietário.** A API pode ser acedida com a função **Reader,** mas apenas as operações **de Get** or **List** estarão disponíveis.Para mais informações, consulte o [controlo de acesso baseado em papéis para as contas dos Serviços de Media](rbac-overview.md).

Em vez de criar um diretor de serviço, considere usar identidades geridas para os recursos do Azure para aceder à API dos Serviços de Media através do Gestor de Recursos Azure. Para saber mais sobre identidades geridas para os recursos Azure, veja [o que é gerida identidades para os recursos Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="azure-ad-service-principal"></a>Diretor de serviço da Azure AD

Se está a criar uma app e diretor de serviço da Azure AD, a aplicação tem de estar no seu próprio inquilino. Depois de criar a app, dê à app **O colaborador** ou **o proprietário** tem acesso à conta de Media Services.

Se não tem a certeza se tem permissões para criar uma aplicação Azure AD, consulte [as permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Nos seguintes números, os números representam o fluxo dos pedidos por ordem cronológica:

![Autenticação de aplicativode nível médio com AAD a partir de uma API web](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Uma aplicação de nível médio solicita um sinal de acesso Azure AD que tem os seguintes parâmetros:  

   * Ponto final do inquilino da AD Azure.
   * Recurso URI dos Serviços de Media.
   * Recursos URI para REST Media Services.
   * Valores da aplicação Azure AD: o ID do cliente e o segredo do cliente.

   Para obter todos os valores necessários, consulte [access Azure Media Services API com o Azure CLI](access-api-cli-how-to.md).

2. O sinal de acesso azure ad é enviado para o nível médio.
4. O nível médio envia um pedido à API Azure Media REST com o token Azure AD.
5. O nível médio recupera os dados dos Serviços de Media.

### <a name="samples"></a>Amostras

Consulte as seguintes amostras que mostram como se conectar com o diretor de serviço da Azure AD:

* [Conecte-se com o REST](media-rest-apis-with-postman.md)  
* [Ligar com Java](configure-connect-java-howto.md)
* [Ligar com .NET](configure-connect-dotnet-howto.md)
* [Ligar com Node.js](configure-connect-nodejs-howto.md)
* [Ligar com Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenções de nomenclatura

Os nomes de recursos dos Serviços de Multimédia do Azure v3 (por exemplo, Ativos, Tarefas, Transformações) estão sujeitos às restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes de recursos são sempre exclusivos. Desta forma, pode utilizar todas as cadeias de identificador exclusivo (por exemplo, GUIDs) para os nomes de recursos.

Os nomes dos recursos dos Serviços de Media não podem incluir: '<', '>', '&', ':', '&#92;', '?', '/', '*', '+', '.', o personagem de citação única, ou quaisquer caracteres de controlo. Todos os outros carateres são permitidos. O comprimento máximo de um nome de recurso é superior a 260 carateres.

Para obter mais informações sobre o nome do Gestor de Recursos Azure, consulte [os requisitos](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) de nomeação e [as convenções de nomeação.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

### <a name="names-of-filesblobs-within-an-asset"></a>Nomes de ficheiros/bolhas dentro de um ativo

Os nomes dos ficheiros/bolhas dentro de um ativo devem seguir os requisitos de [nome blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os requisitos de [nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). A razão para estes requisitos é que os ficheiros podem ser copiados do armazenamento de blob para um disco NTFS local para processamento.

## <a name="long-running-operations"></a>Operações de longo prazo

As operações `x-ms-long-running-operation` marcadas nos [ficheiros da Azure](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) Media Services são operações de longo prazo. 

Para mais detalhes sobre como rastrear operações assíncronas do Azure, consulte [as operações da Async.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)

A Media Services tem as seguintes operações de longo prazo:

* [Criar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Atualizar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Excluir evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Iniciar evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Pare o LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Utilize `removeOutputsOnStop` o parâmetro para eliminar todas as saídas ao vivo associadas ao parar o evento.  
* [Reset LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Criar LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Eliminar liveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Criar o StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Update StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Eliminar o Ponto de StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Iniciar o StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Pare o ponto de paragem do streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Escala StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Após a apresentação bem sucedida de uma longa operação, recebe um '202 Aceito' e deve fazer uma sondagem para a conclusão da operação utilizando o ID de operação devolvido.

O artigo de [operações assíncronos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) da pista do Azure explica em profundidade como acompanhar o estado das operações assíncronas do Azure através de valores devolvidos na resposta.

Apenas uma operação de longa duração é suportada para um dado Live Event ou qualquer uma das suas saídas ao vivo associadas. Uma vez iniciado, uma operação de longa duração deve ser concluída antes de iniciar uma operação subsequente de longo prazo no mesmo LiveEvent ou em quaisquer saídas ao vivo associadas. Para eventos ao vivo com várias saídas ao vivo, deve aguardar a conclusão de uma operação de longa duração numa saída ao vivo antes de desencadear uma operação de longa duração noutra Saída Ao Vivo. 

## <a name="sdks"></a>SDKs

> [!NOTE]
> Os Serviços De Mídia Azure v3 SDKs não são garantidos como seguros de fio. Ao desenvolver uma aplicação com fios múltiplos, deve adicionar a sua própria lógica de sincronização de fios para proteger o cliente ou utilizar um novo objeto AzureMediaServicesClient por fio. Deve também ter cuidado com questões multi-threading introduzidas por objetos opcionais fornecidos pelo seu código ao cliente (como uma instância httpClient em .NET).

|SDK|Referência|
|---|---|
|[SDK .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referência a .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[SDK Java](https://aka.ms/ams-v3-java-sdk)|[Referência a Java](https://aka.ms/ams-v3-java-ref)|
|[SDK Python](https://aka.ms/ams-v3-python-sdk)|[Referência a Python](https://aka.ms/ams-v3-python-ref)|
|[SDK Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Referência a Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[SDK Go](https://aka.ms/ams-v3-go-sdk) |[Referência a Go](https://aka.ms/ams-v3-go-ref)|
|[SDK Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Consulte também

- [EventGrid .NET SDK que inclui eventos do Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Media](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Explorador dos Serviços de Multimédia do Azure

[O Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) é uma ferramenta disponível para clientes windows que querem aprender sobre os Serviços de Media. A AMSE é uma aplicação Winforms/C# que faz upload, download, codificação, streaming VOD e conteúdo ao vivo com os Media Services. A ferramenta AMSE destina-se a clientes que pretendam testar os Serviços de Media sem escrever qualquer código. O código AMSE é fornecido como um recurso para os clientes que querem desenvolver-se com os Media Services.

A AMSE é um projeto Open Source, o apoio é https://github.com/Azure/Azure-Media-Services-Explorer/issues)prestado pela comunidade (as questões podem ser reportadas a . Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Para mais informações, consulte o Código opencode@microsoft.com de Conduta DAS [FAQ](https://opensource.microsoft.com/codeofconduct/faq/) ou contacte quaisquer outras questões ou comentários.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, encomenda, paging de entidades de Serviços de Media

Ver [Filtragem, encomenda, paging de entidades da Azure Media Services.](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se aos Serviços de Media com Java](configure-connect-java-howto.md)
* [Ligue-se aos Serviços de Media com .NET](configure-connect-dotnet-howto.md)
* [Ligue-se aos Serviços de Media com Node.js](configure-connect-nodejs-howto.md)
* [Ligue-se aos Serviços de Media com Python](configure-connect-python-howto.md)
