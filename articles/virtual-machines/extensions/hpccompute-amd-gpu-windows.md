---
title: Extensão do controlador GPU da AMD - Azure Windows VMs
description: Extensão do Microsoft Azure para instalar condutores DE GPU AMD em VMs da série NVv4 que executam o Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/10/2020
ms.author: vikancha
ms.openlocfilehash: e0a5074061b190088441ed4f6787b112fb11fe5a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966008"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>Extensão do controlador GPU da AMD para Windows

Este artigo fornece uma visão geral da extensão VM para implantar controladores GPU AMD em VMs [da série Windows NVv4.](../nvv4-series.md) Ao instalar controladores AMD usando esta extensão, está a aceitar e a concordar com os termos do Contrato de [Licença End-User AMD](https://amd.com/radeonsoftwarems). Durante o processo de instalação, o VM pode reiniciar para completar a configuração do controlador.

As instruções sobre a instalação manual dos controladores e as versões suportadas atuais estão disponíveis [aqui](../windows/n-series-amd-driver-setup.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão suporta os seguintes OS:

| Distribuição | Versão |
|---|---|
| Windows 10 EMS | Construir 1903 |
| Windows 10 | Construir 1809 |
| Windows Server 2016 | Principal |
| Windows Server 2019 | Principal |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Microsoft Azure para os controladores de GPU da AMD requer que o VM alvo esteja ligado à internet e tenha acesso.

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Propriedades

| Nome | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| publicador | Microsoft.HpcCompute | string |
| tipo | AmdGpuDriverWindows | string |
| typeHandlerVersion | 1,0 | int |


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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
    -ExtensionName "AmdGpuDriverWindows" `
    -ExtensionType "AmdGpuDriverWindows" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name AmdGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
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
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
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
