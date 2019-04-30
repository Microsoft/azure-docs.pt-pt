---
title: Implementar uma aplicação a um conjunto de dimensionamento de máquina virtual do Azure | Documentos da Microsoft
description: Saiba como implementar aplicações para Linux e Windows instâncias da máquina virtual num conjunto de dimensionamento
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 09145612821cb669e26e3ccb8d15611112eca700
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618630"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implementar a sua aplicação em conjuntos de dimensionamento de máquina virtual

Para executar aplicações em instâncias de máquina virtual (VM) num conjunto de dimensionamento, primeiro tem de instalar os componentes da aplicação e os ficheiros necessários. Este artigo apresenta as formas de criar uma imagem VM personalizada para as instâncias numa escala definido ou executar automaticamente scripts de instalação nas instâncias VM existentes. Também irá aprender a gerir aplicações ou atualizações de sistema operacional num conjunto de dimensionamento.


## <a name="build-a-custom-vm-image"></a>Criar uma imagem VM personalizada
Quando utilizar uma das imagens da plataforma do Azure para criar as instâncias no seu conjunto de dimensionamento, nenhum software adicional está instalado ou configurado. Pode automatizar a instalação destes componentes, no entanto que adiciona ao tempo necessário para aprovisionar instâncias de VM para seus conjuntos de dimensionamento. Se aplicar muitas alterações de configuração para as instâncias de VM, há a gestão de custos gerais com esses scripts de configuração e tarefas.

Para reduzir a gestão de configuração e a hora para Aprovisionar uma VM, pode criar uma imagem VM personalizada que está pronta para executar a sua aplicação, assim como uma instância é aprovisionada no conjunto de dimensionamento. Para obter mais informações sobre como criar e utilizar uma imagem VM personalizada com uma escala definido, veja os tutoriais seguintes:

- [CLI do Azure](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Instalar uma aplicação com a extensão de Script personalizado
A Extensão de Script Personalizado transfere e executa scripts em VMs do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. Para obter mais informações sobre como criar e utilizar uma imagem VM personalizada com uma escala definido, veja os tutoriais seguintes:

- [CLI do Azure](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Modelo do Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalar uma aplicação para um VM com o PowerShell DSC do Windows
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) é uma plataforma de gestão para definir a configuração de máquinas de destino. Configurações de DSC definem o que instalar num computador e como configurar o anfitrião. Um mecanismo de Gestor de configuração Local (LCM) é executado em cada nó de destino que processa as ações pedidas com base nas configurações enviada por push.

A extensão de PowerShell DSC permite-lhe personalizar a instâncias de VM no conjunto de dimensionamento com o PowerShell. O exemplo seguinte:

- Instrui as instâncias VM para transferir um pacote de DSC a partir do GitHub- *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Define a extensão para executar um script de instalação- `configure-http.ps1`
- Obtém informações sobre um conjunto de dimensionamento com [Get AzVmss](/powershell/module/az.compute/get-azvmss)
- Aplica-se a extensão para as instâncias VM com [AzVmss de atualização](/powershell/module/az.compute/update-azvmss)

A extensão DSC é aplicada para o *myScaleSet* instâncias de VM no grupo de recursos chamado *myResourceGroup*. Introduza os seus próprios nomes da seguinte forma:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Se a política de atualização no seu conjunto de dimensionamento for *manual*, atualizar as instâncias VM com [atualização AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Este cmdlet aplica-se a configuração de conjunto de dimensionamento atualizada para as instâncias de VM e instala a aplicação.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalar uma aplicação a uma VM do Linux com o cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como o cloud-init é executado durante o processo de arranque inicial, não existem passos adicionais ou agentes necessários a aplicar à configuração.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Para obter mais informações, incluindo um exemplo *cloud-Init* de ficheiros, consulte [utilizar o cloud-init para personalizar as VMs do Azure](../virtual-machines/linux/using-cloud-init.md).

Para criar um conjunto de dimensionamento e utilizar um ficheiro cloud-init, adicione a `--custom-data` parâmetro para o [az vmss criar](/cli/azure/vmss) de comando e especifique o nome de um ficheiro cloud-init. O exemplo seguinte cria um conjunto nomeado de dimensionamento *myScaleSet* na *myResourceGroup* e configura as instâncias de VM com um arquivo chamado *cloud-Init*. Introduza os seus próprios nomes da seguinte forma:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Instalar aplicações com as atualizações do SO
Quando novas versões de sistema operacional estiverem disponíveis, pode utilizar ou criar uma nova imagem personalizada e [implementar as atualizações de SO](virtual-machine-scale-sets-upgrade-scale-set.md) para uma escala definido. Cada instância de VM é atualizada para a imagem mais recente que especificar. Pode utilizar uma imagem personalizada com a aplicação previamente instalada, a extensão de Script personalizado ou o PowerShell DSC para que seu aplicativo automaticamente disponível, à medida que efetua a atualização. Terá de planear a manutenção de aplicativos à medida que efetua este processo para assegurar que não haja nenhuma versão problemas de compatibilidade.

Se utilizar uma imagem VM personalizada com o aplicativo previamente instalado, pode integrar as atualizações de aplicação com um pipeline de implementação para criar as novas imagens e implementar as atualizações de SO no conjunto de dimensionamento. Essa abordagem permite que o pipeline escolher as compilações de aplicação mais recente, criar e validar uma imagem de VM, em seguida, atualizar as instâncias VM no conjunto de dimensionamento. Para executar um pipeline de implementação que cria e implementa atualizações de aplicativos em imagens de VM personalizadas, poderia [criar uma imagem do Packer e implementar com os serviços do Azure DevOps](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), ou utilize outra plataforma, como [Spinnaker](https://www.spinnaker.io/) ou [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Passos Seguintes
Como criar e implementar aplicações em seus conjuntos de dimensionamento, pode rever o [descrição geral do Design definir dimensionamento](virtual-machine-scale-sets-design-overview.md). Para obter mais informações sobre como gerir o seu conjunto de dimensionamento, veja [utilize o PowerShell para gerir o seu conjunto de dimensionamento](virtual-machine-scale-sets-windows-manage.md).
