---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: f7dcf342a1b9b2107138044dfc207d6dbcb42e9e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260841"
---
Para criar e gerir máquinas virtuais do Azure (VMs) de forma consistente à escala, alguma forma de automatização é normalmente pretende-se. Existem muitas ferramentas e soluções que permitem-lhe automatizar a implementação de toda a infraestrutura do Azure e o ciclo de vida de gestão. Este artigo apresenta algumas das ferramentas de automatização de infraestruturas que pode utilizar no Azure. Essas ferramentas normalmente se adaptam a uma das abordagens seguintes:

- Automatizar a configuração de VMs
    - As ferramentas incluem [Ansible](#ansible), [chefe](#chef)e [Puppet](#puppet).
    - Ferramentas específicas para a personalização de VM incluem [Cloud-init](#cloud-init) para VMs Linux, [DSC (configuração de estado desejado) do PowerShell](#powershell-dsc)e a [extensão de script personalizado do Azure](#azure-custom-script-extension) para todas as VMs do Azure.
 
- Automatizar a gestão de infraestrutura
    - As ferramentas incluem o [Packer](#packer) para automatizar compilações de imagem de VM personalizadas e [Terraform](#terraform) para automatizar o processo de criação de infraestrutura.
    - A [automação do Azure](#azure-automation) pode executar ações em sua infraestrutura do Azure e local.

- Automatizar a implantação de aplicativos e de entrega
    - Os exemplos incluem [Azure DevOps Services](#azure-devops-services) e [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) é um mecanismo de automação para gerenciamento de configuração, criação de VM ou implantação de aplicativo. Ansible utiliza um modelo sem agente, normalmente, com chaves SSH, para autenticar e gerir máquinas de destino. Tarefas de configuração são definidas no playbooks, com um número de módulos do Ansible disponíveis para executar tarefas específicas. Para obter mais informações, consulte [como o Ansible funciona](https://www.ansible.com/how-ansible-works).

Aprenda a:

- [Instale e configure o Ansible no Linux para uso com o Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Crie uma máquina virtual do Linux](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Gerenciar uma máquina virtual do Linux](../articles/virtual-machines/linux/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chefe](https://www.chef.io/) é uma plataforma de automação que ajuda a definir como sua infraestrutura é configurada, implantada e gerenciada. Componentes adicionais incluídos Habitat do Chef para automação de ciclo de vida de aplicativos, em vez da infraestrutura e Chef InSpec que ajuda a automatizar a conformidade com a segurança e os requisitos da política. Chef clientes são instalados nos computadores de destino, com um ou mais Chef servidores centrais de armazenar e gerenciar as configurações. Para obter mais informações, consulte [uma visão geral do chefe](https://docs.chef.io/chef_overview.html).

Aprenda a:

- [Implante o chefe Automate do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Instale o chefe no Windows e crie VMs do Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
O [Puppet](https://www.puppet.com) é uma plataforma de automação pronta para a empresa que lida com o processo de implantação e entrega de aplicativos. Agentes são instalados nos computadores de destino para permitir que o mestre de Puppet para executar manifestos que definem a configuração pretendida para a infraestrutura do Azure e VMs. Puppet pode integrar com outras soluções, como Jenkins e o GitHub para um fluxo de trabalho do devops melhorada. Para obter mais informações, consulte [como o Puppet funciona](https://puppet.com/products/how-puppet-works).

Aprenda a:

- [Implante o Puppet do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Inicialização da cloud
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Porque o cloud-init é chamado durante o processo de inicialização inicial, não são passos adicionais ou agentes necessários a aplicar a configuração.  Para obter mais informações sobre como formatar corretamente os arquivos de `#cloud-config`, consulte o [site de documentação de Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` arquivos são arquivos de texto codificados em base64.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Estamos a trabalhar ativamente com os nossos parceiros de distribuição do Linux apoiados para ter imagens de cloud-init ativado disponíveis no Azure marketplace. Estas imagens certifique as suas implementações de cloud-init e configurações funcionam sem problemas com VMs e conjuntos de dimensionamento de máquina virtual. Saiba mais detalhes sobre a inicialização da cloud no Azure:

- [Suporte de inicialização de nuvem para máquinas virtuais Linux no Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Experimente um tutorial sobre a configuração de VM automatizada usando Cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
A [DSC (configuração de estado desejado) do PowerShell](/powershell/scripting/dsc/overview/overview) é uma plataforma de gerenciamento para definir a configuração de computadores de destino. A DSC também pode ser usada no Linux por meio do [servidor OMI (infraestrutura de gerenciamento aberto)](https://collaboration.opengroup.org/omi/).

Configurações de DSC definem o que instalar num computador e como configurar o anfitrião. Um mecanismo de Gestor de configuração Local (LCM) é executado em cada nó de destino que processa as ações pedidas com base nas configurações enviada por push. Um servidor de solicitação é um serviço web que é executada num anfitrião central para armazenar as configurações de DSC e recursos associados. O servidor de solicitação se comunica com o mecanismo de LCM em cada anfitrião de destino para fornecer as configurações necessárias e gerar relatórios sobre conformidade.

Aprenda a:

- [Crie uma configuração de DSC básica](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Configurar um servidor de pull de DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Use o DSC para Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Extensão de Script personalizado do Azure
A extensão de script personalizado do Azure para [Linux](../articles/virtual-machines/linux/extensions-customscript.md) ou [Windows](../articles/virtual-machines/windows/extensions-customscript.md) baixa e executa scripts em VMs do Azure. Pode utilizar a extensão quando criar uma VM ou a qualquer altura depois da VM está em utilização. 

Scripts podem ser transferidos a partir do armazenamento do Azure ou de qualquer localização pública, como um repositório do GitHub. Com a extensão de Script personalizado, pode escrever scripts em qualquer idioma que é executado na VM de origem. Esses scripts podem ser utilizados para instalar aplicativos ou configurar a VM conforme pretendido. Para proteger as credenciais, informações confidenciais como palavras-passe podem ser armazenadas numa configuração protegida. Estas credenciais são desencriptadas apenas dentro da VM.

Aprenda a:

- [Crie uma VM do Linux com o CLI do Azure e use a extensão de script personalizado](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Crie uma VM do Windows com Azure PowerShell e use a extensão de script personalizado](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
O [Pack](https://www.packer.io) automatiza o processo de compilação quando você cria uma imagem de VM personalizada no Azure. Utilize o Packer para definir o sistema operacional e executar scripts pós-configuração que personalizar as VMS para as suas necessidades específicas. Depois de configurada, a VM, em seguida, é capturada como uma imagem de disco gerido. Packer automatiza o processo para criar a origem de recursos VM, rede e armazenamento, executar scripts de configuração e, em seguida, criar a imagem de VM.

Aprenda a:

- [Use o Packer para criar uma imagem de VM do Linux no Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Use o Packer para criar uma imagem de VM do Windows no Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
O [Terraform](https://www.terraform.io) é uma ferramenta de automação que permite definir e criar uma infraestrutura inteira do Azure com uma linguagem de formato de modelo único – a HCL (linguagem de configuração do HashiCorp). Com o Terraform, definir modelos que automatizam o processo de criação de rede, armazenamento e recursos da VM para uma solução de determinado aplicativo. Pode utilizar os seus modelos existentes do Terraform para outras plataformas com o Azure para garantir a consistência e simplificar a implementação de infraestrutura sem ser necessário converter um modelo Azure Resource Manager.

Aprenda a:

- [Instale e configure o Terraform com o Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Crie uma infraestrutura do Azure com o Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Automatização do Azure
A [automação do Azure](https://azure.microsoft.com/services/automation/) usa runbooks para processar um conjunto de tarefas nas VMs que você tem como destino. A automatização do Azure é utilizada para gerir as VMs existentes em vez de criar uma infra-estrutura. A automatização do Azure pode ser executados em Linux e VMs do Windows, os e máquinas físicas ou virtuais no local com uma função de trabalho de runbook híbrida. Runbooks podem ser armazenados num repositório de controle de origem, como o GitHub. Estes runbooks, em seguida, pode executar manualmente ou com base numa agenda definida.

A automatização do Azure também fornece um serviço de Desired State Configuration (DSC) que permite-lhe criar definições para a forma como deve ser configurado um determinado conjunto de VMs. DSC, em seguida, garante que é aplicada a configuração necessária e a VM permanece consistente. DSC de automatização do Azure é executado em computadores Windows e Linux.

Aprenda a:

- [Criar um runbook do PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Use Hybrid runbook Worker para gerenciar recursos locais](../articles/automation/automation-hybrid-runbook-worker.md).
- [Use o DSC de automação do Azure](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Serviços de DevOps do Azure
[Azure DevOps Services](https://www.visualstudio.com/team-services/) é um conjunto de ferramentas que ajudam você a compartilhar e acompanhar código, usar compilações automatizadas e criar um pipeline de CI/CD (integração contínua e desenvolvimento) completo. Serviços de DevOps do Azure integra-se com o Visual Studio e outros editores para simplificar a utilização. Serviços de DevOps do Azure pode também criar e configurar as VMs do Azure e, em seguida, implementar código a eles.

Saiba mais sobre:

- [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
O [Jenkins](https://www.jenkins.io) é um servidor de integração contínua que ajuda a implantar e testar aplicativos e criar pipelines automatizados para a entrega de código. Existem centenas de plug-ins para expandir a plataforma principal do Jenkins e, também pode integrar com muitos outros produtos e soluções através de webhooks. Pode, manualmente, instale o Jenkins numa VM do Azure, executar Jenkins a partir de dentro de um contentor de Docker ou utilizar uma imagem previamente criada do Azure Marketplace.

Aprenda a:

- [Crie uma infraestrutura de desenvolvimento em uma VM do Linux no Azure com Jenkins, GitHub e Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Passos seguintes
Existem muitas opções diferentes para utilizar as ferramentas de automatização de infraestruturas no Azure. Tem a liberdade de utilizar a solução que melhor se adequa às suas necessidades e ambiente. Para começar e experimentar algumas das ferramentas internas do Azure, consulte Como automatizar a personalização de uma VM [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) ou [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) .
