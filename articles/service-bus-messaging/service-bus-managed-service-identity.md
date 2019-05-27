---
title: Gerido identidades para recursos do Azure com a pré-visualização do Azure Service Bus | Documentos da Microsoft
description: Utilizar identidades geridas para recursos do Azure com o Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: 8477ff8c8ff0bc1629ff4cdc61f7c28c6eed778c
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978805"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>Identidades geridas para recursos do Azure com o Service Bus 

[Gerido identidades para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) é uma funcionalidade de entre o Azure permite-lhe criar uma identidade segura associada à implementação em que o código da aplicação é executado. Em seguida, pode associar essa identidade com funções de controlo de acesso que concedem permissões personalizadas para aceder a recursos específicos do Azure que a aplicação precisa.

Com identidades geridas, a plataforma do Azure gere esta identidade de tempo de execução. Não é necessário armazenar e proteger chaves de acesso no seu código da aplicação ou a configuração, para a identidade propriamente dita, ou para os recursos que precisa acessar. Um barramento de serviço de aplicação de cliente em execução dentro de um aplicativo de serviço de aplicações do Azure ou numa máquina virtual com ativada gerida entidades para recursos do Azure não tem suporte para lidar com regras SAS e as chaves ou quaisquer outros tokens de acesso. A aplicação cliente precisa apenas o endereço de ponto final do espaço de nomes de mensagens do Service Bus. Quando se liga a aplicação, o Service Bus vincula contexto a entidade gerida para o cliente de uma operação que é mostrado um exemplo deste artigo. Assim que estiver associado a uma identidade gerida, o cliente do Service Bus pode fazer operações de contas autorizadas. Autorização é concedida ao associar uma entidade gerida com as funções do Service Bus. 

## <a name="service-bus-roles-and-permissions"></a>Permissões e funções do Service Bus

Pode adicionar uma identidade gerida para a função "Proprietário de dados do Service Bus" de um espaço de nomes do Service Bus. Concede acesso a identidade, controlo total (para gestão e operações de dados) em todas as entidades no espaço de nomes.

>[!IMPORTANT]
> Suportámos anteriormente a adição de uma identidade gerida para o **"Proprietário"** ou **"Contribuinte"** função.
>
> No entanto, de acesso a dados privilégios para **"Proprietário"** e **"Contribuinte"** função já não será cumprida. Se estava a utilizar o **"Proprietário"** ou **"Contribuinte"** função, então eles terão de ser adaptado para utilizar o **"Proprietário de dados do Service Bus"** função.

Para utilizar a nova função incorporada, conclua os passos - abaixo

1. avance para o [portal do Azure](https://portal.azure.com)
2. Navegue para o espaço de nomes do Service Bus em que tem atualmente a configuração a função "Proprietário" ou "Contribuinte".
3. Clique em "Acesso Control(IAM)" no menu do painel esquerdo.
4. Adicionar uma nova atribuição de função, conforme mostrado a seguir

    ![](./media/service-bus-role-based-access-control/ServiceBus_RBAC_SBDataOwner.png)

5. Prima "Save" para guardar a nova atribuição de função.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Utilizar o Service Bus com identidades geridas para recursos do Azure

A secção seguinte descreve os passos necessários para criar e implementar uma aplicação de exemplo que é executado sob uma identidade gerida, como pode conceder esse acesso de identidade para um espaço de nomes de mensagens do Service Bus e como o aplicativo interage com entidades do Service Bus com o essa identidade.

Esta introdução descreve uma aplicação web alojada na [App Service do Azure](https://azure.microsoft.com/services/app-service/). Os passos necessários para um aplicativo hospedado por VM são semelhantes.

### <a name="create-an-app-service-web-application"></a>Criar uma aplicação web do serviço de aplicações

A primeira etapa é criar um aplicativo ASP.NET de serviço de aplicações. Se não estiver familiarizado com a forma de fazê-lo no Azure, siga [este guia de procedimentos](../app-service/app-service-web-get-started-dotnet-framework.md). No entanto, em vez de criar uma aplicação de MVC, conforme mostrado no tutorial, crie uma aplicação de Web Forms.

### <a name="set-up-the-managed-identity"></a>Configurar a identidade gerida

Depois de criar a aplicação, navegue para a aplicação web recentemente criada no portal do Azure (também mostrada os procedimentos), em seguida, navegue para o **identidade do serviço gerido** de páginas e ativar a funcionalidade: 

![](./media/service-bus-managed-service-identity/msi1.png)

Assim que tive ativado a funcionalidade, uma nova identidade de serviço é criada no Azure Active Directory e configurada o Host de serviço de aplicações.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Criar um novo namespace de mensagens do Service Bus

Em seguida, [criar um espaço de nomes de mensagens do Service Bus](service-bus-create-namespace-portal.md). 

Navegue para o espaço de nomes **controlo de acesso (IAM)** página no portal e, em seguida, clique em **adicionar atribuição de função** para adicionar a identidade gerida para o **proprietário** função. Para tal, procure o nome da aplicação web no **adicionar permissões** painel **selecione** campo e, em seguida, clique na entrada. Em seguida, clique em **Guardar**.

Identidade da aplicação web de gerida tem agora acesso ao espaço de nomes do Service Bus e para a fila que criou anteriormente. 

### <a name="run-the-app"></a>Executar a aplicação

Agora, modifique a página predefinida do aplicativo ASP.NET que criou. Pode usar o código de aplicativo da web [este repositório de GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

A página default. aspx é sua página de destino. O código pode ser encontrado no arquivo Default.aspx.cs. O resultado é um aplicativo web mínima com alguns campos de entrada e com **enviar** e **receber** botões que ligar ao Service Bus para enviar ou receber mensagens.

Observe como o [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objeto é inicializado. Em vez de utilizar o fornecedor do token de acesso partilhado Token (SAS), o código cria um provedor de token para a identidade gerida com o `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` chamar. Como tal, não há nenhum segredos para manter e utilizar. O fluxo do contexto de identidade gerida do Service Bus e o handshake de autorização são manipulados automaticamente pelo fornecedor do token. É um modelo mais simples do que através da SAS.

Depois de efetuar estas alterações, publicar e executar a aplicação. Pode obter facilmente os dados de publicação corretos ao transferir e, em seguida, importar um perfil de publicação no Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, introduza o nome do espaço de nomes e o nome da entidade que criou. Em seguida, clique em **enviar** ou **receber**.


> [!NOTE]
> - A identidade gerida funciona apenas dentro do ambiente do Azure, nos serviços de aplicações, as VMs do Azure, e os conjuntos de dimensionamento. Para aplicativos .NET, a biblioteca de Microsoft.Azure.Services.AppAuthentication, que é utilizada pelo pacote NuGet do Service Bus, fornece uma abstração sobre este protocolo e oferece suporte a uma experiência de desenvolvimento local. Esta biblioteca também permite-lhe testar o seu código localmente no computador de desenvolvimento, com a sua conta de utilizador do Visual Studio, a CLI 2.0 do Azure ou a autenticação integrada do Active Directory. Para obter mais informações sobre as opções de desenvolvimento local com esta biblioteca, consulte [autenticação de serviço a serviço para o Azure Key Vault com o .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Atualmente, identidades geridas não funcionam com blocos de implementação do serviço de aplicações.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
