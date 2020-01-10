---
title: Criar perfil de aplicativos Web em uma VM do Azure-Application Insights Profiler
description: Criar o perfil de aplicativos Web em uma VM do Azure usando Application Insights Profiler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 046128fda385486f5a92d215e349760483c5dfa4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432372"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Criar perfil de aplicativos Web em execução em uma máquina virtual do Azure ou em um conjunto de dimensionamento de máquinas virtuais usando Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você também pode implantar o Aplicativo Azure insights Profiler nesses serviços:
* [Serviço de Aplicações do Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços Cloud do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Implantar o Profiler em uma máquina virtual ou um conjunto de dimensionamento de máquinas virtuais
Este artigo mostra como obter Application Insights Profiler em execução na VM (máquina virtual) do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure. O profiler é instalado com a extensão de Diagnóstico do Azure para VMs. Configure a extensão para executar o Profiler e compile o Application Insights SDK em seu aplicativo.

1. Adicione o SDK do Application Insights ao seu [aplicativo ASP.net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

   Para exibir perfis para suas solicitações, você deve enviar telemetria de solicitação para Application Insights.

1. Instale a extensão Diagnóstico do Azure em sua VM. Para obter exemplos completos de modelos do Resource Manager, consulte:  
   * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Conjunto de dimensionamento de máquinas virtuais](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     A parte principal é o ApplicationInsightsProfilerSink no WadCfg. Para que Diagnóstico do Azure habilite o Profiler para enviar dados para seu iKey, adicione outro coletor a esta seção.
    
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

1. Implante a definição de implantação de ambiente modificada.  

   A aplicação das modificações geralmente envolve uma implantação de modelo completo ou uma publicação baseada em serviço de nuvem por meio de cmdlets do PowerShell ou do Visual Studio.  

   Os comandos do PowerShell a seguir são uma abordagem alternativa para máquinas virtuais existentes que tocam somente a extensão de Diagnóstico do Azure. Adicione o ProfilerSink mencionado anteriormente à configuração retornada pelo comando Get-AzVMDiagnosticsExtension. Em seguida, passe a configuração atualizada para o comando Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se o aplicativo pretendido estiver sendo executado por meio do [IIS](https://www.microsoft.com/web/downloads/platform.aspx), habilite o `IIS Http Tracing` recurso do Windows.

   a. Estabeleça o acesso remoto ao ambiente e, em seguida, use a janela [Adicionar recursos do Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) . Ou execute o seguinte comando no PowerShell (como administrador):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se o estabelecimento do acesso remoto for um problema, você poderá usar o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para executar o seguinte comando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Implante seu aplicativo.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Definir o coletor do criador de perfil usando Azure Resource Explorer
Ainda não temos uma maneira de definir o coletor de Application Insights Profiler do Portal. Em vez de usar o PowerShell, como descrito acima, você pode usar Azure Resource Explorer para definir o coletor. Mas Observe que, se você implantar a VM novamente, o coletor será perdido. Você precisará atualizar a configuração usada ao implantar a VM para preservar essa configuração.

1. Verifique se a extensão de Diagnóstico do Azure do Windows está instalada exibindo as extensões instaladas para sua máquina virtual.  

    ![Verificar se a extensão WAD está instalada][wadextension]

2. Localize a extensão de diagnóstico de VM para sua VM. Vá para [https://resources.azure.com](https://resources.azure.com). Expanda seu grupo de recursos, Microsoft. Compute virtualMachines, nome da máquina virtual e extensões.  

    ![Navegue até WAD config no Azure Resource Explorer][azureresourceexplorer]

3. Adicione o coletor de Application Insights Profiler ao nó SinksConfig em WadCfg. Se você ainda não tiver uma seção SinksConfig, talvez seja necessário adicionar uma. Certifique-se de especificar o Application Insights apropriado iKey em suas configurações. Você precisará alternar o modo de gerenciadores para leitura/gravação no canto superior direito e pressionar o botão "Editar" azul.

    ![Adicionar coletor de Application Insights Profiler][resourceexplorersinksconfig]

4. Quando terminar de editar a configuração, pressione ' put '. Se Put for bem-sucedido, uma marca de seleção verde aparecerá no meio da tela.

    ![Enviar solicitação Put para aplicar alterações][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>O profiler pode ser executado em servidores locais?
Não temos nenhum plano para dar suporte a Application Insights Profiler para servidores locais.

## <a name="next-steps"></a>Passos seguintes

- Gere o tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, aguarde de 10 a 15 minutos para que os rastreamentos comecem a ser enviados para a instância de Application Insights.
- Consulte [rastreamentos do profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
- Para obter ajuda com solução de problemas de criador de perfil, consulte [solução de problemas do profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
