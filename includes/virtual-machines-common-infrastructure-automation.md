---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 90ccc89f80da7c2abce324c077f0fb1a436b8d44
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72171031"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Usar ferramentas de automação de infraestrutura com máquinas virtuais no Azure
Para criar e gerenciar VMs (máquinas virtuais) do Azure de maneira consistente em escala, alguma forma de automação é normalmente desejada. Há muitas ferramentas e soluções que permitem automatizar o ciclo de vida completo de implantação e gerenciamento de infraestrutura do Azure. Este artigo apresenta algumas das ferramentas de automação de infraestrutura que você pode usar no Azure. Essas ferramentas normalmente se encaixam em uma das seguintes abordagens:

- Automatizar a configuração de VMs
    - As ferramentas incluem [Ansible](#ansible), [chefe](#chef)e [Puppet](#puppet).
    - Ferramentas específicas para a personalização de VM incluem [Cloud-init](#cloud-init) para VMs Linux, [DSC (configuração de estado desejado) do PowerShell](#powershell-dsc)e a [extensão de script personalizado do Azure](#azure-custom-script-extension) para todas as VMs do Azure.
 
- Automatizar o gerenciamento de infraestrutura
    - As ferramentas incluem o [Packer](#packer) para automatizar compilações de imagem de VM personalizadas e [Terraform](#terraform) para automatizar o processo de criação de infraestrutura.
    - A [automação do Azure](#azure-automation) pode executar ações em sua infraestrutura do Azure e local.

- Automatizar a implantação e a entrega de aplicativos
    - Os exemplos incluem [Azure DevOps Services](#azure-devops-services) e [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) é um mecanismo de automação para gerenciamento de configuração, criação de VM ou implantação de aplicativo. O Ansible usa um modelo sem agente, normalmente com chaves SSH, para autenticar e gerenciar computadores de destino. As tarefas de configuração são definidas em guias estratégicos, com vários módulos Ansible disponíveis para realizar tarefas específicas. Para obter mais informações, consulte [como o Ansible funciona](https://www.ansible.com/how-ansible-works).

Saiba como:

- [Instale e configure o Ansible no Linux para uso com o Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Crie uma máquina virtual do Linux](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Gerenciar uma máquina virtual do Linux](../articles/virtual-machines/linux/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chefe](https://www.chef.io/) é uma plataforma de automação que ajuda a definir como sua infraestrutura é configurada, implantada e gerenciada. Os componentes adicionais incluíam o chefe habitat para automação do ciclo de vida do aplicativo, em vez da infraestrutura, e o chefe inspecs que ajuda a automatizar a conformidade com os requisitos de segurança e política. Os clientes chefe são instalados em computadores de destino, com um ou mais servidores chefe centrais que armazenam e gerenciam as configurações. Para obter mais informações, consulte [uma visão geral do chefe](https://docs.chef.io/chef_overview.html).

Saiba como:

- [Implante o chefe Automate do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Instale o chefe no Windows e crie VMs do Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
O [Puppet](https://www.puppet.com) é uma plataforma de automação pronta para a empresa que lida com o processo de implantação e entrega de aplicativos. Os agentes são instalados em computadores de destino para permitir que o mestre do Puppet execute manifestos que definem a configuração desejada da infraestrutura e das VMs do Azure. O Puppet pode se integrar a outras soluções, como Jenkins e GitHub, para um fluxo de trabalho DevOps aprimorado. Para obter mais informações, consulte [como o Puppet funciona](https://puppet.com/products/how-puppet-works).

Saiba como:

- [Implante o Puppet do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Inicialização da cloud
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como Cloud-init é chamado durante o processo de inicialização inicial, não há etapas adicionais ou agentes necessários para aplicar sua configuração.  Para obter mais informações sobre como formatar corretamente os arquivos `#cloud-config`, consulte o [site de documentação de Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  os arquivos `#cloud-config` são arquivos de texto codificados em base64.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Estamos trabalhando ativamente com nossos parceiros endossados do Linux distribuição para ter imagens habilitadas para Cloud-init disponíveis no Azure Marketplace. Essas imagens fazem com que suas implantações e configurações de nuvem funcionem de forma integrada com VMs e conjuntos de dimensionamento de máquinas virtuais. Saiba mais detalhes sobre a inicialização de nuvem no Azure:

- [Suporte de inicialização de nuvem para máquinas virtuais Linux no Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Experimente um tutorial sobre a configuração de VM automatizada usando Cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
A [DSC (configuração de estado desejado) do PowerShell](/powershell/scripting/dsc/overview/overview) é uma plataforma de gerenciamento para definir a configuração de computadores de destino. A DSC também pode ser usada no Linux por meio do [servidor OMI (infraestrutura de gerenciamento aberto)](https://collaboration.opengroup.org/omi/).

As configurações de DSC definem o que instalar em um computador e como configurar o host. Um mecanismo de Configuration Manager local (LCM) é executado em cada nó de destino que processa ações solicitadas com base em configurações enviadas por push. Um servidor de pull é um serviço Web que é executado em um host central para armazenar as configurações de DSC e os recursos associados. O servidor de pull se comunica com o mecanismo do LCM em cada host de destino para fornecer as configurações necessárias e relatar a conformidade.

Saiba como:

- [Crie uma configuração de DSC básica](https://msdn.microsoft.com/powershell/dsc/quickstarts/website-quickstart).
- [Configurar um servidor de pull de DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Use o DSC para Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Extensão de script personalizado do Azure
A extensão de script personalizado do Azure para [Linux](../articles/virtual-machines/linux/extensions-customscript.md) ou [Windows](../articles/virtual-machines/windows/extensions-customscript.md) baixa e executa scripts em VMs do Azure. Você pode usar a extensão ao criar uma VM ou a qualquer momento depois que a VM estiver em uso. 

Os scripts podem ser baixados do armazenamento do Azure ou de qualquer local público, como um repositório GitHub. Com a extensão de script personalizado, você pode gravar scripts em qualquer linguagem executada na VM de origem. Esses scripts podem ser usados para instalar aplicativos ou configurar a VM conforme desejado. Para proteger as credenciais, informações confidenciais, como senhas, podem ser armazenadas em uma configuração protegida. Essas credenciais são descriptografadas apenas dentro da VM.

Saiba como:

- [Crie uma VM do Linux com o CLI do Azure e use a extensão de script personalizado](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Crie uma VM do Windows com Azure PowerShell e use a extensão de script personalizado](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
O [Pack](https://www.packer.io) automatiza o processo de compilação quando você cria uma imagem de VM personalizada no Azure. Você usa o Packer para definir o sistema operacional e executar scripts de pós-configuração que personalizam a VM para suas necessidades específicas. Uma vez configurado, a VM é capturada como uma imagem de disco gerenciado. O Pack automatiza o processo para criar a VM de origem, os recursos de rede e de armazenamento, executar scripts de configuração e, em seguida, criar a imagem da VM.

Saiba como:

- [Use o Packer para criar uma imagem de VM do Linux no Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Use o Packer para criar uma imagem de VM do Windows no Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
O [Terraform](https://www.terraform.io) é uma ferramenta de automação que permite definir e criar uma infraestrutura inteira do Azure com uma linguagem de formato de modelo único – a HCL (linguagem de configuração do HashiCorp). Com o Terraform, você define modelos que automatizam o processo para criar recursos de rede, armazenamento e VM para uma determinada solução de aplicativo. Você pode usar seus modelos de Terraform existentes para outras plataformas com o Azure para garantir a consistência e simplificar a implantação da infraestrutura sem a necessidade de converter em um modelo de Azure Resource Manager.

Saiba como:

- [Instale e configure o Terraform com o Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Crie uma infraestrutura do Azure com o Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Automatização do Azure
A [automação do Azure](https://azure.microsoft.com/services/automation/) usa runbooks para processar um conjunto de tarefas nas VMs que você tem como destino. A automação do Azure é usada para gerenciar VMs existentes em vez de criar uma infraestrutura. A automação do Azure pode ser executada em VMs Linux e Windows, bem como em máquinas virtuais ou físicas locais com um Hybrid runbook Worker. Os Runbooks podem ser armazenados em um repositório de controle do código-fonte, como o GitHub. Esses runbooks podem ser executados manualmente ou em um agendamento definido.

A automação do Azure também fornece um serviço de configuração de estado desejado (DSC) que permite que você crie definições de como um determinado conjunto de VMs deve ser configurado. Em seguida, o DSC garante que a configuração necessária seja aplicada e a VM permaneça consistente. O Azure DSC de Automação é executado em computadores Windows e Linux.

Saiba como:

- [Criar um runbook do PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Use Hybrid runbook Worker para gerenciar recursos locais](../articles/automation/automation-hybrid-runbook-worker.md).
- [Use o DSC de automação do Azure](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Serviços de DevOps do Azure
[Azure DevOps Services](https://www.visualstudio.com/team-services/) é um conjunto de ferramentas que ajudam você a compartilhar e acompanhar código, usar compilações automatizadas e criar um pipeline de CI/CD (integração contínua e desenvolvimento) completo. O Azure DevOps Services integra-se ao Visual Studio e a outros editores para simplificar o uso. Azure DevOps Services também pode criar e configurar VMs do Azure e, em seguida, implantar código para elas.

Saiba mais sobre:

- [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
O [Jenkins](https://www.jenkins.io) é um servidor de integração contínua que ajuda a implantar e testar aplicativos e criar pipelines automatizados para a entrega de código. Há centenas de plug-ins para estender a plataforma Jenkins básica, e você também pode integrar com muitos outros produtos e soluções por meio de WebHooks. Você pode instalar manualmente o Jenkins em uma VM do Azure, executar o Jenkins de dentro de um contêiner do Docker ou usar uma imagem do Azure Marketplace criada previamente.

Saiba como:

- [Crie uma infraestrutura de desenvolvimento em uma VM do Linux no Azure com Jenkins, GitHub e Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Passos seguintes
Há muitas opções diferentes para usar as ferramentas de automação de infraestrutura no Azure. Você tem a liberdade de usar a solução que melhor se adapta às suas necessidades e ao seu ambiente. Para começar e experimentar algumas das ferramentas internas do Azure, consulte Como automatizar a personalização de uma VM [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) ou [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) .
