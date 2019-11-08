---
title: Benefício Híbrido do Azure para Windows Server | Microsoft Docs
description: Saiba como maximizar seus benefícios do Windows Software Assurance para trazer licenças locais para o Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: 1c16ebe1f0b07e5ee5ef73dc3dd4781161f934b6
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749381"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Benefício Híbrido do Azure para o Windows Server
Para clientes com Software Assurance, o Benefício Híbrido do Azure para Windows Server permite que você use suas licenças locais do Windows Server e execute máquinas virtuais do Windows no Azure a um custo reduzido. Você pode usar Benefício Híbrido do Azure para Windows Server para implantar novas máquinas virtuais com o sistema operacional Windows. Este artigo apresenta as etapas sobre como implantar novas VMs com o Benefício Híbrido do Azure para o Windows Server e como você pode atualizar as VMs em execução existentes. Para obter mais informações sobre Benefício Híbrido do Azure para licenciamento e economia de custo do Windows Server, consulte a [página benefício híbrido do Azure para licenciamento do Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Cada licença de dois processadores ou cada conjunto de licenças de 16 núcleos são elegíveis para duas instâncias de até oito núcleos ou uma instância de até 16 núcleos. O Benefício Híbrido do Azure para licenças da Edição Standard só pode ser utilizado uma vez, no local ou no Azure. Os benefícios da Datacenter Edition permitem a utilização no local e no Azure em simultâneo.
>

> [!Important]
> O uso do Benefício Híbrido do Azure para Windows Server com qualquer VM que executa o sistema operacional Windows Server agora tem suporte em todas as regiões, incluindo VMs com software adicional, como SQL Server ou software do Marketplace de terceiros. 
>

> [!NOTE]
> Para VMs clássicas, há suporte apenas para implantar uma nova VM a partir de imagens personalizadas locais. Para aproveitar os recursos com suporte neste artigo, você deve primeiro migrar VMs clássicas para o modelo do Resource Manager.
>

 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Maneiras de usar Benefício Híbrido do Azure para o Windows Server
Há algumas maneiras de usar máquinas virtuais do Windows com o Benefício Híbrido do Azure:

1. Você pode implantar VMs de uma das imagens do Windows Server fornecidas no Azure Marketplace
2. Você pode carregar uma VM personalizada e implantá-la usando um modelo do Resource Manager ou Azure PowerShell
3. Você pode alternar e converter a VM existente entre a execução com Benefício Híbrido do Azure ou pagar o custo sob demanda para o Windows Server
4. Você também pode aplicar Benefício Híbrido do Azure para o Windows Server no conjunto de dimensionamento de máquinas virtuais também


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Criar uma VM com o Benefício Híbrido do Azure para Windows Server
Todas as imagens baseadas no sistema operacional Windows Server têm suporte para o Benefício Híbrido do Azure para Windows Server. Você pode usar imagens de suporte da plataforma Azure ou carregar suas próprias imagens personalizadas do Windows Server. 

### <a name="portal"></a>Portal
Para criar uma VM com o Benefício Híbrido do Azure para Windows Server, use a alternância na seção "economizar dinheiro".

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
Em seus modelos do Resource Manager, um parâmetro adicional `licenseType` deve ser especificado. Você pode ler mais sobre a [criação de modelos de Azure Resource Manager](../../resource-group-authoring-templates.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Converter uma VM existente usando o Benefício Híbrido do Azure para Windows Server
Se você tiver uma VM existente que deseja converter para tirar proveito do Benefício Híbrido do Azure para o Windows Server, poderá atualizar o tipo de licença da VM seguindo as instruções abaixo.

> [!NOTE]
> A alteração do tipo de licença na VM não faz com que o sistema seja reinicializado ou cause um interuption de serviço.  É simplesmente uma atualização para um sinalizador de metadados.
> 

### <a name="portal"></a>Portal
Na folha VM do portal, você pode atualizar a VM para usar Benefício Híbrido do Azure selecionando a opção "configuração" e alternando a opção "benefício híbrido do Azure"

### <a name="powershell"></a>PowerShell
- Converter VMs do Windows Server existentes em Benefício Híbrido do Azure para o Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Converta VMs do Windows Server com o benefício de volta para o pré-pago

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Converter VMs do Windows Server existentes em Benefício Híbrido do Azure para o Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Como verificar se sua VM está utilizando o benefício de licenciamento
Depois de implantar sua VM por meio do PowerShell, do modelo do Resource Manager ou do portal, você pode verificar a configuração nos métodos a seguir.

### <a name="portal"></a>Portal
Na folha VM do portal, você pode exibir a alternância para Benefício Híbrido do Azure do Windows Server selecionando a guia "configuração".

### <a name="powershell"></a>PowerShell
O exemplo a seguir mostra o tipo de licença para uma única VM
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Saída:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Essa saída contrasta com a seguinte VM implantada sem Benefício Híbrido do Azure para licenciamento do Windows Server:
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
> A alteração do tipo de licença na VM não faz com que o sistema seja reinicializado ou cause um interuption de serviço. É apenas um sinalizador de licenciamento de metadados.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Listar todas as VMs com Benefício Híbrido do Azure para Windows Server em uma assinatura
Para ver e contar todas as máquinas virtuais implantadas com o Benefício Híbrido do Azure para Windows Server, você pode executar o seguinte comando em sua assinatura:

### <a name="portal"></a>Portal
Na folha de recursos da máquina virtual ou dos conjuntos de dimensionamento de máquinas virtuais, você pode exibir uma lista de todas as VMs e o tipo de licenciamento Configurando a coluna da tabela para incluir "Benefício Híbrido do Azure". A configuração de VM pode estar no estado "habilitado", "não habilitado" ou "sem suporte".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Implantar um conjunto de dimensionamento de máquinas virtuais com o Benefício Híbrido do Azure para Windows Server
Em seus modelos do Gerenciador de recursos do conjunto de dimensionamento de máquinas virtuais, um parâmetro adicional `licenseType` deve ser especificado dentro de sua propriedade VirtualMachineProfile. Você pode fazer isso durante criar ou atualizar para seu conjunto de dimensionamento por meio do modelo ARM, PowerShell, CLI do Azure ou REST.

O exemplo a seguir usa o modelo ARM com uma imagem do Windows Server 2016 datacenter:
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
Você também pode aprender mais sobre como [modificar um conjunto de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) para obter mais maneiras de atualizar seu conjunto de dimensionamento.

## <a name="next-steps"></a>Passos seguintes
- Leia mais sobre [como economizar dinheiro com o benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Leia mais sobre as perguntas frequentes [para benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Saiba mais sobre o [benefício híbrido do Azure para obter orientações detalhadas sobre licenciamento do Windows Server](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Saiba mais sobre o [benefício híbrido do Azure para o Windows Server e Azure site Recovery tornar a migração de aplicativos para o Azure ainda mais](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/) econômica
- Saiba mais sobre o [Windows 10 no Azure com o direito de hospedagem multilocatário](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Saiba mais sobre como [usar modelos do Resource Manager](../../azure-resource-manager/resource-group-overview.md)
