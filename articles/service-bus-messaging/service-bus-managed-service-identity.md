---
title: Identidades gerenciadas para recursos do Azure com o barramento de serviço do Azure | Microsoft Docs
description: Usar identidades gerenciadas para recursos do Azure com o barramento de serviço do Azure
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 139ebe02727bab8cc80f6f0c6bbbd2156f025c21
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70992313"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos do barramento de serviço do Azure
[Gerido identidades para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) é uma funcionalidade de entre o Azure permite-lhe criar uma identidade segura associada à implementação em que o código da aplicação é executado. Em seguida, pode associar essa identidade com funções de controlo de acesso que concedem permissões personalizadas para aceder a recursos específicos do Azure que a aplicação precisa.

Com identidades geridas, a plataforma do Azure gere esta identidade de tempo de execução. Não é necessário armazenar e proteger chaves de acesso no seu código da aplicação ou a configuração, para a identidade propriamente dita, ou para os recursos que precisa acessar. Um aplicativo cliente do barramento de serviço em execução dentro de um aplicativo de serviço Azure App ou em uma máquina virtual com entidades gerenciadas habilitadas para o suporte de recursos do Azure não precisa lidar com regras e chaves de SAS ou quaisquer outros tokens de acesso. O aplicativo cliente precisa apenas do endereço do ponto de extremidade do namespace de mensagens do barramento de serviço. Quando o aplicativo se conecta, o barramento de serviço associa o contexto da entidade gerenciada ao cliente em uma operação que é mostrada em um exemplo mais adiante neste artigo. Quando ele estiver associado a uma identidade gerenciada, o cliente do barramento de serviço poderá realizar todas as operações autorizadas. A autorização é concedida por meio da Associação de uma entidade gerenciada com funções do barramento de serviço. 

## <a name="overview"></a>Descrição geral
Quando uma entidade de segurança (um usuário, grupo ou aplicativo) tenta acessar uma entidade do barramento de serviço, a solicitação deve ser autorizada. Com o Azure AD, o acesso a um recurso é um processo de duas etapas. 

 1. Primeiro, a identidade da entidade de segurança é autenticada e um token OAuth 2,0 é retornado. O nome do recurso para solicitar um token `https://servicebus.azure.net`é.
 1. Em seguida, o token é passado como parte de uma solicitação para o serviço do barramento de serviço para autorizar o acesso ao recurso especificado.

A etapa de autenticação requer que uma solicitação de aplicativo contenha um token de acesso OAuth 2,0 em tempo de execução. Se um aplicativo estiver em execução em uma entidade do Azure, como uma VM do Azure, um conjunto de dimensionamento de máquinas virtuais ou um aplicativo de funções do Azure, ele poderá usar uma identidade gerenciada para acessar os recursos. Para saber como autenticar solicitações feitas por uma identidade gerenciada para o serviço do barramento de serviço, consulte [autenticar o acesso aos recursos do barramento de serviço do Azure com Azure Active Directory e identidades gerenciadas para recursos do Azure](service-bus-managed-service-identity.md). 

