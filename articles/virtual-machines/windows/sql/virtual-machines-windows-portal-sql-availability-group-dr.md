---
title: Configure grupo de disponibilidade em diferentes regiões
description: Este artigo explica como configurar um grupo de disponibilidade do SQL Server em máquinas virtuais Azure com uma réplica numa região diferente.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8ca871a6f525d4e68ce70060e6faddbcfc8e1f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060116"
---
# <a name="configure-an-availability-group-on-azure-sql-server-virtual-machines-in-different-regions"></a>Configure um grupo de disponibilidade em máquinas virtuais Do Servidor Azure SQL em diferentes regiões

Este artigo explica como configurar uma réplica do grupo SQL Server Always On em máquinas virtuais Azure numa localização remota do Azure. Utilize esta configuração para apoiar a recuperação de desastres.

Este artigo aplica-se às Máquinas Virtuais Azure no modo Gestor de Recursos.

A imagem seguinte mostra uma implantação comum de um grupo de disponibilidade em máquinas virtuais Azure:

   ![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

Nesta implantação, todas as máquinas virtuais estão numa região do Azure. As réplicas do grupo de disponibilidade podem ter compromisso sincronizado com falha automática em SQL-1 e SQL-2. Para construir esta arquitetura, consulte [o modelo de Grupo de Disponibilidade ou tutorial.](virtual-machines-windows-portal-sql-availability-group-overview.md)

Esta arquitetura é vulnerável ao tempo de inatividade se a região de Azure se tornar inacessível. Para ultrapassar esta vulnerabilidade, adicione uma réplica numa região azure diferente. O diagrama que se segue mostra como a nova arquitetura ficaria:

   ![Grupo de Disponibilidade DR](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

O diagrama anterior mostra uma nova máquina virtual chamada SQL-3. O SQL-3 está numa região de Azure diferente. O SQL-3 é adicionado ao Cluster de Falha do Servidor do Windows. O SQL-3 pode acolher uma réplica do grupo de disponibilidade. Por fim, note que a região de Azure para o SQL-3 tem um novo equilibrador de carga Azure.

>[!NOTE]
> É necessário um conjunto de disponibilidade azure quando mais de uma máquina virtual está na mesma região. Se apenas uma máquina virtual estiver na região, então o conjunto de disponibilidade não é necessário. Só é possível colocar uma máquina virtual num conjunto de disponibilidade no momento da criação. Se a máquina virtual já estiver num conjunto de disponibilidade, pode adicionar uma máquina virtual para uma réplica adicional mais tarde.

Nesta arquitetura, a réplica na região remota é normalmente configurada com o modo de disponibilidade de compromisso assíncrono e o modo de failover manual.

Quando as réplicas do grupo de disponibilidade estão em máquinas virtuais Azure em diferentes regiões do Azure, cada região requer:

* Um portal de rede virtual
* Uma ligação de gateway de rede virtual

O diagrama seguinte mostra como as redes comunicam entre centros de dados.

   ![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Esta arquitetura incorre em encargos de dados de saída para dados replicados entre regiões de Azure. Ver [Preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Criar réplica remota

Para criar uma réplica num centro de dados remoto, faça os seguintes passos:

1. [Criar uma rede virtual na nova região.](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)

1. [Configure uma ligação VNet-to-VNet utilizando o portal Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Em alguns casos, poderá ter de utilizar o PowerShell para criar a ligação VNet-to-VNet. Por exemplo, se utilizar diferentes contas Azure, não pode configurar a ligação no portal. Neste caso, [consulte, configure uma ligação VNet-to-VNet utilizando o portal Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Crie um controlador de domínio na nova região.](../../../active-directory/active-directory-new-forest-virtual-machine.md)

   Este controlador de domínio fornece a autenticação se o controlador de domínio no local principal não estiver disponível.

1. [Crie uma máquina virtual SQL Server na nova região.](virtual-machines-windows-portal-sql-server-provision.md)

1. [Crie um equilibrador de carga Azure na rede da nova região.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)

   Este equilibrador de carga deve:

   - Esteja na mesma rede e subnet que a nova máquina virtual.
   - Tenha um endereço IP estático para o ouvinte do grupo de disponibilidade.
   - Inclua uma piscina de backend composta apenas por máquinas virtuais na mesma região que o equilibrador de carga.
   - Utilize uma sonda de porta TCP específica do endereço IP.
   - Tenha uma regra de equilíbrio de carga específica para o Servidor SQL na mesma região.  
   - Seja um Balancer de carga padrão se as máquinas virtuais na piscina de backend não fizerem parte de um único conjunto de disponibilidade ou de um conjunto de escala de máquina virtual. Para uma análise adicional de informações, a visão geral da Norma De equilíbrio de [carga azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Adicione a função de Clustering Failover ao novo Servidor SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Junte-se ao novo Servidor SQL para o domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Detete a nova conta de serviço SQL Server para utilizar uma conta de domínio](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Adicione o novo Servidor SQL ao Cluster de Falha do Servidor do Windows](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Adicione um recurso de endereço IP ao cluster.

   Pode criar o recurso de endereço IP no Failover Cluster Manager. Selecione o nome do cluster e, em seguida, clique no nome do cluster em **Recursos Core cluster** e selecione **Propriedades:** 

   ![Propriedades de cluster](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-name-properties.png)

   Na caixa de diálogo **Properties,** **selecione Adicionar** em **endereço IP**e, em seguida, adicionar o endereço IP do nome do cluster da região da rede remota. Selecione **OK** na caixa de diálogo **IP Address** e, em seguida, selecione **OK** novamente na caixa de diálogo **Cluster Properties** para guardar o novo endereço IP.. 

   ![Adicionar cluster IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/add-cluster-ip-address.png)


1. Adicione o endereço IP como uma dependência para o nome do cluster principal.

   Abra as propriedades do cluster mais uma vez e selecione o separador **Dependencies.** Configure uma dependência de OR para os dois endereços IP: 

   ![Propriedades de cluster](./media/virtual-machines-windows-portal-sql-availability-group-dr/cluster-ip-dependencies.png)

1. Adicione um recurso de endereço IP à função de grupo de disponibilidade no cluster. 

   Clique direito na função do grupo de disponibilidade no Failover Cluster Manager, selecione **Adicionar Recursos,** **Mais Recursos,** e selecione **IP Address**.

   ![Criar endereço IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Configure este endereço IP da seguinte forma:

   - Utilize a rede a partir do centro de dados remoto.
   - Atribua o endereço IP do novo equilibrador de carga Azure. 

1. Adicione o recurso de endereço IP como uma dependência para o cluster do ponto de acesso ao cliente ouvinte (nome da rede).

   A imagem que se segue mostra um recurso de cluster de endereçoip devidamente configurado:

   ![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >O grupo de recursos de cluster inclui ambos os endereços IP. Ambos os endereços IP são dependências para o ponto de acesso ao cliente ouvinte. Utilize o operador **DE** na configuração da dependência do cluster.

1. [Defina os parâmetros do cluster em PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Execute o script PowerShell com o nome da rede de cluster, endereço IP e porta de sonda que configurano equilibrador de carga na nova região.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. No novo Servidor SQL no Gestor de Configuração do Servidor SQL, [ative sempre em grupos de disponibilidade](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. [Abra portas de firewall no novo Servidor SQL](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Os números de porta que precisa de abrir dependem do seu ambiente. Abrir portas para o ponto final espelhado e sonda de saúde azure load balancer.


1. [Adicione uma réplica ao grupo de disponibilidade no novo Servidor SQL](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Para uma réplica numa região remota de Azure, detetete-a para uma replicação assíncrona com falha manual.  

## <a name="set-connection-for-multiple-subnets"></a>Definir a ligação para várias subredes

A réplica no centro de dados remoto faz parte do grupo de disponibilidade, mas está numa sub-rede diferente. Se esta réplica se tornar a réplica primária, podem ocorrer intervalos de ligação de aplicação. Este comportamento é o mesmo que um grupo de disponibilidade no local numa implantação multi-sub-rede. Para permitir ligações a partir de aplicações de clientes, ou atualizar a ligação do cliente ou configurar a resolução de nomes no recurso de nome da rede cluster.

De preferência, atualize as cordas `MultiSubnetFailover=Yes`de ligação ao cliente para definir . Ver [Ligação com MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Se não conseguir modificar as cordas de ligação, pode configurar o cache de resolução de nomes. Consulte o [erro de time-out e não pode ligar-se a um ouvinte de grupo SQL Server 2012 AlwaysOn num ambiente multi-sub-rede](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Falhar na região remota

Para testar a conectividade do ouvinte com a região remota, pode falhar a réplica para a região remota. Embora a réplica seja assíncrona, a falha é vulnerável a potenciais perdas de dados. Para falhar sem perda de dados, altere o modo de disponibilidade para sincronizar e desloque o modo de failover para automático. Utilize os passos seguintes:

1. No **Object Explorer,** ligue-se à instância do Servidor SQL que acolhe a réplica primária.
1. Em **Grupos de Disponibilidade AlwaysOn**, Grupos de **Disponibilidade,** clique à direita no seu grupo de disponibilidade e clique em **Propriedades**.
1. Na página **Geral,** em **Replicas de Disponibilidade,** delineie a réplica secundária no site DR para utilizar o modo de disponibilidade **de compromisso sincronizado** e o modo de falha **automática.**
1. Se tiver uma réplica secundária no mesmo local que a sua réplica primária para alta disponibilidade, detete esta réplica para Commit e **Manual** **Assíncronos** .
1. Clique em OK.
1. No **Object Explorer,** clique no grupo de disponibilidade e clique no **Show Dashboard**.
1. No painel de instrumentos, verifique se a réplica no local da DR está sincronizada.
1. No **Object Explorer,** clique no grupo de disponibilidade e clique em **Failover...**. A SQL Server Management Studios abre um assistente para falhar sobre o Servidor SQL.  
1. Clique em **Seguinte**, e selecione a instância do Servidor SQL no site DR. Clique **em Next** novamente.
1. Ligue-se à instância do Servidor SQL no site DR e clique em **Next**.
1. Na página **Resumo,** verifique as definições e clique em **Terminar**.

Depois de testar a conectividade, desloque a réplica primária de volta para o seu centro de dados primário e recoloque o modo de disponibilidade para as suas definições normais de funcionamento. A tabela que se segue mostra as configurações operacionais normais para a arquitetura descrita neste documento:

| Localização | Instância do servidor | Função | Modo de Disponibilidade | Modo Failover
| ----- | ----- | ----- | ----- | -----
| Centro de dados primário | SQL-1 | Primária | Síncrono | Automático
| Centro de dados primário | SQL-2 | Secundária | Síncrono | Automático
| Centro de dados secundário ou remoto | SQL-3 | Secundária | Assíncrono | Manual


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Mais informações sobre falhas manuais planeadas e forçadas

Para obter mais informações, consulte os seguintes tópicos:

- [Execute uma falha manual planeada de um grupo de disponibilidade (Servidor SQL)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Execute uma falha manual forçada de um grupo de disponibilidade (Servidor SQL)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Links Adicionais

* [Sempre em Grupos de Disponibilidade](https://msdn.microsoft.com/library/hh510230.aspx)
* [Máquinas Virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Equilibradores de carga Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Conjuntos de disponibilidade azure](../manage-availability.md)
