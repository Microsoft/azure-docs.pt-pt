---
title: Utilize ferramentas de automatização de infraestruturas
description: Aprenda a usar ferramentas de automatização de infraestruturas como Ansible, Chef, Puppet, Terraform e Packer para criar e gerir máquinas virtuais em Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/17/2020
ms.author: cynthn
ms.openlocfilehash: a20c19c3da89801570ca4d74a646e3cf0d8434b6
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675907"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Utilize ferramentas de automação de infraestruturas com máquinas virtuais em Azure

Para criar e gerir máquinas virtuais Azure (VMs) de forma consistente em escala, alguma forma de automação é tipicamente desejada. Existem muitas ferramentas e soluções que lhe permitem automatizar o ciclo de vida completo da infraestrutura Azure. Este artigo introduz algumas das ferramentas de automatização de infraestruturas que pode utilizar no Azure. Estas ferramentas geralmente se enquadram numa das seguintes abordagens:

- Automatizar a configuração dos VMs
    - As ferramentas incluem o modelo [Ansible,](#ansible) [Chef,](#chef) [Puppet](#puppet)e [Azure Resource Manager.](#azure-resource-manager-template)
    - Ferramentas específicas para a personalização de VM incluem [inição em nuvem](#cloud-init) para VMs Linux, [Configuração de Estado Desejada (DSC)](#powershell-dsc)e a [extensão de script personalizado Azure](#azure-custom-script-extension) para todos os VMs Azure.

- Automatizar a gestão das infraestruturas
    - As ferramentas incluem [packer](#packer) para automatizar construções de imagem VM personalizadas, e [Terraform](#terraform) para automatizar o processo de construção de infraestrutura.
    - [A Azure Automation](#azure-automation) pode realizar ações através da sua infraestrutura Azure e no local.

- Automatizar a implementação e entrega de aplicações
    - Exemplos incluem [Azure DevOps Services](#azure-devops-services) e [Jenkins.](#jenkins)

## <a name="ansible"></a>Ansible
[O Ansible](https://www.ansible.com/) é um motor de automatização para gestão de configuração, criação de VM ou implementação de aplicações. A Ansible utiliza um modelo sem agente, tipicamente com chaves SSH, para autenticar e gerir máquinas-alvo. As tarefas de configuração são definidas em playbooks, com uma série de módulos Ansible disponíveis para executar tarefas específicas. Para mais informações, consulte [como funciona o Ansible.](https://www.ansible.com/how-ansible-works)

Aprenda a:

- [Instale e configuure Ansível no Linux para utilização com a Azure](/azure/developer/ansible/install-on-linux-vm).
- [Crie uma máquina virtual Linux.](/azure/developer/ansible/vm-configure)
- [Gerir uma máquina virtual Linux.](/azure/developer/ansible/vm-manage)


## <a name="chef"></a>Chef
[O Chef](https://www.chef.io/) é uma plataforma de automação que ajuda a definir como a sua infraestrutura é configurada, implantada e gerida. Componentes adicionais incluem Chef Habitat para automação de ciclo de vida de aplicação em vez da infraestrutura, e Chef InSpec que ajuda a automatizar o cumprimento com os requisitos de segurança e política. Os Clientes Chef são instalados em máquinas-alvo, com um ou mais servidores centrais do Chef que armazenam e gerem as configurações. Para mais informações, consulte [Uma Visão Geral do Chef.](https://docs.chef.io/chef_overview.html)

Aprenda a:

- [Implementar chef Automate do Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview)
- [Instale o Chef no Windows e crie VMs Azure](/azure/developer/chef/windows-vm-configure).


## <a name="puppet"></a>Puppet
[A Puppet](https://www.puppet.com) é uma plataforma de automatização pronta para a empresa que trata do processo de entrega e implementação de aplicações. Os agentes são instalados em máquinas-alvo para permitir que o Mestre das Marionetas corram manifestos que definam a configuração desejada da infraestrutura Azure e VMs. O boneco pode integrar-se com outras soluções, como Jenkins e GitHub, para um fluxo de trabalho de devops melhorado. Para mais informações, consulte [como funciona o Boneco.](https://puppet.com/products/how-puppet-works)

Aprenda a:

- [Desdobrar marionetas](https://puppet.com/docs/puppet/5.5/install_windows.html).


## <a name="cloud-init"></a>Inicialização da cloud
[Cloud-init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux quando arranca pela primeira vez. Pode utilizar o cloud-init para instalar pacotes e escrever ficheiros ou para configurar utilizadores e segurança. Como a cloud-init é chamada durante o processo inicial de arranque, não existem passos adicionais ou agentes necessários para aplicar a sua configuração.  Para obter mais informações sobre como formatar corretamente os seus `#cloud-config` ficheiros, consulte o [site de documentação cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` os ficheiros são ficheiros de texto codificados na base64.

O cloud-init também funciona em distribuições. Por exemplo, não utiliza **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes a instalar. O cloud-init utiliza automaticamente a ferramenta de gestão de pacotes nativa para a distribuição que selecionar.

Estamos a trabalhar ativamente com os nossos parceiros de distro Linux endossados, de forma a ter imagens ativadas em nuvem disponíveis no Azure Marketplace. Estas imagens fazem com que as suas implementações e configurações de ineção em nuvem funcionem perfeitamente com VMs e conjuntos de escala de máquinas virtuais.
Saiba mais detalhes sobre cloud-init em Azure:

- [Suporte cloud-init para máquinas virtuais Linux em Azure](./linux/using-cloud-init.md)
- [Experimente um tutorial sobre a configuração de VM automatizada utilizando o cloud-init](./linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>DSC do PowerShell
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) é uma plataforma de gestão para definir a configuração das máquinas-alvo. O DSC também pode ser usado no Linux através do [servidor Open Management Infrastructure (OMI).](https://collaboration.opengroup.org/omi/)

As configurações do DSC definem o que instalar numa máquina e como configurar o hospedeiro. Um motor de configuração local (LCM) funciona em cada nó-alvo que processa as ações solicitadas com base em configurações empurradas. Um servidor pull é um serviço web que funciona num anfitrião central para armazenar as configurações do DSC e os recursos associados. O servidor pull comunica com o motor LCM em cada anfitrião alvo para fornecer as configurações necessárias e reportar sobre a conformidade.

Aprenda a:

- [Criar uma configuração DSC básica.](/powershell/scripting/dsc/quickstarts/website-quickstart)
- [Configurar um servidor de puxar DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Utilize o DSC para Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Extensão de Script Personalizado do Azure
A extensão de script personalizado Azure para [o Linux](./extensions/custom-script-linux.md) ou [Windows](./extensions/custom-script-windows.md) descarrega e executa scripts em VMs Azure. Pode utilizar a extensão quando criar um VM, ou em qualquer momento após a utilização do VM.

Os scripts podem ser descarregados a partir de um armazenamento Azure ou de qualquer local público, como um repositório GitHub. Com a extensão de script personalizado, pode escrever scripts em qualquer idioma que seja executado no VM de origem. Estes scripts podem ser utilizados para instalar aplicações ou configurar o VM conforme desejado. Para obter credenciais seguras, informações sensíveis como palavras-passe podem ser armazenadas numa configuração protegida. Estas credenciais só são desencriptadas dentro do VM.

Aprenda a:

- [Crie um Linux VM com o CLI Azure e use a extensão de script personalizada.](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx?toc=%2fcli%2fazure%2ftoc.json)
- [Crie um VM do Windows com Azure PowerShell e utilize a extensão de script personalizada](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis).


## <a name="packer"></a>Embalador
[O Packer](https://www.packer.io) automatiza o processo de construção quando cria uma imagem VM personalizada em Azure. Utiliza o Packer para definir o SISTEMA e executar scripts pós-configuração que personalizam o VM para as suas necessidades específicas. Uma vez configurado, o VM é então capturado como uma imagem de disco gerido. O Packer automatiza o processo para criar os recursos de VM de origem, rede e armazenamento, executar scripts de configuração e, em seguida, criar a imagem VM.

Aprenda a:

- [Utilize o Packer para criar uma imagem Linux VM em Azure](./linux/build-image-with-packer.md).
- [Utilize o Packer para criar uma imagem VM do Windows em Azure](./windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) é uma ferramenta de automação que permite definir e criar toda uma infraestrutura Azure com uma única linguagem de formato de modelo - a Linguagem de Configuração HashiCorp (HCL). Com a Terraform, define modelos que automatizam o processo para criar recursos de rede, armazenamento e VM para uma determinada solução de aplicação. Pode utilizar os seus modelos Terraform existentes para outras plataformas com a Azure para garantir consistência e simplificar a implementação da infraestrutura sem precisar de se converter num modelo de Gestor de Recursos Azure.

Aprenda a:

- [Instale e configuure Terraform com Azure](/azure/developer/terraform/getting-started-cloud-shell).
- [Criar uma infraestrutura Azure com Terraform.](/azure/developer/terraform/create-linux-virtual-machine-with-infrastructure)


## <a name="azure-automation"></a>Automatização do Azure
[A Azure Automation](https://azure.microsoft.com/services/automation/) utiliza livros de execução para processar um conjunto de tarefas nos VMs que tem como alvo. A Azure Automation é usada para gerir os VM existentes em vez de criar uma infraestrutura. A Azure Automation pode correr através de Linux e Windows VMs, bem como máquinas virtuais ou físicas no local com um trabalhador de runbook híbrido. Os runbooks podem ser armazenados num repositório de controlo de origem, como o GitHub. Estes livros podem então funcionar manualmente ou com um horário definido.

A Azure Automation também fornece um serviço de Configuração Estatal (DSC) que permite criar definições de como um determinado conjunto de VMs deve ser configurado. A DSC assegura então que a configuração necessária é aplicada e que o VM permanece consistente. O Azure Automation DSC funciona em ambas as máquinas Windows e Linux.

Aprenda a:

- [Criar um livro de execução PowerShell](../automation/learn/automation-tutorial-runbook-textual-powershell.md).
- [Utilize o Trabalhador de Runbook Híbrido para gerir os recursos no local.](../automation/automation-hybrid-runbook-worker.md)
- [Utilizar a Azure Automation DSC](../automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[O Azure DevOps Services](https://www.visualstudio.com/team-services/) é um conjunto de ferramentas que o ajudam a partilhar e rastrear código, a usar construções automatizadas e a criar um oleoduto completo de integração e desenvolvimento contínuo (CI/CD). A Azure DevOps Services integra-se com o Visual Studio e outros editores para simplificar o uso. Os Serviços Azure DevOps também podem criar e configurar VMs Azure e, em seguida, implementar código para eles.

Saiba mais sobre:

- [Serviços Azure DevOps](/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://jenkins.io) é um servidor de integração contínua que ajuda a implementar e testar aplicações, e criar oleodutos automatizados para a entrega de códigos. Existem centenas de plugins para estender a plataforma core Jenkins, e você também pode integrar-se com muitos outros produtos e soluções através de webhooks. Pode instalar manualmente o Jenkins num Azure VM, executar o Jenkins dentro de um contentor Docker ou utilizar uma imagem pré-construída do Azure Marketplace.

Aprenda a:

- [Crie uma infraestrutura de desenvolvimento num Linux VM em Azure com Jenkins, GitHub e Docker.](/azure/developer/jenkins/pipeline-with-github-and-docker)


## <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/templates/overview.md) é o serviço de implementação e gestão da Azure. Fornece uma camada de gestão que lhe permite criar, atualizar e eliminar recursos na sua subscrição Azure. Pode utilizar funcionalidades de gestão, como controlo de acesso, bloqueios e etiquetas, para proteger e organizar os seus recursos após a implementação.

Aprenda a:

- [Implementar VMs spot utilizando um modelo de Gestor de Recursos](./linux/spot-template.md).
- [Crie uma máquina virtual Windows a partir de um modelo de Gestor de Recursos.](./windows/ps-template.md)
- [Descarregue o modelo para um VM](/previous-versions/azure/virtual-machines/windows/download-template).
- [Crie um modelo de construtor de imagens Azure.](./linux/image-builder-json.md)

## <a name="next-steps"></a>Passos seguintes
Existem muitas opções diferentes para usar ferramentas de automação de infraestruturas em Azure. Tem a liberdade de usar a solução que melhor se adequa às suas necessidades e ambiente. Para começar e experimentar algumas das ferramentas incorporadas no Azure, veja como automatizar a personalização de um [Linux](./linux/tutorial-automate-vm-deployment.md) ou [Windows](./windows/tutorial-automate-vm-deployment.md) VM.