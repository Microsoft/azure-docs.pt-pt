---
title: Crie um centro de notificação Azure usando o modelo de Gestor de Recursos Azure
description: Aprenda a criar um hub de notificação Azure utilizando um modelo de Gestor de Recursos Azure.
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 05/15/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: aefccb831fe35898962893a173c5bd1125877def
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743521"
---
# <a name="quickstart-create-a-notification-hub-using-an-azure-resource-manager-template"></a>Quickstart: criar um centro de notificação usando um modelo de Gestor de Recursos Azure

O Azure Notification Hubs fornece um motor push fácil de usar e dimensionado que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle, etc.) a partir de qualquer backend (cloud ou on-premir). Para mais informações sobre o serviço, consulte [o What is Azure Notification Hubs](notification-hubs-push-notification-overview.md).

Este quickstart usa um modelo de Gestor de Recursos Azure para criar um espaço de nome do Azure Notification Hubs, e um centro de notificação chamado "MyHub" dentro desse espaço de nome.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="create-a-notification-hubs-namespace-and-hub"></a>Criar um espaço de nome saque de Centros de Notificação e hub

<!-- The second H2 must start with "Create a". For example,  'Create a Key Vault', 'Create a virtual machine', etc. -->

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-notification-hub/).

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json" range="1-45" highlight="22-40":::

* [Microsoft.NotificationHubs/espaços de nome](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces)
* [Microsoft.NotificationHubs/namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>Implementar o modelo

Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo tem um nome de espaço de nome de Centros de Notificação como parâmetro. O modelo cria então um espaço de nome com esse nome e um centro de notificação chamado **MyHub** dentro desse espaço de nome.

[![Implementar no Azure](./media/create-notification-hub-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Pode utilizar o portal Azure para verificar os recursos implantados, ou utilizar o script Azure CLI ou Azure PowerShell para listar o espaço de nome e hubs de notificação implantados:

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Crie e implante o seu primeiro modelo de Gestor de Recursos Azure](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
