---
title: Visão geral dos VMs Linux em Azure
description: Visão geral das máquinas virtuais Linux em Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4067fc4e47dce83852af971daae6cda3933d0e39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667171"
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
Existem sempre inúmeras [considerações de design](/azure/architecture/reference-architectures/n-tier/linux-vm) quando está a desenvolver uma infraestrutura de aplicação no Azure. Estes aspetos de uma VM são importantes e devem ser ponderados antes de começar:

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
| Azure PowerShell |Use o comando [Get-AzLocation.](/powershell/module/az.resources/get-azlocation) |
| API REST |Utilize a operação [Listar localizações](/rest/api/resources/subscriptions). |
| CLI do Azure |Utilize a operação [az account list-locations](/cli/azure/account). |

## <a name="availability"></a>Disponibilidade
O Azure anunciou um Contrato de Nível de Serviço líder da indústria de 99,9% para máquinas virtuais de instância única, desde que implemente a VM com armazenamento premium para todos os discos.  Para a sua implementação se qualificar para o Contrato de Nível de Serviço de VM de 99,95% standard, continua a ter de implementar duas ou mais VMs que executem a sua carga de trabalho num conjunto de disponibilidade. Um conjunto de disponibilidade garante que as suas VMs são distribuídas em vários domínios de falha nos datacenters do Azure, bem como implementadas em anfitriões com diferentes janelas de manutenção. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.

## <a name="vm-size"></a>Tamanho da VM
O [tamanho](../sizes.md) da VM que utiliza é determinado pela carga de trabalho que pretende executar. O tamanho que escolher determina fatores como o poder de processamento, a memória e capacidade de armazenamento. O Azure disponibiliza uma vasta variedade de tamanhos para suportar muitos tipos de utilizações.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) com base no tamanho e sistema operativo do VM. Para horas parciais, o Azure cobra apenas os minutos utilizados. O armazenamento tem um preço à parte e é cobrado separadamente.

## <a name="vm-limits"></a>Limites da VM
A subscrição tem [limites de quota](../../azure-resource-manager/management/azure-subscription-service-limits.md) predefinidos num local que pode afetar a implementação de muitas VMs para o seu projeto. O limite atual numa base por subscrição é de 20 VMs por região. Os limites podem ser aumentados ao [preencher um pedido de suporte a pedir um aumento](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Managed Disks

O Managed Disks processa a criação e a gestão da conta de Armazenamento do Azure em segundo plano por si e assegura que não tem de se preocupar com os limites de escalabilidade da conta de armazenamento. Especifique o tamanho do disco e o escalão de desempenho (Standard ou Premium), e o Azure cria e gere o disco. Conforme adiciona discos ou aumente e reduza verticalmente a VM, não terá de se preocupar se o armazenamento está a ser utilizado. Se estiver a criar VMs novas, [utilize a CLI do Azure](quick-create-cli.md) ou o portal do Azure para criar as VMs com discos de dados e de SO geridos. Se tiver VMs com discos não geridos, pode [converter as VMs para a criação de cópias de segurança com o Managed Disk](convert-unmanaged-to-managed-disks.md).

Também pode gerir as imagens personalizadas numa conta de armazenamento por região do Azure e utilizá-las para criar centenas de VMs na mesma subscrição. Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](../managed-disks-overview.md).

## <a name="distributions"></a>Distribuições 
O Microsoft Azure suporta a execução de diversas distribuições Linux populares, que são disponibilizadas e mantidas por múltiplos parceiros.  Pode encontrar distribuições disponíveis no Azure Marketplace. A Microsoft trabalha ativamente com várias comunidades do Linux para adicionar ainda mais tipos à lista [Azure endorsed Linux Distros](endorsed-distros.md)(Distribuições Linux aprovadas pelo Azure).

Se a distribuição que preferir não estiver incluída atualmente na galeria, pode [criar e carregar um VHD do Linux para o Azure](create-upload-generic.md) para “Trazer a sua própria VM do Linux”.

A Microsoft trabalha de perto com os parceiros para garantir que as imagens disponíveis são atualizadas e otimizadas para runtimes do Azure.  Para obter mais informações sobre as ofertas do parceiro Azure, consulte os seguintes links:

* Linux no Azure - [Endorsed Distributions](endorsed-distros.md) (Distribuições Suportadas)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Chapéu Vermelho - [Mercado Azure - Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Canonical - [Azure Marketplace - Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian - [Azure Marketplace - Debian](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD - [Mercado Azure - FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Flatcar - [Azure Marketplace - Flatcar Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS - [Azure Marketplace - RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/services/kubernetes-service/mesosphere/) (Mesosphere DC/OS no Azure)
* Docker - [Azure Marketplace - Imagens docker](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Inicialização da cloud 

Para obter uma cultura de Dev/Ops adequada, toda a infraestrutura deve ser código.  Quando toda a infraestrutura vive em código, pode ser facilmente recriada.  O Azure funciona com as principais ferramentas de automatização, como Ansible, Chef, SaltStack e Puppet.  O Azure também tem as suas próprias ferramentas de automatização:

* [Modelos Azure](create-ssh-secured-vm-from-template.md)
* [Rio Azure `VMaccess`](../extensions/vmaccess.md)

O Azure suporta [a inibição de nuvens na](https://cloud-init.io/) maioria dos Distros Linux que o suportam.  Estamos a trabalhar ativamente com os nossos parceiros de distro Linux endossados, de forma a ter imagens ativadas em nuvem disponíveis no mercado Azure. Estas imagens farão com que as suas implementações e configurações de ineção em nuvem funcionem perfeitamente com VMs e conjuntos de escala de máquinas virtuais.

* [Utilizar o cloud-init em VMs do Linux no Azure](using-cloud-init.md)

## <a name="storage"></a>Armazenamento
* [Introdução ao Storage do Microsoft Azure](../../storage/common/storage-introduction.md)
* [Add a disk to a Linux VM using the azure-cli](add-disk.md) (Adicionar um disco a uma VM do Linux com a CLI do Azure)
* [How to attach a data disk to a Linux VM in the Azure portal](attach-disk-portal.md) (Como anexar um disco de dados a uma VM do Linux no portal do Azure)

## <a name="networking"></a>Rede
* [Visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../virtual-network/public-ip-addresses.md)
* [Opening ports to a Linux VM in Azure](nsg-quickstart.md) (Abrir portas para uma VM do Linux no Azure)
* [Create a Fully Qualified Domain Name in the Azure portal](../create-fqdn.md) (Criar um Nome de Domínio Completamente Qualificado no portal do Azure)


## <a name="data-residency"></a>Residência dos dados

Em Azure, a funcionalidade para permitir o armazenamento de dados de clientes numa única região está atualmente disponível apenas na Região do Sudeste Asiático (Singapura) da Região Ásia-Pacífico Geo e Brasil Sul (Estado de São Paulo) do Brasil Geo. Para todas as outras regiões, os dados dos clientes são armazenados na Geo. Para mais informações, consulte [o Trust Center.](https://azure.microsoft.com/global-infrastructure/data-residency/)


## <a name="next-steps"></a>Passos seguintes

Crie o seu primeiro VM!

- [Portal](quick-create-portal.md)
- [CLI do Azure](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
