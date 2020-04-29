---
title: Implementar uma aplicação para um conjunto de escala de máquina virtual Azure
description: Saiba como implementar aplicações para casos de máquinas virtuais Linux e Windows num conjunto de escala
author: ju-shim
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: e157db79f1835a8ca891dd360a99e3319565d1d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81011468"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implementar uma aplicação em conjuntos de dimensionamento de máquinas virtuais

Para executar aplicações em instâncias de máquina virtual (VM) num conjunto de dimensionamento, primeiro tem de instalar os componentes da aplicação e os ficheiros necessários. Este artigo introduz formas de construir uma imagem VM personalizada, por exemplo, num conjunto de escala, ou executar automaticamente scripts de instalação em instâncias VM existentes. Também aprende a gerir as atualizações de aplicações ou sotações de SISTEMA num conjunto de escala.


## <a name="build-a-custom-vm-image"></a>Construa uma imagem VM personalizada
Quando utiliza uma das imagens da plataforma Azure para criar as instâncias no seu conjunto de escala, nenhum software adicional é instalado ou configurado. Pode automatizar a instalação destes componentes, no entanto, isso aumenta o tempo que demora a fornecer instâncias VM aos seus conjuntos de escala. Se aplicar muitas alterações de configuração nas instâncias VM, existe uma despesa de gestão com esses scripts e tarefas de configuração.

Para reduzir a gestão da configuração e o tempo para fornecer um VM, pode criar uma imagem VM personalizada que esteja pronta para executar a sua aplicação assim que uma instância for aprovisionada no conjunto de escala. Para obter mais informações sobre como criar e utilizar uma imagem VM personalizada com um conjunto de escala, consulte os seguintes tutoriais:

- [CLI do Azure](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Instale uma aplicação com a extensão personalizada do script
A Extensão de Script Personalizado transfere e executa scripts em VMs do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. Para obter mais informações sobre como instalar uma aplicação com uma extensão de script personalizada, consulte os seguintes tutoriais:

- [CLI do Azure](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Modelo de Gestor de Recursos Azure](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instale uma aplicação para um Windows VM com PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) é uma plataforma de gestão para definir a configuração das máquinas-alvo. As configurações dSC definem o que instalar numa máquina e como configurar o hospedeiro. Um motor De Configuração Local (LCM) funciona em cada nó alvo que processa as ações solicitadas com base em configurações empurradas.

A extensão PowerShell DSC permite personalizar as instâncias VM num conjunto de escala com powerShell. O seguinte exemplo:

- Instrui as instâncias vM para descarregar um pacote DSC do GitHub -*https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Define a extensão para executar um script de instalação -`configure-http.ps1`
- Obtém informações sobre um conjunto de escala com [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Aplica a extensão aos casos vm com [Actualização-AzVmss](/powershell/module/az.compute/update-azvmss)

A extensão DSC é aplicada às instâncias *myScaleSet* VM no grupo de recursos chamado *myResourceGroup*. Insira os seus próprios nomes da seguinte forma:

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

Se a política de atualização no seu conjunto de escala for *manual*, atualize as instâncias vM com [ATualização-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Este cmdlet aplica a configuração de conjunto de escala atualizada para as instâncias VM e instala a sua aplicação.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instale uma aplicação para um Linux VM com cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como o cloud-init é executado durante o processo de arranque inicial, não existem passos adicionais ou agentes necessários a aplicar à configuração.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Para mais informações, incluindo um ficheiro *cloud-init.txt,* consulte [Use cloud-init para personalizar Os VMs Azure](../virtual-machines/linux/using-cloud-init.md).

Para criar um conjunto de escala e utilizar `--custom-data` um ficheiro cloud-init, adicione o parâmetro ao [az vmss criar](/cli/azure/vmss) comando e especificar o nome de um ficheiro cloud-init. O exemplo seguinte cria um conjunto de escala chamado *myScaleSet* no *myResourceGroup* e configura casos vM com um ficheiro chamado *cloud-init.txt*. Insira os seus próprios nomes da seguinte forma:

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


### <a name="install-applications-with-os-updates"></a>Instalar aplicações com atualizações de SO
Quando estiverem disponíveis novos lançamentos de SO, pode utilizar ou construir uma nova imagem personalizada e [implementar upgrades de SO](virtual-machine-scale-sets-upgrade-scale-set.md) para um conjunto de escala. Cada instância VM é atualizada para a imagem mais recente que especifica. Pode utilizar uma imagem personalizada com a aplicação pré-instalada, a extensão do script personalizado ou o PowerShell DSC para ter a sua aplicação automaticamente disponível à medida que executa a atualização. Poderá ter de planear a manutenção da aplicação à medida que realiza este processo para garantir que não existem problemas de compatibilidade com a versão.

Se utilizar uma imagem VM personalizada com a aplicação pré-instalada, poderá integrar as atualizações da aplicação com um pipeline de implementação para construir as novas imagens e implementar upgrades de SO em toda a escala definida. Esta abordagem permite que o gasoduto recolha as mais recentes construções de aplicações, crie e valide uma imagem VM, em seguida, atualizar as instâncias VM no conjunto de escala. Para executar um pipeline de implementação que constrói e implementa atualizações de aplicações através de imagens VM personalizadas, você poderia [criar uma imagem Packer e implantar com os Serviços Azure DevOps](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), ou usar outra plataforma como [Spinnaker](https://www.spinnaker.io/) ou [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Passos seguintes
À medida que constrói e implementa aplicações para os seus conjuntos de escala, pode rever a [visão geral](virtual-machine-scale-sets-design-overview.md)do design de conjunto de escala . Para obter mais informações sobre como gerir o seu conjunto de escala, consulte [use PowerShell para gerir o seu conjunto](virtual-machine-scale-sets-windows-manage.md)de escala .
