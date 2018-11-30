---
title: Extensão de controladores de NVIDIA GPU - VMs do Windows do Azure | Documentos da Microsoft
description: Extensão do Microsoft Azure para instalar controladores de GPU da NVIDIA em série N de computação de VMs que executam o Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: roiyz
ms.openlocfilehash: ee74d4520e867604f50c70f2b6449f12ff3bd8b9
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495968"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Extensão de controladores de NVIDIA GPU para Windows

## <a name="overview"></a>Descrição geral

Esta extensão instala controladores NVIDIA GPU em VMs de série N do Windows. Dependendo da família VM, a instalação da extensão controladores CUDA ou GRADE. Quando instalar NVIDIA os controladores com essa extensão, é abertos ao recebimento e aceita os termos do [contrato de licença de utilizador final NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Durante o processo de instalação, a VM poderá reiniciar para concluir a configuração de controlador.

Uma extensão também está disponível para instalar controladores NVIDIA GPU no [VMs de série N Linux](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão oferece suporte a OSs seguintes:

| Distribuição | Versão |
|---|---|
| Windows 10 | Principal |
| Windows Server 2016 | Principal |
| Windows Server 2012R2 | Principal |

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Propriedades

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.HpcCompute | cadeia |
| tipo | NvidiaGpuDriverWindows | cadeia |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Definições

Todas as definições são opcionais. O comportamento padrão é instalar o controlador mais recente suportado conforme aplicável.

| Nome | Descrição | Valor Predefinido | Valores válidos | Tipo de Dados |
| ---- | ---- | ---- | ---- | ---- |
| driverVersion | NV: Versão do controlador de GRADE<br> NC/ND: Versão do controlador CUDA | mais recente | GRID: "411.81", "391.81", "391.58", "391.03"<br> CUDA: "398.75", "397.44", "390.85" | cadeia |
| installGridND | Instale o controlador de GRADE na série ND VMs | false | VERDADEIRO, FALSO | boolean |

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

O exemplo seguinte Espelha o exemplo acima do ARM e o PowerShell e também adiciona definições personalizadas como um exemplo para a instalação de driver de não-padrão. Especificamente, ele instala um controlador específico de GRADE, mesmo que uma VM da série ND está a ser aprovisionada.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "driverVersion": "391.03",
    "installGridND": true
  }'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com o Azure PowerShell e CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado ao diretório seguinte:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Códigos de erro

| Código de Erro | Significado | Ação possível |
| :---: | --- | --- |
| 0 | Operação bem-sucedida |
| 1 | Operação concluída com êxito. Reinício requerido. |
| 100 | Operação não suportada ou não foi possível concluir. | As causas possíveis: PowerShell versão não suportada, tamanho da VM não é uma VM de série N, falha de transferência de dados. Verifique os ficheiros de registo para determinar a causa do erro. |
| 240, 840 | Tempo limite da operação. | Repita a operação. |
| -1 | Ocorreu uma exceção. | Verifique os ficheiros de registo para determinar a causa de exceção. |
| -5 x | Operação interrompida devido a reinício pendente. | Reinicie a VM. A instalação irá continuar após a reinicialização. Desinstalar deve ser invocada manualmente. |


### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre as extensões, consulte [extensões de Máquina Virtual e funcionalidades para o Windows](features-windows.md).

Para obter mais informações sobre VMs de série N, consulte [tamanhos de máquinas virtuais com otimização de GPU](../windows/sizes-gpu.md).
