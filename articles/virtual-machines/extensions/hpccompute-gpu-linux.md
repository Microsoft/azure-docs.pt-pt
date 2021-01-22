---
title: Extensão do condutor do GPU da NVIDIA - Azure Linux VMs
description: Extensão do Microsoft Azure para instalar os controladores gpu da NVIDIA em VMs de computação da série N que executam o Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.openlocfilehash: a241086e6a590096cf40cbdb7a84838b14889f73
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678293"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Extensão do condutor do GPU da NVIDIA para o Linux

## <a name="overview"></a>Descrição geral

Esta extensão instala controladores GPU NVIDIA em VMs da série Linux N. Dependendo da família VM, a extensão instala os controladores CUDA ou GRID. Ao instalar controladores NVIDIA utilizando esta extensão, está a aceitar e a concordar com os termos do Contrato de [Licença End-User NVIDIA.](https://go.microsoft.com/fwlink/?linkid=874330) Durante o processo de instalação, o VM pode reiniciar para completar a configuração do controlador.

As instruções sobre a instalação manual dos controladores e as versões suportadas atuais estão disponíveis [aqui](../linux/n-series-driver-setup.md).
Uma extensão também está disponível para instalar controladores GPU NVIDIA em [VMs da série N do Windows](hpccompute-gpu-windows.md)N.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão suporta os seguintes distros de SO, dependendo do suporte do condutor para a versão específica do SO.

| Distribuição | Versão |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6, 7.7, 7.8 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6, 7.7, 7.8 |

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
    "type": "NvidiaGpuDriverLinux",
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
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.HpcCompute | string |
| tipo | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1.3 | int |

### <a name="settings"></a>Definições

Todas as configurações são opcionais. O comportamento predefinido é não atualizar o núcleo se não for necessário para a instalação do condutor, instalar o mais recente controlador suportado e o conjunto de ferramentas CUDA (conforme aplicável).

| Nome | Descrição | Valor Predefinido | Valores válidos | Tipo de Dados |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Atualizar o núcleo mesmo que não for necessário para a instalação do condutor | false | TRUE, false | boolean |
| motoristaVersão | NV: Versão do controlador GRID<br> Versão NC/ND: Kit de ferramentas CUDA. Os controladores mais recentes da CUDA escolhida são instalados automaticamente. | mais recente | [Lista](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) de versões de motorista apoiadas | string |
| instalar aCUDA | Instale o kit de ferramentas CUDA. Apenas relevante para vMs da série NC/ND. | true | TRUE, false | boolean |


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
    "type": "NvidiaGpuDriverLinux",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

O exemplo a seguir espelha os exemplos acima do Azure Resource Manager e PowerShell.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

O exemplo a seguir também adiciona duas configurações personalizadas opcionais como exemplo para a instalação não padrão do controlador. Especificamente, atualiza o núcleo de OS para o mais recente e instala um controlador específico de ferramentas CUDA. Mais uma vez, note que as "definições" são opcionais e predefinidos. Note que a atualização do núcleo pode aumentar os tempos de instalação da extensão. Também escolher uma versão específica (mais antiga) do tolkit CUDA pode nem sempre ser compatível com os núcleos mais recentes.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
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

A saída de execução de extensão é registada no seguinte ficheiro. Consulte este ficheiro para acompanhar o estado da instalação (em qualquer longo prazo), bem como para resolver eventuais falhas.

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Códigos de saída

| Código de Saída | Significado | Possível Ação |
| :---: | --- | --- |
| 0 | Operação bem sucedida |
| 1 | Utilização incorreta da extensão | Verifique o registo de saída de execução |
| 10 | Serviços de Integração Linux para Hiper-V e Azure não disponíveis ou instalados | Verifique a saída do ispci |
| 11 | GPU NVIDIA não encontrado neste tamanho VM | Use um [tamanho VM suportado e SO](../linux/n-series-driver-setup.md) |
| 12 | Oferta de imagem não suportada |
| 13 | Tamanho VM não suportado | Use um VM da série N para implementar |
| 14 | Operação mal sucedida | Verifique o registo de saída de execução |


### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões, consulte [extensões e funcionalidades de máquina virtual para Linux.](features-linux.md)

Para obter mais informações sobre VMs da série N, consulte [os tamanhos de máquinas virtuais otimizados da GPU.](../sizes-gpu.md)
