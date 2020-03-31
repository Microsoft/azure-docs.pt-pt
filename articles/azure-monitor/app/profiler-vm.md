---
title: Aplicativos web de perfil num Azure VM - Profiler de Insights de Aplicação
description: Perfilie aplicações web num Azure VM utilizando o Perfil de Insights de Aplicação.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671584"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Aplicativos web de perfil que executam uma máquina virtual Azure ou uma escala de máquina virtual definida usando o Perfil de Insights de Aplicação

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Também pode implementar o Perfil de Insights de Aplicação Azure nestes serviços:
* [Serviço de Aplicações do Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços Cloud do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Desloque o Perfil numa máquina virtual ou num conjunto de escala de máquina virtual
Este artigo mostra-lhe como fazer com que o Perfil de Insights de Aplicação esteja a funcionar na sua máquina virtual Azure (VM) ou no conjunto de máquinas virtuais Azure. O Profiler está instalado com a extensão de Diagnóstico Azure para VMs. Configure a extensão para executar o Profiler e construa o SDK de Insights de Aplicação na sua aplicação.

1. Adicione o SDK de Insights de Aplicação à sua [aplicação ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

   Para ver perfis para os seus pedidos, deve enviar telemetria de pedido para Informações de Aplicação.

1. Instale a extensão de Diagnóstico Azure no seu VM. Para exemplos completos do modelo do Gestor de Recursos, consulte:  
   * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Conjuntos de dimensionamento de máquinas virtuais](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     A parte chave é o ApplicationInsightsProfilerSink no WadCfg. Para que o Azure Diagnostics ative o Profiler a enviar dados para o seu iKey, adicione mais um lavatório a esta secção.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Implementar a definição de implantação do ambiente modificado.  

   Aplicar as modificações geralmente envolve uma implementação completa do modelo ou uma publicação baseada em serviço na nuvem através de cmdlets PowerShell ou Visual Studio.  

   Os seguintes comandos PowerShell são uma abordagem alternativa para as máquinas virtuais existentes que tocam apenas a extensão de Diagnóstico Azure. Adicione o PerfilrSink anteriormente mencionado ao config que é devolvido pelo comando Get-AzVMDiagnosticsExtension. Em seguida, passe o config atualizado para o comando set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se a aplicação pretendida estiver `IIS Http Tracing` a ser recorrida através [do IIS,](https://www.microsoft.com/web/downloads/platform.aspx)ative a funcionalidade Windows.

   a. Estabeleça o acesso remoto ao ambiente e, em seguida, utilize a janela [de funcionalidades Add Windows.]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) Ou executar o seguinte comando na PowerShell (como administrador):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se estabelecer o acesso remoto for um problema, pode utilizar o [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para executar o seguinte comando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Implante a sua aplicação.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Definir apia do perfil usando o Explorador de Recursos Azure
Ainda não temos uma maneira de definir a pia do Perfil de Insights de Aplicação do portal. Em vez de usar a fonte de alimentação como acima descrita, pode utilizar o Azure Resource Explorer para definir a pia. Mas note que, se colocar o VM de novo, a pia estará perdida. Terá de atualizar o config que utiliza ao implementar o VM para preservar esta definição.

1. Verifique se a extensão de Diagnóstico do Windows Azure está instalada visualizando as extensões instaladas para a sua máquina virtual.  

    ![Verifique se a extensão WAD está instalada][wadextension]

2. Encontre a extensão de Diagnóstico VM para o seu VM. Vai [https://resources.azure.com](https://resources.azure.com)para. Expanda o seu grupo de recursos, Microsoft.Compute virtualMachines, nome de máquina virtual e extensões.  

    ![Navegue até wad config no Azure Resource Explorer][azureresourceexplorer]

3. Adicione o perfil de insights de aplicação ao nó SinksConfig sob wadCfg. Se ainda não tiver uma secção SinksConfig, poderá ter de adicionar uma. Certifique-se de especificar os insights de aplicação adequados iKey nas suas definições. Terá de mudar o modo de exploradores para Ler/Escrever no canto superior direito e premir o botão azul 'Editar'.

    ![Adicionar Afundador de Perfis de Insights de Aplicação][resourceexplorersinksconfig]

4. Quando terminar de editar o config, prima "Put". Se o put for bem sucedido, aparecerá uma verificação verde no meio do ecrã.

    ![Enviar pedido de colocação para aplicar alterações][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>O Profiler pode funcionar em servidores no local?
Não temos nenhum plano para apoiar o Perfil de Insights de Aplicação para servidores no local.

## <a name="next-steps"></a>Passos seguintes

- Gere tráfego na sua aplicação (por exemplo, lance um [teste de disponibilidade).](monitor-web-app-availability.md) Em seguida, aguarde 10 a 15 minutos para que os vestígios comecem a ser enviados para a instância Deinsights de Aplicação.
- Consulte [os vestígios](profiler-overview.md?toc=/azure/azure-monitor/toc.json) do Profiler no portal Azure.
- Para obter ajuda com problemas de resolução de problemas, consulte a resolução de [problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
