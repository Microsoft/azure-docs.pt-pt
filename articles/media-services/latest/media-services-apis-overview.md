---
title: Desenvolvendo com APIs v3 – Azure | Microsoft Docs
description: Este artigo discute as regras que se aplicam a entidades e APIs ao desenvolver com os serviços de mídia v3.
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
ms.openlocfilehash: 79f1bd95451709485f92050a882c790f9e281eb5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049019"
---
# <a name="developing-with-media-services-v3-apis"></a>Desenvolvendo com APIs dos serviços de mídia v3

Como desenvolvedor, você pode usar a [API REST](https://aka.ms/ams-v3-rest-ref) dos serviços de mídia ou as bibliotecas de cliente que permitem que você interaja com a API REST para criar, gerenciar e manter facilmente fluxos de trabalho de mídia personalizados. A API [dos serviços de mídia v3](https://aka.ms/ams-v3-rest-sdk) é baseada na especificação openapi (anteriormente conhecida como Swagger).

Este artigo discute as regras que se aplicam a entidades e APIs ao desenvolver com os serviços de mídia v3.

## <a name="accessing-the-azure-media-services-api"></a>Acessando a API dos serviços de mídia do Azure

Para ser autorizado a acessar os recursos dos serviços de mídia e a API dos serviços de mídia, você deve primeiro ser autenticado. Os serviços de mídia oferecem suporte à autenticação [baseada no Azure Active Directory (AD do Azure)](../../active-directory/fundamentals/active-directory-whatis.md) . Duas opções comuns de autenticação são:
 
* **Autenticação de entidade de serviço** – usada para autenticar um serviço (por exemplo: aplicativos Web, aplicativos de funções, aplicativos lógicos, API e microservices). Os aplicativos que normalmente usam esse método de autenticação são aplicativos que executam serviços daemon, serviços de camada intermediária ou trabalhos agendados. Por exemplo, para aplicativos Web, sempre deve haver uma camada intermediária que se conecte aos serviços de mídia com uma entidade de serviço.
* **Autenticação de usuário** – usada para autenticar uma pessoa que está usando o aplicativo para interagir com os recursos dos serviços de mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou transmissão ao vivo.

A API dos serviços de mídia requer que o usuário ou aplicativo que faz as solicitações da API REST tenha acesso ao recurso de conta dos serviços de mídia e use uma função de **colaborador** ou **proprietário** . A API pode ser acessada com a função **leitor** , mas somente as operações **Get** ou **list** estarão disponíveis. Para obter mais informações, consulte [controle de acesso baseado em função para contas de serviços de mídia](rbac-overview.md).

Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para acessar a API dos serviços de mídia por meio de Azure Resource Manager. Para saber mais sobre identidades gerenciadas para recursos do Azure, confira [o que são identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Entidade de serviço do Azure AD 

Se você estiver criando um aplicativo do Azure AD e uma entidade de serviço, o aplicativo terá que estar em seu próprio locatário. Depois de criar o aplicativo, conceda ao **colaborador** do aplicativo ou à função de **proprietário** acesso à conta dos serviços de mídia. 

Se você não tiver certeza se tem permissões para criar um aplicativo do Azure AD, consulte [permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na figura a seguir, os números representam o fluxo das solicitações em ordem cronológica:

![Aplicativos de camada intermediária](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Um aplicativo de camada intermediária solicita um token de acesso do Azure AD que tem os seguintes parâmetros:  

   * Ponto de extremidade de locatário do Azure AD.
   * URI de recurso dos serviços de mídia.
   * URI de recurso para serviços de mídia REST.
   * Valores de aplicativo do Azure AD: a ID do cliente e o segredo do cliente.
   
   Para obter todos os valores necessários, consulte [acessar a API dos serviços de mídia do Azure com o CLI do Azure](access-api-cli-how-to.md)

2. O token de acesso do AD do Azure é enviado para a camada intermediária.
4. A camada intermediária envia a solicitação para a API REST de mídia do Azure com o token do Azure AD.
5. A camada intermediária retorna os dados dos serviços de mídia.

### <a name="samples"></a>Amostras

Consulte os seguintes exemplos que mostram como se conectar à entidade de serviço do Azure AD:

* [Conectar com REST](media-rest-apis-with-postman.md)  
* [Connect with Java](configure-connect-java-howto.md) (Ligar com Java)
* [Connect with .NET](configure-connect-dotnet-howto.md) (Ligar com .NET)
* [Connect with Node.js](configure-connect-nodejs-howto.md) (Ligar com Node.js)
* [Connect with Python](configure-connect-python-howto.md) (Ligar com Python)

## <a name="naming-conventions"></a>Convenções de nomenclatura

Os nomes de recursos dos Serviços de Multimédia do Azure v3 (por exemplo, Ativos, Tarefas, Transformações) estão sujeitos às restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes de recursos são sempre exclusivos. Desta forma, pode utilizar todas as cadeias de identificador exclusivo (por exemplo, GUIDs) para os nomes de recursos. 

Os nomes de recursos dos Serviços de Multimédia não podem incluir: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", a plica ou qualquer caráter de controlo. Todos os outros carateres são permitidos. O comprimento máximo de um nome de recurso é superior a 260 carateres. 

Para obter mais informações sobre a nomenclatura do Azure Resource Manager, veja: [Requisitos de nomenclatura](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenções de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Nomes de Arquivos/blobs em um ativo

Os nomes de Arquivos/blobs em um ativo devem seguir os [requisitos de nome do blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os requisitos de nome do [NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). O motivo para esses requisitos é que os arquivos podem ser copiados do armazenamento de BLOBs para um disco NTFS local para processamento.

## <a name="long-running-operations"></a>Operações de execução longa

As operações marcadas com `x-ms-long-running-operation` nos [arquivos Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) dos serviços de mídia do Azure são operações de longa execução. 

Para obter detalhes sobre como rastrear operações assíncronas do Azure, consulte [operações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)assíncronas.

Os serviços de mídia têm as seguintes operações de execução longa:

* [Criar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Atualizar eventos ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Excluir evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Iniciar evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Parar LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Use o parâmetro `removeOutputsOnStop` para excluir todas as saídas dinâmicas associadas ao parar o evento.  
* [Redefinir LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Criar LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Excluir LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Criar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Atualizar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Excluir StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Iniciar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Parar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Dimensionar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Após o envio bem-sucedido de uma operação longa, você receberá um ' 202 aceito ' e deverá sondar a conclusão da operação usando a ID da operação retornada.

O artigo [rastrear operações assíncronas do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) explica em detalhes como acompanhar o status das operações assíncronas do Azure por meio de valores retornados na resposta.

Somente uma operação de execução longa tem suporte para um determinado evento ao vivo ou qualquer uma de suas saídas dinâmicas associadas. Depois de iniciada, uma operação de execução longa deve ser concluída antes de iniciar uma operação de execução longa subsequente no mesmo LiveEvent ou em qualquer saída ao vivo associada. Para eventos ao vivo com várias saídas ao vivo, você deve aguardar a conclusão de uma operação de execução longa em uma saída dinâmica antes de disparar uma operação de execução longa em outra saída ao vivo. 

## <a name="sdks"></a>SDKs

> [!NOTE]
> Não há garantia de que os SDKs dos serviços de mídia do Azure v3 sejam thread-safe. Ao desenvolver um aplicativo multi-threaded, você deve adicionar sua própria lógica de sincronização de thread para proteger o cliente ou usar um novo objeto AzureMediaServicesClient por thread. Você também deve ter cuidado com os problemas de multithreading introduzidos por objetos opcionais fornecidos pelo seu código para o cliente (como uma instância HttpClient no .NET).

|SDK|Referência|
|---|---|
|[SDK do .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referência a .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[SDK Java](https://aka.ms/ams-v3-java-sdk)|[Referência a Java](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Referência a Python](https://aka.ms/ams-v3-python-ref)|
|[SDK Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Referência a Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Referência a Go](https://aka.ms/ams-v3-go-ref)|
|[SDK Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Consulte também

- [SDK do .NET do EventGrid que inclui eventos do serviço de mídia](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições dos eventos dos serviços de mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Explorador dos Serviços de Multimédia do Azure

O [Gerenciador de serviços de mídia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) é uma ferramenta disponível para clientes do Windows que desejam aprender sobre os serviços de mídia. O AMSE é um aplicativo WinForms/C# que carrega, baixa, codifica, transmite conteúdo de VOD e ao vivo com os serviços de mídia. A ferramenta AMSE é para clientes que desejam testar os serviços de mídia sem escrever nenhum código. O código AMSE é fornecido como um recurso para os clientes que desejam desenvolver com os serviços de mídia.

AMSE é um projeto de software livre, o suporte é fornecido pela Comunidade (os problemas podem ser relatados para https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, consulte as [perguntas frequentes sobre o código de conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate opencode@microsoft.com com perguntas ou comentários adicionais.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, ordenação, paginação de entidades de serviços de mídia

Consulte [filtragem, ordenação, paginação de entidades dos serviços de mídia do Azure](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Passos seguintes

* [Conectar-se aos serviços de mídia com Java](configure-connect-java-howto.md)
* [Conectar-se aos serviços de mídia com o .NET](configure-connect-dotnet-howto.md)
* [Conectar-se aos serviços de mídia com o Node. js](configure-connect-nodejs-howto.md)
* [Conectar-se aos serviços de mídia com Python](configure-connect-python-howto.md)
