---
title: Implantar um aplicativo em um conjunto de dimensionamento de máquinas virtuais do Azure
description: Saiba como implantar aplicativos em instâncias de máquina virtual do Linux e do Windows em um conjunto de dimensionamento
author: cynthn
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 6bc319ea50da4ff6a654b2c9ab09bbe218695533
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278112"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implantar seu aplicativo em conjuntos de dimensionamento de máquinas virtuais

Para executar aplicações em instâncias de máquina virtual (VM) num conjunto de dimensionamento, primeiro tem de instalar os componentes da aplicação e os ficheiros necessários. Este artigo apresenta maneiras de criar uma imagem de VM personalizada para instâncias em um conjunto de dimensionamento ou executar automaticamente scripts de instalação em instâncias de VM existentes. Você também aprenderá como gerenciar atualizações de aplicativo ou de sistema operacional em um conjunto de dimensionamento.


## <a name="build-a-custom-vm-image"></a>Criar uma imagem de VM personalizada
Quando você usa uma das imagens da plataforma do Azure para criar as instâncias em seu conjunto de dimensionamento, nenhum software adicional é instalado ou configurado. Você pode automatizar a instalação desses componentes, no entanto, isso aumenta o tempo necessário para provisionar instâncias de VM para seus conjuntos de dimensionamento. Se você aplicar muitas alterações de configuração às instâncias de VM, haverá sobrecarga de gerenciamento com esses scripts e tarefas de configuração.

Para reduzir o gerenciamento de configuração e o tempo para provisionar uma VM, você pode criar uma imagem de VM personalizada que esteja pronta para executar seu aplicativo assim que uma instância for provisionada no conjunto de dimensionamento. Para obter mais informações sobre como criar e usar uma imagem de VM personalizada com um conjunto de dimensionamento, consulte os seguintes tutoriais:

- [CLI do Azure](tutorial-use-custom-image-cli.md)
- [O Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Instalar um aplicativo com a extensão de script personalizado
A Extensão de Script Personalizado transfere e executa scripts em VMs do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. Para obter mais informações sobre como instalar um aplicativo com uma extensão de script personalizado, consulte os seguintes tutoriais:

- [CLI do Azure](tutorial-install-apps-cli.md)
- [O Azure PowerShell](tutorial-install-apps-powershell.md)
- [Modelo do Azure Resource Manager](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalar um aplicativo em uma VM do Windows com o DSC do PowerShell
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) é uma plataforma de gestão para definir a configuração de máquinas de destino. Configurações de DSC definem o que instalar num computador e como configurar o anfitrião. Um mecanismo de Gestor de configuração Local (LCM) é executado em cada nó de destino que processa as ações pedidas com base nas configurações enviada por push.

A extensão de DSC do PowerShell permite que você personalize instâncias de VM em um conjunto de dimensionamento com o PowerShell. O exemplo a seguir:

- Instrui as instâncias de VM para baixar um pacote DSC do GitHub- *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Define a extensão para executar um script de instalação-`configure-http.ps1`
- Obtém informações sobre um conjunto de dimensionamento com [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Aplica a extensão às instâncias de VM com [Update-AzVmss](/powershell/module/az.compute/update-azvmss)

A extensão DSC é aplicada às instâncias de VM *Myscaleset* no grupo de recursos chamado *MyResource*Group. Insira seus próprios nomes da seguinte maneira:

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

Se a política de atualização em seu conjunto de dimensionamento for *manual*, atualize suas instâncias de VM com [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Esse cmdlet aplica a configuração atualizada do conjunto de dimensionamento às instâncias de VM e instala seu aplicativo.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalar um aplicativo em uma VM do Linux com Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como o cloud-init é executado durante o processo de arranque inicial, não existem passos adicionais ou agentes necessários a aplicar à configuração.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Para obter mais informações, incluindo um arquivo *Cloud-init. txt* de exemplo, consulte [usar Cloud-init para personalizar as VMs do Azure](../virtual-machines/linux/using-cloud-init.md).

Para criar um conjunto de dimensionamento e usar um arquivo Cloud-init, adicione o parâmetro `--custom-data` ao comando [AZ vmss Create](/cli/azure/vmss) e especifique o nome de um arquivo Cloud-init. O exemplo a seguir cria um conjunto de dimensionamento chamado *Myscalemodeset* em *MyResource* e configura as instâncias de VM com um arquivo chamado *Cloud-init. txt*. Insira seus próprios nomes da seguinte maneira:

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


### <a name="install-applications-with-os-updates"></a>Instalar aplicativos com atualizações do sistema operacional
Quando novas versões do sistema operacional estiverem disponíveis, você poderá usar ou criar uma nova imagem personalizada e [implantar atualizações do sistema operacional](virtual-machine-scale-sets-upgrade-scale-set.md) em um conjunto de dimensionamento. Cada instância de VM é atualizada para a imagem mais recente que você especificar. Você pode usar uma imagem personalizada com o aplicativo pré-instalado, a extensão de script personalizado ou a DSC do PowerShell para que seu aplicativo fique disponível automaticamente à medida que você executar a atualização. Talvez seja necessário planejar a manutenção do aplicativo à medida que você executar esse processo para garantir que não haja nenhum problema de compatibilidade de versão.

Se você usar uma imagem de VM personalizada com o aplicativo pré-instalado, poderá integrar as atualizações do aplicativo a um pipeline de implantação para criar as novas imagens e implantar atualizações do sistema operacional no conjunto de dimensionamento. Essa abordagem permite que o pipeline pegue as compilações mais recentes do aplicativo, crie e valide uma imagem de VM e, em seguida, atualize as instâncias de VM no conjunto de dimensionamento. Para executar um pipeline de implantação que cria e implanta atualizações de aplicativo em imagens de VM personalizadas, você pode [criar uma imagem de empacotador e implantá-la com Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)ou usar outra plataforma, como [Spinnaker](https://www.spinnaker.io/) ou [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Passos seguintes
Ao criar e implantar aplicativos em seus conjuntos de dimensionamento, você pode examinar a [visão geral do design do conjunto de dimensionamento](virtual-machine-scale-sets-design-overview.md). Para obter mais informações sobre como gerenciar seu conjunto de dimensionamento, consulte [usar o PowerShell para gerenciar seu conjunto de dimensionamento](virtual-machine-scale-sets-windows-manage.md).
