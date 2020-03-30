---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 9cbc48d8bca2f7491d0464be1c5bd64054927dc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77608754"
---
Para criar e gerir as máquinas virtuais Azure (VMs) de forma consistente à escala, é tipicamente desejada alguma forma de automatização. Existem muitas ferramentas e soluções que lhe permitem automatizar o ciclo de vida completo da infraestrutura Azure e gestão. Este artigo introduz algumas das ferramentas de automação de infraestruturas que pode utilizar no Azure. Estas ferramentas geralmente se encaixam numa das seguintes abordagens:

- Automatizar a configuração dos VMs
    - As ferramentas incluem [Ansible,](#ansible) [Chef](#chef)e [Puppet.](#puppet)
    - As ferramentas específicas para a personalização vM incluem [cloud-init](#cloud-init) para VMs Linux, [PowerShell Desired State Configuration (DSC)](#powershell-dsc)e a extensão de [script personalizado Azure](#azure-custom-script-extension) para todos os VMs Azure.
 
- Gestão de infraestruturas automatizadas
    - As ferramentas incluem [packer](#packer) para automatizar construções de imagem VM personalizadas, e [Terraform](#terraform) para automatizar o processo de construção de infraestruturas.
    - A [Azure Automation](#azure-automation) pode realizar ações em toda a sua infraestrutura Azure e no local.

- Automatizar a implementação e entrega de aplicações
    - Exemplos incluem [Azure DevOps Services](#azure-devops-services) e [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) é um motor de automatização para a gestão de configuração, criação vM ou implementação de aplicações. Ansible usa um modelo sem agente, tipicamente com teclas SSH, para autenticar e gerir máquinas-alvo. As tarefas de configuração são definidas em playbooks, com vários módulos Ansible disponíveis para realizar tarefas específicas. Para mais informações, consulte [como funciona o Ansible.](https://www.ansible.com/how-ansible-works)

Aprenda a:

- [Instale e configure Ansible no Linux para utilização com o Azure](../articles/ansible/ansible-install-configure.md).
- [Crie uma máquina virtual Linux.](../articles/ansible/ansible-create-vm.md)
- [Gerencie uma máquina virtual Linux.](../articles/ansible/ansible-manage-linux-vm.md)


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) é uma plataforma de automação que ajuda a definir como a sua infraestrutura é configurada, implantada e gerida. Componentes adicionais incluíam o Chef Habitat para a automatização de ciclo de vida de aplicação em vez da infraestrutura, e o Chef InSpec que ajuda a automatizar o cumprimento dos requisitos de segurança e política. Os Clientes Chef estão instalados em máquinas-alvo, com um ou mais Servidores chef centrais que armazenam e gerem as configurações. Para mais informações, consulte [Uma visão geral do Chef.](https://docs.chef.io/chef_overview.html)

Aprenda a:

- [Implementar chef automatizar do Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview)
- [Instale chef no Windows e crie VMs Azure.](../articles/chef/chef-automation.md)


## <a name="puppet"></a>Puppet
[A Puppet](https://www.puppet.com) é uma plataforma de automação pronta para a empresa que trata do processo de entrega e implementação de aplicações. Os agentes são instalados em máquinas-alvo para permitir que o Mestre das Marionetas execute manifestos que definam a configuração desejada da infraestrutura Azure e VMs. A marioneta pode integrar-se com outras soluções, como jenkins e GitHub para um fluxo de trabalho de devops melhorado. Para mais informações, consulte como funciona o Boneco de [Marionetas.](https://puppet.com/products/how-puppet-works)

Aprenda a:

- [Implementar marionetas do Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview)


## <a name="cloud-init"></a>Inicialização da cloud
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como o cloud-init é chamado durante o processo inicial de arranque, não existem passos adicionais ou agentes necessários para aplicar a sua configuração.  Para obter mais informações sobre `#cloud-config` como formatar corretamente os seus ficheiros, consulte o [site de documentação init init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`os ficheiros são ficheiros de texto codificados na base64.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Estamos a trabalhar ativamente com os nossos parceiros de distro linux endossados para ter imagens ativadas em nuvem disponíveis no mercado Azure. Estas imagens fazem com que as suas implementações e configurações de cloud-init funcionem perfeitamente com VMs e conjuntos de escala de máquinas virtuais. Saiba mais detalhes sobre cloud-init em Azure:

- [Suporte cloud-init para máquinas virtuais Linux em Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Experimente um tutorial sobre a configuração vm automatizada utilizando cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) é uma plataforma de gestão para definir a configuração das máquinas-alvo. O DSC também pode ser utilizado no Linux através do [servidor Open Management Infrastructure (OMI).](https://collaboration.opengroup.org/omi/)

As configurações dSC definem o que instalar numa máquina e como configurar o hospedeiro. Um motor De Configuração Local (LCM) funciona em cada nó alvo que processa as ações solicitadas com base em configurações empurradas. Um servidor de pull é um serviço web que funciona num anfitrião central para armazenar as configurações dSC e recursos associados. O servidor pull comunica com o motor LCM em cada anfitrião-alvo para fornecer as configurações necessárias e reportar sobre a conformidade.

Aprenda a:

- [Criar uma configuração Básica de DSC.](/powershell/scripting/dsc/quickstarts/website-quickstart)
- [Configure um servidor de puxar DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Utilize DSC para Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Extensão de Script Personalizado do Azure
A extensão do script personalizado Azure para [o Linux](../articles/virtual-machines/linux/extensions-customscript.md) ou [Windows](../articles/virtual-machines/windows/extensions-customscript.md) descarrega e executa scripts em VMs Azure. Pode utilizar a extensão quando criar um VM, ou em qualquer momento após a utilização do VM. 

Scripts podem ser descarregados a partir do armazenamento Azure ou qualquer local público, como um repositório GitHub. Com a extensão do script personalizado, pode escrever scripts em qualquer idioma que seja executado na fonte VM. Estes scripts podem ser utilizados para instalar aplicações ou configurar o VM conforme desejado. Para garantir credenciais, informações sensíveis, como senhas, podem ser armazenadas numa configuração protegida. Estas credenciais só são desencriptadas dentro do VM.

Aprenda a:

- [Crie um VM Linux com o ClI Azure e utilize a extensão do script personalizado](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Crie um VM Windows com PowerShell Azure e utilize a extensão do script personalizado](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Embalador
[A Packer](https://www.packer.io) automatiza o processo de construção quando cria uma imagem VM personalizada em Azure. Você usa packer para definir o OS e executar scripts de pós-configuração que personalizam o VM para as suas necessidades específicas. Uma vez configurado, o VM é então capturado como uma imagem de Disco Gerido. A Packer automatiza o processo para criar os recursos VM de origem, rede e armazenamento, executar scripts de configuração e, em seguida, criar a imagem VM.

Aprenda a:

- [Use packer para criar uma imagem Linux VM em Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Utilize o Packer para criar uma imagem VM do Windows em Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) é uma ferramenta de automação que permite definir e criar uma infraestrutura Azure inteira com uma única linguagem de formato de modelo - a Linguagem de Configuração HashiCorp (HCL). Com a Terraform, define modelos que automatizam o processo para criar recursos de rede, armazenamento e VM para uma determinada solução de aplicação. Você pode usar os seus modelos Terraform existentes para outras plataformas com O Azure para garantir consistência e simplificar a implementação da infraestrutura sem precisar de se converter para um modelo de Gestor de Recursos Azure.

Aprenda a:

- [Instale e configure terraforma com Azure](../articles/terraform/terraform-install-configure.md).
- [Crie uma infraestrutura Azure com Terraform.](../articles/terraform/terraform-create-complete-vm.md)


## <a name="azure-automation"></a>Automatização do Azure
[A Azure Automation](https://azure.microsoft.com/services/automation/) utiliza livros de execução para processar um conjunto de tarefas nos VMs que você alvo. A Azure Automation é usada para gerir os VMs existentes em vez de criar uma infraestrutura. A Azure Automation pode passar por VMs Linux e Windows, bem como máquinas virtuais ou físicas no local com um trabalhador híbrido do livro de corridas. Os livros de execução podem ser armazenados num repositório de controlo de fonte, como o GitHub. Estes livros de execução podem então ser executados manualmente ou em horário definido.

A Azure Automation também fornece um serviço de Configuração do Estado Desejado (DSC) que lhe permite criar definições para a configuração de um determinado conjunto de VMs. A DSC garante então que a configuração necessária é aplicada e que o VM se mantém consistente. O Azure Automation DSC funciona tanto nas máquinas Windows como linux.

Aprenda a:

- [Crie um livro de execução PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- Utilize o Trabalhador do [Livro híbrido para gerir os recursos no local.](../articles/automation/automation-hybrid-runbook-worker.md)
- [Utilizar o Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Serviços de DevOps do Azure
[A Azure DevOps Services](https://www.visualstudio.com/team-services/) é um conjunto de ferramentas que o ajudam a partilhar e rastrear código, usar construções automatizadas e criar um pipeline de integração e desenvolvimento contínuo completo (CI/CD). A Azure DevOps Services integra-se com o Visual Studio e outros editores para simplificar o uso. Os Serviços Azure DevOps também podem criar e configurar VMs Azure e, em seguida, implementar código para eles.

Saiba mais sobre:

- [Serviços Azure DevOps.](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts)


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) é um servidor de integração contínua que ajuda a implementar e testar aplicações, e criar pipelines automatizados para entrega de códigos. Existem centenas de plugins para estender a plataforma core Jenkins, e você também pode integrar-se com muitos outros produtos e soluções através de webhooks. Pode instalar o Jenkins manualmente num VM Azure, executar jenkins dentro de um contentor Docker ou usar uma imagem azure marketplace pré-construída.

Aprenda a:

- [Crie uma infraestrutura de desenvolvimento num Linux VM em Azure com Jenkins, GitHub e Docker.](../articles/jenkins/tutorial-jenkins-github-docker-cicd.md)


## <a name="next-steps"></a>Passos seguintes
Existem muitas opções diferentes para usar ferramentas de automação de infraestruturas em Azure. Tem a liberdade de usar a solução que melhor se adequa às suas necessidades e ambiente. Para começar e experimentar algumas das ferramentas incorporadas no Azure, veja como automatizar a personalização de um [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) ou [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM.
