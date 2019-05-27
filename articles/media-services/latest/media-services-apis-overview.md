---
title: Programar com v3 APIs - Azure | Documentos da Microsoft
description: Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com serviços de multimédia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3ce20b56fc2cbebbed4b525eeccc2c12d14cccc3
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556235"
---
# <a name="developing-with-media-services-v3-apis"></a>Desenvolvimento com os serviços de multimédia de v3 APIs

Como desenvolvedor, pode utilizar os serviços de multimédia [REST API](https://aka.ms/ams-v3-rest-ref) ou bibliotecas de cliente que permitem-lhe interagir com a API de REST para facilmente criar, gerir e manter fluxos de trabalho de suporte de dados personalizado. O [serviços de multimédia v3](https://aka.ms/ams-v3-rest-sdk) API baseia-se a especificação de OpenAPI (anteriormente conhecida como um Swagger).

Este artigo discute as regras que se aplicam às entidades e APIs ao desenvolver com serviços de multimédia v3.

## <a name="accessing-the-azure-media-services-api"></a>Acessar a API dos serviços de multimédia do Azure

Para ficar autorizada a aceder a recursos de serviços de multimédia e a API de serviços de suporte de dados, tem primeiro de ser autenticado. Serviços de multimédia suportam [do Azure Active Directory (Azure AD)-com base](../../active-directory/fundamentals/active-directory-whatis.md) autenticação. Duas opções de autenticação comuns são:
 
* **Autenticação do principal de serviço** - utilizado para autenticar um serviço (por exemplo: web apps, aplicações de funções, aplicações lógicas, API e microsserviços). Aplicativos geralmente usam este método de autenticação são as aplicações que executem serviços de daemon, serviços de camada intermediária ou tarefas agendadas. Por exemplo, para a Web há aplicativos devem ser sempre um escalão médio que se liga a serviços de multimédia com um Principal de serviço.
* **Autenticação de utilizador** - utilizado para autenticar uma pessoa que está a utilizar a aplicação para interagir com os recursos de serviços de multimédia. O aplicativo interativo deve primeiro solicitar ao utilizador as credenciais do utilizador. Um exemplo é uma aplicação de consola de gestão utilizada por utilizadores autorizados para monitorizar tarefas de codificação ou transmissão em direto.

A API de serviços de suporte de dados requer que o utilizador ou aplicação que faz a API REST solicita tem acesso ao recurso de conta dos serviços de multimédia e utilizar um **contribuinte** ou **proprietário** função. A API pode ser acessada com o **leitor** função, mas apenas **obter** ou **lista**   operações estarão disponíveis. Para obter mais informações, consulte [controlo de acesso baseado em funções para contas de serviços de multimédia](rbac-overview.md).

Em vez de criar um principal de serviço, considere a utilização de identidades geridas para recursos do Azure para aceder à API de serviços de multimédia através do Gestor de recursos do Azure. Para saber mais sobre identidades geridas para recursos do Azure, veja [o que há de identidades geridas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Principal de serviço do Azure AD 

Se estiver a criar uma aplicação do Azure AD e o serviço principal, a aplicação tem de ser no seu próprio inquilino. Depois de criar a aplicação, atribuir a aplicação **contribuinte** ou **proprietário** acesso à função para a conta de Media Services. 

Se não tiver a certeza se tem permissões para criar uma aplicação do Azure AD, consulte [permissões obrigatórias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na figura a seguir, os números representam o fluxo de pedidos por ordem cronológica:

![Aplicações de camada intermediária](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Uma aplicação de camada intermediária solicita um token de acesso do Azure AD que tenha os seguintes parâmetros:  

   * Endpoint de inquilino do Azure AD.
   * URI do recurso de serviços de multimédia.
   * URI do recurso para os serviços de multimédia REST.
   * Valores de aplicações do Azure AD: o ID de cliente e o segredo de cliente.
   
   Para obter todos os valores necessários, consulte [acesso à API de serviços de multimédia do Azure com a CLI do Azure](access-api-cli-how-to.md)

2. O token de acesso do Azure AD é enviado para a camada intermediária.
4. A camada média envia o pedido à API de REST de multimédia do Azure com o token do Azure AD.
5. A camada média volta obtém os dados dos serviços de multimédia.

### <a name="samples"></a>Exemplos

Consulte os seguintes exemplos que mostram como ligar com o principal de serviço do Azure AD:

* [Ligar com REST](media-rest-apis-with-postman.md)  
* [Connect with Java](configure-connect-java-howto.md) (Ligar com Java)
* [Connect with .NET](configure-connect-dotnet-howto.md) (Ligar com .NET)
* [Connect with Node.js](configure-connect-nodejs-howto.md) (Ligar com Node.js)
* [Connect with Python](configure-connect-python-howto.md) (Ligar com Python)

## <a name="naming-conventions"></a>Convenções de nomenclatura

Os nomes de recursos dos Serviços de Multimédia do Azure v3 (por exemplo, Ativos, Tarefas, Transformações) estão sujeitos às restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes de recursos são sempre exclusivos. Desta forma, pode utilizar todas as cadeias de identificador exclusivo (por exemplo, GUIDs) para os nomes de recursos. 

Os nomes de recursos dos Serviços de Multimédia não podem incluir: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", a plica ou qualquer caráter de controlo. Todos os outros carateres são permitidos. O comprimento máximo de um nome de recurso é superior a 260 carateres. 

Para obter mais informações sobre a nomenclatura do Azure Resource Manager, consulte: [Requisitos de nomes](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Operações de longa execução

As operações marcadas com `x-ms-long-running-operation` nos serviços de multimédia do Azure [swagger ficheiros](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) são longos em execução de operações. 

Para obter detalhes sobre como controlar as operações assíncronas do Azure, consulte [operações assíncronas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Serviços de multimédia têm as seguintes operações de longa execução:

* Criar LiveEvent
* Atualizar LiveEvent
* Delete LiveEvent
* Iniciar LiveEvent
* Parar LiveEvent
* Reset LiveEvent
* Criar LiveOutput
* Delete LiveOutput
* Criar StreamingEndpoint
* Atualizar StreamingEndpoint
* Eliminar StreamingEndpoint
* Iniciar StreamingEndpoint
* Parar StreamingEndpoint
* Dimensionamento StreamingEndpoint


## <a name="sdks"></a>SDKs

> [!NOTE]
> Não são garantidos que os SDKs do Media Services do Azure v3 ser thread-safe. Ao desenvolver um aplicativo multithreaded, deve adicionar a sua própria lógica de sincronização de thread para proteger o cliente ou utilize um novo objeto de AzureMediaServicesClient por thread. Também deve ter cuidado dos problemas de multithreading introduzidos por objetos opcionais fornecidos pelo seu código para o cliente (como uma instância do HttpClient no .NET).

|SDK|Referência|
|---|---|
|[SDK do .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referência a .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[SDK Java](https://aka.ms/ams-v3-java-sdk)|[Referência a Java](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Referência a Python](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Referência a Node.js](https://aka.ms/ams-v3-nodejs-ref)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Referência a Go](https://aka.ms/ams-v3-go-ref)|
|[SDK Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Consulte também

- [SDK de .NET de EventGrid que inclui eventos de serviço de multimédia](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de serviços de multimédia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Explorador dos Serviços de Multimédia do Azure

[Explorador dos serviços de multimédia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) é uma ferramenta disponível para os clientes do Windows que desejam saber mais sobre os serviços de multimédia. AMSE é uma Winforms /C# aplicação que carregar, transferir, codificar, transmitir em fluxo VOD e live conteúdo com os Media Services. A ferramenta AMSE é para os clientes que desejam testar os serviços de multimédia sem escrever nenhum código. O código AMSE é fornecido como um recurso para os clientes que pretendem desenvolver com os serviços de multimédia.

AMSE é um projeto de código-fonte aberto, o suporte é fornecido pela Comunidade (problemas podem ser reportados para https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, consulte a [código de conduta FAQ](https://opensource.microsoft.com/codeofconduct/faq/) ou contacte opencode@microsoft.com com quaisquer perguntas ou comentários adicionais.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, ordenação, paginação de entidades de serviços de multimédia

Consulte [filtragem, ordenação, a paginação de entidades de serviços de multimédia do Azure](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obter atualizações

Veja a [Comunidade dos serviços de multimédia do Azure](media-services-community.md) artigo para ver formas diferentes, pode fazer perguntas, comentários e obter atualizações sobre os serviços de multimédia.

## <a name="next-steps"></a>Passos Seguintes

* [Ligar aos Media Services com Java](configure-connect-java-howto.md)
* [Ligar a serviços de multimédia com .NET](configure-connect-dotnet-howto.md)
* [Ligar a serviços de multimédia com node. js](configure-connect-nodejs-howto.md)
* [Ligar a serviços de multimédia com Python](configure-connect-python-howto.md)
