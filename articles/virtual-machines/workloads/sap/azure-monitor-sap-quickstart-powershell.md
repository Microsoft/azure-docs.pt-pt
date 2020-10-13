---
title: Implementar monitor de Azure para soluções SAP com Azure PowerShell
description: Implementar monitor de Azure para soluções SAP com Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines
ms.devlang: azurepowershell
ms.date: 09/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f0a81d6c1849de52bd3103005afaa9ccd01a517a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961158"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Quickstart: Implementar monitor Azure para soluções SAP com Azure PowerShell

Este artigo descreve como pode criar o Azure Monitor para recursos SAP Solutions utilizando o módulo [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell.

> [!CAUTION]
> O Azure Monitor for SAP Solutions encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço. Não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps). Se optar por utilizar a Cloud Shell, consulte [a visão geral da Azure Cloud Shell](../../../cloud-shell/overview.md) para obter mais informações.

> [!IMPORTANT]
> Enquanto o módulo **Az.HanaOnAzure** PowerShell estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Uma vez que este módulo PowerShell fica geralmente disponível, torna-se parte de futuros lançamentos de módulos Az PowerShell e disponível de forma nativa a partir de Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione uma subscrição específica utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos Azure](../../../azure-resource-manager/management/overview.md) utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo a seguir cria um grupo de recursos com o nome especificado e no local especificado.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>Monitor SAP

Para criar um monitor SAP, utilize o [cmdlet New-AzSapMonitor.](/powershell/module/az.hanaonazure/new-azsapmonitor) O exemplo a seguir cria um monitor SAP para a subscrição especificada, grupo de recursos e nome de recursos.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Para recuperar as propriedades de um monitor SAP, utilize o [cmdlet Get-AzSapMonitor.](/powershell/module/az.hanaonazure/get-azsapmonitor) O exemplo a seguir obtém propriedades de um monitor SAP para a subscrição especificada, grupo de recursos e nome de recursos.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Instância do provedor

Para criar uma instância de fornecedor, utilize o [cmdlet New-AzSapMonitorProviderInstance.](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) O exemplo a seguir cria uma instância de fornecedor para a subscrição especificada, grupo de recursos e nome de recursos.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Para obter propriedades de uma instância do fornecedor, utilize o [cmdlet Get-AzSapMonitorProviderInstance.](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) O exemplo a seguir obtém propriedades de uma instância de fornecedor para a subscrição especificada, grupo de recursos, nome SapMonitor e nome de recurso.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Limpar recursos

Se os recursos criados neste artigo não forem necessários, pode eliminá-los executando os seguintes exemplos.

### <a name="delete-the-provider-instance"></a>Eliminar a instância do fornecedor

Para remover uma instância do fornecedor, utilize o [cmdlet Remove-AzSapMonitorProviderInstance.](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) O exemplo a seguir elimina uma instância do fornecedor para a subscrição especificada, grupo de recursos, nome SapMonitor e nome de recurso.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Eliminar o monitor SAP

Para remover um monitor SAP, utilize o [cmdlet Remove-AzSapMonitor.](/powershell/module/az.hanaonazure/remove-azsapmonitor) O exemplo a seguir elimina um monitor SAP para a subscrição especificada, grupo de recursos e nome do monitor.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

> [!CAUTION]
> O exemplo a seguir elimina o grupo de recursos especificado e todos os recursos contidos no mesmo.
> Se existirem recursos fora do âmbito deste artigo no grupo de recursos especificados, também serão eliminados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Monitor para Soluções SAP.](azure-monitor-overview.md)