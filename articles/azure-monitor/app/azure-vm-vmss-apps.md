---
title: Monitor desempenho em VMs Azure - Azure Application Insights
description: Monitorização do desempenho da aplicação para conjuntos de balanças de máquinas virtuais Azure VM e Azure. Cartografe o tempo de carga e resposta, informações de dependência e definir alertas sobre o desempenho.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: ed56bc88a9d2e8a9490331605cd4a72aef6930db
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677948"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Implementar o Agente de Insights de Aplicação do Monitor Azure em máquinas virtuais Azure e conjuntos de escala de máquina virtual Azure

Ativar a monitorização nas aplicações web baseadas em .NET em funcionamento em [máquinas virtuais Azure](https://azure.microsoft.com/services/virtual-machines/) e [conjuntos de escala de máquina virtual Azure](../../virtual-machine-scale-sets/index.yml) é agora mais fácil do que nunca. Obtenha todos os benefícios de usar Insights de Aplicação sem modificar o seu código.

Este artigo acompanha-o através da monitorização de Insights de Aplicação utilizando o Application Insights Agent e fornece orientações preliminares para automatizar o processo para implementações em larga escala.

> [!IMPORTANT]
> O Agente de Insights de Aplicação Azure para aplicações ASP.NET em execução em **VMs E VMSS E VMSS** está atualmente em pré-visualização pública. Para monitorizar as suas aplicações ASP.Net em funcionamento **no local,** utilize o [Agente de Insights de Aplicação Azure para servidores no local](./status-monitor-v2-overview.md), que geralmente está disponível e totalmente suportado.
> A versão de pré-visualização de Azure VMs e VMSS é fornecida sem um acordo de nível de serviço, e não recomendamos para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, e algumas podem ter capacidades restritas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Ativar o Application Insights

Existem duas formas de permitir a monitorização de aplicações para máquinas virtuais Azure e conjuntos de escala de máquina virtual Azure hospedados aplicações:

* **Codeless** via Application Insights Agent
    * Este método é o mais fácil de ativar, e não é necessária nenhuma configuração avançada. É frequentemente referido como monitorização do "tempo de execução".

    * Para máquinas virtuais Azure e conjuntos de balanças de máquinas virtuais Azure recomendamos, no mínimo, este nível de monitorização. Depois disso, com base no seu cenário específico, pode avaliar se a instrumentação manual é necessária.

    * O Application Insights Agent recolhe automaticamente os mesmos sinais de dependência fora da caixa que o .NET SDK. Consulte [a coleção automática Dependency](./auto-collect-dependencies.md#net) para saber mais.
        > [!NOTE]
        > Atualmente apenas são suportadas aplicações .Net IIS- hospedadas. Utilize um SDK para instrumentar ASP.NET Aplicações Core, Java e Node.js hospedadas em máquinas virtuais Azure e conjuntos de escala de máquinas virtuais.

* **Baseado em código** via SDK

    * Esta abordagem é muito mais personalizável, mas requer [a adição de uma dependência dos pacotes SDK NuGet da Aplicação Insights.](./asp-net.md) Este método também significa que você tem que gerir as atualizações para a versão mais recente dos pacotes por si mesmo.

    * Se precisar de fazer chamadas de API personalizadas para rastrear eventos/dependências não capturados por padrão com monitorização baseada em agentes, terá de utilizar este método. Confira a [API para obter eventos personalizados e artigo de métrica](./api-custom-events-metrics.md) para saber mais.

> [!NOTE]
> Se for detetada uma monitorização baseada em agente e uma instrumentação manual baseada em SDK, apenas serão respeitadas as definições de instrumentação manual. Isto é para evitar que os dados duplicados sejam enviados. Para saber mais sobre este ponto, consulte a [secção de resolução de problemas abaixo.](#troubleshooting)

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Gerir o Agente de Insights de Aplicações para aplicações .NET em máquinas virtuais Azure utilizando o PowerShell

> [!NOTE]
> Antes de instalar o Application Insights Agent, necessitará de uma cadeia de ligação. [Crie um novo Recurso de Insights de Aplicação](./create-new-resource.md) ou copie a cadeia de ligação a partir de um recurso de insights de aplicação existente.

> [!NOTE]
> Novo em Powershell? Confira o [Guia Get Started](/powershell/azure/get-started-azureps?view=azps-2.5.0).

Instale ou atualize o Application Insights Agent como uma extensão para máquinas virtuais Azure
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
> Pode instalar ou atualizar o Application Insights Agent como uma extensão através de várias Máquinas Virtuais à escala utilizando um loop Powershell.

Desinstalar extensão do Agente de Insights de Aplicação a partir da máquina virtual Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Consulta Aplicação Insights Estado de extensão do agente para máquina virtual Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Obtenha a lista de extensões instaladas para máquina virtual Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Também pode ver extensões instaladas na [lâmina da máquina virtual Azure](../../virtual-machines/extensions/overview.md) no Portal.

> [!NOTE]
> Verifique a instalação clicando no Live Metrics Stream dentro do Recurso Application Insights associado à cadeia de ligação utilizada para implementar a Extensão do Agente de Insights de Aplicação. Se estiver a enviar dados de várias Máquinas Virtuais, selecione as máquinas virtuais target Azure em Nome do Servidor. Pode levar até um minuto para os dados começarem a fluir.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Gerir o Agente de Insights de Aplicação para aplicações .NET em conjuntos de escala de máquina virtual Azure usando powershell

Instale ou atualize o Application Insights Agent como uma extensão para conjunto de escala de máquina virtual Azure
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

Desinstalar a extensão de monitorização da aplicação a partir de conjuntos de escala de máquina virtual Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Estado de extensão de monitorização de aplicação de consulta para conjuntos de escala de máquina virtual Azure
```powershell
# Not supported by extensions framework
```

Obtenha a lista de extensões instaladas para conjuntos de escala de máquina virtual Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Resolução de problemas

Encontre dicas de resolução de problemas para a extensão do agente de monitorização de insights de aplicação para aplicações .NET em execução em máquinas virtuais Azure e conjuntos de escala de máquina virtual.

> [!NOTE]
> .NET Core, Java e Node.js aplicações são suportadas apenas em máquinas virtuais Azure e conjuntos de escala de máquina virtual Azure através de instrumentação manual baseada em SDK e, portanto, os passos abaixo não se aplicam a estes cenários.

A saída de execução de extensão é registada em ficheiros encontrados nos seguintes diretórios:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Passos seguintes
* Saiba como [implementar uma aplicação num conjunto de escala de máquina virtual Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Configurar os testes web availability](monitor-web-app-availability.md) para serem alertados se o seu ponto final estiver em baixo.