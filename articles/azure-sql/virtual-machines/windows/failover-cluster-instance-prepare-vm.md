---
title: Preparar máquinas virtuais para um FCI
description: Prepare as suas máquinas virtuais Azure para as utilizar com uma instância de cluster de failover (FCI) e SQL Server em Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 1b8d88167dac6b2d0b1ba2afc90c443fd80b9e46
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223164"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Preparar máquinas virtuais para um FCI (SQL Server em VMs Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como preparar máquinas virtuais Azure (VMs) para usá-las com uma instância de cluster de failover do SQL Server (FCI). As configurações variam consoante a solução de armazenamento FCI, por isso valide que está a escolher a configuração correta para se adequar ao seu ambiente e negócio. 

Para saber mais, consulte uma visão geral da [FCI com o SQL Server em VMs Azure](failover-cluster-instance-overview.md) e [as melhores práticas do cluster](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Pré-requisitos 

- Uma subscrição do Microsoft Azure. Começa de [graça.](https://azure.microsoft.com/free/) 
- Um domínio Windows em máquinas virtuais Azure ou um datacenter no local estendido ao Azure com emparelhamento de rede virtual.
- Uma conta que tem permissões para criar objetos em máquinas virtuais Azure e no Ative Directory.
- Uma rede virtual Azure e uma sub-rede com espaço de endereço IP suficiente para estes componentes:
   - Ambas as máquinas virtuais
   - O endereço IP do cluster failover do Windows
   - Um endereço IP para cada FCI
- DNS configurado na rede Azure, apontando para os controladores de domínio.

## <a name="choose-an-fci-storage-option"></a>Escolha uma opção de armazenamento FCI

As definições de configuração da sua máquina virtual variam consoante a opção de armazenamento que planeia utilizar para a sua instância de cluster de falha do SQL Server. Antes de preparar a máquina virtual, reveja as [opções de armazenamento FCI disponíveis](failover-cluster-instance-overview.md#storage) e escolha a opção que melhor se adequa ao seu ambiente e às suas necessidades empresariais. Em seguida, selecione cuidadosamente as opções de configuração VM apropriadas ao longo deste artigo com base na sua seleção de armazenamento. 

## <a name="configure-vm-availability"></a>Configure disponibilidade VM 

A funcionalidade de cluster failover requer que as máquinas virtuais sejam colocadas num [conjunto de disponibilidade](../../../virtual-machines/linux/tutorial-availability-sets.md) ou numa zona de [disponibilidade.](../../../availability-zones/az-overview.md#availability-zones) Se escolher conjuntos de disponibilidade, pode utilizar [grupos de colocação de proximidade](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) para localizar os VMs mais perto. Na verdade, os grupos de colocação de proximidade são um pré-requisito para a utilização de discos partilhados Azure. 

Selecione cuidadosamente a opção de disponibilidade de VM que corresponda à configuração do cluster pretendido: 

- **Discos partilhados Azure**: a opção de disponibilidade varia se estiver a utilizar SSDs Premium ou UltraDisk:
   - Premium SSD: [Disponibilidade definida](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) em diferentes domínios de falha/atualização para SSDs Premium colocados dentro de um [grupo de colocação de proximidade](../../../virtual-machines/windows/proximity-placement-groups-portal.md).
   - Ultra Disco: [Zona de disponibilidade](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) mas os VMs devem ser colocados na mesma zona de disponibilidade que reduz a disponibilidade do cluster para 99,9%. 
- **Ações de ficheiro premium**: [Conjunto de disponibilidade](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) ou zona [de disponibilidade](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Espaços de Armazenamento Direto**: [Conjunto de disponibilidade](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> Não é possível definir ou alterar o conjunto de disponibilidade depois de ter criado uma máquina virtual.

## <a name="create-the-virtual-machines"></a>Criar as máquinas virtuais

Depois de configurar a sua disponibilidade em VM, está pronto para criar as suas máquinas virtuais. Pode optar por utilizar uma imagem do Azure Marketplace que tem ou não o SQL Server já instalado na mesmo. No entanto, se escolher uma imagem para SQL Server em VMs Azure, terá de desinstalar o SQL Server a partir da máquina virtual antes de configurar a instância do cluster de failover. 

### <a name="considerations"></a>Considerações

Num cluster de failover de hóspedes Azure VM, recomendamos um único NIC por servidor (nó de cluster) e uma única sub-rede. O azure networking tem redundância física, o que torna NICs adicionais e sub-redes desnecessários em um cluster de hóspedes Azure IaaS VM. Embora o relatório de validação do cluster emita um aviso de que os nós apenas são acessíveis numa única rede, este aviso pode ser ignorado com segurança nos clusters de ativação pós-falha convidados da VM IaaS do Azure.

Coloque ambas as máquinas virtuais:

- No mesmo grupo de recursos Azure que o seu conjunto de disponibilidade, se estiver a utilizar conjuntos de disponibilidade.
- Na mesma rede virtual que o seu controlador de domínio ou numa rede virtual que tenha uma conectividade adequada ao seu controlador de domínio.
- Numa sub-rede que tem espaço suficiente para endereços IP tanto para máquinas virtuais como para todas as FCIs que poderá eventualmente utilizar no cluster.
- Na zona de disponibilidade ou disponibilidade do Azure.

Pode criar uma máquina virtual Azure utilizando uma imagem [com](sql-vm-create-portal-quickstart.md) ou [sem](../../../virtual-machines/windows/quick-create-portal.md) SQL Server pré-instalada. Se escolher a imagem do SQL Server, terá de desinstalar manualmente a instância do SQL Server antes de instalar a instância de cluster de falha. 


## <a name="uninstall-sql-server"></a>Desinstalar o servidor SQL

Como parte do processo de criação da FCI, instalará o SQL Server como uma instância agrupada para o cluster failover. *Se implementou uma máquina virtual com uma imagem do Azure Marketplace sem o SQL Server, pode saltar este passo.* Se implementou uma imagem com o SQL Server pré-instalado, terá de desinstalar o SQL Server VM a partir da extensão sql IaaS Agent e, em seguida, desinstalar o SqL Server. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Não registro da extensão do Agente IAAS SQL

As imagens SQL Server VM do Azure Marketplace são automaticamente registadas com a extensão do Agente IAAS SQL. Antes de desinstalar a instância do SqL Server pré-instalada, deve primeiro [não registar cada SQL Server VM a partir da extensão SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>Desinstalar o servidor SQL

Depois de não ter sido registado a partir da extensão, pode desinstalar o SQL Server. Siga estes passos em cada máquina virtual: 

1. Ligue-se à máquina virtual utilizando RDP.

   Quando se liga a uma máquina virtual utilizando RDP, um pedido pergunta-lhe se pretende permitir que o PC seja detetável na rede. Selecione **Yes** (Sim).

1. Se estiver a utilizar uma das imagens de máquinas virtuais baseadas no SQL Server, remova a instância do SQL Server:

   1. Em **Programas e Funcionalidades,** clique com o botão direito **Microsoft SQL Server 201_ (64-bit)** e selecione **Desinstalar/Alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância padrão.
   1. Remova todas as funcionalidades nos **serviços de motores de base de dados.** Não remova nada em **Funcionalidades Partilhadas.** Verá algo como a seguinte imagem:

      ![Selecionar funcionalidades](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. Selecione **Seguinte** e, em seguida, selecione **Remover**.
   1. Depois de a instância ser removida com sucesso, reinicie a máquina virtual. 

## <a name="open-the-firewall"></a>Abra a firewall 

Em cada máquina virtual, abra a porta TCP do Windows Firewall que o SQL Server utiliza. Por predefinição, esta é a porta 1433. Mas pode alterar a porta do Servidor SQL numa implementação Azure VM, de modo a abrir a porta que o SQL Server utiliza no seu ambiente. Esta porta é automaticamente aberta em imagens sql Server implementadas a partir do Azure Marketplace. 

Se utilizar um [equilibrador de carga,](failover-cluster-instance-vnn-azure-load-balancer-configure.md)também terá de abrir a porta que a sonda de saúde utiliza. Por predefinição, esta é a porta 59999. Mas pode ser qualquer porta TCP que especifique quando cria o equilibrador de carga. 

Esta tabela detalha as portas que poderá ter de abrir, dependendo da configuração do FCI: 

   | Objetivo | Porta | Notas
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Porta normal para instâncias padrão do SQL Server. Se utilizar uma imagem da galeria, esta porta é aberta automaticamente. </br> </br> **Utilizado por:** Todas as configurações do FCI. |
   | Sonda de estado de funcionamento | TCP 59999 | Qualquer porta TCP aberta. Configure a [sonda de saúde](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) do balanceador de carga e o cluster para utilizar esta porta. </br> </br> **Utilizado por**: FCI com equilibrador de carga. |
   | Partilha de ficheiros | UDP 445 | Port que o serviço de partilha de ficheiros usa. </br> </br> **Utilizado por:** FCI com partilha de ficheiros Premium. |

## <a name="join-the-domain"></a>Associar o domínio

Também precisa de juntar as suas máquinas virtuais ao domínio. Pode fazê-lo utilizando um [modelo de arranque rápido.](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain) 

## <a name="review-storage-configuration"></a>Rever configuração de armazenamento

As máquinas virtuais criadas a partir do Azure Marketplace vêm com armazenamento anexo. Se planeia configurar o seu armazenamento FCI utilizando ações de ficheiros Premium ou discos partilhados Azure, pode remover o armazenamento anexado para economizar custos, uma vez que o armazenamento local não é utilizado para a instância de cluster failover. No entanto, é possível utilizar o armazenamento anexado para os espaços de armazenamento soluções FCI diretas, por isso removê-las neste caso pode não ser útil. Reveja a sua solução de armazenamento FCI para determinar se a remoção do armazenamento anexado é ótima para poupar custos. 


## <a name="next-steps"></a>Passos seguintes

Agora que preparou o seu ambiente de máquina virtual, está pronto para configurar o seu caso de aglomerado de falhas. 

Escolha um dos seguintes guias para configurar o ambiente FCI que é apropriado para o seu negócio: 
- [Configure FCI com discos partilhados Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Configure a FCI com uma partilha de ficheiros Premium](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Configure FCI com espaços de armazenamento direto](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Para saber mais, consulte uma visão geral da [FCI com o SQL Server em VMs Azure](failover-cluster-instance-overview.md) e [configurações HADR suportadas](hadr-cluster-best-practices.md). 

Para obter informações adicionais, consulte: 
- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server falha casos de cluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
