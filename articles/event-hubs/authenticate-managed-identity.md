---
title: Autenticação de uma identidade gerenciada com o Azure Active Directory
description: Este artigo fornece informações sobre como autenticar uma identidade gerenciada com Azure Active Directory para acessar os recursos dos hubs de eventos do Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992838"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos de hubs de eventos
Os hubs de eventos do Azure dão suporte à autenticação Azure Active Directory (Azure AD) com [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). Identidades gerenciadas para recursos do Azure podem autorizar o acesso a recursos de hubs de eventos usando as credenciais do Azure AD de aplicativos em execução em VMs (máquinas virtuais) do Azure, aplicativos de funções, conjuntos de dimensionamento de máquinas virtuais e outros serviços. Usando identidades gerenciadas para recursos do Azure junto com a autenticação do Azure AD, você pode evitar o armazenamento de credenciais com seus aplicativos que são executados na nuvem.

Este artigo mostra como autorizar o acesso a um hub de eventos usando uma identidade gerenciada de uma VM do Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM
Antes de poder usar identidades gerenciadas para recursos do Azure para autorizar recursos de hubs de eventos de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para recursos do Azure, consulte um destes artigos:

- [Azure portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de cliente Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerenciada no Azure AD
Para autorizar uma solicitação ao serviço de hubs de eventos de uma identidade gerenciada em seu aplicativo, primeiro configure as configurações de RBAC (controle de acesso baseado em função) para essa identidade gerenciada. Os hubs de eventos do Azure definem funções RBAC que abrangem permissões para enviar e ler de hubs de eventos. Quando a função RBAC é atribuída a uma identidade gerenciada, a identidade gerenciada recebe acesso aos dados dos hubs de eventos no escopo apropriado.

Para obter mais informações sobre como atribuir funções RBAC, consulte [autenticar com Azure Active Directory para acessar os recursos dos hubs de eventos](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Usar hubs de eventos com identidades gerenciadas
Para usar os hubs de eventos com identidades gerenciadas, você precisa atribuir a identidade a função e o escopo apropriado. O procedimento nesta seção usa um aplicativo simples que é executado sob uma identidade gerenciada e acessa recursos de hubs de eventos.

Aqui, estamos usando um aplicativo Web de exemplo hospedado no [serviço Azure app](https://azure.microsoft.com/services/app-service/). Para obter as instruções passo a passo para criar um aplicativo Web, consulte [criar um ASP.NET Core aplicativo Web no Azure](../app-service/app-service-web-get-started-dotnet.md)

Depois que o aplicativo for criado, siga estas etapas: 

1. Vá para **configurações** e selecione **identidade**. 
1. Selecione o **status** a ser **ativado**. 
1. Selecione **Guardar** para guardar a definição. 

    ![Identidade gerenciada para um aplicativo Web](./media/authenticate-managed-identity/identity-web-app.png)

Depois de habilitar essa configuração, uma nova identidade de serviço será criada em seu Azure Active Directory (AD do Azure) e configurada no host do serviço de aplicativo.

Agora, atribua essa identidade de serviço a uma função no escopo necessário em seus recursos de hubs de eventos.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Para atribuir funções de RBAC usando o portal do Azure
Para atribuir uma função aos recursos dos hubs de eventos, navegue até esse recurso na portal do Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições de função:

> [!NOTE]
> As etapas a seguir atribui uma função de identidade de serviço aos seus namespaces de hubs de eventos. Você pode seguir as mesmas etapas para atribuir uma função com escopo a qualquer recurso de hubs de eventos. 

1. Na portal do Azure, navegue até o namespace de seus hubs de eventos e exiba a **visão geral** do namespace. 
1. Selecione **controle de acesso (iam)** no menu à esquerda para exibir as configurações de controle de acesso para o Hub de eventos.
1.  Selecione a guia atribuições de **função** para ver a lista de atribuições de função.
3.  Selecione **Adicionar** para adicionar uma nova função.
4.  Na página **Adicionar atribuição de função** , selecione as funções de hubs de eventos que você deseja atribuir. Em seguida, pesquise para localizar a identidade do serviço que você registrou para atribuir a função.
    
    ![Página Adicionar atribuição de função](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Selecione **Guardar**. A identidade para a qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra que a identidade de serviço tem proprietário de dados de hubs de eventos.
    
    ![Identidade atribuída a uma função](./media/authenticate-managed-identity/role-assigned.png)

Depois de atribuir a função, o aplicativo Web terá acesso aos recursos dos hubs de eventos no escopo definido. 

### <a name="test-the-web-application"></a>Testar o aplicativo Web
Agora você pode iniciar o aplicativo Web e apontar seu navegador para a página aspx de exemplo. Você pode encontrar o aplicativo Web de exemplo que envia e recebe dados de recursos de hubs de eventos no [repositório GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Instale o pacote mais recente do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)e comece a enviar e receber dados de hubs de eventos usando o EventHubClient, conforme mostrado no código a seguir: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>Passos Seguintes
- Baixe o [exemplo](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp) do github.
- Consulte o artigo a seguir para saber mais sobre identidades gerenciadas para recursos do Azure: [O que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Consulte os seguintes artigos relacionados:
    - [Autenticar solicitações para hubs de eventos do Azure por meio de um aplicativo usando Azure Active Directory](authenticate-application.md)
    - [Autenticar solicitações para hubs de eventos do Azure usando assinaturas de acesso compartilhado](authenticate-shared-access-signature.md)
    - [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizar o acesso a recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)