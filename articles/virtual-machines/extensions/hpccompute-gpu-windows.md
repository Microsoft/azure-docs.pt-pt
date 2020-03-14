---
title: Extensão do condutor da GPU da NVIDIA - VMs do Windows Azure
description: Extensão Microsoft Azure para instalar controladores GPU NVIDIA em VMs computacionais da série N executando Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: c388f433327b5328483f10fbef637a6fdfd08832
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250546"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Extensão do condutor da GPU da NVIDIA para Windows

## <a name="overview"></a>Descrição geral

Esta extensão instala controladores GPU da NVIDIA em VMs da série N do Windows. Dependendo da família VM, a extensão instala os condutores CUDA ou GRID. Quando instala os controladores NVIDIA utilizando esta extensão, está a aceitar e a concordar com os termos do Contrato de Licença de Utilizador Final da [NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Durante o processo de instalação, o VM pode reiniciar para completar a configuração do controlador.

As instruções sobre a instalação manual dos controladores e as versões suportadas atuais estão disponíveis [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup).
Uma extensão também está disponível para instalar os pilotos gpu da NVIDIA em [VMs da série N Linux](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão suporta as seguintes OSs:

| Distribuição | Versão |
|---|---|
| Windows 10 | Core |
| Windows Server 2016 | Core |
| Windows Server 2012 R2 | Core |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão Microsoft Azure para os controladores GPU da NVIDIA requer que o VM alvo esteja ligado à internet e tenha acesso.

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão.

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
| publicador | Microsoft.HpcCompute | Cadeia de caracteres |
| tipo | NvidiaGpuDriverWindows | Cadeia de caracteres |
| typeHandlerVersion | 1.2 | int |


## <a name="deployment"></a>Implementação

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager 

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais ao implantar uma ou mais máquinas virtuais que requerem a configuração da implementação do post.

A configuração do JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual ou colocada na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação da configuração do JSON afeta o valor do tipo e nome do recurso. Para mais informações, consulte o nome e o [tipo de definição para os recursos infantis.](../../azure-resource-manager/resource-manager-template-child-resource.md) 

O exemplo que se segue pressupõe que a extensão está aninhada dentro do recurso virtual da máquina. Ao nidificar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.

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
Set-AzVMExtension
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

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolver Problemas

Os dados sobre o estado das implementações de extensões podem ser recuperados a partir do portal Azure e utilizando o Azure PowerShell e o Azure CLI. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução de extensão é registada no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Códigos de erro

| Código de Erro | Significado | Ação possível |
| :---: | --- | --- |
| 0 | Operação bem sucedida |
| 1 | Operação bem sucedida. Reiniciar necessário. |
| 100 | Operação não suportada ou não pôde ser concluída. | Possíveis causas: Versão PowerShell não suportada, tamanho VM não é um VM da série N, falha de dados de descarregamento. Verifique os ficheiros de registo para determinar a causa do erro. |
| 240, 840 | Intervalo de funcionamento. | Operação de retry. |
| -1 | A exceção ocorreu. | Verifique os ficheiros de registo para determinar a causa da exceção. |
| -5x | Operação interrompida devido a reinicialização pendente. | Reiniciar VM. A instalação continuará após o reboot. A desinstalação deve ser invocada manualmente. |


### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre extensões, consulte [extensões e funcionalidades da máquina Virtual para Windows](features-windows.md).

Para obter mais informações sobre VMs da série N, consulte [gpu otimizado tamanhos](../windows/sizes-gpu.md)de máquinavirtual .
