---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: fd635d4c0563c35979f8d85c33dfbde35f05f9e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401093"
---
O ouvinte do grupo de disponibilidade é um endereço IP e nome de rede que o grupo de disponibilidade do SQL Server ouve. Para criar o ouvinte do grupo de disponibilidade, faça o seguinte:

1. <a name="getnet"></a>Obtenha o nome do recurso de rede de cluster.

    a. Utilize RDP para ligar à máquina virtual Azure que acolhe a réplica primária. 

    b. Open Failover Cluster Manager.

    c. Selecione o nó **'Redes'** e observe o nome da rede de cluster. Utilize este nome na `$ClusterNetworkName` variável no script PowerShell. Na imagem a seguir, o nome da rede de cluster é **Cluster Network 1**:

   ![Nome da rede de cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Adicione o ponto de acesso ao cliente.  
    O ponto de acesso ao cliente é o nome de rede que as aplicações utilizam para ligar às bases de dados num grupo de disponibilidade. Crie o ponto de acesso ao cliente no Failover Cluster Manager.

    a. Expanda o nome do cluster e, em seguida, clique em **Funções**.

    b. No painel **Roles,** clique com o botão direito no nome do grupo de disponibilidade e, em seguida, **selecione Adicionar** Ponto de Acesso ao Cliente de Recurso  >  .

   ![Screenshot que mostra a opção do menu Ponto de Acesso ao Cliente.](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Na caixa **Nome,** crie um nome para este novo ouvinte. 
   O nome para o novo ouvinte é o nome de rede que as aplicações usam para se ligarem às bases de dados do grupo de disponibilidade do SQL Server.

    d. Para terminar de criar o ouvinte, clique em **Next** duas vezes e, em seguida, clique em **Terminar**. Não leve o ouvinte ou o recurso on-line neste momento.

1. Leve offline o papel de cluster de grupo de disponibilidade. Em **Failover Cluster Manager** sob **Funções,** clique com o botão direito no papel e selecione **Stop Role**.

1. <a name="congroup"></a>Configurar o recurso IP para o grupo de disponibilidade.

    a. Clique no separador **Recursos** e, em seguida, expanda o ponto de acesso ao cliente que criou.  
    O ponto de acesso ao cliente está offline.

   ![Ponto de Acesso ao Cliente](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Clique com o botão direito no recurso IP e, em seguida, clique em propriedades. Note o nome do endereço IP e use-o na `$IPResourceName` variável no script PowerShell.

    c. No **endereço IP**, clique no Endereço IP **estático.** Desconfigue o endereço IP como o mesmo endereço que utilizou quando definir o endereço do balançador de carga no portal Azure.

   ![Screenshot que mostra onde define o endereço IP.](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Tornar o recurso de grupo de disponibilidade SQL dependente do ponto de acesso do cliente.

    a. Em Failover Cluster Manager, clique em **Roles** e, em seguida, clique no seu grupo de disponibilidade.

    b. No separador **Recursos,** em **Outros Recursos,** clique com o botão certo no grupo de recursos de disponibilidade e, em seguida, clique em **Propriedades.** 

    c. No separador dependências, adicione o nome do recurso ponto de acesso ao cliente (o ouvinte).

   ![Screenshot que mostra onde adicionar o nome no separador Dependências.](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Clique em **OK**.

1. <a name="listname"></a>Tornar o recurso do ponto de acesso do cliente dependente do endereço IP.

    a. Em Failover Cluster Manager, clique em **Roles** e, em seguida, clique no seu grupo de disponibilidade. 

    b. No separador **Recursos,** clique com o botão direito no recurso ponto de acesso do cliente sob **o Nome do Servidor** e, em seguida, clique em **Propriedades**. 

   ![Screenshot que mostra a opção do menu Propriedades para o nome do servidor.](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Clique no separador **Dependências.** Verifique se o endereço IP é uma dependência. Se não for, desaponseeça uma dependência do endereço IP. Se houver vários recursos listados, verifique se os endereços IP têm ou, não E, dependências. Clique em **OK**. 

   ![Recurso IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Pode validar que as dependências estão corretamente configuradas. Em Failover Cluster Manager, vá a Roles, clique com o botão direito no grupo de disponibilidade, clique em **Mais Ações** e, em seguida, clique em  **Mostrar Relatório de Dependência**. Quando as dependências estão corretamente configuradas, o grupo de disponibilidade depende do nome da rede e o nome da rede depende do endereço IP. 


1. <a name="setparam"></a>Desa estale os parâmetros do cluster no PowerShell.

   a. Copie o seguinte script PowerShell para uma das suas instâncias do SQL Server. Atualize as variáveis para o seu ambiente.

   - `$ListenerILBIP` é o endereço IP que criou no equilibrador de carga Azure para o ouvinte do grupo de disponibilidade.
    
   - `$ListenerProbePort` é a porta que configuraste no equilibrador de carga Azure para o ouvinte do grupo de disponibilidade.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Desacorda os parâmetros do cluster executando o script PowerShell num dos nós do cluster.  

   > [!NOTE]
   > Se as suas instâncias do SQL Server estiverem em regiões separadas, tem de executar o script PowerShell duas vezes. Da primeira vez, use o `$ListenerILBIP` e `$ListenerProbePort` da primeira região. Pela segunda vez, use o `$ListenerILBIP` e `$ListenerProbePort` da segunda região. O nome da rede de cluster e o nome do recurso IP do cluster também são diferentes para cada região.

1. Leve online o papel de cluster de grupo de disponibilidade. Em **Failover Cluster Manager** sob **Funções,** clique no papel e selecione **Start Role**.

Se necessário, repita os passos acima para definir os parâmetros de cluster para o endereço IP do cluster WSFC.

1. Obtenha o nome do endereço IP do endereço IP do Cluster WSFC. In **Failover Cluster Manager** under Cluster Core **Resources**, localizar o nome **do servidor**.

1. Clique no **endereço IP** com direito e selecione **Propriedades.**

1. Copie o **Nome** do endereço IP. Pode ser `Cluster IP Address` que. 

1. <a name="setwsfcparam"></a>Desa estale os parâmetros do cluster no PowerShell.
  
   a. Copie o seguinte script PowerShell para uma das suas instâncias do SQL Server. Atualize as variáveis para o seu ambiente.

   - `$ClusterCoreIP` é o endereço IP que criou no equilibrador de carga Azure para o recurso de cluster de núcleo WSFC. É diferente do endereço IP para o ouvinte do grupo de disponibilidade.

   - `$ClusterProbePort` é a porta que configuraste no equilibrador de carga Azure para a sonda de saúde WSFC. É diferente da sonda para o ouvinte do grupo de disponibilidade.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Desacorda os parâmetros do cluster executando o script PowerShell num dos nós do cluster.  

>[!WARNING]
>A porta da sonda de saúde do grupo de disponibilidade tem de ser diferente da porta de sonda de saúde do núcleo ip do cluster. Nestes exemplos, a porta do ouvinte é 59999 e a porta de sonda ip do núcleo do cluster é 58888. Ambas as portas requerem uma regra de firewall de entrada.
