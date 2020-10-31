---
title: Configure um servidor SQL sempre em grupo de disponibilidade em diferentes regiões
description: Este artigo explica como configurar um sql servidor sempre em grupo de disponibilidade em máquinas virtuais Azure com uma réplica em uma região diferente.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 335cc707cb1192d3dbf08f51e78d4e82441dd05a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094460"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>Configure um SQL Server Always On availability group em diferentes regiões do Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como configurar um SQL Server Always On availability group replica em máquinas virtuais Azure numa localização remota do Azure. Utilize esta configuração para apoiar a recuperação de desastres.

Este artigo aplica-se às Máquinas Virtuais Azure no modo Gestor de Recursos.

A imagem a seguir mostra uma implantação comum de um grupo de disponibilidade em máquinas virtuais Azure:

   ![Diagrama que mostra o equilibrador de carga Azure e o conjunto de Disponibilidade com um "Windows Server Failover Cluster" e "Always On Availability Group".](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

Nesta implantação, todas as máquinas virtuais estão numa região de Azure. As réplicas do grupo de disponibilidade podem ter compromisso sincronizado com falha automática em SQL-1 e SQL-2. Para construir esta arquitetura, consulte [o modelo do Grupo Availability ou tutorial.](availability-group-overview.md)

Esta arquitetura é vulnerável ao tempo de inatividade se a região de Azure se tornar inacessível. Para ultrapassar esta vulnerabilidade, adicione uma réplica numa região de Azure diferente. O seguinte diagrama mostra como a nova arquitetura ficaria:

   ![Grupo de Disponibilidade DR](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

O diagrama anterior mostra uma nova máquina virtual chamada SQL-3. SQL-3 está numa região de Azure diferente. SQL-3 é adicionado ao Cluster de Falha do Servidor do Windows. O SQL-3 pode acolher uma réplica do grupo de disponibilidade. Por último, note que a região de Azure para o SQL-3 tem um novo equilibrador de carga Azure.

>[!NOTE]
> É necessário um conjunto de disponibilidade azure quando mais de uma máquina virtual está na mesma região. Se apenas uma máquina virtual estiver na região, então o conjunto de disponibilidade não é necessário. Só é possível colocar uma máquina virtual num conjunto de disponibilidade na hora da criação. Se a máquina virtual já estiver num conjunto de disponibilidade, pode adicionar uma máquina virtual para uma réplica adicional mais tarde.

Nesta arquitetura, a réplica na região remota é normalmente configurada com modo de disponibilidade assíncrona e modo de failover manual.

Quando as réplicas do grupo de disponibilidade estão em máquinas virtuais Azure em diferentes regiões do Azure, cada região requer:

* Um portal de rede virtual
* Uma ligação virtual de gateway de rede

O diagrama seguinte mostra como as redes comunicam entre centros de dados.

   ![Diagrama que mostra as duas Redes Virtuais em diferentes Regiões de Azure comunicando usando V P N Gateways.](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>Esta arquitetura incorre em taxas de dados de saída para dados replicados entre regiões de Azure. Consulte [o preço da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Criar réplica remota

Para criar uma réplica num centro de dados remoto, faça os seguintes passos:

1. [Criar uma rede virtual na nova região.](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)

1. [Configure uma ligação VNet-vNet utilizando o portal Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Em alguns casos, poderá ter de utilizar o PowerShell para criar a ligação VNet-to-VNet. Por exemplo, se utilizar diferentes contas Azure não poderá configurar a ligação no portal. Neste caso, [consulte, Configure uma ligação VNet-vNet utilizando o portal Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Criar um controlador de domínio na nova região.](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)

   Este controlador de domínio fornece autenticação se o controlador de domínio no local principal não estiver disponível.

1. [Crie uma máquina virtual SQL Server na nova região.](create-sql-vm-portal.md)

1. [Criar um equilibrador de carga Azure na rede da nova região.](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)

   Este equilibrador de carga deve:

   - Esteja na mesma rede e sub-rede que a nova máquina virtual.
   - Tenha um endereço IP estático para o ouvinte do grupo de disponibilidade.
   - Inclua uma piscina de backend composta apenas pelas máquinas virtuais na mesma região que o equilibrador de carga.
   - Utilize uma sonda de porta TCP específica para o endereço IP.
   - Tenha uma regra de equilíbrio de carga específica para o SQL Server na mesma região.  
   - Seja um Balanceador de Carga Padrão se as máquinas virtuais no pool de backend não fizerem parte de um conjunto de disponibilidade único ou de um conjunto de balança de máquina virtual. Para obter informações adicionais, revê [o padrão do balanço de carga Azure](../../../load-balancer/load-balancer-overview.md).

1. [Adicione a função de Clustering Failover ao novo SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Junte o novo SQL Server ao domínio](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

1. [Desa estale a nova conta de serviço DO SQL Server para utilizar uma conta de domínio](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount).

1. [Adicione o novo Servidor SQL ao Cluster de Falha do Servidor do Windows](availability-group-manually-configure-tutorial.md#addNode).

1. Adicione um recurso de endereço IP ao cluster.

   Pode criar o recurso de endereço IP no Failover Cluster Manager. Selecione o nome do cluster e, em seguida, clique com o nome do cluster em **Recursos Core cluster** e selecione **Propriedades** : 

   ![Screenshot que mostra o "Failover Cluster Manager" com um nome de cluster, "Nome do servidor" e "Propriedades" selecionados.](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   Na caixa de diálogo **Propriedades,** **selecione Adicionar** em **Endereço IP** e, em seguida, adicionar o endereço IP do nome do cluster da região da rede remota. Selecione **OK** na caixa de diálogo **do endereço IP** e, em seguida, selecione **OK** novamente na caixa de diálogo **Cluster Properties** para guardar o novo endereço IP. 

   ![Adicionar IP de cluster](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. Adicione o endereço IP como uma dependência para o nome do cluster principal.

   Abra mais uma vez as propriedades do cluster e selecione o **separador Dependências.** Configure uma dependência de OR para os dois endereços IP: 

   ![Propriedades de cluster](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. Adicione um recurso de endereço IP à função de grupo de disponibilidade no cluster. 

   Clique com o botão direito no papel de grupo de disponibilidade no Failover Cluster Manager, escolha **Adicionar Recurso** , **Mais Recursos** e selecione Endereço **IP** .

   ![Criar endereço IP](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   Configure este endereço IP da seguinte forma:

   - Utilize a rede a partir do centro de dados remoto.
   - Atribua o endereço IP do novo equilibrador de carga Azure. 

1. Adicione o recurso de endereço IP como uma dependência para o cluster do ponto de acesso do cliente ouvinte (nome de rede).

   A imagem que se segue mostra um recurso de cluster de endereço IP devidamente configurado:

   ![Grupo de Disponibilidade](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >O grupo de recursos de cluster inclui ambos os endereços IP. Ambos os endereços IP são dependências para o ponto de acesso do cliente ouvinte. Utilize o operador **OR** na configuração de dependência do cluster.

1. [Desa estale os parâmetros do cluster no PowerShell](availability-group-manually-configure-tutorial.md#setparam).

   Execute o script PowerShell com o nome da rede de cluster, endereço IP e porta de sonda que configuraste no equilibrador de carga da nova região.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. No novo Servidor SQL no Gestor de Configuração do Servidor SQL, [ative sempre os grupos de disponibilidade](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. [Abra as portas de firewall no novo SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

   Os números de porta que precisa de abrir dependem do seu ambiente. Abra as portas para o ponto final espelhante e sonda de saúde do balanceador de carga Azure.


1. [Adicione uma réplica ao grupo de disponibilidade no novo SQL Server](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Para uma réplica numa região remota de Azure, desaperte-a para a replicação assíncrona com falha manual.  

## <a name="set-connection-for-multiple-subnets"></a>Definir ligação para várias sub-redes

A réplica no centro de dados remoto faz parte do grupo de disponibilidade, mas está numa sub-rede diferente. Se esta réplica se tornar a réplica primária, podem ocorrer intervalos de ligação da aplicação. Este comportamento é o mesmo que um grupo de disponibilidade no local numa implementação multi-sub-rede. Para permitir ligações a partir de aplicações do cliente, atualizar a ligação do cliente ou configurar a resolução de nomes no recurso de nome de rede de cluster.

De preferência, atualize as cordas de ligação do cliente para definir `MultiSubnetFailover=Yes` . Ver [Ligação com MultiSubnetFailover](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#Anchor_0).

Se não conseguir modificar as cordas de ligação, pode configurar o caching de resolução de nome. Consulte [o erro de tempo e não pode ligar-se a um ouvinte do grupo de disponibilidade SQL Server 2012 AlwaysOn num ambiente multi-sub-rede](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Falha na região remota

Para testar a conectividade do ouvinte com a região remota, pode falhar sobre a réplica da região remota. Embora a réplica seja assíncronea, o failover é vulnerável à perda de dados potenciais. Para falhar sem perda de dados, altere o modo de disponibilidade para sincronizado e desloque o modo de failover para automático. Utilize os passos seguintes:

1. No **Object Explorer,** ligue-se à instância do SQL Server que acolhe a réplica primária.
1. Em **Grupos de Disponibilidade AlwaysOn** , **Grupos de Disponibilidade,** clique à direita no seu grupo de disponibilidade e selecione **Propriedades.**
1. Na página **Geral,** em **Replicas Disponibilidade,** desloque a réplica secundária no site DR para utilizar o modo de disponibilidade **Synchronous Commit** e o modo de failover **automático.**
1. Se tiver uma réplica secundária no mesmo local que a sua réplica primária para alta disponibilidade, desista desta réplica para **Assíncronose e** **Manual** .
1. Selecione OK.
1. No **Object Explorer,** clique com o botão direito do grupo de disponibilidade e selecione **Mostrar Painel** .
1. No painel de instrumentos, verifique se a réplica no site DR está sincronizada.
1. No **Object Explorer,** clique com o botão direito no grupo de disponibilidade e selecione **Failover...** . SQL Server Management Studios abre um assistente para falhar sobre o SQL Server.  
1. Selecione **Seguinte** , e selecione a instância sql Server no site DR. Selecione **Next** novamente.
1. Ligue-se à instância do SQL Server no site DR e selecione **Seguinte** .
1. Na página **Resumo,** verifique as definições e **selecione Terminar** .

Depois de testar a conectividade, mova a réplica primária de volta para o seu centro de dados primário e volte a definir o modo de disponibilidade para as suas definições normais de funcionamento. O quadro a seguir mostra as configurações operacionais normais para a arquitetura descritas neste documento:

| Localização | Instância do servidor | Função | Modo disponibilidade | Modo de failover
| ----- | ----- | ----- | ----- | -----
| Centro de dados primários | SQL-1 | Primário | Synchronous (Síncrono) | Automático
| Centro de dados primários | SQL-2 | Secundária | Synchronous (Síncrono) | Automático
| Centro de dados secundário ou remoto | SQL-3 | Secundária | Assíncrono | Manual


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Mais informações sobre o failover manual planeado e forçado

Para obter mais informações, consulte os seguintes tópicos:

- [Executar uma falha manual planeada de um Grupo de Disponibilidade (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server)
- [Executar uma falha manual forçada de um Grupo de Disponibilidade (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server)

## <a name="next-steps"></a>Passos seguintes

* [Grupos de Disponibilidade Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Máquinas Virtuais do Azure](../../../virtual-machines/windows/index.yml)
* [Balançadores de carga Azure](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)
* [Conjuntos de disponibilidade de Azure](../../../virtual-machines/manage-availability.md)