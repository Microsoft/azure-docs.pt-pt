---
title: Visualização de controlo de acesso baseado em funções - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre o controlo de acesso baseado em funções para os Hubs de eventos do Azure.
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
ms.openlocfilehash: 549cfb84ff247295e01c800aa41ba265bb8921c7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540084"
---
# <a name="active-directory-role-based-access-control-preview"></a>Controlo de acesso do Active Directory Directory Role-Based (pré-visualização)

O Microsoft Azure fornece gestão de controlo de acesso integrada para os recursos e aplicações com base no Azure Active Directory (Azure AD). Com o Azure AD, pode optar por gerir contas de utilizador e de aplicações especificamente para seus aplicativos baseados no Azure ou, pode federar sua infraestrutura existente do Active Directory com o Azure AD para toda a empresa single-sign-on que abranja também recursos do Azure e aplicações alojados no Azure. Em seguida, pode atribuir essas identidades de utilizador e da aplicação do Azure AD para as funções globais e específicos do serviço para conceder acesso aos recursos do Azure.

Para os Hubs de eventos do Azure, a gestão de espaços de nomes e todos os recursos relacionados através do portal do Azure e a API de gestão de recursos do Azure já estão protegidas com o *controlo de acesso baseado em funções* modelo (RBAC). RBAC para operações de tempo de execução é uma funcionalidade agora em pré-visualização pública. 

Uma aplicação que utiliza o RBAC do Azure AD não precisa lidar com regras SAS e as chaves ou quaisquer outros tokens de acesso específicos aos Hubs de eventos. A aplicação de cliente interage com o Azure AD para estabelecer um contexto de autenticação e adquire um token de acesso para os Hubs de eventos. Com contas de utilizador de domínio que requerem o início de sessão interativo, o aplicativo nunca processa quaisquer credenciais diretamente.

## <a name="event-hubs-roles-and-permissions"></a>Permissões e funções de Hubs de eventos

Para a pré-visualização pública inicial, pode adicionar apenas contas do Azure AD e principais de serviço para as funções de "Proprietário" ou "Contribuinte" de um espaço de nomes de Hubs de eventos. Esta operação concede o controlo total de identidade ao longo de todas as entidades no espaço de nomes. Operações de gestão que alteram a topologia de espaço de nomes inicialmente são apenas suportadas através para o Azure resource Manager e não através da interface de gestão do REST dos Hubs de eventos nativa. Esse suporte também significa que o cliente do .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objeto não pode ser utilizado com uma conta do Azure AD.  

## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Utilizar os Hubs de eventos com uma conta de utilizador de domínio do Azure AD

A secção seguinte descreve os passos necessários para criar e executar um aplicativo de exemplo que solicita um Azure interativa utilizador do AD para início de sessão, como pode conceder acesso de Hubs de eventos para essa conta de utilizador e como usar essa identidade para acessar os Hubs de eventos. 

Esta introdução descreve um aplicativo de console simples, o [código para o qual é no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Rbac/EventHubsSenderReceiverRbac/)

### <a name="create-an-active-directory-user-account"></a>Criar uma conta de utilizador do Active Directory

Neste primeiro passo é opcional. Cada subscrição do Azure automaticamente é emparelhada com um inquilino do Azure Active Directory e se tiver acesso a uma subscrição do Azure, a sua conta de utilizador já está registrada. Isso significa que pode simplesmente usar sua conta. 

Se pretender continuar a criar uma conta específica para este cenário [siga estes passos](../automation/automation-create-aduser-account.md). Tem de ter permissão para criar contas no inquilino do Azure Active Directory, o que talvez não seja o caso de cenários de empresa maiores.

### <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Em seguida, [criar um espaço de nomes de Hubs de eventos](event-hubs-create.md) das regiões do Azure com suporte de pré-visualização de Hubs de eventos para RBAC: **E.U.A. Leste**, **E.U.A. Leste 2**, ou **Europa Ocidental**. 

Depois de criar o espaço de nomes, navegue até à respetiva **controlo de acesso (IAM)** página no portal e, em seguida, clique em **adicionar atribuição de função** para adicionar a conta de utilizador do Azure AD para a função de proprietário. Se usar sua própria conta de utilizador e criou o espaço de nomes, já está na função de proprietário. Para adicionar uma conta diferente para a função, procure o nome da aplicação web no **adicionar permissões** painel **selecione** campo e, em seguida, clique na entrada. Em seguida, clique em **Guardar**. A conta de utilizador tem agora acesso ao espaço de nomes dos Hubs de eventos e para o hub de eventos que criou anteriormente.
 
### <a name="register-the-application"></a>Registar a aplicação

Antes de poder executar o aplicativo de exemplo, registrá-la no Azure AD e aprovar a solicitação de consentimento que permite que a aplicação a aceder os Hubs de eventos em seu nome. 

Como o aplicativo de exemplo é uma aplicação de consola, tem de registar uma aplicação nativa e adicionar as permissões de API para **eventhub** ao conjunto de "permissões obrigatórias". Aplicativos nativos também tem um **redirect-URI** no Azure AD que serve como um identificador; o URI não precisa de ser um destino de rede. Utilize `https://eventhubs.microsoft.com` para este exemplo, uma vez que o exemplo de código já utiliza esse URI.

Os passos de registo detalhados são explicados em [deste tutorial](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Siga os passos para registar um **nativo** aplicação e, em seguida, siga as instruções de atualização para adicionar o **eventhub** API para as permissões necessárias. À medida que seguir os passos, anote o **TenantId** e o **ApplicationId**, pois irá precisar destes valores para executar a aplicação.

### <a name="run-the-app"></a>Executar a aplicação

Antes de poder executar o exemplo, edite o ficheiro App. config e, dependendo do seu cenário, defina os seguintes valores:

- `tenantId`: Defina como **TenantId** valor.
- `clientId`: Defina como **ApplicationId** valor. 
- `clientSecret`: Se quiser iniciar sessão com o segredo do cliente, crie-o no Azure AD. Além disso, utilize uma aplicação web ou a API em vez de uma aplicação nativa. Além disso, adicione a aplicação em **controlo de acesso (IAM)** no espaço de nomes que criou anteriormente.
- `eventHubNamespaceFQDN`: Definido como o nome DNS completamente qualificado do seu espaço de nomes de Hubs de eventos criado recentemente; Por exemplo, `example.servicebus.windows.net`.
- `eventHubName`: Definido como o nome do hub de eventos que criou.
- O URI de redirecionamento que especificou na sua aplicação nos passos anteriores.
 
Ao executar o aplicativo de console, lhe for pedido para selecionar um cenário. Clique em **início de sessão do utilizador interativo** , escrevendo o respetivo número e pressionar ENTER. O aplicativo exibe uma janela de início de sessão, pede sua permissão aceder aos Hubs de eventos e, em seguida, utiliza o serviço para executar o cenário de envio/receção de mensagens em fila usando a identidade de início de sessão.

A aplicação utiliza `ServiceAudience.EventHubsAudience` como a audiência do token. Quando utilizar outros idiomas ou SDKs onde o público-alvo não está disponível como uma constante, o valor correto a utilizar é `https://eventhubs.azure.net/`.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Os detalhes dos preços dos Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
