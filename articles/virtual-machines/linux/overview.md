---
title: Visão geral dos VMs linux em Azure
description: Visão geral das máquinas virtuais Linux em Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 69a9722436aea1cf794e6e3f3ce02ec79180cff3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80159477"
---
# <a name="linux-virtual-machines-in-azure"></a>Máquinas virtuais do Linux no Azure

As Máquinas Virtuais (VMs) do Azure são um dos vários tipos de [recursos informáticos a pedido](/azure/architecture/guide/technology-choices/compute-decision-tree), dimensionáveis que o Azure oferece. Normalmente, escolher uma VM, se precisar de mais controlo sobre o ambiente informático que as outras opções oferecem. Este artigo dá-lhe informações sobre o que deve considerar antes de criar uma VM, como criá-la e geri-la.

Uma VM do Azure fornece-lhe a flexibilidade de virtualização sem ter de comprar e manter o hardware físico que executa a VM. No entanto, ainda tem de manter a VM a realizar tarefas, como configurar, aplicar patches e instalar o software que é executado na mesma.

As máquinas virtuais do Azure podem ser utilizadas de várias formas. Alguns exemplos incluem:

* **Desenvolvimento e teste** – as VMs do Azure oferecem uma forma rápida e fácil de criar um computador com as configurações específicas necessárias para codificar e testar uma aplicação.
* **Aplicações na nuvem** – uma vez que a procura da sua aplicação pode variar, em termos económicos, poderá fazer sentido executá-la numa VM no Azure. Paga pelas VMs adicionais quando precisar delas e encerra-as quando não precisar.
* **Datacenter expandido** – as máquinas virtuais numa rede virtual do Azure podem ser facilmente ligadas à rede da sua organização.

O número de VMs que a aplicação utiliza pode ser vertical e horizontalmente aumentado para o valor que for preciso para satisfazer as necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é preciso ter em conta antes de criar uma VM?
Existem sempre inúmeras [considerações de design](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) quando está a desenvolver uma infraestrutura de aplicação no Azure. Estes aspetos de uma VM são importantes e devem ser ponderados antes de começar:

* Os nomes dos recursos da aplicação
* A localização onde os recursos são armazenados
* O tamanho da VM
* O número máximo de VMs que podem ser criadas
* O sistema operativo que a VM executa
* A configuração da VM depois de iniciar
* Os recursos relacionados que a VM precisa

