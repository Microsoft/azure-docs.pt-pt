---
title: Utilização de extensões de script personalizadas para VMs no seu dispositivo Azure Stack Edge Pro
description: Descreve como instalar extensões de script personalizadas em máquinas virtuais (VMs) em execução num dispositivo Azure Stack Edge Pro usando modelos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 8b233211f47250d4742d35cd0782cdd241839496
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98804868"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Implemente a extensão de script personalizado em VMs em execução no seu dispositivo Azure Stack Edge Pro

A Extensão de Script Personalizada descarrega e executa scripts ou comandos em máquinas virtuais que executam nos seus dispositivos Azure Stack Edge Pro. Este artigo detalha como instalar e executar a extensão de script personalizada usando um modelo de Gestor de Recursos Azure. 

Este artigo aplica-se aos dispositivos Azure Stack Edge Pro GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R.

## <a name="about-custom-script-extension"></a>Sobre a extensão de script personalizado

A Extensão de Script Personalizado é útil para configuração pós-implantação, instalação de software ou qualquer outra tarefa de configuração/gestão. Você pode baixar scripts a partir de Azure Storage ou outro local de internet acessível, ou você pode fornecer scripts ou comandos para o tempo de execução da extensão.

A extensão de script personalizado integra-se com modelos de Gestor de Recursos Azure. Também pode executá-lo utilizando Azure CLI, PowerShell ou a API das Máquinas Virtuais Azure.

## <a name="os-for-custom-script-extension"></a>SO para extensão de script personalizado

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Sistema operativo suportado para extensão de script personalizado no Windows

A extensão de script personalizada para windows será executada nas seguintes OSs. Outras versões podem funcionar, mas não foram testadas internamente em VMs em execução em dispositivos Azure Stack Edge Pro.

| Distribuição | Versão |
|---|---|
| Windows Server 2019 | Principal |
| Windows Server 2016 | Principal |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Sistema operativo suportado para extensão de script personalizado no Linux

A extensão de script personalizada para Linux será executada nas seguintes OSs. Outras versões podem funcionar, mas não foram testadas internamente em VMs em execução em dispositivos Azure Stack Edge Pro.

| Distribuição | Versão |
|---|---|
| Linux: Ubuntu | 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.4, 7.5, 7.7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Pré-requisitos

1. [Descarregue os modelos e os ficheiros de parâmetros VM](https://aka.ms/ase-vm-templates) para a sua máquina de clientes. Desaperte o download para um diretório que usará como diretório de trabalho.

1. Deverá ter um VM criado e implantado no seu dispositivo. Para criar VMs, siga todos os passos em [Implementar VM no seu Azure Stack Edge Pro utilizando modelos](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

    Se precisar de descarregar um script como no GitHub ou no Azure Storage externamente, enquanto configura a rede de computação, ative a porta que está ligada à Internet para calcular. Isto permite-lhe descarregar o script.

    No exemplo seguinte, o Porto 2 estava ligado à internet e foi utilizado para permitir a rede de computação. Se identificar que kubernetes não é necessário no passo anterior, pode saltar o nó de Kubernetes IP e a atribuição ip de serviço externo.

    ![Ativar definições de cálculo na porta ligada à internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Instale extensão de script personalizado

Dependendo do sistema operativo para o seu VM, pode instalar a extensão de script personalizada para Windows ou linux.
 

### <a name="custom-script-extension-for-windows"></a>Extensão de Script Personalizado para o Windows

Para implementar a extensão de script personalizada para o Windows para um VM em execução no seu dispositivo, edite o `addCSExtWindowsVM.parameters.json` ficheiro de parâmetros e, em seguida, implemente o modelo `addCSextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

O ficheiro `addCSExtWindowsVM.parameters.json` toma os seguintes parâmetros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Forneça o seu nome VM, nome para a extensão e o comando que pretende executar.

Aqui está o ficheiro do parâmetro da amostra que foi usado neste artigo.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Implementar o modelo

Implemente o modelo `addCSextensiontoVM.json` . Este modelo implementa a extensão para um VM existente. Execute o seguinte comando:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> A extensão é um trabalho de longa duração e leva cerca de 10 minutos para ser concluída.

Aqui está uma amostra de saída:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Implantação de faixas

Para verificar o estado de implantação das extensões para um determinado VM, executar o seguinte comando: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Aqui está uma amostra de saída:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Quando a implantação estiver concluída, a `ProvisioningState` alteração muda para `Succeeded` .

A saída de extensão é registada em ficheiros encontrados na seguinte pasta na máquina virtual alvo. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os ficheiros especificados são transferidos para a seguinte pasta na máquina virtual alvo.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
onde <n> é um inteiro decimal, que pode mudar entre execuções da extensão. O valor de 1.* corresponde ao valor atual real `typeHandlerVersion` da extensão. Por exemplo, o diretório real neste caso era `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` . 


Neste caso, o comando para executar para a extensão personalizada foi: `md C:\\Users\\Public\\Documents\\test` . Quando a extensão for instalada com sucesso, pode verificar se o diretório foi criado no VM no caminho especificado no comando. 


### <a name="custom-script-extension-for-linux"></a>Extensão de script personalizado para Linux

Para implementar a extensão de script personalizada para o Windows para um VM em execução no seu dispositivo, edite o `addCSExtLinuxVM.parameters.json` ficheiro de parâmetros e, em seguida, implemente o modelo `addCSExtensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

O ficheiro `addCSExtLinuxVM.parameters.json` toma os seguintes parâmetros:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Forneça o seu nome VM, nome para a extensão e o comando que pretende executar.

Aqui está um ficheiro de parâmetro de amostra que foi usado neste artigo:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> A extensão é um trabalho de longa duração e leva cerca de 10 minutos para ser concluída.

Aqui está uma amostra de saída:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

O `commandToExecute` foi definido para criar um ficheiro no `file2.txt` `/home/Administrator` diretório e o conteúdo do ficheiro são `some text` . Neste caso, pode verificar se o ficheiro foi criado no caminho especificado.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Estado de implantação da faixa    
    
A implementação do modelo é um trabalho de longa duração. Para verificar o estado de implantação das extensões para um determinado VM, abra outra sessão PowerShell (executada como administrador). Execute o seguinte comando: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Aqui está uma amostra de saída: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Quando a implantação estiver concluída, a `ProvisioningState` alteração muda para `Succeeded` .

A saída de execução de extensão é registada no seguinte ficheiro: `/var/lib/waagent/custom-script/download/0/` .


## <a name="remove-custom-script-extension"></a>Remover extensão de script personalizado

Para remover a extensão do script personalizado, utilize o seguinte comando:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Aqui está uma amostra de saída:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Próximos passos

[Cmdlets do Gestor de Recursos Azure](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
