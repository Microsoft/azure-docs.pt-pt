---
title: Grupos de disponibilidade do SQL Server - máquinas virtuais do Azure - recuperação de desastres | Documentos da Microsoft
description: Este artigo explica como configurar um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure com uma réplica numa região diferente.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 5e4b63d4fc8b437bd4d476c72d23f9dbf5242d5b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780563"
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Configurar um grupo de disponibilidade Always On em máquinas virtuais do Azure em regiões diferentes

Este artigo explica como configurar uma réplica de grupo SQL Server Always On disponibilidade em máquinas virtuais do Azure numa localização remota do Azure. Utilize esta configuração para suportar a recuperação após desastre.

Este artigo aplica-se às máquinas de virtuais do Azure no modo Resource Manager.

A imagem seguinte mostra uma implementação comum de um grupo de disponibilidade em máquinas virtuais do Azure:

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

Nesta implementação, todas as máquinas virtuais estão numa região do Azure. As réplicas do grupo de disponibilidade podem ter consolidação síncrona com ativação pós-falha automática em SQL-1 e 2 do SQL. Para criar esta arquitetura, veja [modelo de grupo de disponibilidade ou tutorial](virtual-machines-windows-portal-sql-availability-group-overview.md).

Esta arquitetura é vulnerável a período de indisponibilidade se a região do Azure ficar inacessível. Para superar essa vulnerabilidade, adicione uma réplica na região do Azure diferente. O diagrama seguinte mostra como seria a nova arquitetura:

   ![DR de grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

O diagrama anterior mostra uma nova máquina virtual chamada SQL-3. SQL-3 é a região do Azure diferente. SQL-3 é adicionado ao Cluster de ativação pós-falha do Windows Server. SQL-3 pode alojar uma réplica de grupo de disponibilidade. Por fim, tenha em atenção que a região do Azure para SQL-3 tem um novo Balanceador de carga do Azure.

>[!NOTE]
> Um conjunto de disponibilidade do Azure é necessário quando mais de uma máquina virtual está na mesma região. Se apenas uma máquina virtual está na região, o conjunto de disponibilidade não é necessário. Só é possível colocar uma máquina virtual num conjunto no momento da criação de disponibilidade. Se a máquina virtual já está a ser um conjunto de disponibilidade, pode adicionar uma máquina virtual para uma réplica adicional mais tarde.

Nesta arquitetura, a réplica na região remota normalmente está configurada com o modo de disponibilidade de consolidação assíncrona e o modo de ativação pós-falha manual.

Quando as réplicas do grupo de disponibilidade estão em máquinas virtuais do Azure em diferentes regiões do Azure, requer a cada região:

* Um gateway de rede virtual
* Uma ligação de gateway de rede virtual

O diagrama seguinte mostra como as redes comunicam entre centros de dados.

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Esta arquitetura incorre em custos de dados de saída para os dados replicados entre regiões do Azure. Ver [preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Criar a réplica remota

Para criar uma réplica num Datacenter remoto, siga os passos abaixo:

1. [Criar uma rede virtual na nova região](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Configurar uma ligação de VNet a VNet com o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Em alguns casos, poderá ter de utilizar o PowerShell para criar a ligação de VNet a VNet. Por exemplo, se utilizar contas do Azure diferentes não é possível configurar a ligação no portal. Neste caso, ver [configurar uma ligação de VNet a VNet com o portal do Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Criar um controlador de domínio na nova região](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Este controlador de domínio fornece autenticação, se o controlador de domínio no site primário não estiver disponível.

1. [Criar uma máquina virtual do SQL Server na nova região](virtual-machines-windows-portal-sql-server-provision.md).

1. [Criar um balanceador de carga na rede na nova região](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Este Balanceador de carga tem de:

   - Estar na mesma rede e sub-rede como a nova máquina virtual.
   - Ter um endereço IP estático para o serviço de escuta do grupo de disponibilidade.
   - Inclua um conjunto de back-end consiste apenas as máquinas virtuais na mesma região que o Balanceador de carga.
   - Utilize uma sonda de porta TCP específica para o endereço IP.
   - Ter uma regra específica para o SQL Server na mesma região de balanceamento de carga.  
   - Se as máquinas virtuais no conjunto de back-end não fazem parte de um único conjunto de disponibilidade ou o conjunto de dimensionamento de máquina virtual de ser um balanceador de carga Standard. Para informações adicionais, veja [descrição geral do Azure Balanceador de carga Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Adicionar a funcionalidade de Clustering de ativação pós-falha para o novo servidor SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Associar o novo SQL Server ao domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Definir a nova conta de serviço do SQL Server para utilizar uma conta de domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Adicionar o novo SQL Server para o Cluster de ativação pós-falha do Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Crie um recurso de endereço IP no cluster.

   É possível criar o recurso de endereço IP no Gestor de clusters de ativação pós-falha. A função de grupo de disponibilidade com o botão direito, clique em **adicionar recursos**, **mais recursos**e clique em **endereço IP**.

   ![Criar endereço IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Configure este endereço IP da seguinte forma:

   - Utilize a rede a partir do Centro de dados remota.
   - Atribua o endereço IP do Balanceador de carga do Azure novo. 

1. No novo servidor SQL no SQL Server Configuration Manager, [ativar grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/ff878259.aspx).

1. [Abrir portas da firewall no novo servidor SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Os números de porta que tem de abrir dependem do seu ambiente. Sonda de estado de funcionamento do Balanceador de carga de abrir portas para o ponto final de espelhamento e o Azure.

1. [Adicionar uma réplica para o grupo de disponibilidade no novo servidor SQL](https://msdn.microsoft.com/library/hh213239.aspx).

   Para uma réplica remota região do Azure, tem de defini-la para a replicação assíncrona com ativação pós-falha manual.  

1. Adicione o recurso de endereço IP como uma dependência para o cluster de ponto (nome de rede) de acesso de cliente do serviço de escuta.

   Captura de ecrã seguinte mostra um recurso de cluster de endereço IP configurado corretamente:

   ![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >O grupo de recursos de cluster inclui os dois endereços IP. Os dois endereços IP são dependências para o ponto de acesso de cliente do serviço de escuta. Utilize o **ou** operador na configuração de dependência do cluster.

1. [Definir os parâmetros de cluster no PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Execute o script do PowerShell com o nome de rede do cluster, o endereço IP e a porta de sonda que configurou no balanceador de carga na nova região.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Conjunto de ligação para várias sub-redes

A réplica no Centro de dados remoto faz parte do grupo de disponibilidade, mas é numa sub-rede diferente. Se esta réplica torna-se a réplica primária, pode ocorrer tempos limite de ligação do aplicativo. Este comportamento é o mesmo que um grupo de disponibilidade no local numa implementação com várias sub-redes. Para permitir ligações de cliente aplicativos, atualize a ligação de cliente ou configurar a resolução de nomes de colocação em cache no recurso de nome de rede do cluster.

De preferência, Atualize as cadeias de ligação de cliente para definir `MultiSubnetFailover=Yes`. Ver [ligar com MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Se não é possível modificar as cadeias de ligação, pode configurar a colocação em cache de resolução de nome. Ver [tempos limite de ligação no grupo de disponibilidade com várias sub-redes](https://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Ativação pós-falha para a região remota

Para testar a conectividade de serviço de escuta para a região remota, pode efetuar a ativação pós-falha da réplica para a região remota. Enquanto a réplica é assíncrona, a ativação pós-falha é vulnerável a potencial perda de dados. Para efetuar a ativação pós-falha sem perda de dados, altere o modo de disponibilidade para síncrono e definir o modo de ativação pós-falha para automático. Utilize os passos seguintes:

1. Na **Object Explorer**, ligue à instância do SQL Server que aloja a réplica primária.
1. Sob **grupos de Disponibilidade AlwaysOn**, **grupos de disponibilidade**, o seu grupo de disponibilidade com o botão direito e clique em **propriedades**.
1. Sobre o **gerais** página, em **réplicas de disponibilidade**, definir a réplica secundária no site de DR para utilizar **consolidação síncrona** modo de disponibilidade e  **Automática** modo de ativação pós-falha.
1. Se tiver uma réplica secundária no mesmo site que a réplica primária para elevada disponibilidade, definido desta réplica como **consolidação assíncrona** e **Manual**.
1. Clique em OK.
1. Na **Object Explorer**, o grupo de disponibilidade com o botão direito e clique em **Mostrar Dashboard**.
1. No dashboard, certifique-se de que a réplica no site de DR é sincronizada.
1. Na **Object Explorer**, o grupo de disponibilidade com o botão direito e clique em **ativação pós-falha...** . SQL Server Management Studio abre um Assistente para efetuar a ativação pós-falha do SQL Server.  
1. Clique em **seguinte**e selecione a instância do SQL Server no site de DR. Clique em **seguinte** novamente.
1. Ligar à instância do SQL Server no site de DR e clique em **seguinte**.
1. Sobre o **resumo** página, verifique as definições e clique em **concluir**.

Depois de testar a conectividade, regresse a réplica primária para o seu Datacenter primário e definir o modo de disponibilidade para as definições de funcionamento normais. A tabela seguinte mostra as definições operacionais normais para a arquitetura descrita neste documento:

| Localização | Instância de servidor | Função | Modo de disponibilidade | Modo de ativação pós-falha
| ----- | ----- | ----- | ----- | -----
| Centro de dados primária | SQL-1 | Primário | Síncrona | Automático
| Centro de dados primária | SQL-2 | Secundário | Síncrona | Automático
| Centro de dados secundário ou remoto | SQL-3 | Secundário | Assíncrona | Manual


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Obter mais informações sobre a ativação pós-falha manual de forçado e não planeado

Para obter mais informações, consulte os seguintes tópicos:

- [Execute uma ativação pós-falha Manual planeada de um grupo de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Executar uma ativação pós-falha Manual forçada de um grupo de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Ligações Adicionais

* [Grupos de disponibilidade Always On](https://msdn.microsoft.com/library/hh510230.aspx)
* [Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Balanceadores de carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Conjuntos de disponibilidade do Azure](../manage-availability.md)