### <a name="locations"></a>Localizações
Todos os recursos criados no Azure são distribuídos em várias [regiões geográficas](https://azure.microsoft.com/regions/) em todo o mundo. Normalmente, a região é designada por **localização** ao criar uma VM. Para uma VM, a localização especifica onde os discos rígidos virtuais são armazenados.

Esta tabela apresenta algumas das formas de obter uma lista de localizações disponíveis.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Selecione uma localização da lista ao criar uma VM. |
| Azure PowerShell |Utilize o comando [Get-AzLocation.](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) |
| API REST |Utilize a operação [Listar localizações](https://docs.microsoft.com/rest/api/resources/subscriptions). |
| CLI do Azure |Utilize a operação [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest). |

## <a name="availability"></a>Disponibilidade
O Azure anunciou um Contrato de Nível de Serviço líder da indústria de 99,9% para máquinas virtuais de instância única, desde que implemente a VM com armazenamento premium para todos os discos.  Para a sua implementação se qualificar para o Contrato de Nível de Serviço de VM de 99,95% standard, continua a ter de implementar duas ou mais VMs que executem a sua carga de trabalho num conjunto de disponibilidade. Um conjunto de disponibilidade garante que as suas VMs são distribuídas em vários domínios de falha nos datacenters do Azure, bem como implementadas em anfitriões com diferentes janelas de manutenção. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

## <a name="vm-size"></a>Tamanho da VM
O [tamanho](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) da VM que utiliza é determinado pela carga de trabalho que pretende executar. O tamanho que escolher determina fatores como o poder de processamento, a memória e capacidade de armazenamento. O Azure disponibiliza uma vasta variedade de tamanhos para suportar muitos tipos de utilizações.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) com base no tamanho e no sistema operativo do VM. Para horas parciais, o Azure cobra apenas os minutos utilizados. O armazenamento tem um preço à parte e é cobrado separadamente.

## <a name="vm-limits"></a>Limites da VM
A subscrição tem [limites de quota](../../azure-resource-manager/management/azure-subscription-service-limits.md) predefinidos num local que pode afetar a implementação de muitas VMs para o seu projeto. O limite atual numa base por subscrição é de 20 VMs por região. Os limites podem ser aumentados ao [preencher um pedido de suporte a pedir um aumento](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Managed Disks

O Managed Disks processa a criação e a gestão da conta de Armazenamento do Azure em segundo plano por si e assegura que não tem de se preocupar com os limites de escalabilidade da conta de armazenamento. Especifique o tamanho do disco e o escalão de desempenho (Standard ou Premium), e o Azure cria e gere o disco. Conforme adiciona discos ou aumente e reduza verticalmente a VM, não terá de se preocupar se o armazenamento está a ser utilizado. Se estiver a criar VMs novas, [utilize a CLI do Azure](quick-create-cli.md) ou o portal do Azure para criar as VMs com discos de dados e de SO geridos. Se tiver VMs com discos não geridos, pode [converter as VMs para a criação de cópias de segurança com o Managed Disk](convert-unmanaged-to-managed-disks.md).

Também pode gerir as imagens personalizadas numa conta de armazenamento por região do Azure e utilizá-las para criar centenas de VMs na mesma subscrição. Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](../linux/managed-disks-overview.md).

## <a name="distributions"></a>Distribuição 
O Microsoft Azure suporta a execução de diversas distribuições Linux populares, que são disponibilizadas e mantidas por múltiplos parceiros.  Pode encontrar distribuições como Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD, entre outras, no Azure Marketplace. A Microsoft trabalha ativamente com várias comunidades do Linux para adicionar ainda mais tipos à lista [Azure endorsed Linux Distros](endorsed-distros.md)(Distribuições Linux aprovadas pelo Azure).

Se a distribuição que preferir não estiver incluída atualmente na galeria, pode [criar e carregar um VHD do Linux para o Azure](create-upload-generic.md) para “Trazer a sua própria VM do Linux”.

A Microsoft trabalha de perto com os parceiros para garantir que as imagens disponíveis são atualizadas e otimizadas para runtimes do Azure.  Para obter mais informações sobre os parceiros do Azure, veja as seguintes ligações:

* Linux no Azure - [Endorsed Distributions](endorsed-distros.md) (Distribuições Suportadas)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat - [Azure Marketplace - Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/) (Mesosphere DC/OS no Azure)
* Docker - [Azure Marketplace - Azure Container Service with Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/) (Azure Container Service com Docker Swarm)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Inicialização da cloud 

Para obter uma cultura de Dev/Ops adequada, toda a infraestrutura deve ser código.  Quando toda a infraestrutura vive em código, pode facilmente ser recriada.  O Azure funciona com as principais ferramentas de automatização, como Ansible, Chef, SaltStack e Puppet.  O Azure também tem as suas próprias ferramentas de automatização:

* [Modelos azure](create-ssh-secured-vm-from-template.md)
* [VMAccess do Azure](using-vmaccess-extension.md)

Azure suporta para [cloud-init](https://cloud-init.io/) em toda a maioria DeStros Linux que o suportam.  Estamos a trabalhar ativamente com os nossos parceiros de distro linux endossados para ter imagens ativadas em nuvem disponíveis no mercado Azure. Estas imagens farão com que as suas implementações e configurações de cloud-init funcionem perfeitamente com VMs e conjuntos de escala de máquinas virtuais.

* [Utilizar o cloud-init em VMs do Linux no Azure](using-cloud-init.md)

## <a name="storage"></a>Storage
* [Introdução ao Storage do Microsoft Azure](../../storage/common/storage-introduction.md)
* [Add a disk to a Linux VM using the azure-cli](add-disk.md) (Adicionar um disco a uma VM do Linux com a CLI do Azure)
* [How to attach a data disk to a Linux VM in the Azure portal](attach-disk-portal.md) (Como anexar um disco de dados a uma VM do Linux no portal do Azure)

## <a name="networking"></a>Redes
* [Descrição Geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Opening ports to a Linux VM in Azure](nsg-quickstart.md) (Abrir portas para uma VM do Linux no Azure)
* [Create a Fully Qualified Domain Name in the Azure portal](portal-create-fqdn.md) (Criar um Nome de Domínio Completamente Qualificado no portal do Azure)


## <a name="next-steps"></a>Passos seguintes

Crie o seu primeiro VM!

- [Portal](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)

