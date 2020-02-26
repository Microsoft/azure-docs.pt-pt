---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 9cbc48d8bca2f7491d0464be1c5bd64054927dc9
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77608754"
---
Para criar e gerir máquinas virtuais do Azure (VMs) de forma consistente à escala, alguma forma de automatização é normalmente pretende-se. Existem muitas ferramentas e soluções que permitem-lhe automatizar a implementação de toda a infraestrutura do Azure e o ciclo de vida de gestão. Este artigo apresenta algumas das ferramentas de automatização de infraestruturas que pode utilizar no Azure. Essas ferramentas normalmente se adaptam a uma das abordagens seguintes:

- Automatizar a configuração de VMs
    - As ferramentas incluem [Ansible,](#ansible) [Chef](#chef)e [Puppet.](#puppet)
    - As ferramentas específicas para a personalização vM incluem [cloud-init](#cloud-init) para VMs Linux, [PowerShell Desired State Configuration (DSC)](#powershell-dsc)e a extensão de [script personalizado Azure](#azure-custom-script-extension) para todos os VMs Azure.
 
- Automatizar a gestão de infraestrutura
    - As ferramentas incluem [packer](#packer) para automatizar construções de imagem VM personalizadas, e [Terraform](#terraform) para automatizar o processo de construção de infraestruturas.
    - A [Azure Automation](#azure-automation) pode realizar ações em toda a sua infraestrutura Azure e no local.

- Automatizar a implantação de aplicativos e de entrega
    - Exemplos incluem [Azure DevOps Services](#azure-devops-services) e [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) é um motor de automatização para a gestão de configuração, criação vM ou implementação de aplicações. Ansible utiliza um modelo sem agente, normalmente, com chaves SSH, para autenticar e gerir máquinas de destino. Tarefas de configuração são definidas no playbooks, com um número de módulos do Ansible disponíveis para executar tarefas específicas. Para mais informações, consulte [como funciona o Ansible.](https://www.ansible.com/how-ansible-works)

Aprenda a:

- [Instale e configure Ansible no Linux para utilização com o Azure](../articles/ansible/ansible-install-configure.md).
- [Crie uma máquina virtual Linux.](../articles/ansible/ansible-create-vm.md)
- [Gerencie uma máquina virtual Linux.](../articles/ansible/ansible-manage-linux-vm.md)


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) é uma plataforma de automação que ajuda a definir como a sua infraestrutura é configurada, implantada e gerida. Componentes adicionais incluídos Habitat do Chef para automação de ciclo de vida de aplicativos, em vez da infraestrutura e Chef InSpec que ajuda a automatizar a conformidade com a segurança e os requisitos da política. Chef clientes são instalados nos computadores de destino, com um ou mais Chef servidores centrais de armazenar e gerenciar as configurações. Para mais informações, consulte [Uma visão geral do Chef.](https://docs.chef.io/chef_overview.html)

Aprenda a:

- [Implementar chef automatizar do Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview)
- [Instale chef no Windows e crie VMs Azure.](../articles/chef/chef-automation.md)


## <a name="puppet"></a>Puppet
[A Puppet](https://www.puppet.com) é uma plataforma de automação pronta para a empresa que trata do processo de entrega e implementação de aplicações. Agentes são instalados nos computadores de destino para permitir que o mestre de Puppet para executar manifestos que definem a configuração pretendida para a infraestrutura do Azure e VMs. Puppet pode integrar com outras soluções, como Jenkins e o GitHub para um fluxo de trabalho do devops melhorada. Para mais informações, consulte como funciona o Boneco de [Marionetas.](https://puppet.com/products/how-puppet-works)

Aprenda a:

- [Implementar marionetas do Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview)


## <a name="cloud-init"></a>Inicialização da cloud
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Porque o cloud-init é chamado durante o processo de inicialização inicial, não são passos adicionais ou agentes necessários a aplicar a configuração.  Para obter mais informações sobre como formatar adequadamente os seus ficheiros `#cloud-config`, consulte o [site de documentação init init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` ficheiros são ficheiros de texto codificados na base64.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Estamos a trabalhar ativamente com os nossos parceiros de distribuição do Linux apoiados para ter imagens de cloud-init ativado disponíveis no Azure marketplace. Estas imagens certifique as suas implementações de cloud-init e configurações funcionam sem problemas com VMs e conjuntos de dimensionamento de máquina virtual. Saiba mais detalhes sobre a inicialização da cloud no Azure:

- [Suporte cloud-init para máquinas virtuais Linux em Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Experimente um tutorial sobre a configuração vm automatizada utilizando cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>DSC do PowerShell
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) é uma plataforma de gestão para definir a configuração das máquinas-alvo. O DSC também pode ser utilizado no Linux através do [servidor Open Management Infrastructure (OMI).](https://collaboration.opengroup.org/omi/)

Configurações de DSC definem o que instalar num computador e como configurar o anfitrião. Um mecanismo de Gestor de configuração Local (LCM) é executado em cada nó de destino que processa as ações pedidas com base nas configurações enviada por push. Um servidor de solicitação é um serviço web que é executada num anfitrião central para armazenar as configurações de DSC e recursos associados. O servidor de solicitação se comunica com o mecanismo de LCM em cada anfitrião de destino para fornecer as configurações necessárias e gerar relatórios sobre conformidade.

Aprenda a:

- [Criar uma configuração Básica de DSC.](/powershell/scripting/dsc/quickstarts/website-quickstart)
- [Configure um servidor de puxar DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Utilize DSC para Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Extensão de Script personalizado do Azure
A extensão do script personalizado Azure para [o Linux](../articles/virtual-machines/linux/extensions-customscript.md) ou [Windows](../articles/virtual-machines/windows/extensions-customscript.md) descarrega e executa scripts em VMs Azure. Pode utilizar a extensão quando criar uma VM ou a qualquer altura depois da VM está em utilização. 

Scripts podem ser transferidos a partir do armazenamento do Azure ou de qualquer localização pública, como um repositório do GitHub. Com a extensão de Script personalizado, pode escrever scripts em qualquer idioma que é executado na VM de origem. Esses scripts podem ser utilizados para instalar aplicativos ou configurar a VM conforme pretendido. Para proteger as credenciais, informações confidenciais como palavras-passe podem ser armazenadas numa configuração protegida. Estas credenciais são desencriptadas apenas dentro da VM.

Aprenda a:

- [Crie um VM Linux com o ClI Azure e utilize a extensão do script personalizado](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Crie um VM Windows com PowerShell Azure e utilize a extensão do script personalizado](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[A Packer](https://www.packer.io) automatiza o processo de construção quando cria uma imagem VM personalizada em Azure. Utilize o Packer para definir o sistema operacional e executar scripts pós-configuração que personalizar as VMS para as suas necessidades específicas. Depois de configurada, a VM, em seguida, é capturada como uma imagem de disco gerido. Packer automatiza o processo para criar a origem de recursos VM, rede e armazenamento, executar scripts de configuração e, em seguida, criar a imagem de VM.

Aprenda a:

- [Use packer para criar uma imagem Linux VM em Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Utilize o Packer para criar uma imagem VM do Windows em Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) é uma ferramenta de automação que permite definir e criar uma infraestrutura Azure inteira com uma única linguagem de formato de modelo - a Linguagem de Configuração HashiCorp (HCL). Com o Terraform, definir modelos que automatizam o processo de criação de rede, armazenamento e recursos da VM para uma solução de determinado aplicativo. Pode utilizar os seus modelos existentes do Terraform para outras plataformas com o Azure para garantir a consistência e simplificar a implementação de infraestrutura sem ser necessário converter um modelo Azure Resource Manager.

Aprenda a:

- [Instale e configure terraforma com Azure](../articles/terraform/terraform-install-configure.md).
- [Crie uma infraestrutura Azure com Terraform.](../articles/terraform/terraform-create-complete-vm.md)


## <a name="azure-automation"></a>Automatização do Azure
[A Azure Automation](https://azure.microsoft.com/services/automation/) utiliza livros de execução para processar um conjunto de tarefas nos VMs que você alvo. A automatização do Azure é utilizada para gerir as VMs existentes em vez de criar uma infra-estrutura. A automatização do Azure pode ser executados em Linux e VMs do Windows, os e máquinas físicas ou virtuais no local com uma função de trabalho de runbook híbrida. Runbooks podem ser armazenados num repositório de controle de origem, como o GitHub. Estes runbooks, em seguida, pode executar manualmente ou com base numa agenda definida.

A automatização do Azure também fornece um serviço de Desired State Configuration (DSC) que permite-lhe criar definições para a forma como deve ser configurado um determinado conjunto de VMs. DSC, em seguida, garante que é aplicada a configuração necessária e a VM permanece consistente. DSC de automatização do Azure é executado em computadores Windows e Linux.

Aprenda a:

- [Crie um livro de execução PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- Utilize o Trabalhador do [Livro híbrido para gerir os recursos no local.](../articles/automation/automation-hybrid-runbook-worker.md)
- [Utilizar o Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Serviços de DevOps do Azure
[A Azure DevOps Services](https://www.visualstudio.com/team-services/) é um conjunto de ferramentas que o ajudam a partilhar e rastrear código, usar construções automatizadas e criar um pipeline de integração e desenvolvimento contínuo completo (CI/CD). Serviços de DevOps do Azure integra-se com o Visual Studio e outros editores para simplificar a utilização. Serviços de DevOps do Azure pode também criar e configurar as VMs do Azure e, em seguida, implementar código a eles.

Saiba mais sobre:

- [Serviços Azure DevOps.](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts)


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) é um servidor de integração contínua que ajuda a implementar e testar aplicações, e criar pipelines automatizados para entrega de códigos. Existem centenas de plug-ins para expandir a plataforma principal do Jenkins e, também pode integrar com muitos outros produtos e soluções através de webhooks. Pode, manualmente, instale o Jenkins numa VM do Azure, executar Jenkins a partir de dentro de um contentor de Docker ou utilizar uma imagem previamente criada do Azure Marketplace.

Aprenda a:

- [Crie uma infraestrutura de desenvolvimento num Linux VM em Azure com Jenkins, GitHub e Docker.](../articles/jenkins/tutorial-jenkins-github-docker-cicd.md)


## <a name="next-steps"></a>Passos seguintes
Existem muitas opções diferentes para utilizar as ferramentas de automatização de infraestruturas no Azure. Tem a liberdade de utilizar a solução que melhor se adequa às suas necessidades e ambiente. Para começar e experimentar algumas das ferramentas incorporadas no Azure, veja como automatizar a personalização de um [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) ou [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM.
