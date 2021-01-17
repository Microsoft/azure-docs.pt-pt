---
title: Benefício Híbrido do Azure para o Windows Server
description: Saiba como maximizar os benefícios da garantia do software do Windows para levar licenças no local ao Azure.
author: xujing-ms
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: c13203c076378e1ff8f213971466eb5f63dfc4f4
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539166"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Benefício Híbrido do Azure para o Windows Server
Para clientes com Garantia de Software, o Azure Hybrid Benefit for Windows Server permite-lhe utilizar as licenças do Windows Server no local e executar máquinas virtuais do Windows no Azure a um custo reduzido. Pode utilizar o Azure Hybrid Benefit para o Windows Server para implementar novas máquinas virtuais com o Windows OS. Este artigo percorre os passos sobre como implementar novos VMs com Azure Hybrid Benefit para o Windows Server e como pode atualizar os VMs existentes em execução. Para obter mais informações sobre o Benefício Híbrido Azure para licenciamento e poupança de custos do Windows Server, consulte a [página de licenciamento Azure Hybrid Benefit for Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Cada licença de 2 processadores ou cada conjunto de licenças de 16 núcleos tem direito a duas instâncias de até 8 núcleos, ou uma instância de até 16 núcleos. O Benefício Híbrido Azure para as licenças Standard Edition só pode ser usado uma vez no local ou em Azure. Os benefícios da Datacenter Edition permitem uma utilização simultânea tanto no local como no Azure.

A utilização do Azure Hybrid Benefit para o Windows Server com quaisquer VMs em execução do Windows Server OS são agora suportadas em todas as regiões, incluindo VMs com software adicional, como o SQL Server ou software de marketplace de terceiros. 


## <a name="classic-vms"></a>VMs clássicas

Para os VM clássicos, apenas a implementação de novos VM a partir de imagens personalizadas no local é suportada. Para tirar partido das capacidades suportadas neste artigo, tem primeiro de migrar VMs clássicos para o modelo Resource Manager.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Formas de utilizar o Benefício Híbrido Azure para o Servidor windows
Existem poucas formas de utilizar máquinas virtuais do Windows com o Benefício Híbrido Azure:

1. Pode implementar VMs a partir de uma das imagens fornecidas do Windows Server no Azure Marketplace
2. Você pode carregar um VM personalizado e implementar usando um modelo de Gestor de Recursos ou Azure PowerShell
3. Pode alternar e converter VM existente entre correr com O Benefício Híbrido Azure ou pagar o custo a pedido do Windows Server
4. Também pode aplicar O Benefício Híbrido Azure para o Servidor do Windows em conjunto de escala de máquina virtual


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Criar um VM com benefício híbrido Azure para o Servidor windows
Todas as imagens baseadas no Windows Server OS são suportadas para O Benefício Híbrido Azure para o Servidor do Windows. Pode utilizar imagens de suporte à plataforma Azure ou fazer upload das suas próprias imagens personalizadas do Windows Server. 

### <a name="portal"></a>Portal
Para criar um VM com benefício híbrido Azure para o Servidor do Windows, percorra para a parte inferior do separador **Basics** durante o processo de criação e sob **licenciamento** verifique a caixa para utilizar uma licença existente do Windows Server. 

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
Dentro dos modelos do Gestor de Recursos, deve ser especificado um parâmetro `licenseType` adicional. Você pode ler mais sobre [a autoria de modelos de Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Converter um VM existente utilizando o Benefício Híbrido Azure para o Servidor do Windows
Se tiver um VM existente que gostaria de converter para tirar partido do Benefício Híbrido Azure para o Windows Server, pode atualizar o tipo de licença do seu VM seguindo as instruções abaixo.

> [!NOTE]
> Alterar o tipo de licença no VM não faz com que o sistema reinicie ou cause uma interligação de serviço.  É simplesmente uma atualização para uma bandeira de metadados.
> 

### <a name="portal"></a>Portal
A partir da lâmina VM do portal, pode atualizar o VM para utilizar o Benefício Híbrido Azure selecionando a opção "Configuração" e alternando a opção "Benefício híbrido Azure"

### <a name="powershell"></a>PowerShell
- Converter VMs existentes no Servidor do Windows para benefício híbrido Azure para o Servidor do Windows

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Converter VMs do Servidor do Windows com benefício de volta para pay-as-you-go

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Converter VMs existentes no Servidor do Windows para benefício híbrido Azure para o Servidor do Windows

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Como verificar se o seu VM está a utilizar o benefício de licenciamento
Uma vez implantado o seu VM através do modelo PowerShell, Resource Manager ou portal, pode verificar a definição nos seguintes métodos.

### <a name="portal"></a>Portal
A partir da lâmina VM do portal, pode ver o toggle para Azure Hybrid Benefit para o Windows Server selecionando o separador "Configuração".

### <a name="powershell"></a>PowerShell
O exemplo a seguir mostra o tipo de licença para um único VM
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Resultado:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Esta saída contrasta com o seguinte VM implantado sem benefício híbrido Azure para o licenciamento do Windows Server:
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
> Alterar o tipo de licença no VM não faz com que o sistema reinicie ou cause uma interligação de serviço. É apenas uma bandeira de licenciamento de metadados.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Listar todos os VMs com benefício híbrido Azure para o Servidor do Windows numa subscrição
Para ver e contar todas as máquinas virtuais implementadas com O Benefício Híbrido Azure para o Windows Server, pode executar o seguinte comando a partir da sua subscrição:

### <a name="portal"></a>Portal
A partir da máquina virtual ou da balança de máquina virtual, pode ver uma lista de todos os seus VM e tipo de licenciamento, configurando a coluna de tabela para incluir "Benefício Híbrido Azure". A definição de VM pode ser ativada, "Não ativada" ou "não suportada".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Implemente um conjunto de balança de máquina virtual com benefício híbrido Azure para servidor do Windows
Dentro dos modelos de gestor de recursos definidos em escala de máquina virtual, deve ser especificado um parâmetro adicional `licenseType` dentro da sua propriedade VirtualMachineProfile. Pode fazê-lo durante a criação ou atualização da sua escala definida através do modelo ARM, PowerShell, Azure CLI ou REST.

O exemplo a seguir utiliza o modelo ARM com uma imagem do Datacenter 2016 do Windows Server:
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
Também pode aprender mais sobre como [modificar uma escala de máquina virtual definida](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) para mais formas de atualizar o seu conjunto de escala.

## <a name="next-steps"></a>Passos seguintes
- Leia mais sobre [Como economizar dinheiro com o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Leia mais sobre [perguntas frequentes para benefício híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Saiba mais sobre [o Azure Hybrid Benefit para o Windows Server licenciando orientação detalhada](/windows-server/get-started/azure-hybrid-benefit)
- Saiba mais sobre [o Benefício Híbrido Azure para o Windows Server e Azure Site Recovery tornam as aplicações migratórias para o Azure ainda mais rentáveis](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Saiba mais sobre [o Windows 10 no Azure com o Multitenant Hosting Right](./windows-desktop-multitenant-hosting-deployment.md)
- Saiba mais sobre [a utilização de modelos de Gestor de Recursos](../../azure-resource-manager/management/overview.md)
