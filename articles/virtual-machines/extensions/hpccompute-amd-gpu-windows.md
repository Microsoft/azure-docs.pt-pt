---
title: Extensão do condutor DA GPU da AMD - VMs Do Windows Azure
description: Extensão Microsoft Azure para instalar controladores GPU AMD em VMs da série NVv4 executando Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vikancha
manager: jkabat
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/10/2020
ms.author: vikancha
ms.openlocfilehash: 0819eec5275fa9783fff363cabbd8c3ed22b7cd6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119557"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>Extensão do condutor da GPU da AMD para Windows

Este artigo fornece uma visão geral da extensão VM para implementar controladores GPU AMD em VMs da série Windows [NVv4.](https://docs.microsoft.com/azure/virtual-machines/nvv4-series) Quando instala os controladores AMD utilizando esta extensão, está a aceitar e a concordar com os termos do Contrato de [Licença de Utilizador Final da AMD](https://amd.com/radeonsoftwarems). Durante o processo de instalação, o VM pode reiniciar para completar a configuração do controlador.

As instruções sobre a instalação manual dos controladores e as versões suportadas atuais estão disponíveis [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão suporta as seguintes OSs:

| Distribuição | Versão |
|---|---|
| Windows 10 EMS | Construir 1903 |
| Windows 10 | Construir 1809 |
| Windows Server 2016 | Principal |
| Windows Server 2019 | Principal |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Microsoft Azure para controladores GPU DA AMD requer que o VM alvo esteja ligado à internet e tenha acesso.

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
| tipo | AmdGpuDriverWindows | string |
| typeHandlerVersion | 1.0 | int |


## <a name="deployment"></a>Implementação

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager 

As extensões VM azure podem ser implantadas com modelos de Gestor de Recursos Azure. Os modelos são ideais ao implantar uma ou mais máquinas virtuais que requerem a configuração da implementação do post.

A configuração JSON para uma extensão virtual da máquina pode ser aninhada dentro do recurso virtual da máquina, ou colocada no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação da configuração JSON afeta o valor do nome e do tipo de recursos. Para mais informações, consulte o nome e o [tipo de definição para os recursos infantis.](../../azure-resource-manager/resource-manager-template-child-resource.md) 

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

| Código de Erro | Significado | Ação Possível |
| :---: | --- | --- |
| 0 | Operação bem sucedida |
| 1 | Operação bem sucedida. Reiniciar necessário. |
| 100 | Operação não suportada ou não pôde ser concluída. | Possíveis causas: Versão PowerShell não suportada, tamanho VM não é um VM da série N, falha de dados de descarregamento. Verifique os ficheiros de registo para determinar a causa do erro. |
| 240, 840 | Intervalo de funcionamento. | Operação de retry. |
| -1 | A exceção ocorreu. | Verifique os ficheiros de registo para determinar a causa da exceção. |
| -5x | Operação interrompida devido a reinicialização pendente. | Reiniciar VM. A instalação continuará após o reboot. A desinstalação deve ser invocada manualmente. |


### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões, consulte [extensões e funcionalidades da máquina Virtual para Windows](features-windows.md).

Para obter mais informações sobre VMs da série N, consulte [gpu otimizado tamanhos](../windows/sizes-gpu.md)de máquinavirtual .
