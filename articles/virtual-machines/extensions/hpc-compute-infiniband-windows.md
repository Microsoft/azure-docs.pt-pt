---
title: Extensão do controlador InfiniBand - Azure Windows VMs
description: Extensão do Microsoft Azure para instalar controladores InfiniBand em VMs de computação da série H e N que executam o Windows.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: amverma
ms.openlocfilehash: 81720c18ce858cd5667413a9d39afdadc95acb23
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559703"
---
# <a name="infiniband-driver-extension-for-windows"></a>Extensão do controlador infiniband para windows

Esta extensão instala controladores InfiniBand ND (para condutores não-SR-IOV habilitados) e condutores OFED (para os tamanhos SR-IOV) ('r' tamanhos) [série H](../sizes-hpc.md) e VMs da [série N](../sizes-gpu.md) que executam o Windows. Dependendo da família VM, a extensão instala os controladores apropriados para o Connect-X NIC.

Uma extensão também está disponível para instalar controladores InfiniBand para [Os VMs Linux.](hpc-compute-infiniband-linux.md)

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão suporta os seguintes distros de SO, dependendo do suporte do condutor para a versão específica do SO. Note o NIC InfiniBand apropriado para os tamanhos de interesse da série H e N.

| Distribuição | Condutores de NIC InfiniBand |
|---|---|
| Windows 10 | CX5, CX6 |
| Windows Server 2019 | CX5, CX6 |
| Windows Server 2016 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 R2 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 | CX3-Pro, CX5, CX6 |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão do Microsoft Azure para controladores InfiniBand requer que o VM alvo esteja ligado e tenha acesso à internet.

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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
| tipo | InfiniBandDriverWindows | string |
| typeHandlerVersion | 1.2 | int |



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
    "type": "InfiniBandDriverWindows",
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
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Adicione a extensão a um conjunto de escala de máquina virtual

O exemplo a seguir instala a mais recente extensão 1.2 InfiniBandDriverWindows em todos os VMs capazes de RDMA num conjunto de escala de máquina virtual existente chamado *myVMSS* implantado no grupo de recursos chamado *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
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

A saída de execução de extensão é registada no seguinte ficheiro. Consulte este ficheiro para acompanhar o estado da instalação, bem como para resolver eventuais falhas.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Códigos de saída

A tabela a seguir descreve o significado e a ação recomendada com base nos códigos de saída do processo de instalação de extensão.

| Código de Erro | Significado | Possível Ação |
| :---: | --- | --- |
| 0 | Operação bem sucedida |
| 3010 | Operação bem sucedida. Reinicialização necessária. |
| 100 | A operação não foi suportada ou não pôde ser concluída. | Possíveis causas: A versão PowerShell não suportada, o tamanho VM não é um VM ativado por InfiniBand, falha nos dados de descarregamento. Verifique os ficheiros de registo para determinar a causa do erro. |
| 240, 840 | Tempo de operação. | Relemisso operação. |
| -1 | A exceção ocorreu. | Verifique os ficheiros de registo para determinar a causa da exceção. |

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode arquivar um incidente de suporte através do [site de suporte do Azure.](https://azure.microsoft.com/support/options/) Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os tamanhos infiniband-habilitados para o infiniband), consulte [sérieS H](../sizes-hpc.md) e [VMs da série N.](../sizes-gpu.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre extensões e funcionalidades linux VMs](features-linux.md)
