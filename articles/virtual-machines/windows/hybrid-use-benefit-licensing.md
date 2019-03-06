---
title: Benefício híbrido do Azure para o Windows Server | Documentos da Microsoft
description: Saiba como maximizar os benefícios do Windows Software Assurance para colocar licenças no local para o Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: d692eb471c514015271a688e4660700788f1baaa
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431467"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Benefício Híbrido do Azure para o Windows Server
Para clientes com Software Assurance, o Azure híbrido benefício para o Windows Server permite-lhe utilizar as suas licenças do Windows Server no local e executar as máquinas virtuais do Windows no Azure a um custo reduzido. Pode utilizar o Azure híbrido benefício para o Windows Server para implementar novas máquinas virtuais com o SO Windows. Este artigo vai sobre os passos sobre como implementar novas VMs com o Azure híbrido benefício para o Windows Server e como pode atualizar existente VMs em execução. Para obter mais informações sobre o Azure híbrido benefício para o Windows Server poupanças de custos e licenciamento, consulte a [página de licenciamento do Azure híbrido benefício para o Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Cada licença de dois processadores ou cada conjunto de licenças de 16 núcleos são elegíveis para duas instâncias de até oito núcleos ou uma instância de até 16 núcleos. O Benefício Híbrido do Azure para licenças da Edição Standard só pode ser utilizado uma vez, no local ou no Azure. Os benefícios da Datacenter Edition permitem a utilização no local e no Azure em simultâneo.
>

> [!Important]
> Utilizar o Azure híbrido benefício para o Windows Server com todas as VMs a executar o SO do Windows Server agora são suportadas em todas as regiões, incluindo VMs com o software adicional, como o SQL Server ou software de terceiros marketplace. 
>

> [!NOTE]
> Para VMs clássicas, a única implementação nova VM a partir em imagens personalizadas do local é suportado. Para tirar partido das capacidades de suportadas neste artigo, deve primeiro migrar VMs clássicas ao modelo do Resource Manager.
>

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Formas de utilizar o Azure híbrido benefício para o Windows Server
Existem algumas formas de utilizar as máquinas virtuais do Windows com o benefício híbrido do Azure:

1. Pode implementar VMs a partir de uma das imagens do Windows Server fornecidas no Azure Marketplace
2. Pode carregar VMS personalizadas e implementar com um modelo do Resource Manager ou o Azure PowerShell
3. Pode ativar/desativar e converter uma VM existente entre executar com o benefício híbrido do Azure ou paga um custo de sob demanda para o Windows Server
4. Também pode aplicar Azure híbrido benefício para o Windows Server no conjunto também de dimensionamento de máquina virtual


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Criar uma VM com o benefício híbrido do Azure para o Windows Server
Todas as imagens de SO do Windows Server com base são suportadas para Azure híbrido benefício para o Windows Server. Pode utilizar imagens de suporte de plataforma do Azure ou carregar suas próprias imagens personalizadas do Windows Server. 

### <a name="portal"></a>Portal
Para criar uma VM com o Azure híbrido benefício para o Windows Server, utilize o botão de alternar na seção "Poupar dinheiro".

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Modelo
Dentro de modelos do Resource Manager, um parâmetro adicional `licenseType` tem de ser especificado. Pode ler mais sobre [criar modelos do Azure Resource Manager](../../resource-group-authoring-templates.md)
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Converter uma VM existente com o Azure híbrido benefício para o Windows Server
Se tiver uma VM existente que pretende converter para tirar partido do Azure híbrido benefício para o Windows Server, pode atualizar o tipo de licença da VM ao seguir as instruções abaixo.

> [!NOTE]
> Alterar o tipo de licença na VM não faz com que o sistema reiniciar ou fazer com que um interuption de serviço.  É simplesmente uma atualização para um sinalizador de metadados.
> 

### <a name="portal"></a>Portal
No portal do painel da VM, pode atualizar a VM para utilizar o benefício híbrido do Azure ao selecionar a opção de "Configuração" e ativar/desativar a opção "benefício híbrido do Azure"

### <a name="powershell"></a>PowerShell
- Converter VMs existentes do Windows Server em Azure híbrido benefício para o Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Converter VMs do Windows Server com o benefício de volta para pay as you go

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Converter VMs existentes do Windows Server em Azure híbrido benefício para o Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Como verificar a sua VM está a utilizar o benefício de licenciamento
Assim que tiver implementado a sua VM através do PowerShell, o modelo do Resource Manager ou do portal, pode verificar a definição nos métodos seguintes.

### <a name="portal"></a>Portal
No portal do painel da VM, pode ver o botão de alternar para o Azure híbrido benefício para o Windows Server selecionando o separador "Configuração".

### <a name="powershell"></a>PowerShell
O exemplo seguinte mostra o tipo de licença para uma única VM
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Saída:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Esta saída contrasta com a VM seguinte implementada sem licenciamento Azure híbrido benefício para o Windows Server:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Alterar o tipo de licença na VM não faz com que o sistema reiniciar ou fazer com que um interuption de serviço. É uma sinalizador só de licenciamento de metadados.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Listar todas as VMs com o Azure híbrido benefício para o Windows Server numa subscrição
Para ver e contagem de todas as máquinas virtuais implementadas com o Azure híbrido benefício para o Windows Server, pode executar o seguinte comando da sua subscrição:

### <a name="portal"></a>Portal
O Máquina Virtual ou Máquina Virtual dimensionamento conjuntos de recursos no painel de, pode ver uma lista de todas as suas VM (s) e o tipo de licenciamento, configurando a coluna da tabela para incluir o "Benefício híbrido do Azure". A definição de VM pode estar em "Ativada", "Não está ativada" ou "Não suportado" Estado.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Implementar um conjunto de dimensionamento de Máquina Virtual com o benefício híbrido do Azure para o Windows Server
Dentro de sua máquina virtual do conjunto de dimensionamento de modelos do Resource Manager, um parâmetro adicional `licenseType` tem de ser especificada dentro de sua propriedade VirtualMachineProfile. Pode fazê-lo durante a criação ou atualização para o seu conjunto com o modelo de ARM do Powershell, CLI do Azure ou REST de dimensionamento.

O exemplo seguinte utiliza o modelo ARM com uma imagem do Windows Server 2016 Datacenter:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Também pode saber mais sobre como [modificar um conjunto de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) para obter mais maneiras de atualizar seu dimensionamento definido.

## <a name="next-steps"></a>Passos Seguintes
- Leia mais sobre [como poupar dinheiro com o benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Leia mais sobre [perguntas mais frequentes sobre o benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Saiba mais sobre [Azure híbrido benefício para o Windows Server orientações detalhadas de licenciamento](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Saiba mais sobre [Azure híbrido benefício para o Windows Server e o Azure Site Recovery fazem migração de aplicativos para o Azure ainda mais económico](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Saiba mais sobre [Windows 10 no Azure com o direito de alojamento multi-inquilino](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Saiba mais sobre [modelos usando o Resource Manager](../../azure-resource-manager/resource-group-overview.md)
