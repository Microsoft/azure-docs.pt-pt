---
title: Benefício Híbrido do Azure para o Windows Server
description: Saiba como maximizar os benefícios da Garantia de Software do Windows para levar licenças no local para o Azure
author: xujing-ms
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: f84d4fcd85f1e718f414e63bbe76fd29fa32427d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869572"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Benefício Híbrido do Azure para o Windows Server
Para clientes com Garantia de Software, o Azure Hybrid Benefit para o Windows Server permite-lhe utilizar as suas licenças de Windows Server no local e executar máquinas virtuais Windows no Azure a um custo reduzido. Pode utilizar o Azure Hybrid Benefit para o Windows Server para implementar novas máquinas virtuais com o Windows OS. Este artigo vai ao longo das etapas sobre como implementar novos VMs com O Benefício Híbrido Azure para o Windows Server e como pode atualizar os VMs existentes. Para obter mais informações sobre o Benefício Híbrido Azure para licenciamento do Windows Server e poupança de custos, consulte a página de licenciamento Do Servidor [Híbrido Azure para o Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Cada licença de 2 processadores ou cada conjunto de licenças de 16 núcleos tem direito a duas instâncias de até 8 núcleos, ou um caso de até 16 núcleos. As licenças Azure Hybrid Benefit for Standard Edition só podem ser utilizadas uma vez no local ou no Azure. Os benefícios da Datacenter Edition permitem uma utilização simultânea tanto no local como no Azure.

A utilização do Benefício Híbrido Azure para o Windows Server com quaisquer VMs que executem o Sistema operativo Windows Server OS são agora suportados em todas as regiões, incluindo VMs com software adicional, como o SQL Server ou software de marketplace de terceiros. 


## <a name="classic-vms"></a>VMs clássicas

Para vMs clássicos, apenas é suportada a implementação de novos VM a partir de instalações imagens personalizadas. Para tirar partido das capacidades suportadas neste artigo, tem primeiro de migrar VMs clássicos para o modelo De Gestor de Recursos.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Formas de utilizar o Benefício Híbrido Azure para o Servidor Windows
Existem poucas formas de utilizar máquinas virtuais do Windows com o Benefício Híbrido Azure:

1. Pode implementar VMs a partir de uma das imagens fornecidas pelo Windows Server no Mercado Azure
2. Você pode carregar um VM personalizado e implementar usando um modelo de Gestor de Recursos ou Azure PowerShell
3. Pode alternar e converter VM existente entre correr com o Azure Hybrid Benefit ou pagar o custo a pedido do Windows Server
4. Também pode aplicar o Benefício Híbrido Azure para o Windows Server no conjunto de escala de máquinas virtuais


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Crie um VM com benefício híbrido Azure para o Servidor Windows
Todas as imagens baseadas em Sistema operativo Windows são suportadas para o Benefício Híbrido Azure para o Windows Server. Pode utilizar imagens de suporte da plataforma Azure ou carregar as suas próprias imagens personalizadas do Windows Server. 

### <a name="portal"></a>Portal
Para criar um VM com Benefício Híbrido Azure para o Windows Server, utilize o alternância sob a secção "Economizar dinheiro".

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
Dentro dos seus modelos de `licenseType` Gestor de Recursos, deve especificar-se um parâmetro adicional. Você pode ler mais sobre a autoria de modelos de [Gestor de Recursos Azure](../../resource-group-authoring-templates.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Converter um VM existente utilizando o Benefício Híbrido Azure para o Servidor Windows
Se tiver um VM existente que gostaria de converter para tirar partido do Benefício Híbrido Azure para o Windows Server, pode atualizar o tipo de licença do seu VM seguindo as instruções abaixo.

> [!NOTE]
> Alterar o tipo de licença no VM não faz com que o sistema reinicie ou cause uma inerupção de serviço.  É simplesmente uma atualização para uma bandeira de metadados.
> 

### <a name="portal"></a>Portal
A partir da lâmina VM do portal, pode atualizar o VM para utilizar o Benefício Híbrido Azure, selecionando a opção "Configuração" e alternando a opção "Benefício Híbrido Azure"

### <a name="powershell"></a>PowerShell
- Converter vMs de servidor windows existentes para benefício híbrido Azure para servidor windows

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Converter VMs do Windows Server com benefício de volta para pay-as-you-go

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Converter vMs de servidor windows existentes para benefício híbrido Azure para servidor windows

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Como verificar se o seu VM está a utilizar o benefício de licenciamento
Uma vez implementado o seu VM através do modelo PowerShell, Do Gestor de Recursos ou portal, pode verificar a definição nos seguintes métodos.

### <a name="portal"></a>Portal
A partir da lâmina VM do portal, pode ver o toggle para O Benefício Híbrido Azure para o Servidor Windows, selecionando o separador "Configuração".

### <a name="powershell"></a>PowerShell
O exemplo que se segue mostra o tipo de licença para um único VM
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Saída:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Esta saída contrasta com o seguinte VM implantado sem o Benefício Híbrido Azure para o licenciamento do Windows Server:
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
> Alterar o tipo de licença no VM não faz com que o sistema reinicie ou cause uma inerupção de serviço. É apenas uma bandeira de licenciamento de metadados.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Listar todos os VMs com Benefício Híbrido Azure para o Windows Server numa subscrição
Para ver e contar todas as máquinas virtuais implementadas com o Azure Hybrid Benefit para o Windows Server, pode executar o seguinte comando a partir da sua subscrição:

### <a name="portal"></a>Portal
A partir da máquina virtual ou da escala virtual de conjuntos de recursos, você pode ver uma lista de todos os seus VM(s) e tipo de licenciamento configurando a coluna de tabela para incluir "Azure Hybrid Benefit". A definição de VM pode estar em estado "Ativado", "Não ativado" ou "Não suportado".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Implemente um conjunto de escala de máquina virtual com benefício híbrido azure para o servidor do Windows
Dentro dos modelos de Gestor de Recursos `licenseType` da escala virtual da máquina, deve ser especificado um parâmetro adicional dentro da sua propriedade VirtualMachineProfile. Pode fazê-lo durante a criação ou atualização para a sua escala definida através do modelo ARM, PowerShell, Azure CLI ou REST.

O exemplo seguinte utiliza o modelo ARM com uma imagem do Datacenter do Windows Server 2016:
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
- Leia mais sobre [como economizar dinheiro com o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Ler mais sobre [perguntas frequentes para o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Saiba mais sobre [o Benefício Híbrido Azure para o licenciamento de](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit) licenças do Windows Server
- Saiba mais sobre [o Benefício Híbrido Azure para o Windows Server e a Recuperação do Site Azure tornam as aplicações migratórias para o Azure ainda mais rentáveis](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Saiba mais sobre [o Windows 10 em Azure com o Direito de Alojamento Multiarrendatário](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Saiba mais sobre [usar modelos de Gestor de Recursos](../../azure-resource-manager/management/overview.md)
