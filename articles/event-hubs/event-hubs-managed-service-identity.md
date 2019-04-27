---
title: Gerido identidades para recursos do Azure - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre como utilizar identidades geridas para recursos do Azure com os Hubs de eventos do Azure
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 784d8c9280aeff7224f90ecee0b16c9c30381aeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746910"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Identidades geridas para recursos do Azure com os Hubs de eventos

[Gerido identidades para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) é uma funcionalidade de entre o Azure permite-lhe criar uma identidade segura associada à implementação em que o código da aplicação é executado. Em seguida, pode associar essa identidade com funções de controlo de acesso que concedem permissões personalizadas para aceder a recursos específicos do Azure que a aplicação precisa. 

Com identidades geridas, a plataforma do Azure gere esta identidade de tempo de execução. Não é necessário armazenar e proteger chaves de acesso no seu código da aplicação ou a configuração, para a identidade propriamente dita, ou para os recursos que precisa acessar. Uma aplicação de cliente dos Hubs de eventos em execução dentro de um aplicativo de serviço de aplicações do Azure ou numa máquina virtual com identidades geridas para o suporte de recursos do Azure ativado não precisa lidar com regras SAS e as chaves ou quaisquer outros tokens de acesso. A aplicação cliente precisa apenas o endereço de ponto final do espaço de nomes dos Hubs de eventos. Quando se liga a aplicação, os Hubs de eventos vincula o contexto de a identidade gerida para o cliente de uma operação que é mostrado um exemplo deste artigo.

Assim que estiver associado a uma identidade gerida, um cliente dos Hubs de eventos pode fazer operações de contas autorizadas. Autorização é concedida ao associar uma identidade gerida com as funções de Hubs de eventos. 

## <a name="event-hubs-roles-and-permissions"></a>Permissões e funções de Hubs de eventos

Só pode adicionar uma identidade gerida para as funções de "Proprietário" ou "Contribuinte" de um espaço de nomes de Hubs de eventos, que concede o controlo total de identidade em todas as entidades no espaço de nomes. No entanto, o gerenciamento de operações que alteram a topologia de espaço de nomes são inicialmente apenas entanto suportados do Azure Resource Manager. Não é por meio da interface de gestão do REST dos Hubs de eventos nativa. Esse suporte também significa que não é possível utilizar o cliente do .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objeto dentro de uma identidade gerida. 
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Utilizar os Hubs de eventos com identidades geridas para recursos do Azure

A secção seguinte descreve os passos seguintes:

1. Criar e implementar uma aplicação de exemplo que é executado sob uma identidade gerida.
2. Conceda à identidade acesso para um espaço de nomes de Hubs de eventos.
3. Como o aplicativo interage com os hubs de eventos com essa identidade.

Esta introdução descreve uma aplicação web alojada na [App Service do Azure](https://azure.microsoft.com/services/app-service/). Os passos necessários para um aplicativo hospedado por VM são semelhantes.

### <a name="create-an-app-service-web-application"></a>Criar uma aplicação web do serviço de aplicações

A primeira etapa é criar um aplicativo ASP.NET de serviço de aplicações. Se não estiver familiarizado com a forma de fazê-lo no Azure, siga [este guia de procedimentos](../app-service/app-service-web-get-started-dotnet-framework.md). No entanto, em vez de criar uma aplicação de MVC, conforme mostrado no tutorial, crie uma aplicação de Web Forms.

### <a name="set-up-the-managed-identity"></a>Configurar a identidade gerida

Depois de criar a aplicação, navegue para a aplicação web recentemente criada no portal do Azure (também mostrada os procedimentos), em seguida, navegue para o **identidade do serviço gerido** de páginas e ativar a funcionalidade: 

![Página de identidade do serviço gerida](./media/event-hubs-managed-service-identity/msi1.png)
 
Assim que tive ativado a funcionalidade, uma nova identidade de serviço é criada no Azure Active Directory e configurada o Host de serviço de aplicações.

### <a name="create-a-new-event-hubs-namespace"></a>Criar um novo espaço de nomes de Hubs de eventos

Em seguida, [criar um espaço de nomes de Hubs de eventos](event-hubs-create.md) das regiões do Azure que tem suporte de pré-visualização para identidades geridas para recursos do Azure: **E.U.A. Leste**, **E.U.A. Leste 2**, ou **Europa Ocidental**. 

Navegue para o espaço de nomes **controlo de acesso (IAM)** página no portal e, em seguida, clique em **adicionar atribuição de função** para adicionar a identidade gerida para o **proprietário** função. Para tal, procure o nome da aplicação web no **adicionar permissões** painel **selecione** campo e, em seguida, clique na entrada. Em seguida, clique em **Guardar**. A identidade gerida para a aplicação web agora tem acesso ao espaço de nomes dos Hubs de eventos e para o hub de eventos que criou anteriormente. 

### <a name="run-the-app"></a>Executar a aplicação

Agora, modifique a página predefinida do aplicativo ASP.NET que criou. Também pode utilizar o código de aplicativo da web [este repositório de GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Depois de iniciar a aplicação, o browser aponte para EventHubsMSIDemo.aspx. Também pode configurá-lo como a página inicial. O código pode ser encontrado no arquivo EventHubsMSIDemo.aspx.cs. O resultado é um aplicativo web mínima com alguns campos de entrada e com **enviar** e **receber** botões que se ligam aos Hubs de eventos para enviar ou receber eventos. 

Observe como o [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objeto é inicializado. Em vez de utilizar o fornecedor do token de acesso partilhado Token (SAS), o código cria um provedor de token para a identidade gerida com o `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` chamar. Como tal, não há nenhum segredos para guardar e utilizar. O fluxo do contexto de identidade gerida para os Hubs de eventos e o handshake de autorização são manipulados automaticamente pelo fornecedor do token, que é um modelo mais simples do que através da SAS.

Depois de efetuar estas alterações, publicar e executar a aplicação. Pode obter os dados de publicação corretos ao transferir e, em seguida, importar um perfil de publicação no Visual Studio:

![Importar perfil de publicação](./media/event-hubs-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, introduza o nome do espaço de nomes e o nome da entidade que criou, em seguida, clique em **enviar** ou **receber**. 
 
A identidade gerida funciona apenas dentro do ambiente do Azure e apenas no âmbito do implementação de serviço de aplicações em que tiver configurado. Identidades geridas não funcionam com blocos de implementação do serviço de aplicações neste momento.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Os detalhes dos preços dos Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
