---
title: Autenticação de identidade gerida com Diretório Ativo Azure
description: Este artigo fornece informações sobre a autenticação de uma identidade gerida com o Azure Ative Directory para aceder ao Serviço Azure SignalR
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: c561bb507a5178f4a838b370a3da8af9447829f4
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092548"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos Azure SignalR
O Serviço Azure SignalR suporta a autenticação do Azure Ative Directory (Azure AD) com [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md). Identidades geridas para recursos Azure podem autorizar o acesso aos recursos do Serviço Azure SignalR utilizando credenciais AZure AD de aplicações executadas em Máquinas Virtuais Azure (VMs), apps de funções, conjuntos de escala de máquinas virtuais e outros serviços. Ao utilizar identidades geridas para recursos Azure juntamente com a autenticação Azure AD, pode evitar armazenar credenciais com as suas aplicações que funcionam na nuvem.

Este artigo mostra como autorizar o acesso a um Serviço Azure SignalR utilizando uma identidade gerida a partir de um Azure VM.

## <a name="enable-managed-identities-on-a-vm"></a>Permitir identidades geridas num VM
Antes de utilizar identidades geridas para a Azure Resources para autorizar os recursos do Serviço Azure SIgnalR a partir do seu VM, deve primeiro ativar identidades geridas para recursos Azure no VM. Para aprender como permitir identidades geridas para recursos Azure, consulte um destes artigos:

- [Portal do Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerida em Azure AD
Para autorizar um pedido ao Serviço Azure SignalR a partir de uma identidade gerida na sua aplicação, configurar primeiro as definições de controlo de acesso baseado em funções (RBAC) para essa identidade gerida. O Serviço Azure SignalR define funções RBAC que englobam permissões para aquisição `AccessKey` ou `ClientToken` . Quando a função RBAC é atribuída a uma identidade gerida, a identidade gerida é concedida acesso ao Serviço Azure SignalR no âmbito apropriado.

Para obter mais informações sobre a atribuição de funções de RBAC, consulte [Authenticate com Azure Ative Directory para aceder aos recursos do Serviço Azure SignalR](authorize-access-azure-active-directory.md).

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>Ligue-se ao Serviço Azure SignalR com identidades geridas
Para ligar ao Serviço Azure SignalR com identidades geridas, é necessário atribuir à identidade o papel e o âmbito adequado. O procedimento nesta secção utiliza uma aplicação simples que funciona sob uma identidade gerida e acede aos recursos do Serviço Azure SignalR.

Aqui estamos a usar uma amostra de recurso de máquina virtual Azure.

1. Vá a **Definições** e selecione **Identidade**. 
1. Selecione o **Estado** a ser **ligado**. 
1. Selecione **Guardar** para guardar a definição. 

    ![Identidade gerida para uma máquina virtual](./media/authenticate/identity-virtual-machine.png)

Uma vez ativada esta definição, é criada uma nova identidade de serviço no seu Azure Ative Directory (Azure AD) e configurada no anfitrião do Serviço de Aplicações.

Agora, atribua esta identidade de serviço a um papel no âmbito exigido nos seus recursos de Serviço Azure SignalR.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Atribuir funções Azure usando o portal Azure  
Para saber mais sobre a gestão do acesso aos recursos do Azure utilizando o Azure RBAC e o portal Azure, consulte [este artigo](..//role-based-access-control/role-assignments-portal.md). 

Depois de ter determinado a possibilidade adequada para uma atribuição de funções, navegue para esse recurso no portal Azure. Mostrar as definições de controlo de acesso (IAM) para o recurso e seguir estas instruções para gerir as atribuições de funções:

1. No [portal Azure,](https://portal.azure.com/)navegue para o seu recurso SignalR.
1. Selecione **Access Control (IAM)** para exibir as definições de controlo de acesso para o Azure SignalR. 
1. Selecione o **separador funções** para ver a lista de atribuições de funções. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **Adicionar a atribuição de função**. 

    ![Adicione botão na barra de ferramentas](./media/authenticate/role-assignments-add-button.png)

1. Na página **de atribuição de funções Adicionar,** faça os seguintes passos:
    1. Selecione a **função Azure SignalR** que pretende atribuir. 
    1. Procure localizar o **principal de segurança** (utilizador, grupo, principal serviço) ao qual pretende atribuir a função.
    1. **Selecione Guardar** para guardar a atribuição de funções. 

        ![Atribuir papel a uma aplicação](./media/authenticate/assign-role-to-application.png)

    1. A identidade a quem atribuiu o papel aparece listada nessa função. Por exemplo, a imagem a seguir mostra essa aplicação `signalr-dev` e `signalr-service` está na função SignalR App Server. 
        
        ![Lista de atribuição de funções](./media/authenticate/role-assignment-list.png)

Pode seguir passos semelhantes para atribuir uma função a um grupo de recursos ou subscrição. Uma vez definido o papel e o seu âmbito, pode testar este comportamento com amostras [neste local do GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)

## <a name="samples-code-while-configuring-your-app-server"></a>Código de amostras ao configurar o servidor de aplicações

Adicione as seguintes opções `AddAzureSignalR` quando:

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre o RBAC, veja [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)](../role-based-access-control/overview.md)?
- Para aprender a atribuir e gerir atribuições de funções da Azure PowerShell, Azure CLI ou da REST API, consulte estes artigos:
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gerir o controlo de acesso baseado em funções (RBAC) com o Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gerir o controlo de acesso baseado em funções (RBAC) com modelos de gestor de recursos Azure](../role-based-access-control/role-assignments-template.md)

Consulte os seguintes artigos relacionados:
- [Autenticar uma aplicação com diretório ativo Azure para aceder ao Serviço Azure SignalR](authenticate-application.md)
- [Autorizar o acesso ao Serviço Azure SignalR utilizando o Diretório Ativo Azure](authorize-access-azure-active-directory.md)