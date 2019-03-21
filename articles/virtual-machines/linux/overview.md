---
title: Descrição Geral das VMs do Linux no Azure | Microsoft Docs
description: Descreve os serviços de Computação, Armazenamento e de Rede do Azure com as máquinas virtuais do Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: jeconnoc
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: a5203d75e673a7b8206446553414ff933e00b0b6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008937"
---
# <a name="azure-and-linux"></a>Azure e Linux
O Microsoft Azure é uma coleção crescente de serviços na cloud pública, incluindo análises, máquinas virtuais, bases de dados e capacidades móveis, Web, de rede e de armazenamento,&mdash;ideal para alojar as suas soluções.  O Microsoft Azure fornece uma plataforma informática dimensionável que lhe permite pagar apenas aquilo que utiliza, quando quer utilizar, sem que precise de investir em hardware no local.  O Azure está sempre pronto quando precisa de aumentar as suas soluções vertical e horizontalmente até atingir a dimensão de que necessita para satisfazer as necessidades dos seus clientes.

Se estiver familiarizado com as várias funcionalidades do AWS da Amazon, pode examinar o [documento de mapeamento de definições](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

## <a name="regions"></a>Regiões
Os recursos do Microsoft Azure são distribuídos em várias regiões geográficas em todo o mundo.  Uma "região" representa vários datacenters numa única área geográfica. O Azure tem atualmente (em agosto de 2018) 42 regiões geralmente disponíveis em todo o mundo, com outras 12 regiões anunciadas - mais regiões globais que qualquer outro fornecedor de cloud. É possível encontrar uma lista atualizada de regiões existentes e recentemente anunciadas na seguinte página:

* [Regiões do Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilidade
O Azure anunciou um Contrato de Nível de Serviço líder da indústria de 99,9% para máquinas virtuais de instância única, desde que implemente a VM com armazenamento premium para todos os discos.  Para a sua implementação se qualificar para o Contrato de Nível de Serviço de VM de 99,95% standard, continua a ter de implementar duas ou mais VMs que executem a sua carga de trabalho num conjunto de disponibilidade. Um conjunto de disponibilidade garante que as suas VMs são distribuídas em vários domínios de falha nos datacenters do Azure, bem como implementadas em anfitriões com diferentes janelas de manutenção. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

## <a name="managed-disks"></a>Managed Disks

O Managed Disks processa a criação e a gestão da conta de Armazenamento do Azure em segundo plano por si e assegura que não tem de se preocupar com os limites de escalabilidade da conta de armazenamento. Especifique o tamanho do disco e o escalão de desempenho (Standard ou Premium), e o Azure cria e gere o disco. Conforme adiciona discos ou aumente e reduza verticalmente a VM, não terá de se preocupar se o armazenamento está a ser utilizado. Se estiver a criar VMs novas, [utilize a CLI do Azure](quick-create-cli.md) ou o portal do Azure para criar as VMs com discos de dados e de SO geridos. Se tiver VMs com discos não geridos, pode [converter as VMs para a criação de cópias de segurança com o Managed Disk](convert-unmanaged-to-managed-disks.md).

Também pode gerir as imagens personalizadas numa conta de armazenamento por região do Azure e utilizá-las para criar centenas de VMs na mesma subscrição. Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](../linux/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Máquinas Virtuais do Azure e Instâncias
O Microsoft Azure suporta a execução de diversas distribuições Linux populares, que são disponibilizadas e mantidas por múltiplos parceiros.  Pode encontrar distribuições como Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD, entre outras, no Azure Marketplace. A Microsoft trabalha ativamente com várias comunidades do Linux para adicionar ainda mais tipos à lista [Azure endorsed Linux Distros](endorsed-distros.md)(Distribuições Linux aprovadas pelo Azure).

Se a distribuição que preferir não estiver incluída atualmente na galeria, pode [criar e carregar um VHD do Linux para o Azure](create-upload-generic.md) para “Trazer a sua própria VM do Linux”.

As máquinas virtuais do Azure permitem-lhe implementar uma vasta gama de soluções de computação de forma ágil. Pode implementar virtualmente qualquer carga de trabalho e qualquer linguagem em praticamente qualquer sistema operativo - Windows, Linux ou num sistema personalizado criado com base na crescente lista de parceiros. Ainda não encontrou o que procura?  Não se preocupe, também pode incluir as suas próprias imagens do local.

## <a name="vm-sizes"></a>Tamanhos de VMs
O [tamanho](sizes.md) da VM que utiliza é determinado pela carga de trabalho que pretende executar. O tamanho que escolher determina fatores como o poder de processamento, a memória e capacidade de armazenamento. O Azure disponibiliza uma vasta variedade de tamanhos para suportar muitos tipos de utilizações.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) com base no tamanho da VM e do sistema operativo. Para horas parciais, o Azure cobra apenas os minutos utilizados. O armazenamento tem um preço à parte e é cobrado separadamente.

## <a name="automation"></a>Automatização
Para obter uma cultura de Dev/Ops adequada, toda a infraestrutura deve ser código.  Se toda a infraestrutura residir em código, pode ser facilmente recriada (Servidores Phoenix).  O Azure funciona com as principais ferramentas de automatização, como Ansible, Chef, SaltStack e Puppet.  O Azure também tem as suas próprias ferramentas de automatização:

* [Modelos do Azure](create-ssh-secured-vm-from-template.md)
* [VMAccess do Azure](using-vmaccess-extension.md)

O Azure está a implementar o suporte para o [cloud-init](https://cloud-init.io/) em todas as Distribuições Linux que o suportem.  Atualmente, as VMs Ubuntu da Canonical são implementadas com o cloud-init ativado por predefinição.  Red Hat RHEL, CentOS e Fedora suportam o cloud-init; contudo, as imagens do Azure que o Red Hat mantém não o têm atualmente instalado.  Para utilizar o cloud-init numa família de SO Red Hat, tem de criar uma imagem personalizada com o cloud-init instalado.

* [Utilizar o cloud-init em VMs do Linux no Azure](using-cloud-init.md)

## <a name="quotas"></a>Quotas
Cada subscrição do Azure tem limites de quota predefinidos que podem afetar a implementação de um grande número de VMs do seu projeto. O limite atual numa base por subscrição é de 20 VMs por região.  Os limites de quotas podem ser levantados de forma rápida e fácil, mediante o envio de um pedido de aumento do limite.  Para obter mais detalhes sobre os limites de quotas:

* [Limites do Serviço das Subscrições do Azure](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Parceiros
A Microsoft trabalha de perto com os parceiros para garantir que as imagens disponíveis são atualizadas e otimizadas para runtimes do Azure.  Para obter mais informações sobre os parceiros do Azure, veja as seguintes ligações:

* Linux no Azure - [Endorsed Distributions](endorsed-distros.md) (Distribuições Suportadas)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat - [Azure Marketplace - Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [do Azure Marketplace - FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/) (Mesosphere DC/OS no Azure)
* Docker - [Azure Marketplace - Azure Container Service with Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/) (Azure Container Service com Docker Swarm)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)

## <a name="getting-started-with-linux-on-azure"></a>Introdução ao Linux no Azure
Para começar a utilizar o Azure, precisa de uma conta do Azure, da CLI do Azure instalada e de um par de chaves SSH públicas e privadas.

### <a name="sign-up-for-an-account"></a>Inscrever-se para obter uma conta
O primeiro passo para utilizar a Cloud do Azure é inscrever-se numa conta do Azure.  Para começar, aceda à página [Incsrição na Conta do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="install-the-cli"></a>Instalar a CLI
Com a conta do Azure nova, pode começar a utilizar o portal do Azure imediatamente, que é um painel de administração baseado na Web.  Para gerir a Cloud do Azure através da linha de comandos, instale `azure-cli`.  Instale a [CLI do Azure](/cli/azure/install-azure-cli) na sua área de trabalho Mac ou Linux.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
Tem agora uma conta do Azure, o portal Web do Azure e a CLI do Azure.  O passo seguinte consiste em criar um par de chaves SSH que é utilizado para aceder através de SSH ao Linux sem utilizar uma palavra-passe.  [Crie chaves SSH em Linux e Mac](mac-create-ssh-keys.md) para permitir inícios de sessão sem palavra-passe e uma segurança melhor.

### <a name="create-a-vm-using-the-cli"></a>Criar uma VM com a CLI
Criar VMs do Linux com a CLI é uma forma rápida de implementá-las sem sair do terminal onde está a trabalhar.  Tudo o que puder ser especificado no portal Web está disponível através de um sinalizador ou de um comutador da linha de comandos.  

* [Criar uma VM do Linux através da CLI](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Criar uma VM no portal
Uma forma fácil de criar máquinas virtuais do Linux no portal Web do Azure é selecionar as diversas opções de implementação e clicar nas mesmas.  Em vez de utilizar os sinalizadores ou os comutadores da linha de comandos, pode ver um esquema Web agradável com as várias opções e definições.  Tudo o que estiver disponível através da interface da linha de comandos também está disponível no portal.

* [Criar uma VM do Linux através do portal](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Iniciar sessão através de SSH sem utilizar palavra-passe
A VM está agora em execução no Azure e já pode iniciar sessão.  A utilização de palavras-passe para iniciar sessão através de SSH é insegura e demorada.  A utilização de chaves SSH é a forma mais segura e mais rápida de iniciar sessão.  Quando cria uma VM do Linux com o portal ou a CLI, tem duas opções de autenticação.  Se escolher uma palavra-passe para SSH, o Azure configura a VM de modo a permitir inícios de sessão através de palavra-passe.  Se optar por utilizar uma chave pública SSH, o Azure configura a VM para permitir apenas inícios de sessão através de chaves SSH e desativa os inícios de sessão com palavra-passe. Para proteger a VM do Linux mediante a permissão de apenas inícios de sessão com chave SSH, utilize a opção de chave SSH pública durante a criação da mesma no portal ou na CLI.

## <a name="related-azure-components"></a>Componentes do Azure relacionados
## <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
* [Add a disk to a Linux VM using the azure-cli](add-disk.md) (Adicionar um disco a uma VM do Linux com a CLI do Azure)
* [How to attach a data disk to a Linux VM in the Azure portal](attach-disk-portal.md) (Como anexar um disco de dados a uma VM do Linux no portal do Azure)

## <a name="networking"></a>Redes
* [Virtual Network Overview](../../virtual-network/virtual-networks-overview.md) (Descrição Geral da Rede Virtual)
* [Endereços IP do Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Opening ports to a Linux VM in Azure](nsg-quickstart.md) (Abrir portas para uma VM do Linux no Azure)
* [Create a Fully Qualified Domain Name in the Azure portal](portal-create-fqdn.md) (Criar um Nome de Domínio Completamente Qualificado no portal do Azure)

## <a name="containers"></a>Contentores
* [Máquinas Virtuais e Contentores no Azure](containers.md)
* [Introdução ao Azure Container Service](../../container-service/container-service-intro.md)
* [Implementar um cluster do Azure Container Service](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Passos Seguintes
Tem agora uma descrição geral do Linux no Azure.  O passo seguinte é avançar e criar algumas VMs!

* [Explore the growing list of sample scripts for common tasks via AzureCLI](cli-samples.md) (Explorar a crescente lista de scripts de exemplo para tarefas comuns através da CLI do Azure)
