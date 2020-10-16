---
title: Criar um painel de portais Azure com PowerShell
description: Saiba como criar um dashboard no portal Azure utilizando a Azure PowerShell.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.author: mblythe
ms.date: 07/24/2020
ms.openlocfilehash: de0394f9b3254931537441d9f44606d16392a62d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073891"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Quickstart: Criar um painel de porta-porta Azure com PowerShell

Um dashboard no portal Azure é uma visão focada e organizada dos seus recursos em nuvem. Este artigo centra-se no processo de utilização do módulo Az.Portal PowerShell para criar um dashboard.
O painel de instrumentos mostra o desempenho de uma máquina virtual (VM), bem como algumas informações estáticas e ligações.

## <a name="requirements"></a>Requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo **Az.Portal** PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o `Install-Module` cmdlet. Uma vez que este módulo PowerShell fica geralmente disponível, torna-se parte de futuros lançamentos de módulos Az PowerShell e disponível de forma nativa a partir de Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Escolha uma subscrição específica do Azure

Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione uma subscrição específica utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definir variáveis

Vai usar várias informações repetidamente. Criar variáveis para armazenar a informação.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo a seguir cria um grupo de recursos com base no nome da `$resourceGroupName` variável na região especificada na `$location` variável.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

O painel de instrumentos que cria na parte seguinte deste quickstart requer um VM existente. Crie um VM seguindo estes passos.

Guarde as credenciais de login para o VM numa variável. A senha deve ser complexa. Este é um novo nome de utilizador e senha; não é, por exemplo, a conta que usa para entrar no Azure. Para mais informações, consulte [os requisitos do nome de utilizador](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) e os [requisitos de senha.](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

```azurepowershell-interactive
$Cred = Get-Credential
```

Criar o VM.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

A implantação em VM começa agora e normalmente demora alguns minutos a ser concluída. Após a colocação concluída, passe para a secção seguinte.

## <a name="download-the-dashboard-template"></a>Descarregue o modelo do painel de instrumentos

Uma vez que os dashboards Azure são recursos, podem ser representados como JSON. O seguinte código descarrega uma representação JSON de um painel de amostras. Para mais informações, consulte [a estrutura dos Dashboards Azure.](./azure-portal-dashboards-structure.md)

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>Personalizar o modelo

Personalize o modelo descarregado executando o seguinte código.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

Para obter mais informações, consulte a [referência do modelo do portal do Microsoft.](/azure/templates/microsoft.portal/dashboards)

## <a name="deploy-the-dashboard-template"></a>Implemente o modelo do painel de instrumentos

Pode utilizar o `New-AzPortalDashboard` cmdlet que faz parte do módulo Az.Portal para implantar o modelo diretamente a partir do PowerShell.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>Rever os recursos implantados

Verifique se o painel foi criado com sucesso.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

Verifique se pode ver dados sobre o VM dentro do portal Azure.

1. No portal do Azure, selecione **Dashboard**.

   ![Navegação do portal Azure para painel](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. Na página do painel de instrumentos, selecione **Simple VM Dashboard**.

   ![Navegue para o simples painel VM](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Reveja o dashboard. Pode ver que alguns dos conteúdos são estáticos, mas também existem gráficos que mostram o desempenho do VM.

   ![Rever simples painel VM](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Limpar recursos

Para remover o VM e o painel de instrumentos associado, elimine o grupo de recursos que os contém.

> [!CAUTION]
> O exemplo a seguir elimina o grupo de recursos especificado e todos os recursos contidos no mesmo.
> Se existirem recursos fora do âmbito deste artigo no grupo de recursos especificados, também serão eliminados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os cmdlets contidos no módulo Az.Portal PowerShell, consulte:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: Cmdlets do Painel de Instrumentos do Portal](/powershell/module/Az.Portal/)