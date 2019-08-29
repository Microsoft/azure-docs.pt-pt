---
title: Extensão do driver NVIDIA GPU – VMs Linux do Azure | Microsoft Docs
description: Microsoft Azure extensão para instalar drivers NVIDIA GPU em VMs de computação da série N executando o Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: roiyz
ms.openlocfilehash: 167780971ec59efd1ca197958798564d1ef2d596
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092322"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Extensão de driver NVIDIA GPU para Linux

## <a name="overview"></a>Descrição geral

Essa extensão instala drivers NVIDIA GPU em VMs da série N do Linux. Dependendo da família de VMs, a extensão instala os drivers CUDA ou GRID. Ao instalar os drivers NVIDIA usando essa extensão, você estará aceitando e concordando com os termos do [contrato de licença de usuário final da NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Durante o processo de instalação, a VM pode ser reiniciada para concluir a configuração do driver.

As instruções sobre a instalação manual dos drivers e as versões atuais com suporte [estão](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)disponíveis aqui.
Uma extensão também está disponível para instalar drivers NVIDIA GPU em [VMs da série N do Windows](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Essa extensão dá suporte aos seguintes distribuições de sistema operacional, dependendo do suporte de driver para a versão específica do sistema operacional.

| Distribuição | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7,3, 7,4, 7,5, 7,6 |
| Linux: CentOS | 7,3, 7,4, 7,5, 7,6 |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de Microsoft Azure para Drivers NVIDIA GPU requer que a VM de destino esteja conectada à Internet e tenha acesso.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão.

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

### <a name="properties"></a>properties

| Name | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | string |
| type | NvidiaGpuDriverLinux | Cadeia de caracteres |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Definições

Todas as configurações são opcionais. O comportamento padrão é não atualizar o kernel se não for necessário para a instalação do driver, instalar o driver mais recente com suporte e o kit de ferramentas do CUDA (conforme aplicável).

| Name | Descrição | Default Value | Valores válidos | Tipo de Dados |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Atualizar o kernel mesmo se não for necessário para a instalação do driver | false | true, false | boolean |
| driverVersion | NV Versão do driver de grade<br> NC/ND: Versão do kit de ferramentas CUDA. Os drivers mais recentes para o CUDA escolhido são instalados automaticamente. | latest | GRADE "430.30", "418.70", "410.92", "410.71", "390.75", "390.57", "390.42"<br> CUDA "10.0.130", "9.2.88", "9.1.85" | Cadeia de caracteres |
| installCUDA | Instale o kit de ferramentas do CUDA. Somente relevante para VMs da série NC/ND. | true | true, false | boolean |


## <a name="deployment"></a>Implementação


### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager 

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem a configuração pós-implantação.

A configuração do JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual ou colocada na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação da configuração do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [defina o nome e tipo para recursos subordinados](../../azure-resource-manager/resource-manager-template-child-resource.md). 

O exemplo a seguir pressupõe que a extensão esteja aninhada dentro do recurso de máquina virtual. Quando aninhar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.

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

O exemplo a seguir espelha os exemplos de Azure Resource Manager e PowerShell acima e também adiciona configurações personalizadas como um exemplo de instalação de driver não padrão. Especificamente, ele atualiza o kernel do sistema operacional e instala um driver específico de versão do kit de ferramentas do CUDA.

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

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure e usando Azure PowerShell e CLI do Azure. Para ver o estado de implantação das extensões de uma determinada VM, execute o comando a seguir.

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
| 1 | Uso incorreto da extensão | Verificar log de saída de execução |
| 10 | Linux Integration Services para Hyper-V e Azure não disponível ou instalado | Verificar saída de lspci |
| 11 | GPU NVIDIA não encontrada neste tamanho de VM | Usar um [so e um tamanho de VM com suporte](../linux/n-series-driver-setup.md) |
| 12 | Oferta de imagem sem suporte |
| 13 | Tamanho da VM sem suporte | Usar uma VM da série N para implantar |
| 14 | Operação malsucedida | Verificar log de saída de execução |


### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões, consulte [recursos e extensões de máquina virtual para Linux](features-linux.md).

Para obter mais informações sobre as VMs da série N, consulte [tamanhos de máquina virtual com otimização de GPU](../linux/sizes-gpu.md).
