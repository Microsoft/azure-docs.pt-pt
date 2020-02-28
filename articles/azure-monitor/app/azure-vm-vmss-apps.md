---
title: Monitor desempenho em VMs Azure - Insights de aplicação Azure
description: Monitorização do desempenho da aplicação para conjuntos de escala de máquinas virtuais Azure VM e Azure. Tempo de carga e resposta de gráficos, informações de dependência e alertas sobre o desempenho.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661333"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Implemente o Agente de Insights de Aplicação do Monitor Azure em máquinas virtuais Azure e conjuntos de escala de máquinas virtuais Azure

Permitir a monitorização das suas aplicações web baseadas em .NET em funcionamento em [máquinas virtuais Azure](https://azure.microsoft.com/services/virtual-machines/) e conjuntos de [escala de máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) é agora mais fácil do que nunca. Obtenha todos os benefícios de usar O Insights de Aplicação sem modificar o seu código.

Este artigo acompanha-o através da monitorização de Insights de Aplicação utilizando o Agente de Insights de Aplicação e fornece orientações preliminares para automatizar o processo para implementações em larga escala.

> [!IMPORTANT]
> O Agente de Insights de Aplicação Azure para .NET encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não a recomendamos para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, e algumas podem ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Ativar o Application Insights

Existem duas formas de permitir a monitorização da aplicação para máquinas virtuais Azure e conjuntos de máquinas virtuais Azure hospedados aplicações:

* **Sem código** via Agente de Insights de Aplicação
    * Este método é o mais fácil de ativar, e não é necessária nenhuma configuração avançada. É frequentemente referido como monitorização "tempo de funcionamento".

    * Para máquinas virtuais Azure e conjuntos de escala de máquinas virtuais Azure recomendamos, no mínimo, este nível de monitorização. Depois disso, com base no seu cenário específico, pode avaliar se é necessária instrumentação manual.

    * O Agente de Insights de Aplicação recolhe automaticamente os mesmos sinais de dependência fora da caixa que o .NET SDK. Consulte a [coleção automática Dependency](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net) para saber mais.
        > [!NOTE]
        > Atualmente apenas são suportadas aplicações hospedadas em IIS .Net. Utilize um SDK para instrumentos ASP.NET aplicações Core, Java e Node.js alojadas em máquinas virtuais Azure e conjuntos de escala de máquinas virtuais.

* **Baseado em código** via SDK

    * Esta abordagem é muito mais personalizável, mas requer a adição de [uma dependência dos pacotes DeSC NuGet](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)da Aplicação Insights . Este método também significa que você tem que gerir as atualizações para a versão mais recente dos pacotes por si mesmo.

    * Se precisar de fazer chamadas aPI personalizadas para rastrear eventos/dependências não capturadas por padrão com monitorização baseada em agentes, terá de utilizar este método. Confira a API para saber [eventos personalizados e artigo de métricas](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) para saber mais.

> [!NOTE]
> Se for detetada uma monitorização baseada no agente e uma instrumentação manual baseada em SDK, apenas as definições de instrumentação manual serão respeitadas. Isto é para evitar que os dados duplicados sejam enviados. Para saber mais sobre isto, confira a secção de resolução de [problemas](#troubleshooting) abaixo.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Gerir o Agente de Insights de Aplicações para aplicações .NET em máquinas virtuais Azure usando powerShell

> [!NOTE]
> Antes de instalar o Agente de Insights de Aplicação, necessitará de uma cadeia de ligação. [Crie um novo Recurso de Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) de Aplicação ou copie a cadeia de ligação a partir de um recurso de insights de aplicação existente.

> [!NOTE]
> Novo para powershell? Confira o [Guia Iniciar A Iniciação](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

Instale ou atualize o Agente de Insights de Aplicação como uma extensão para máquinas virtuais Azure
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Pode instalar ou atualizar o Agente de Insights de Aplicação como uma extensão através de várias Máquinas Virtuais em escala utilizando um loop Powershell.

Desinstalar extensão do agente insights de aplicação da máquina virtual Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Consulta Aplicação Insights Estatuto de extensão de agente para máquina virtual Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Obtenha a lista de extensões instaladas para a máquina virtual Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Também pode ver extensões instaladas na [lâmina de máquina virtual Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) no Portal.

> [!NOTE]
> Verifique a instalação clicando no Live Metrics Stream dentro do Recurso de Insights de Aplicação associado à cadeia de ligação que utilizou para implementar a extensão do agente de insights de aplicação. Se estiver a enviar dados de várias Máquinas Virtuais, selecione as máquinas virtuais Azure alvo sob o nome do servidor. Pode levar até um minuto para os dados começarem a fluir.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Gerir o Agente de Insights de Aplicação para aplicações .NET em conjuntos de escala de máquinas virtuais Azure usando powershell

Instale ou atualize o Agente de Insights de Aplicação como uma extensão para o conjunto de escala de máquina virtual Azure
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Desinstalar extensão de monitorização de aplicações de conjuntos de escala de máquinas virtuais Do Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Estado de extensão de monitorização de aplicações de consulta para conjuntos de escala de máquinas virtuais Azure
```powershell
# Not supported by extensions framework
```

Obtenha a lista de extensões instaladas para conjuntos de escala de máquinas virtuais Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Resolução de problemas

Encontre dicas de resolução de problemas para a extensão do agente de monitorização de insights de aplicações para aplicações .NET em máquinas virtuais Azure e conjuntos de escala de máquinas virtuais.

> [!NOTE]
> As aplicações .NET Core, Java e Node.js são suportadas apenas em máquinas virtuais Azure e conjuntos de escala de máquinas virtuais Azure através de instrumentação manual baseada em SDK e, portanto, os passos abaixo não se aplicam a estes cenários.

A saída de execução de extensão é registada em ficheiros encontrados nos seguintes diretórios:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Passos seguintes
* Aprenda a implementar uma aplicação para um conjunto de escala de [máquina seleções azul.](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)
* [Configurar os testes web de disponibilidade](monitor-web-app-availability.md) para ser alertado se o seu ponto final estiver em baixo.
