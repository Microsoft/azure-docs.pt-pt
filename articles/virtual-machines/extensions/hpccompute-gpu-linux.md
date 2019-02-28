---
title: Extensão de controladores de NVIDIA GPU - VMs Linux do Azure | Documentos da Microsoft
description: Extensão do Microsoft Azure para instalar controladores de GPU da NVIDIA em série N de computação de VMs que executam o Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: roiyz
ms.openlocfilehash: 5a5472e3790235caec65e56f31096de3abfae93d
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56986367"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Extensão de controladores de NVIDIA GPU para Linux

## <a name="overview"></a>Descrição geral

Esta extensão instala controladores NVIDIA GPU em VMs de série N do Linux. Dependendo da família VM, a instalação da extensão controladores CUDA ou GRADE. Quando instalar NVIDIA os controladores com essa extensão, é abertos ao recebimento e aceita os termos do [contrato de licença de utilizador final NVIDIA](http://developer.download.nvidia.com/compute/cuda/5_5/rel/docs/EULA.pdf). Durante o processo de instalação, a VM poderá reiniciar para concluir a configuração de controlador.

Uma extensão também está disponível para instalar controladores NVIDIA GPU no [VMs de série N Windows](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão suporta distribuições de SO seguintes, consoante o suporte a driver para a versão de SO específica.

| Distribuição | Versão |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Microsoft Azure para controladores de GPU da NVIDIA requer que a VM de destino está ligado à internet e tem acesso.

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Propriedades

| Name | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.HpcCompute | string |
| tipo | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Definições

Todas as definições são opcionais. O comportamento padrão é não atualizar o kernel se não for necessário para a instalação de driver, instale o controlador mais recente suportado e o Kit de ferramentas CUDA (conforme aplicável).

| Name | Descrição | Valor Predefinido | Valores válidos | Tipo de Dados |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Atualizar o kernel, mesmo que não é necessário para instalação de controladores | false | VERDADEIRO, FALSO | boolean |
| driverVersion | NV: Versão do controlador de grelha<br> NC/ND: Versão do Kit de ferramentas CUDA. Os controladores mais recentes para o escolhido CUDA são instalados automaticamente. | mais recente | GRID: "410.92", "410.71", "390.75", "390.57", "390.42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | string |
| installCUDA | Instale o Kit de ferramentas de CUDA. Relevante apenas para VMs de série de NC/ND. | true | VERDADEIRO, FALSO | boolean |


## <a name="deployment"></a>Implementação


### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager 

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando implementar um ou mais máquinas virtuais que necessitam de configuração pós-implementação.

A configuração do JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual ou colocada na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação da configuração do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [defina o nome e tipo para recursos subordinados](../../azure-resource-manager/resource-manager-template-child-resource.md). 

O exemplo a seguir supõe que a extensão é aninhada dentro do recurso de máquina virtual. Quando aninhar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

O exemplo a seguir reflete os exemplos do Azure Resource Manager e PowerShell acima e também adiciona definições personalizadas como um exemplo para a instalação de driver de não-padrão. Especificamente, ele atualiza o kernel do SO e instala um controlador de versão de toolkit CUDA específico.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
  }'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com o Azure PowerShell e CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Códigos de saída

| Código de Saída | Significado | Ação possível |
| :---: | --- | --- |
| 0 | Operação bem-sucedida |
| 1 | Utilização incorreta de extensão | Verifique o registo de saída de execução |
| 10 | Serviços de integração do Linux para Hyper-V e o Azure não disponível ou instalado | Saída de verificação de lspci |
| 11 | NVIDIA GPU não encontrado neste tamanho VM | Utilize um [suportada do SO e o tamanho da VM](../linux/n-series-driver-setup.md) |
| 12 | Oferta de imagem não suportada |
| 13 | Tamanho da VM não suportado | Utilizar uma VM de série N para implementar |
| 14 | Operação concluída com êxito | Verifique o registo de saída de execução |


### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre as extensões, consulte [extensões de Máquina Virtual e funcionalidades para Linux](features-linux.md).

Para obter mais informações sobre VMs de série N, consulte [tamanhos de máquinas virtuais com otimização de GPU](../linux/sizes-gpu.md).
