---
title: Extensão do condutor InfiniBand - Azure Linux VMs
description: Extensão do Microsoft Azure para instalar controladores InfiniBand em VMs de computação da série H e N que executam o Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: eb06f98d1a6e9b76c321e3d202043d656a2d94eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87829055"
---
# <a name="infiniband-driver-extension-for-linux"></a>Extensão do condutor infiniband para Linux

Esta extensão instala controladores InfiniBand OFED em InfiniBand e SÉRIE H ('r' série [H](../sizes-hpc.md) e VMs [da série N](../sizes-gpu.md) que executam o Linux. Dependendo da família VM, a extensão instala os controladores apropriados para o Connect-X NIC.

As instruções sobre a instalação manual dos controladores OFED estão disponíveis [aqui](../workloads/hpc/enable-infiniband.md#manual-installation).

Uma extensão também está disponível para instalar controladores InfiniBand para [VMs Windows](hpc-compute-infiniband-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão suporta os seguintes distros de SO, dependendo do suporte do condutor para a versão específica do SO.

| Distribuição | Versão |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS, 20.04 LTS |
| Linux: CentOS | 7.4, 7.5, 7.6, 7.7, 8.1, 8,2 |
| Linux: Red Hat Enterprise Linux | 7.4, 7.5, 7.6, 7.7, 8.1, 8,2 |

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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Propriedades

| Nome | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.HpcCompute | string |
| tipo | InfiniBandDriverLinux | string |
| typeHandlerVersion | 1.1 | int |



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
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
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
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Adicione a extensão a um conjunto de escala de máquina virtual

O exemplo a seguir instala a mais recente extensão infiniBandDriverLinux da versão 1.1 em todos os VMs capazes de RDMA num conjunto de escala de máquina virtual existente chamado *myVMSS* implantado no grupo de recursos chamado *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
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

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Códigos de saída

A tabela a seguir descreve o significado e a ação recomendada com base nos códigos de saída do processo de instalação de extensão.

| Código de Saída | Significado | Possível Ação |
| :---: | --- | --- |
| 0 | Operação bem sucedida |
| 1 | Utilização incorreta da extensão | Verifique o registo de saída de execução |
| 10 | Serviços de Integração Linux para Hiper-V e Azure não disponíveis ou instalados | Verifique a saída do ispci |
| 11 | Mellanox InfiniBand não encontrado neste tamanho VM | Use um [tamanho VM suportado e SO](../sizes-hpc.md) |
| 12 | Oferta de imagem não suportada |
| 13 | Tamanho VM não suportado | Utilize uma [série H](../sizes-hpc.md) ativada por InfiniBand ('r' série H e VM da série [N](../sizes-gpu.md)para implantar |
| 14 | Operação mal sucedida | Verifique o registo de saída de execução |


### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode arquivar um incidente de suporte através do [site de suporte do Azure.](https://azure.microsoft.com/support/options/) Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os tamanhos infiniband-habilitados para o infiniband), consulte [sérieS H](../sizes-hpc.md) e [VMs da série N.](../sizes-gpu.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre extensões e funcionalidades linux VMs](features-linux.md)