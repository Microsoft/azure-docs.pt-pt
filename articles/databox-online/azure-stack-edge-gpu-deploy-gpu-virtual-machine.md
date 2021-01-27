---
title: Visão geral e implantação de VMs GPU no seu dispositivo Azure Stack Edge Pro
description: Descreve como criar e gerir máquinas virtuais GPU (VMs) num dispositivo Azure Stack Edge Pro utilizando modelos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: 7534052412c2bee0f31e352fc577d376c11215c3
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98804939"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>VMs GPU para o seu dispositivo Azure Stack Edge Pro

Este artigo fornece uma visão geral das máquinas virtuais GPU (VMs) no seu dispositivo Azure Stack Edge Pro. O artigo descreve como criar um VM GPU e, em seguida, instalar extensão do controlador GPU para instalar controladores Nvidia apropriados. Utilize os modelos Azure Resource Manager para criar o VM GPU e instalar a extensão do controlador GPU. 

Este artigo aplica-se aos dispositivos Azure Stack Edge Pro GPU e Azure Stack Edge Pro R.

## <a name="about-gpu-vms"></a>Sobre os VMs da GPU

Os seus dispositivos Azure Stack Edge Pro estão equipados com 1 ou 2 da Tesla T4 GPU da Nvidia. Para implementar cargas de trabalho VM aceleradas por GPU nestes dispositivos, utilize tamanhos de VM otimizados da GPU. Por exemplo, a série NC T4 v3 deve ser utilizada para implantar cargas de trabalho de inferência com GPUs T4. 

