---
title: Implementar uma aplicação para um conjunto de escala de máquina virtual Azure
description: Saiba como implementar aplicações para casos de máquinas virtuais Do Linux e Windows num conjunto de escala
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: e7a4ddaf74df6e04c1597b9c106cd458ddebac55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079596"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implementar uma aplicação em conjuntos de dimensionamento de máquinas virtuais

Para executar aplicações em instâncias de máquina virtual (VM) num conjunto de dimensionamento, primeiro tem de instalar os componentes da aplicação e os ficheiros necessários. Este artigo introduz formas de construir uma imagem VM personalizada, por exemplo, num conjunto de escala, ou executar automaticamente scripts de instalação em instâncias VM existentes. Também aprende a gerir aplicações ou atualizações de SO através de um conjunto de escala.


## <a name="build-a-custom-vm-image"></a>Construa uma imagem VM personalizada
Quando utiliza uma das imagens da plataforma Azure para criar as instâncias do seu conjunto de escala, nenhum software adicional é instalado ou configurado. Pode automatizar a instalação destes componentes, no entanto, isso aumenta o tempo que leva a que as instâncias VM se encontrem nos seus conjuntos de escala. Se aplicar muitas alterações de configuração nas instâncias VM, existe uma sobrecarga de gestão com esses scripts e tarefas de configuração.

Para reduzir a gestão de configuração e tempo para obter um VM, pode criar uma imagem VM personalizada que esteja pronta para executar a sua aplicação assim que um caso seja a provisionado no conjunto de escala. Para obter mais informações sobre como criar e utilizar uma imagem VM personalizada com um conjunto de escala, consulte os seguintes tutoriais:

- [CLI do Azure](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Instale uma aplicação com a extensão de script personalizado
A Extensão de Script Personalizado transfere e executa scripts em VMs do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. Para obter mais informações sobre como instalar uma aplicação com uma extensão de script personalizada, consulte os seguintes tutoriais:

- [CLI do Azure](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Modelo Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instale uma aplicação num Windows VM com PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) é uma plataforma de gestão para definir a configuração das máquinas-alvo. As configurações do DSC definem o que instalar numa máquina e como configurar o hospedeiro. Um motor de configuração local (LCM) funciona em cada nó-alvo que processa as ações solicitadas com base em configurações empurradas.

A extensão DSC PowerShell permite personalizar instâncias VM numa escala definida com PowerShell. O seguinte exemplo:

- Instrui as instâncias VM para descarregar um pacote DSC do GitHub - *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Define a extensão para executar um script de instalação - `configure-http.ps1`
- Obtém informações sobre um conjunto de escala com [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Aplica a extensão aos casos de VM com [Update-AzVmss](/powershell/module/az.compute/update-azvmss)

A extensão DSC é aplicada às instâncias *VM myScaleSet* no grupo de recursos denominado *myResourceGroup*. Insira os seus próprios nomes da seguinte forma:

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

Se a política de atualização no seu conjunto de escalas for *manual,* atualize as suas instâncias VM com [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Este cmdlet aplica a configuração atualizada do conjunto de escalas às instâncias VM e instala a sua aplicação.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instale uma aplicação para um Linux VM com cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como o cloud-init é executado durante o processo de arranque inicial, não existem passos adicionais ou agentes necessários a aplicar à configuração.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Para obter mais informações, incluindo um ficheiro *cloud-init.txt* exemplo, consulte [use cloud-init para personalizar VMs Azure](../virtual-machines/linux/using-cloud-init.md).

Para criar um conjunto de escala e utilizar um ficheiro de inimento em nuvem, adicione o `--custom-data` parâmetro ao [az vmss criar](/cli/azure/vmss) comando e especificar o nome de um ficheiro de inição de nuvem. O exemplo a seguir cria um conjunto de escala chamado *myScaleSet* no *myResourceGroup* e configura instâncias VM com um ficheiro chamado *cloud-init.txt*. Insira os seus próprios nomes da seguinte forma:

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


### <a name="install-applications-with-os-updates"></a>Instalar aplicações com atualizações de SISTEMA
Quando os novos lançamentos de SO estiverem disponíveis, pode utilizar ou construir uma nova imagem personalizada e [implementar upgrades de SO](virtual-machine-scale-sets-upgrade-scale-set.md) para um conjunto de escala. Cada instância VM é atualizada para a imagem mais recente que especifica. Pode utilizar uma imagem personalizada com a aplicação pré-instalada, a Extensão de Script Personalizado ou o PowerShell DSC para ter a sua aplicação automaticamente disponível à medida que executa a atualização. Poderá ter de planear a manutenção da aplicação à medida que executa este processo para garantir que não existem problemas de compatibilidade de versões.

Se utilizar uma imagem VM personalizada com a aplicação pré-instalada, poderá integrar as atualizações da aplicação com um pipeline de implementação para construir as novas imagens e implementar atualizações de SISTEMA em toda a escala definida. Esta abordagem permite ao pipeline recolher as mais recentes construções de aplicações, criar e validar uma imagem VM e, em seguida, atualizar as instâncias VM no conjunto de escala. Para executar um oleoduto de implementação que constrói e implementa atualizações de aplicações através de imagens VM personalizadas, pode [criar uma imagem Packer e implementar com a Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), ou usar outra plataforma como [Spinnaker](https://www.spinnaker.io/) ou [Jenkins.](https://jenkins.io/)


## <a name="next-steps"></a>Passos seguintes
À medida que constrói e implementa aplicações para os seus conjuntos de escala, pode rever a [visão geral do conjunto de escalas](virtual-machine-scale-sets-design-overview.md). Para obter mais informações sobre como gerir o seu conjunto de escalas, consulte [Use PowerShell para gerir o seu conjunto de escalas](./virtual-machine-scale-sets-manage-powershell.md).
