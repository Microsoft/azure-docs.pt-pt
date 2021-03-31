---
title: Extensão do condutor do GPU NVIDIA - Azure Windows VMs
description: Extensão do Microsoft Azure para instalar os Controladores GPU da NVIDIA em VMs de computação da série N que executam o Windows.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: 7cd2c5e54ccb81294a93c0ecebaa174df8d14011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559669"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Extensão do controlador gpu da NVIDIA para windows

## <a name="overview"></a>Descrição Geral

Esta extensão instala controladores GPU NVIDIA em VMs da série N do Windows. Dependendo da família VM, a extensão instala os controladores CUDA ou GRID. Ao instalar controladores NVIDIA utilizando esta extensão, está a aceitar e a concordar com os termos do Contrato de [Licença End-User NVIDIA.](https://go.microsoft.com/fwlink/?linkid=874330) Durante o processo de instalação, o VM pode reiniciar para completar a configuração do controlador.

As instruções sobre a instalação manual dos controladores e as versões suportadas atuais estão disponíveis [aqui](../windows/n-series-driver-setup.md).
Uma extensão também está disponível para instalar controladores GPU NVIDIA em [VMs da série Linux N](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão suporta os seguintes OS:

| Distribuição | Versão |
|---|---|
| Windows 10 | Principal |
| Windows Server 2016 | Principal |
| Windows Server 2012 R2 | Principal |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Microsoft Azure para os controladores gpu da NVIDIA requer que o VM-alvo esteja ligado à internet e tenha acesso.

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
    "typeHandlerVersion": "1.3",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Propriedades

| Name | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| publicador | Microsoft.HpcCompute | string |
| tipo | NvidiaGpuDriverWindows | string |
| typeHandlerVersion | 1.3 | int |


## <a name="deployment"></a>Implementação

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager 

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando se implanta uma ou mais máquinas virtuais que requerem configuração de implantação de posts.

A configuração JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual, ou colocada no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação da configuração JSON afeta o valor do nome e do tipo do recurso. Para obter mais informações, consulte [o nome definido e o tipo para obter recursos para crianças.](../../azure-resource-manager/templates/child-resource-name-type.md) 

O exemplo a seguir pressupõe que a extensão está aninhada dentro do recurso da máquina virtual. Ao nidificar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.

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
    "typeHandlerVersion": "1.3",
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
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
  }'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure, e utilizando a Azure PowerShell e a Azure CLI. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução de extensão é registada no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\
```

### <a name="error-codes"></a>Códigos de erro

| Código de Erro | Significado | Possível Ação |
| :---: | --- | --- |
| 0 | Operação bem sucedida |
| 1 | Operação bem sucedida. Reinicialização necessária. |
| 100 | A operação não foi suportada ou não pôde ser concluída. | Possíveis causas: A versão PowerShell não suportada, o tamanho VM não é um VM da série N, falha em descarregar dados. Verifique os ficheiros de registo para determinar a causa do erro. |
| 240, 840 | Tempo de operação. | Relemisso operação. |
| -1 | A exceção ocorreu. | Verifique os ficheiros de registo para determinar a causa da exceção. |
| -5x | Operação interrompida devido ao reinício pendente. | Reinicie o VM. A instalação continuará após o reinício. Desinstalar deve ser invocado manualmente. |


### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões, consulte [extensões e funcionalidades da máquina Virtual para Windows](features-windows.md).

Para obter mais informações sobre VMs da série N, consulte [os tamanhos de máquinas virtuais otimizados da GPU.](../sizes-gpu.md)