Para obter mais informações, consulte [VMs da série NC T4 v3](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Condutores de OS e GPU apoiados 

Para tirar partido das capacidades da GPU dos VMs da série Azure N, os controladores da Nvidia GPU devem ser instalados. 

A extensão do controlador Nvidia GPU instala controladores Nvidia CUDA ou GRID apropriados. Pode instalar ou gerir a extensão utilizando os modelos Azure Resource Manager.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Sistema operativo suportado para extensão gpu para Windows

Esta extensão suporta os seguintes sistemas operativos (OSs). Outras versões podem funcionar, mas não foram testadas internamente em VMs GPU em execução em dispositivos Azure Stack Edge Pro.

| Distribuição | Versão |
|---|---|
| Windows Server 2019 | Principal |
| Windows Server 2016 | Principal |

### <a name="supported-os-for-gpu-extension-for-linux"></a>SoA apoiado para extensão gpu para Linux

Esta extensão suporta os seguintes distros de SO, dependendo do suporte do controlador para a versão so específica. Outras versões podem funcionar, mas não foram testadas internamente em VMs GPU em execução em dispositivos Azure Stack Edge Pro.


| Distribuição | Versão |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU VMs e Kubernetes

Antes de colocar VMs GPU no seu dispositivo, reveja as seguintes considerações se kubernetes estiver configurado no dispositivo.

#### <a name="for-1-gpu-device"></a>Para dispositivo de 1 GPU: 

- **Crie um VM GPU seguido de configuração Kubernetes no seu dispositivo**: Neste cenário, a criação de VM GPU e a configuração de Kubernetes serão ambas bem sucedidas. Kubernetes não terá acesso à GPU neste caso.

- **Configure kubernetes no seu dispositivo seguido da criação de um VM GPU**: Neste cenário, os Kubernetes irão reivindicar a GPU no seu dispositivo e a criação de VM falhará uma vez que não existem recursos GPU disponíveis.

#### <a name="for-2-gpu-device"></a>Para dispositivo de 2 GPU

- **Crie um GPU VM seguido de configuração Kubernetes no seu dispositivo**: Neste cenário, o VM GPU que cria irá reivindicar um GPU no seu dispositivo e a configuração de Kubernetes também será bem sucedida e reclamará a restante GPU. 

- **Crie dois VMs GPU seguidos pela configuração de Kubernetes no seu dispositivo**: Neste cenário, os dois VMs GPU irão reivindicar as duas GPUs no dispositivo e os Kubernetes são configurados com sucesso sem GPUs. 

- **Configure kubernetes no seu dispositivo seguido da criação de um VM GPU**: Neste cenário, os Kubernetes irão reivindicar tanto as GPUs no seu dispositivo como a criação de VM falharão uma vez que não existem recursos gpu disponíveis.

Se tiver VMs GPU em execução no seu dispositivo e Kubernetes também estiver configurado, então sempre que o VM for transcioso (quando parar ou remover um VM utilizando Stop-AzureRmVM ou Remove-AzureRmVM), existe o risco de o cluster Kubernetes reivindicar todas as GPUs disponíveis no dispositivo. Neste caso, não poderá reiniciar os VMs gpu implantados no seu dispositivo ou criar VMs GPU.


## <a name="create-gpu-vms"></a>Criar VMs GPU

Siga estes passos ao colocar VMs GPU no seu dispositivo:

1. Identifique se o seu dispositivo também estará a executar Kubernetes. Se o dispositivo executar Kubernetes, então terá de criar primeiro o VM GPU e, em seguida, configurar kubernetes. Se kubernetes for configurado primeiro, então irá reivindicar todos os recursos de GPU disponíveis e a criação de VM GPU falhará.

1. [Descarregue os modelos e os ficheiros de parâmetros VM](https://aka.ms/ase-vm-templates) para a sua máquina de clientes. Desaperte-o num diretório que usarás como diretório de trabalho.

1. Para criar VMs GPU, siga todos os passos no [VM de implementação no seu Azure Stack Edge Pro utilizando modelos,](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) exceto para as seguintes diferenças: 

    1. Ao configurar a rede de cálculo, ative a porta que está ligada à Internet, para calcular. Isto permite-lhe descarregar os controladores gpu necessários para extensões GPU para os seus VMs GPU.

        Aqui está um exemplo onde o Porto 2 estava ligado à internet e foi usado para permitir a rede de computação. Se identificou que kubernetes não é necessário no passo anterior, pode saltar o nó de Kubernetes IP e a atribuição ip de serviço externo.

        ![Ativar definições de cálculo na porta ligada à internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. Crie um VM utilizando os modelos. Ao especificar os tamanhos VM da GPU, certifique-se de que utiliza a série NCasT4-v3 na medida em que `CreateVM.parameters.json` estas são suportadas para VMs GPU. Para obter mais informações, consulte [os tamanhos de VM suportados para VMs GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. Uma vez criado o VM GPU VM com sucesso, pode ver este VM na lista de máquinas virtuais no seu recurso Azure Stack Edge no portal Azure.

        ![GPU VM na lista de máquinas virtuais no portal Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Selecione o VM e faça um exercício para os detalhes. Copie o IP atribuído ao VM.

    ![IP atribuído à GPU VM no portal Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. Após a criação do VM, implemente a extensão gpu utilizando o modelo de extensão. Para os VMs linux, consulte [a extensão GPU de instalação para Linux](#gpu-extension-for-linux) e para VMs do Windows, consulte [a extensão GPU de instalação para windows](#gpu-extension-for-windows).

1. Para verificar a instalação de extensão GPU, ligue-se ao VM GPU:
    1. Se utilizar um VM do Windows, siga os passos em [Ligar a um VM do Windows](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-windows-vm). [Verifique a instalação](#verify-windows-driver-installation).
    1. Se utilizar um Linux VM, siga os passos em [Connect to a Linux VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm). [Verifique a instalação](#verify-linux-driver-installation).

1. Se necessário, pode mudar a rede de cálculo de volta para o que precisar. 


> [!NOTE]
> Ao atualizar a versão do software do dispositivo de 2012 para mais tarde, terá de parar manualmente os VMs da GPU.


## <a name="install-gpu-extension"></a>Instalar extensão GPU

Dependendo do sistema operativo para o seu VM, pode instalar a extensão GPU para Windows ou linux.

> [!NOTE]
> Antes de instalar a extensão GPU, certifique-se de que a porta ativada para a rede de computação no seu dispositivo está ligada à Internet e tem acesso. Os controladores da GPU são descarregados através do acesso à internet.

### <a name="gpu-extension-for-windows"></a>Extensão GPU para Windows

Para implantar os controladores gpu da Nvidia para um VM existente, edite o ficheiro de `addGPUExtWindowsVM.parameters.json` parâmetros e, em seguida, implemente o modelo `addGPUextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

O ficheiro `addGPUExtWindowsVM.parameters.json` toma os seguintes parâmetros:

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

Aqui está um arquivo de parâmetros de amostra que foi usado neste artigo:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>Implementar o modelo

Implemente o modelo `addGPUextensiontoVM.json` . Este modelo implementa a extensão para um VM existente. Execute o seguinte comando:

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> A extensão é um trabalho de longa duração e leva cerca de 10 minutos para ser concluída.

Eis uma saída de exemplo:

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
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
Eis uma saída de exemplo:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

A saída de execução de extensão é registada no seguinte ficheiro. Consulte este ficheiro `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` para acompanhar o estado da instalação. 


Uma instalação bem sucedida é indicada por um `message` como `Enable Extension` e como `status` `success` .

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>Verifique a instalação do controlador do Windows

Inscreva-se no VM e corram o utilitário da linha de comando nvidia-smi instalado com o condutor. O `nvidia-smi.exe` está localizado em  `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` . Se não vir o ficheiro, é possível que a instalação do condutor ainda esteja em segundo plano. Espere 10 minutos e verifique de novo.

Se o controlador estiver instalado, vê uma saída semelhante à seguinte: 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

Para mais informações, consulte a [extensão do controlador da Nvidia GPU para windows](../virtual-machines/extensions/hpccompute-gpu-windows.md).

### <a name="gpu-extension-for-linux"></a>Extensão gpu para Linux

Para implantar os controladores gpu da Nvidia para um VM existente, edite o ficheiro de parâmetros e, em seguida, implemente o modelo `addGPUextensiontoVM.json` . Existem ficheiros de parâmetros específicos para Ubuntu e Red Hat Enterprise Linux (RHEL), conforme discutido nas seguintes secções.

#### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

Se utilizar ubuntu, o `addGPUExtLinuxVM.parameters.json` ficheiro toma os seguintes parâmetros:

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Se utilizar o Red Hat Enterprise Linux (RHEL), o `addGPUExtensionRHELVM.parameters.json` ficheiro toma os seguintes parâmetros:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


Aqui está uma amostra do arquivo de parâmetros Ubuntu que foi usado neste artigo:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>Implementar o modelo

Implemente o modelo `addGPUextensiontoVM.json` . Este modelo implementa a extensão para um VM existente. Execute o seguinte comando:

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> A extensão é um trabalho de longa duração e leva cerca de 10 minutos para ser concluída.

Eis uma saída de exemplo:

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>Estado de implantação da faixa    
    
A implementação do modelo é um trabalho de longa duração. Para verificar o estado de implantação das extensões para um determinado VM, abra outra sessão PowerShell (executada como administrador). Execute o seguinte comando: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Eis uma saída de exemplo: 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
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

A saída de execução de extensão é registada no seguinte ficheiro: `/var/log/azure/nvidia-vmext-status` .

#### <a name="verify-linux-driver-installation"></a>Verifique a instalação do condutor linux

Siga estes passos para verificar a instalação do condutor:

1. Ligue-se ao VM gpu. Siga as instruções em [Ligar a um LM Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm). 

    Eis uma saída de exemplo:

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Executar o utilitário da linha de comando nvidia-smi instalado com o controlador. Se o controlador for instalado com sucesso, poderá executar o utilitário e ver a seguinte saída:

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

Para mais informações, consulte a [extensão do controlador da Nvidia GPU para linux](../virtual-machines/extensions/hpccompute-gpu-linux.md).

## <a name="remove-gpu-extension"></a>Remover extensão de GPU

Para remover a extensão da GPU, utilize o seguinte comando:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Eis uma saída de exemplo:

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>Próximos passos

[Cmdlets do Gestor de Recursos Azure](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)