A etapa de autorização requer que uma ou mais funções RBAC sejam atribuídas à entidade de segurança. O barramento de serviço do Azure fornece funções RBAC que abrangem conjuntos de permissões para recursos do barramento de serviço. As funções atribuídas a uma entidade de segurança determinam as permissões que o principal terá. Para saber mais sobre como atribuir funções RBAC ao barramento de serviço do Azure, consulte [funções RBAC internas para o barramento de serviço do Azure](#built-in-rbac-roles-for-azure-service-bus). 

Aplicativos nativos e aplicativos Web que fazem solicitações para o barramento de serviço também podem autorizar com o Azure AD. Este artigo mostra como solicitar um token de acesso e usá-lo para autorizar solicitações para recursos do barramento de serviço. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Atribuindo funções RBAC para direitos de acesso
O Azure Active Directory (AD do Azure) autoriza os direitos de acesso a recursos protegidos por meio [do RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md). O barramento de serviço do Azure define um conjunto de funções RBAC internas que abrangem conjuntos comuns de permissões usadas para acessar entidades do barramento de serviço e você também pode definir funções personalizadas para acessar os dados.

Quando uma função RBAC é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível de assinatura, o grupo de recursos ou o namespace do barramento de serviço. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma identidade gerenciada para recursos do Azure.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Funções RBAC internas para o barramento de serviço do Azure
Para o barramento de serviço do Azure, o gerenciamento de namespaces e todos os recursos relacionados por meio do portal do Azure e a API de gerenciamento de recursos do Azure já estão protegidos usando o modelo RBAC ( *controle de acesso baseado em função* ). O Azure fornece as funções RBAC internas abaixo para autorizar o acesso a um namespace do barramento de serviço:

- [Proprietário dos dados do barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Habilita o acesso a dados para o namespace do barramento de serviço e suas entidades (filas, tópicos, assinaturas e filtros)
- [Remetente de dados do barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Use essa função para fornecer acesso de envio ao namespace do barramento de serviço e suas entidades.
- [Receptor de dados do barramento de serviço do Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Use essa função para conceder acesso de recebimento ao namespace do barramento de serviço e suas entidades. 

## <a name="resource-scope"></a>Escopo de recurso 
Antes de atribuir uma função de RBAC a uma entidade de segurança, determine o escopo de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que é sempre melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode fazer o escopo de acesso aos recursos do barramento de serviço, começando com o escopo mais estreito:

- **Fila**, **tópico**ou **assinatura**: A atribuição de função se aplica à entidade de barramento de serviço específica. Atualmente, o portal do Azure não dá suporte à atribuição de usuários/grupos/identidades gerenciadas às funções de RBAC do barramento de serviço no nível da assinatura. 
- **Namespace do barramento de serviço**: A atribuição de função abrange toda a topologia do barramento de serviço no namespace e no grupo de consumidores associado a ela.
- **Grupo de recursos**: A atribuição de função se aplica a todos os recursos do barramento de serviço no grupo de recursos.
- **Subscrição**: A atribuição de função se aplica a todos os recursos do barramento de serviço em todos os grupos de recursos na assinatura.

> [!NOTE]
> Tenha em mente que as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas. 

Para obter mais informações sobre como as funções internas são definidas, consulte [entender as definições de função](../role-based-access-control/role-definitions.md#management-and-data-operations). Para obter informações sobre como criar funções RBAC personalizadas, consulte [criar funções personalizadas para o controle de acesso baseado em função do Azure](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM
Antes de usar identidades gerenciadas para recursos do Azure para autorizar recursos de barramento de serviço de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para recursos do Azure, consulte um destes artigos:

- [Azure portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de cliente Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerenciada no Azure AD
Para autorizar uma solicitação ao serviço de barramento de serviço de uma identidade gerenciada em seu aplicativo, primeiro configure as configurações de RBAC (controle de acesso baseado em função) para essa identidade gerenciada. O barramento de serviço do Azure define funções RBAC que abrangem permissões para enviar e ler do barramento de serviço. Quando a função RBAC é atribuída a uma identidade gerenciada, a identidade gerenciada recebe acesso às entidades do barramento de serviço no escopo apropriado.

Para obter mais informações sobre como atribuir funções RBAC, consulte [autenticar e autorizar com Azure Active Directory para acessar os recursos do barramento de serviço](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Usar o barramento de serviço com identidades gerenciadas para recursos do Azure
Para usar o barramento de serviço com identidades gerenciadas, você precisa atribuir a identidade a função e o escopo apropriado. O procedimento nesta seção usa um aplicativo simples que é executado em uma identidade gerenciada e acessa os recursos do barramento de serviço.

Aqui, estamos usando um aplicativo Web de exemplo hospedado no [serviço Azure app](https://azure.microsoft.com/services/app-service/). Para obter as instruções passo a passo para criar um aplicativo Web, consulte [criar um ASP.NET Core aplicativo Web no Azure](../app-service/app-service-web-get-started-dotnet.md)

Depois que o aplicativo for criado, siga estas etapas: 

1. Vá para **configurações** e selecione **identidade**. 
1. Selecione o **status** a ser **ativado**. 
1. Selecione **Guardar** para guardar a definição. 

    ![Identidade gerenciada para um aplicativo Web](./media/service-bus-managed-service-identity/identity-web-app.png)

Depois de habilitar essa configuração, uma nova identidade de serviço será criada em seu Azure Active Directory (AD do Azure) e configurada no host do serviço de aplicativo.

Agora, atribua essa identidade de serviço a uma função no escopo necessário nos recursos do barramento de serviço.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Para atribuir funções de RBAC usando o portal do Azure
Para atribuir uma função a um namespace do barramento de serviço, navegue até o namespace na portal do Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições de função:

> [!NOTE]
> As etapas a seguir atribui uma função de identidade de serviço aos namespaces do barramento de serviço. Você pode seguir as mesmas etapas para atribuir uma função em outros escopos com suporte (grupo de recursos e assinatura). 
> 
> [Crie um namespace de mensagens do barramento de serviço](service-bus-create-namespace-portal.md) se você não tiver um. 

1. Na portal do Azure, navegue até o namespace do barramento de serviço e exiba a **visão geral** do namespace. 
1. Selecione **controle de acesso (iam)** no menu à esquerda para exibir as configurações de controle de acesso para o namespace do barramento de serviço.
1.  Selecione a guia **atribuições de função** para ver a lista de atribuições de função.
3.  Selecione **Adicionar** para adicionar uma nova função.
4.  Na página **Adicionar atribuição de função** , selecione as funções do barramento de serviço do Azure que você deseja atribuir. Em seguida, pesquise para localizar a identidade do serviço que você registrou para atribuir a função.
    
    ![Página Adicionar atribuição de função](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Selecione **Guardar**. A identidade para a qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que a identidade do serviço tem proprietário de dados do barramento de serviço do Azure.
    
    ![Identidade atribuída a uma função](./media/service-bus-managed-service-identity/role-assigned.png)

Depois de atribuir a função, o aplicativo Web terá acesso às entidades do barramento de serviço sob o escopo definido. 

### <a name="run-the-app"></a>Executar a aplicação

Agora, modifique a página padrão do aplicativo ASP.NET que você criou. Você pode usar o código do aplicativo Web deste [repositório GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

A página default. aspx é sua página de aterrissagem. O código pode ser encontrado no arquivo Default.aspx.cs. O resultado é um aplicativo Web mínimo com alguns campos de entrada e com os botões **Enviar** e **receber** que se conectam ao barramento de serviço para enviar ou receber mensagens.

Observe como o [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objeto é inicializado. Em vez de utilizar o fornecedor do token de acesso partilhado Token (SAS), o código cria um provedor de token para a identidade gerida com o `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` chamar. Como tal, não há segredos para manter e usar. O fluxo do contexto de identidade gerenciada para o barramento de serviço e o handshake de autorização são manipulados automaticamente pelo provedor de token. É um modelo mais simples do que usar a SAS.

Depois de efetuar estas alterações, publicar e executar a aplicação. Você pode obter os dados de publicação corretos facilmente baixando e importando um perfil de publicação no Visual Studio:

![Obter perfil de publicação](./media/service-bus-managed-service-identity/msi3.png)
 
Para enviar ou receber mensagens, insira o nome do namespace e o nome da entidade que você criou. Em seguida, clique em **Enviar** ou **receber**.


> [!NOTE]
> - A identidade gerenciada funciona apenas dentro do ambiente do Azure, em serviços de aplicativos, em VMs do Azure e em conjuntos de dimensionamento. Para aplicativos .NET, a biblioteca Microsoft. Azure. Services. AppAuthentication, que é usada pelo pacote NuGet do barramento de serviço, fornece uma abstração sobre esse protocolo e dá suporte a uma experiência de desenvolvimento local. Essa biblioteca também permite testar seu código localmente em seu computador de desenvolvimento, usando sua conta de usuário do Visual Studio, CLI do Azure 2,0 ou Active Directory autenticação integrada. Para obter mais informações sobre opções de desenvolvimento local com essa biblioteca, consulte [Autenticação serviço a serviço para Azure Key Vault usando o .net](../key-vault/service-to-service-authentication.md).  
> 
> - Atualmente, as identidades gerenciadas não funcionam com os slots de implantação do serviço de aplicativo.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as mensagens do barramento de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
