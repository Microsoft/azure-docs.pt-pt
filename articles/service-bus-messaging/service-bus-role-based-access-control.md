---
title: Pré-visualização de controlo de acesso de Service Bus Role-Based (RBAC) do Azure | Documentos da Microsoft
description: Controlo de acesso baseado em funções do Service Bus do Azure
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
ms.date: 09/19/2018
ms.author: aschhab
ms.openlocfilehash: 6f5390162ce56a0e77ef41740d7e88f3546c5530
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444739"
---
# <a name="active-directory-role-based-access-control-preview"></a>Controlo de acesso do Active Directory Directory Role-Based (pré-visualização)

O Microsoft Azure fornece gestão de controlo de acesso integrada para os recursos e aplicações com base no Azure Active Directory (Azure AD). Com o Azure AD, pode optar por gerir contas de utilizador e aplicações baseadas em aplicações especificamente para o Azure, ou pode federar sua infraestrutura existente do Active Directory com o Azure AD para toda a empresa single-sign-on que também se estende por recursos do Azure e aplicações alojados no Azure. Em seguida, pode atribuir essas identidades de utilizador e da aplicação do Azure AD para as funções globais e específicos do serviço para conceder acesso aos recursos do Azure.

Para o Azure Service Bus, a gestão de espaços de nomes e todos os recursos relacionados através do portal do Azure e a API de gestão de recursos do Azure já estão protegidas com o *controlo de acesso baseado em funções* modelo (RBAC). RBAC para operações de tempo de execução é uma funcionalidade agora em pré-visualização pública.

Uma aplicação que utiliza o RBAC do Azure AD não precisa lidar com regras SAS e as chaves ou quaisquer outros tokens de acesso específicos para o Service Bus. A aplicação de cliente interage com o Azure AD para estabelecer um contexto de autenticação e adquire um token de acesso para o Service Bus. Com contas de utilizador de domínio que requerem o início de sessão interativo, o aplicativo nunca processa quaisquer credenciais diretamente.

## <a name="service-bus-roles-and-permissions"></a>Permissões e funções do Service Bus

O Azure disponibiliza o abaixo funções RBAC incorporadas para autorizar o acesso a um espaço de nomes do Service Bus:

* [Proprietário de dados de barramento de serviço do Azure (pré-visualização)](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner-preview): Permite o acesso a dados para o espaço de nomes do Service Bus e respetivas entidades (filas, tópicos, subscrições e filtros)

>[!IMPORTANT]
> Suportámos anteriormente a adição de uma identidade gerida para o **"Proprietário"** ou **"Contribuinte"** função.
>
> No entanto, de acesso a dados privilégios para **"Proprietário"** e **"Contribuinte"** função já não será cumprida. Se estava a utilizar o **"Proprietário"** ou **"Contribuinte"** função, então eles terão de ser adaptado para utilizar o **"Do Azure Service Bus proprietário dos dados (pré-visualização)"** função.

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Utilizar o Service Bus com uma conta de utilizador de domínio do Azure AD

A secção seguinte descreve os passos necessários para criar e executar um aplicativo de exemplo que solicita um Azure interativa utilizador do AD para iniciar sessão, como pode conceder acesso de Service Bus para essa conta de utilizador e como usar essa identidade para acessar os Hubs de eventos.

Esta introdução descreve um aplicativo de console simples, o [código para o qual é no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Criar uma conta de utilizador do Active Directory

Neste primeiro passo é opcional. Cada subscrição do Azure automaticamente é emparelhada com um inquilino do Azure Active Directory e se tiver acesso a uma subscrição do Azure, a sua conta de utilizador já está registrada. Isso significa que pode simplesmente usar sua conta.

Se pretender continuar a criar uma conta específica para este cenário [siga estes passos](../automation/automation-create-aduser-account.md). Tem de ter permissão para criar contas no inquilino do Azure Active Directory, o que talvez não seja o caso de cenários de empresa maiores.

### <a name="create-a-service-bus-namespace"></a>Criar um espaço de nomes do Service Bus

Em seguida, [criar um espaço de nomes de mensagens do Service Bus](service-bus-create-namespace-portal.md).

Depois de criar o espaço de nomes, navegue até à respetiva **controlo de acesso (IAM)** página no portal e, em seguida, clique em **adicionar atribuição de função** para adicionar a conta de utilizador do Azure AD para a função de proprietário. Se usar sua própria conta de utilizador e criou o espaço de nomes, já está na função de proprietário. Para adicionar uma conta diferente para a função, procure o nome da aplicação web no **adicionar permissões** painel **selecione** campo e, em seguida, clique na entrada. Em seguida, clique em **Guardar**.

A conta de utilizador tem agora acesso ao espaço de nomes do Service Bus e para a fila que criou anteriormente.

### <a name="register-the-application"></a>Registar a aplicação

Antes de poder executar o aplicativo de exemplo, registrá-la no Azure AD e aprovar a solicitação de consentimento que permite que a aplicação para aceder ao Azure Service Bus em seu nome.

Como o aplicativo de exemplo é uma aplicação de consola, tem de registar uma aplicação nativa e adicionar as permissões de API para **servicebus** ao conjunto de "permissões obrigatórias". Aplicativos nativos também tem um **redirect-URI** no Azure AD que serve como um identificador; o URI não precisa de ser um destino de rede. Utilize `https://servicebus.microsoft.com` para este exemplo, uma vez que o exemplo de código já utiliza esse URI.

Os passos de registo detalhados são explicados em [deste tutorial](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Siga os passos para registar um **nativo** aplicação e, em seguida, siga as instruções de atualização para adicionar o **servicebus** API para as permissões necessárias. À medida que seguir os passos, anote o **TenantId** e o **ApplicationId**, pois irá precisar destes valores para executar a aplicação.

### <a name="run-the-app"></a>Executar a aplicação

Antes de poder executar o exemplo, edite o ficheiro App. config e, dependendo do seu cenário, defina os seguintes valores:

- `tenantId`: Defina como **TenantId** valor.
- `clientId`: Defina como **ApplicationId** valor.
- `clientSecret`: Se quiser iniciar sessão com o segredo do cliente, crie-o no Azure AD. Além disso, utilize uma aplicação web ou a API em vez de uma aplicação nativa. Além disso, adicione a aplicação em **controlo de acesso (IAM)** no espaço de nomes que criou anteriormente.
- `serviceBusNamespaceFQDN`: Definido como o nome DNS completo do seu espaço de nomes do Service Bus recentemente criado; Por exemplo, `example.servicebus.windows.net`.
- `queueName`: Defina o nome da fila que criou.
- O URI de redirecionamento que especificou na sua aplicação nos passos anteriores.

Ao executar o aplicativo de console, lhe for pedido para selecionar um cenário. Clique em **início de sessão do utilizador interativo** , escrevendo o respetivo número e pressionar ENTER. O aplicativo exibe uma janela de início de sessão, pede sua permissão aceder ao Service Bus e, em seguida, utiliza o serviço para executar o cenário de envio/receção de mensagens em fila usando a identidade de início de sessão.

## <a name="next-steps"></a>Passos Seguintes

Para mais informações sobre mensagens do Service Bus, consulte os seguintes tópicos.

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
