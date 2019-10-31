---
title: Grupos de disponibilidade SQL Server-máquinas virtuais do Azure-recuperação de desastre | Microsoft Docs
description: Este artigo explica como configurar um grupo de disponibilidade SQL Server em máquinas virtuais do Azure com uma réplica em uma região diferente.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 9949c389ad0511c3ed5923e0451bc96e7063621f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159739"
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Configurar um grupo de disponibilidade Always On em máquinas virtuais do Azure em regiões diferentes

Este artigo explica como configurar um SQL Server Always On réplica de grupo de disponibilidade em máquinas virtuais do Azure em um local remoto do Azure. Use essa configuração para dar suporte à recuperação de desastre.

Este artigo se aplica a máquinas virtuais do Azure no modo do Gerenciador de recursos.

A imagem a seguir mostra uma implantação comum de um grupo de disponibilidade em máquinas virtuais do Azure:

   ![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

Nessa implantação, todas as máquinas virtuais estão em uma região do Azure. As réplicas do grupo de disponibilidade podem ter confirmação síncrona com failover automático em SQL-1 e SQL-2. Para criar essa arquitetura, consulte [modelo de grupo de disponibilidade ou tutorial](virtual-machines-windows-portal-sql-availability-group-overview.md).

Essa arquitetura estará vulnerável ao tempo de inatividade se a região do Azure se tornar inacessível. Para superar essa vulnerabilidade, adicione uma réplica em uma região diferente do Azure. O diagrama a seguir mostra como a nova arquitetura ficaria:

   ![DR do grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

O diagrama anterior mostra uma nova máquina virtual chamada SQL-3. O SQL-3 está em uma região do Azure diferente. O SQL-3 é adicionado ao cluster de failover do Windows Server. O SQL-3 pode hospedar uma réplica de grupo de disponibilidade. Por fim, observe que a região do Azure para SQL-3 tem um novo balanceador de carga do Azure.

>[!NOTE]
> Um conjunto de disponibilidade do Azure é necessário quando mais de uma máquina virtual está na mesma região. Se apenas uma máquina virtual estiver na região, o conjunto de disponibilidade não será necessário. Você só pode posicionar uma máquina virtual em um conjunto de disponibilidade no momento da criação. Se a máquina virtual já estiver em um conjunto de disponibilidade, você poderá adicionar uma máquina virtual para uma réplica adicional mais tarde.

Nessa arquitetura, a réplica na região remota normalmente é configurada com o modo de disponibilidade de confirmação assíncrona e o modo de failover manual.

Quando as réplicas do grupo de disponibilidade estão em máquinas virtuais do Azure em diferentes regiões do Azure, cada região requer:

* Um gateway de rede virtual
* Uma conexão de gateway de rede virtual

O diagrama a seguir mostra como as redes se comunicam entre os data centers.

   ![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Essa arquitetura incorre em encargos de dados de saída para dados replicados entre regiões do Azure. Consulte [preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Criar réplica remota

Para criar uma réplica em um data center remoto, execute as seguintes etapas:

1. [Crie uma rede virtual na nova região](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Configure uma conexão de vnet para vnet usando o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Em alguns casos, talvez seja necessário usar o PowerShell para criar a conexão de VNet para VNet. Por exemplo, se você usar diferentes contas do Azure, não poderá configurar a conexão no Portal. Nesse caso, consulte [Configurar uma conexão de vnet para vnet usando o portal do Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Crie um controlador de domínio na nova região](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Esse controlador de domínio fornecerá autenticação se o controlador de domínio no site primário não estiver disponível.

1. [Crie uma máquina virtual SQL Server na nova região](virtual-machines-windows-portal-sql-server-provision.md).

1. [Crie um balanceador de carga do Azure na rede na nova região](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Este balanceador de carga deve:

   - Estar na mesma rede e sub-rede que a nova máquina virtual.
   - Ter um endereço IP estático para o ouvinte do grupo de disponibilidade.
   - Inclua um pool de back-end que consiste apenas em máquinas virtuais na mesma região que o balanceador de carga.
   - Use uma investigação de porta TCP específica para o endereço IP.
   - Ter uma regra de balanceamento de carga específica para o SQL Server na mesma região.  
   - Seja um Standard Load Balancer se as máquinas virtuais no pool de back-end não fizerem parte de um único conjunto de disponibilidade ou conjunto de dimensionamento de máquinas virtuais. Para examinar informações adicionais [Azure Load Balancer visão geral padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Adicione o recurso de clustering de failover ao novo SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Ingresse o novo SQL Server no domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Defina a nova conta de serviço SQL Server para usar uma conta de domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Adicione o novo SQL Server ao cluster de failover do Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Crie um recurso de endereço IP no cluster.

   Você pode criar o recurso de endereço IP no Gerenciador de Cluster de Failover. Clique com o botão direito do mouse na função de grupo de disponibilidade, clique em **Adicionar recurso**, **mais recursos**e clique em **endereço IP**.

   ![Criar endereço IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Configure esse endereço IP da seguinte maneira:

   - Use a rede do data center remoto.
   - Atribua o endereço IP do novo balanceador de carga do Azure. 

1. No SQL Server novo no SQL Server Configuration Manager, [habilite Always on grupos de disponibilidade](https://msdn.microsoft.com/library/ff878259.aspx).

1. [Abra portas de firewall no novo SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Os números de porta que você precisa abrir dependem do seu ambiente. Abra as portas para o ponto de extremidade de espelhamento e a investigação de integridade do Azure Load Balancer.

1. [Adicione uma réplica ao grupo de disponibilidade no novo SQL Server](https://msdn.microsoft.com/library/hh213239.aspx).

   Para uma réplica em uma região remota do Azure, defina-a para replicação assíncrona com failover manual.  

1. Adicione o recurso de endereço IP como uma dependência para o cluster de ponto de acesso para cliente do ouvinte (nome da rede).

   A captura de tela a seguir mostra um recurso de cluster de endereço IP configurado corretamente:

   ![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >O grupo de recursos de cluster inclui os dois endereços IP. Ambos os endereços IP são dependências para o ponto de acesso para cliente do ouvinte. Use o operador **or** na configuração de dependência do cluster.

1. [Defina os parâmetros de cluster no PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Execute o script do PowerShell com o nome de rede do cluster, o endereço IP e a porta de investigação que você configurou no balanceador de carga na nova região.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Definir conexão para várias sub-redes

A réplica no data center remoto faz parte do grupo de disponibilidade, mas está em uma sub-rede diferente. Se essa réplica se tornar a réplica primária, poderá ocorrer o tempo limite de conexão do aplicativo. Esse comportamento é o mesmo que um grupo de disponibilidade local em uma implantação de várias sub-redes. Para permitir conexões de aplicativos cliente, atualize a conexão do cliente ou configure o cache de resolução de nomes no recurso de nome de rede do cluster.

Preferivelmente, atualize as cadeias de conexão do cliente para definir `MultiSubnetFailover=Yes`. Consulte [conectando-se com MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Se você não puder modificar as cadeias de conexão, poderá configurar o cache de resolução de nome. Consulte [erro de tempo limite e não é possível se conectar a um ouvinte de grupo de disponibilidade SQL Server 2012 AlwaysOn em um ambiente de várias sub-redes](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Failover para região remota

Para testar a conectividade do ouvinte com a região remota, você pode fazer failover da réplica para a região remota. Enquanto a réplica é assíncrona, o failover é vulnerável à possível perda de dados. Para fazer failover sem perda de dados, altere o modo de disponibilidade para síncrono e defina o modo de failover como automático. Utilize os passos seguintes:

1. No Pesquisador de **objetos**, conecte-se à instância do SQL Server que hospeda a réplica primária.
1. Em **grupos de disponibilidade AlwaysOn**, **grupos de disponibilidade**, clique com o botão direito do mouse no seu grupo de disponibilidade e clique em **Propriedades**.
1. Na página **geral** , em **réplicas de disponibilidade**, defina a réplica secundária no site de recuperação de desastre para usar o modo de disponibilidade de **confirmação síncrona** e o modo de failover **automático** .
1. Se você tiver uma réplica secundária no mesmo site que a réplica primária para alta disponibilidade, defina essa réplica como **confirmação assíncrona** e **manual**.
1. Clique em OK.
1. No Pesquisador de **objetos**, clique com o botão direito do mouse no grupo de disponibilidade e clique em **Mostrar painel**.
1. No painel, verifique se a réplica no site de recuperação de desastre está sincronizada.
1. No Pesquisador de **objetos**, clique com o botão direito do mouse no grupo de disponibilidade e clique em **failover...** . SQL Server Management estúdios abre um assistente para fazer failover de SQL Server.  
1. Clique em **Avançar**e selecione a instância de SQL Server no site de recuperação de desastre. Clique em **Avançar** novamente.
1. Conecte-se à instância de SQL Server no site de recuperação de desastre e clique em **Avançar**.
1. Na página **Resumo** , verifique as configurações e clique em **concluir**.

Após testar a conectividade, mova a réplica primária de volta para o data center primário e defina o modo de disponibilidade de volta para suas configurações operacionais normais. A tabela a seguir mostra as configurações operacionais normais para a arquitetura descrita neste documento:

| Localização | Instância do servidor | Função | Modo de disponibilidade | Modo de failover
| ----- | ----- | ----- | ----- | -----
| data center primário | SQL-1 | Primária | Replicação | Automático
| data center primário | SQL-2 | Secundária | Replicação | Automático
| data center secundários ou remotos | SQL-3 | Secundária | Manipulador | Manual


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Mais informações sobre failover manual forçado e planejado

Para obter mais informações, consulte os seguintes tópicos:

- [Executar um failover manual planejado de um grupo de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Executar um failover manual forçado de um grupo de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Links adicionais

* [Always On grupos de disponibilidade](https://msdn.microsoft.com/library/hh510230.aspx)
* [Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Balanceadores de carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Conjuntos de disponibilidade do Azure](../manage-availability.md)
