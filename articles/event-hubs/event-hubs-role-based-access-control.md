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
ms.date: 05/21/2019
ms.author: shvija
ms.openlocfilehash: 117b7d4adb508628ee768bb9531d0bbc52f61121
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816070"
---
# <a name="active-directory-role-based-access-control-preview"></a>Controlo de acesso do Active Directory Directory Role-Based (pré-visualização)

O Microsoft Azure fornece gestão de controlo de acesso integrada para os recursos e aplicações com base no Azure Active Directory (Azure AD). Com o Azure AD, pode optar por gerir contas de utilizador e de aplicações especificamente para seus aplicativos baseados no Azure ou, pode federar sua infraestrutura existente do Active Directory com o Azure AD para toda a empresa single-sign-on que abranja também recursos do Azure e aplicações alojados no Azure. Em seguida, pode atribuir essas identidades de utilizador e da aplicação do Azure AD para as funções globais e específicos do serviço para conceder acesso aos recursos do Azure.

Para os Hubs de eventos do Azure, a gestão de espaços de nomes e todos os recursos relacionados através do portal do Azure e a API de gestão de recursos do Azure já estão protegidas com o *controlo de acesso baseado em funções* modelo (RBAC). RBAC para operações de tempo de execução é uma funcionalidade agora em pré-visualização pública. 

Uma aplicação que utiliza o RBAC do Azure AD não precisa lidar com regras SAS e as chaves ou quaisquer outros tokens de acesso específicos aos Hubs de eventos. A aplicação de cliente interage com o Azure AD para estabelecer um contexto de autenticação e adquire um token de acesso para os Hubs de eventos. Com contas de utilizador de domínio que requerem o início de sessão interativo, o aplicativo nunca processa quaisquer credenciais diretamente.

## <a name="event-hubs-roles-and-permissions"></a>Permissões e funções de Hubs de eventos
O Azure fornece as seguintes funções RBAC internas para autorizar o acesso a um namespace de hubs de eventos:

A função de [proprietário de dados de hubs de eventos do Azure (versão prévia)](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner-preview) habilita o acesso a dados para um namespace de hubs de eventos e suas entidades (filas, tópicos, assinaturas e filtros)

>[!IMPORTANT]
> Anteriormente, damos suporte à adição de identidade gerenciada à função de **proprietário** ou **colaborador** . No entanto, os privilégios de acesso a dados para a função de **proprietário** e **colaborador** não são mais respeitados. Se você estiver usando a função **proprietário** ou **colaborador** , alterne para usando a função proprietário de dados dos **hubs de eventos do Azure (versão prévia)** .


## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Utilizar os Hubs de eventos com uma conta de utilizador de domínio do Azure AD

A secção seguinte descreve os passos necessários para criar e executar um aplicativo de exemplo que solicita um Azure interativa utilizador do AD para início de sessão, como pode conceder acesso de Hubs de eventos para essa conta de utilizador e como usar essa identidade para acessar os Hubs de eventos. 

Esta introdução descreve um aplicativo de console simples, o [código para o qual está no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

### <a name="create-an-active-directory-user-account"></a>Criar uma conta de utilizador do Active Directory

Neste primeiro passo é opcional. Cada subscrição do Azure automaticamente é emparelhada com um inquilino do Azure Active Directory e se tiver acesso a uma subscrição do Azure, a sua conta de utilizador já está registrada. Isso significa que pode simplesmente usar sua conta. 

Se pretender continuar a criar uma conta específica para este cenário [siga estes passos](../automation/automation-create-aduser-account.md). Tem de ter permissão para criar contas no inquilino do Azure Active Directory, o que talvez não seja o caso de cenários de empresa maiores.

### <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Em seguida, [crie um namespace de hubs de eventos](event-hubs-create.md). 

Depois de criar o espaço de nomes, navegue até à respetiva **controlo de acesso (IAM)** página no portal e, em seguida, clique em **adicionar atribuição de função** para adicionar a conta de utilizador do Azure AD para a função de proprietário. Se usar sua própria conta de utilizador e criou o espaço de nomes, já está na função de proprietário. Para adicionar uma conta diferente para a função, procure o nome da aplicação web no **adicionar permissões** painel **selecione** campo e, em seguida, clique na entrada. Em seguida, clique em **Guardar**. A conta de utilizador tem agora acesso ao espaço de nomes dos Hubs de eventos e para o hub de eventos que criou anteriormente.
 
### <a name="register-the-application"></a>Registar a aplicação

Antes de poder executar o aplicativo de exemplo, registrá-la no Azure AD e aprovar a solicitação de consentimento que permite que a aplicação a aceder os Hubs de eventos em seu nome. 

Como o aplicativo de exemplo é uma aplicação de consola, tem de registar uma aplicação nativa e adicionar as permissões de API para **eventhub** ao conjunto de "permissões obrigatórias". Aplicativos nativos também tem um **redirect-URI** no Azure AD que serve como um identificador; o URI não precisa de ser um destino de rede. Utilize `https://eventhubs.microsoft.com` para este exemplo, uma vez que o exemplo de código já utiliza esse URI.

Os passos de registo detalhados são explicados em [deste tutorial](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Siga os passos para registar um **nativo** aplicação e, em seguida, siga as instruções de atualização para adicionar o **eventhub** API para as permissões necessárias. À medida que seguir os passos, anote o **TenantId** e o **ApplicationId**, pois irá precisar destes valores para executar a aplicação.

### <a name="run-the-app"></a>Executar a aplicação

Antes de poder executar o exemplo, edite o ficheiro App. config e, dependendo do seu cenário, defina os seguintes valores:

- `tenantId`: Defina como valor de tenantid.
- `clientId`: Defina como valor **ApplicationId** . 
- `clientSecret`: Se você quiser entrar usando o segredo do cliente, crie-o no Azure AD. Além disso, utilize uma aplicação web ou a API em vez de uma aplicação nativa. Além disso, adicione a aplicação em **controlo de acesso (IAM)** no espaço de nomes que criou anteriormente.
- `eventHubNamespaceFQDN`: Defina como o nome DNS totalmente qualificado de seu namespace de hubs de eventos recém-criado; por exemplo, `example.servicebus.windows.net`.
- `eventHubName`: Defina como o nome do hub de eventos que você criou.
- O URI de redirecionamento que especificou na sua aplicação nos passos anteriores.
 
Ao executar o aplicativo de console, lhe for pedido para selecionar um cenário. Clique em **início de sessão do utilizador interativo** , escrevendo o respetivo número e pressionar ENTER. O aplicativo exibe uma janela de início de sessão, pede sua permissão aceder aos Hubs de eventos e, em seguida, utiliza o serviço para executar o cenário de envio/receção de mensagens em fila usando a identidade de início de sessão.

O aplicativo usa `ServiceAudience.EventHubsAudience` como o público-alvo do token. Ao usar outros idiomas ou SDKs em que o público-alvo não está disponível como uma constante, o valor correto `https://eventhubs.azure.net/`a ser usado é.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Os detalhes dos preços dos Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)
