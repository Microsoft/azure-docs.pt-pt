---
title: Perfil de aplicações web em execução numa VM do Azure com o Application Insights Profiler | Documentos da Microsoft
description: Criar perfis para aplicações web numa VM do Azure com o Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 4cca65e2be44d2c846cd4034f0a9d7e8c7d9af28
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260051"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Conjunto de aplicações de web de perfil em execução em máquinas virtuais do Azure ou um dimensionamento de máquinas virtuais com o Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Também pode implementar o Azure Application Insights Profiler estes serviços:
* [Serviço de Aplicações do Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Implementar o Profiler numa máquina virtual ou um conjunto de dimensionamento de máquinas virtuais
Este artigo mostra-lhe como obter o Application Insights Profiler em execução em sua máquina virtual do Azure (VM) ou o conjunto de dimensionamento de máquina virtual do Azure. Profiler está instalado com a extensão de diagnóstico do Azure para VMs. Configurar a extensão para executar o Profiler e criar o SDK do Application Insights na sua aplicação.

1. Adicionar o Application Insights SDK para sua [aplicativo ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) ou regular [aplicativo .NET](windows-services.md?toc=/azure/azure-monitor/toc.json).  
   Para ver perfis para seus pedidos, terá de enviar a telemetria de pedido para o Application Insights.

1. Instale a extensão de diagnóstico do Azure na sua VM. Para exemplos de modelo do Resource Manager completo, veja:  
   * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Conjunto de dimensionamento de máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     A parte de chave é ApplicationInsightsProfilerSink no WadCfg. Para que o diagnóstico do Azure ative Profiler enviar dados para a iKey, adicione outro sink a esta secção.
    
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

1. Implemente a definição de implementação do ambiente modificado.  

   Aplicar as modificações geralmente envolve uma implementação de modelo completo ou uma nuvem baseados em serviço publicar através de cmdlets do PowerShell ou o Visual Studio.  

   Os seguintes comandos do PowerShell são uma abordagem alternativa para máquinas virtuais existentes que envolvem a extensão de diagnóstico do Azure. Adicione o ProfilerSink mencionada anteriormente para a configuração que é devolvida pelo comando Get-AzVMDiagnosticsExtension. Em seguida, passe a configuração atualizada para o comando Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se estiver a executar a aplicação pretendida [IIS](https://www.microsoft.com/web/downloads/platform.aspx), ativar o `IIS Http Tracing` funcionalidade do Windows.

   a. Estabeleça o acesso remoto para o ambiente e, em seguida, utilize o [funcionalidades do Windows adicionar]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) janela. Em alternativa, execute o seguinte comando no PowerShell (como administrador):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se estabelecer o acesso remoto é um problema, pode utilizar o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para executar o seguinte comando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Implemente a sua aplicação.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Definir a utilizar o Explorador de recursos do Azure de Sink do Profiler
Ainda não temos uma forma de definir o sink do Application Insights Profiler a partir do portal. Em vez de utilizar o powershell, como descrito acima, pode utilizar o Explorador de recursos do Azure para definir o sink. Mas tenha em atenção que se implementar a VM novamente, o coletor serão perdido. Terá de atualizar a configuração que utiliza ao implementar a VM para preservar essa definição.

1. Certifique-se de que a extensão de diagnóstico do Windows Azure está instalada ao visualizar as extensões instaladas para a máquina virtual.  

    ![Verifique se a extensão WAD está instalado][wadextension]

1. Encontre a extensão de diagnóstico de VM para a sua VM. Expanda o grupo de recursos, virtualMachines da Microsoft. Compute, nome da máquina virtual e extensões.  

    ![Navegue para configuração WAD no Explorador de recursos do Azure][azureresourceexplorer]

1. Adicione o sink do Application Insights Profiler para o nó de SinksConfig em WadCfg. Se ainda não tiver uma secção de SinksConfig, terá de adicionar um. Certifique-se de que especificar a iKey adequada do Application Insights nas suas definições. Terá de mudar o modo de gerenciadores de leitura/escrita no canto superior direito e prima o botão "Editar" azul.

    ![Adicionar o Application Insights Profiler Sink][resourceexplorersinksconfig]

1. Quando terminar a editar a configuração, prima "Put". Se o put for bem-sucedida, uma verificação a verde aparecerá no meio da tela.

    ![Enviar um pedido put para aplicar alterações][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Profiler pode ser executado em servidores no local?
Não temos nenhum plano para suportar o Application Insights Profiler para servidores no local.

## <a name="next-steps"></a>Passos Seguintes

- Gerar tráfego para a aplicação (por exemplo, iniciar uma [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, aguarde 10 a 15 minutos para que os rastreios começar a ser enviados para a instância do Application Insights.
- Ver [rastreios do Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
- Para obter ajuda com a resolução de problemas do Profiler, consulte [Profiler de resolução de problemas](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
