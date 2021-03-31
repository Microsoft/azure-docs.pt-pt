---
title: Aplicativos web de perfil em um Azure VM - Application Insights Profiler
description: Perfile aplicativos web num Azure VM utilizando o Perfil de Insights de Aplicação.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f514dd7b54ac091535aeab43a8a7d2a645b50a09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87315840"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Aplicativos web de perfil em execução em uma máquina virtual Azure ou uma escala de máquina virtual definida usando o Perfil de Insights de Aplicação

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Também pode implementar o Azure Application Insights Profiler nestes serviços:
* [Serviço de Aplicações do Azure](./profiler.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Cloud Services do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Implementar profiler em uma máquina virtual ou um conjunto de escala de máquina virtual
Este artigo mostra-lhe como obter o Profiler Application Insights a funcionar na sua máquina virtual Azure (VM) ou no conjunto de escala de máquina virtual Azure. O profiler é instalado com a extensão Azure Diagnostics para VMs. Configure a extensão para executar profiler e construa o Application Insights SDK na sua aplicação.

1. Adicione o SDK de Insights de Aplicação à sua [aplicação ASP.NET](./asp-net.md).

   Para visualizar perfis para os seus pedidos, tem de enviar telemetria para Informações de Aplicação.

1. Instale a extensão Azure Diagnostics no seu VM. Para obter exemplos completos do modelo do Gestor de Recursos, consulte:  
   * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Conjuntos de dimensionamento de máquinas virtuais](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     A parte chave é o ApplicationInsightsProfilerSink no WadCfg. Para que o Azure Diagnostics permita ao Profiler enviar dados para o seu iKey, adicione outra pia a esta secção.
    
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

1. Implemente a definição de implantação do ambiente modificada.  

   A aplicação das modificações envolve geralmente uma implementação completa do modelo ou uma publicação baseada em serviço na nuvem através de cmdlets PowerShell ou Visual Studio.  

   Os seguintes comandos PowerShell são uma abordagem alternativa para máquinas virtuais existentes que tocam apenas na extensão Azure Diagnostics. Adicione o ProfilerSink anteriormente mencionado ao config que é devolvido pelo comando Get-AzVMDiagnosticsExtension. Em seguida, passe o config atualizado para o comando Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se a aplicação pretendida estiver a ser executada através do [IIS,](https://www.microsoft.com/web/downloads/platform.aspx)ative a `IIS Http Tracing` funcionalidade Windows.

   a. Estabeleça o acesso remoto ao ambiente e, em seguida, utilize a janela [de funcionalidades Add Windows.](/iis/configuration/system.webserver/tracing/) Ou executar o seguinte comando em PowerShell (como administrador):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se estabelecer o acesso remoto é um problema, pode utilizar o [CLI Azure](/cli/azure/get-started-with-azure-cli) para executar o seguinte comando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Desdobre a sua aplicação.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Definir a pia do profiler usando o Explorador de Recursos Azure
Ainda não temos forma de definir o perfil de insights de aplicação do portal. Em vez de utilizar a configuração como descrito acima, pode utilizar o Azure Resource Explorer para definir a pia. Mas note que se voltar a colocar o VM, a pia perder-se-á. Terá de atualizar o config que utiliza ao utilizar o VM para preservar esta definição.

1. Verifique se a extensão do Windows Azure Diagnostics está instalada visualizando as extensões instaladas para a sua máquina virtual.  

    ![Verifique se a extensão DO WAD está instalada][wadextension]

2. Encontre a extensão de Diagnóstico VM para o seu VM. Vai [https://resources.azure.com](https://resources.azure.com) para. Expanda o seu grupo de recursos, Microsoft.Compute virtualMachines, nome de máquina virtual e extensões.  

    ![Navegue até WAD conf em Azure Resource Explorer][azureresourceexplorer]

3. Adicione a pia do Profiler Application Insights ao nó SinksConfig em WadCfg. Se ainda não tiver uma secção SinksConfig, poderá ter de adicionar uma. Certifique-se de especificar o iKey de aplicação adequado nas suas definições. Terá de mudar o modo explorador para Ler/Escrever no canto superior direito e premir o botão azul 'Editar'.

    ![Adicionar a pia do profiler de insights de aplicação][resourceexplorersinksconfig]

4. Quando terminar de editar o config, prima "Put". Se a colocação for bem sucedida, aparecerá uma verificação verde no meio do ecrã.

    ![Enviar pedido de colocação para aplicar alterações][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>O Profiler pode funcionar nos servidores do local?
Não temos nenhum plano para suportar o Profiler Application Insights para servidores no local.

## <a name="next-steps"></a>Passos seguintes

- Gere tráfego para a sua aplicação (por exemplo, lance um [teste de disponibilidade).](monitor-web-app-availability.md) Em seguida, aguarde 10 a 15 minutos para que os vestígios comecem a ser enviados para a instância De Insights de Aplicação.
- Consulte [os vestígios do Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal Azure.
- Para obter ajuda para resolver problemas de perfis, consulte [a resolução de problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png

