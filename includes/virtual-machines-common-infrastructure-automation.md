---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d8ada53082b0ccc95d472b43a0ae2ff63cd76a3e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125188"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Utilize as ferramentas de automatização de infraestrutura com máquinas virtuais no Azure
Para criar e gerir máquinas virtuais do Azure (VMs) de forma consistente à escala, alguma forma de automatização é normalmente pretende-se. Existem muitas ferramentas e soluções que permitem-lhe automatizar a implementação de toda a infraestrutura do Azure e o ciclo de vida de gestão. Este artigo apresenta algumas das ferramentas de automatização de infraestruturas que pode utilizar no Azure. Essas ferramentas normalmente se adaptam a uma das abordagens seguintes:

- Automatizar a configuração de VMs
    - As ferramentas incluem [Ansible](#ansible), [Chef](#chef), e [Puppet](#puppet).
    - Ferramentas específicas de personalização de VM incluem [cloud-init](#cloud-init) para VMs do Linux, [PowerShell Desired State Configuration (DSC)](#powershell-dsc)e o [extensão de Script personalizado do Azure](#azure-custom-script-extension) para todos VMs do Azure.
 
- Automatizar a gestão de infraestrutura
    - As ferramentas incluem [Packer](#packer) para automatizar a VM personalizada se baseia a imagem, e [Terraform](#terraform) para automatizar a infra-estrutura do processo de compilação.
    - [A automatização do Azure](#azure-automation) pode realizar ações em toda a infraestrutura do Azure e no local.

- Automatizar a implantação de aplicativos e de entrega
    - Os exemplos incluem [serviços do Azure DevOps](#azure-devops-services) e [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) é um motor de automatização para gestão de configuração, a criação da VM ou implementação de aplicação. Ansible utiliza um modelo sem agente, normalmente, com chaves SSH, para autenticar e gerir máquinas de destino. Tarefas de configuração são definidas no playbooks, com um número de módulos do Ansible disponíveis para executar tarefas específicas. Para obter mais informações, consulte [funciona como Ansible](https://www.ansible.com/how-ansible-works).

Aprenda a:

- [Instalar e configurar o Ansible no Linux para utilização com o Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Criar uma máquina virtual Linux](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Gerir uma máquina virtual Linux](../articles/virtual-machines/linux/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) é uma plataforma de automação que ajuda a definir a forma como sua infraestrutura está configurada, implementados e geridos. Componentes adicionais incluídos Habitat do Chef para automação de ciclo de vida de aplicativos, em vez da infraestrutura e Chef InSpec que ajuda a automatizar a conformidade com a segurança e os requisitos da política. Chef clientes são instalados nos computadores de destino, com um ou mais Chef servidores centrais de armazenar e gerenciar as configurações. Para obter mais informações, consulte [uma visão geral do Chef](https://docs.chef.io/chef_overview.html).

Aprenda a:

- [Implementar o Chef automatizar no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Instale o Chef no Windows e criar VMs do Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) é uma plataforma de automação de prontas para empresas que lide com o processo de entrega e a implementação de aplicação. Agentes são instalados nos computadores de destino para permitir que o mestre de Puppet para executar manifestos que definem a configuração pretendida para a infraestrutura do Azure e VMs. Puppet pode integrar com outras soluções, como Jenkins e o GitHub para um fluxo de trabalho do devops melhorada. Para obter mais informações, consulte [funciona como a Puppet](https://puppet.com/product/how-puppet-works).

Aprenda a:

- [Implementar o Puppet no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Inicialização da cloud
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Porque o cloud-init é chamado durante o processo de inicialização inicial, não são passos adicionais ou agentes necessários a aplicar a configuração.  Para obter mais informações sobre como formatar corretamente sua `#cloud-config` arquivos, consulte a [site de documentação de cloud-init](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` os arquivos são arquivos de texto codificados em base64.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

 Estamos a trabalhar ativamente com os nossos parceiros de distribuição do Linux apoiados para ter imagens de cloud-init ativado disponíveis no Azure marketplace. Estas imagens certifique as suas implementações de cloud-init e configurações funcionam sem problemas com VMs e conjuntos de dimensionamento de máquina virtual. A tabela seguinte descreve a disponibilidade de imagens atual do cloud-init ativado na plataforma do Azure:

| Publicador | Oferta | SKU | Versão | preparado para o cloud-init
|:--- |:--- |:--- |:--- |:--- 
|Canónico |UbuntuServer |16.04-LTS |mais recente |sim | 
|Canónico |UbuntuServer |14.04.5-LTS |mais recente |sim |
|CoreOS |CoreOS |Estável |mais recente |sim |
|OpenLogic |CentOS |7-CI |mais recente |pré-visualização |
|RedHat |RHEL |7-RAW-CI |mais recente |pré-visualização |

Saiba mais detalhes sobre a inicialização da cloud no Azure:

- [Suporte de cloud-init para máquinas virtuais do Linux no Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Experimente um tutorial sobre a configuração automatizada de VM com o cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) é uma plataforma de gestão para definir a configuração de máquinas de destino. Também pode ser utilizado o DSC no Linux através da [servidor Open Management Infrastructure (OMI)](https://collaboration.opengroup.org/omi/).

Configurações de DSC definem o que instalar num computador e como configurar o anfitrião. Um mecanismo de Gestor de configuração Local (LCM) é executado em cada nó de destino que processa as ações pedidas com base nas configurações enviada por push. Um servidor de solicitação é um serviço web que é executada num anfitrião central para armazenar as configurações de DSC e recursos associados. O servidor de solicitação se comunica com o mecanismo de LCM em cada anfitrião de destino para fornecer as configurações necessárias e gerar relatórios sobre conformidade.

Aprenda a:

- [Criar uma configuração de DSC básica](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [Configurar um servidor de solicitação de DSC](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Utilizar o DSC para Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Extensão de Script personalizado do Azure
A extensão de Script personalizado do Azure para [Linux](../articles/virtual-machines/linux/extensions-customscript.md) ou [Windows](../articles/virtual-machines/windows/extensions-customscript.md) transfere e executa scripts em VMs do Azure. Pode utilizar a extensão quando criar uma VM ou a qualquer altura depois da VM está em utilização. 

Scripts podem ser transferidos a partir do armazenamento do Azure ou de qualquer localização pública, como um repositório do GitHub. Com a extensão de Script personalizado, pode escrever scripts em qualquer idioma que é executado na VM de origem. Esses scripts podem ser utilizados para instalar aplicativos ou configurar a VM conforme pretendido. Para proteger as credenciais, informações confidenciais como palavras-passe podem ser armazenadas numa configuração protegida. Estas credenciais são desencriptadas apenas dentro da VM.

Aprenda a:

- [Criar uma VM do Linux com a CLI do Azure e utilizar a extensão de Script personalizado](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Criar uma VM do Windows com o Azure PowerShell e utilize a extensão de Script personalizado](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatiza o processo de compilação quando criar uma imagem VM personalizada no Azure. Utilize o Packer para definir o sistema operacional e executar scripts pós-configuração que personalizar as VMS para as suas necessidades específicas. Depois de configurada, a VM, em seguida, é capturada como uma imagem de disco gerido. Packer automatiza o processo para criar a origem de recursos VM, rede e armazenamento, executar scripts de configuração e, em seguida, criar a imagem de VM.

Aprenda a:

- [Utilize o Packer para criar uma imagem de VM do Linux no Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Utilize o Packer para criar uma imagem de VM do Windows no Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) é uma ferramenta de automatização que permite-lhe definir e criar uma infraestrutura do Azure inteira com um idioma de formato único modelo - o idioma de configuração da HashiCorp (HCL). Com o Terraform, definir modelos que automatizam o processo de criação de rede, armazenamento e recursos da VM para uma solução de determinado aplicativo. Pode utilizar os seus modelos existentes do Terraform para outras plataformas com o Azure para garantir a consistência e simplificar a implementação de infraestrutura sem ser necessário converter um modelo Azure Resource Manager.

Aprenda a:

- [Instalar e configurar o Terraform com o Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Criar uma infraestrutura do Azure com o Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Automatização do Azure
[A automatização do Azure](https://azure.microsoft.com/services/automation/) utiliza runbooks para processar um conjunto de tarefas nas VMs de destino. A automatização do Azure é utilizada para gerir as VMs existentes em vez de criar uma infra-estrutura. A automatização do Azure pode ser executados em Linux e VMs do Windows, os e máquinas físicas ou virtuais no local com uma função de trabalho de runbook híbrida. Runbooks podem ser armazenados num repositório de controle de origem, como o GitHub. Estes runbooks, em seguida, pode executar manualmente ou com base numa agenda definida.

A automatização do Azure também fornece um serviço de Desired State Configuration (DSC) que permite-lhe criar definições para a forma como deve ser configurado um determinado conjunto de VMs. DSC, em seguida, garante que é aplicada a configuração necessária e a VM permanece consistente. DSC de automatização do Azure é executado em computadores Windows e Linux.

Aprenda a:

- [Criar um runbook do PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Utilizar o Runbook Worker híbrido para gerir recursos no local](../articles/automation/automation-hybrid-runbook-worker.md).
- [Utilizar a DSC de automatização do Azure](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Serviços de DevOps do Azure
[Os serviços do Azure DevOps](https://www.visualstudio.com/team-services/) é um conjunto de ferramentas que ajudam a partilha e controle de código, usar compilações automatizadas e criar um pipeline de desenvolvimento (CI/CD) e a completa integração contínua. Serviços de DevOps do Azure integra-se com o Visual Studio e outros editores para simplificar a utilização. Serviços de DevOps do Azure pode também criar e configurar as VMs do Azure e, em seguida, implementar código a eles.

Saiba mais sobre:

- [Serviços do Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) é um servidor de integração contínua que ajuda a implementar e testar aplicativos e criar pipelines de automatizada para o fornecimento de código. Existem centenas de plug-ins para expandir a plataforma principal do Jenkins e, também pode integrar com muitos outros produtos e soluções através de webhooks. Pode, manualmente, instale o Jenkins numa VM do Azure, executar Jenkins a partir de dentro de um contentor de Docker ou utilizar uma imagem previamente criada do Azure Marketplace.

Aprenda a:

- [Criar uma infraestrutura de desenvolvimento numa VM do Linux no Azure com Jenkins, GitHub e Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Passos Seguintes
Existem muitas opções diferentes para utilizar as ferramentas de automatização de infraestruturas no Azure. Tem a liberdade de utilizar a solução que melhor se adequa às suas necessidades e ambiente. Para começar a utilizar e experimente algumas das ferramentas incorporadas para o Azure, veja como automatizar a personalização de um [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) ou [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM.
