---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 73ba78eca710f0b98b2a209494519cb8003e554b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467554"
---
O ouvinte do grupo de disponibilidade é um nome de rede e endereço IP que o grupo de disponibilidade SQL Server escuta. Para criar o ouvinte do grupo de disponibilidade, faça o seguinte:

1. <a name="getnet"></a>Obtenha o nome do recurso de rede do cluster.

    a. Use o RDP para se conectar à máquina virtual do Azure que hospeda a réplica primária. 

    b. Abra Gerenciador de Cluster de Failover.

    c. Selecione o nó **redes** e anote o nome da rede do cluster. Use esse nome na variável `$ClusterNetworkName` no script do PowerShell. Na imagem a seguir, o nome da rede do cluster é **rede de cluster 1**:

   ![Nome da rede do cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Adicione o ponto de acesso para cliente.  
    O ponto de acesso para cliente é o nome de rede que os aplicativos usam para se conectar aos bancos de dados em um grupo de disponibilidade. Crie o ponto de acesso para cliente no Gerenciador de Cluster de Failover.

    a. Expanda o nome do cluster e clique em **funções**.

    b. No painel **funções** , clique com o botão direito do mouse no nome do grupo de disponibilidade e selecione **Adicionar recurso** > **ponto de acesso para cliente**.

   ![Ponto de acesso para cliente](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Na caixa **nome** , crie um nome para esse novo ouvinte. 
   O nome do novo ouvinte é o nome da rede que os aplicativos usam para se conectar a bancos de dados no grupo de disponibilidade SQL Server.

    d. Para concluir a criação do ouvinte, clique em **Avançar** duas vezes e, em seguida, clique em **concluir**. Não coloque o ouvinte ou recurso online neste momento.

1. Coloque a função de cluster do grupo de disponibilidade offline. Em **Gerenciador de cluster de failover** em **funções**, clique com o botão direito do mouse na função e selecione **parar função**.

1. <a name="congroup"></a>Configure o recurso de IP para o grupo de disponibilidade.

    a. Clique na guia **recursos** e expanda o ponto de acesso para cliente que você criou.  
    O ponto de acesso para cliente está offline.

   ![Ponto de acesso para cliente](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Clique com o botão direito do mouse no recurso IP e clique em Propriedades. Anote o nome do endereço IP e use-o na variável `$IPResourceName` no script do PowerShell.

    c. Em **endereço IP**, clique em **endereço IP estático**. Defina o endereço IP como o mesmo endereço que você usou ao definir o endereço do balanceador de carga no portal do Azure.

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Torne o recurso de grupo de disponibilidade SQL Server dependente do ponto de acesso para cliente.

    a. Em Gerenciador de Cluster de Failover, clique em **funções**e, em seguida, clique em seu grupo de disponibilidade.

    b. Na guia **recursos** , em **outros recursos**, clique com o botão direito do mouse no grupo de recursos de disponibilidade e clique em **Propriedades**. 

    c. Na guia dependências, adicione o nome do recurso de ponto de acesso para cliente (o ouvinte).

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Clique em **OK**.

1. <a name="listname"></a>Torne o recurso de ponto de acesso para cliente dependente do endereço IP.

    a. Em Gerenciador de Cluster de Failover, clique em **funções**e, em seguida, clique em seu grupo de disponibilidade. 

    b. Na guia **recursos** , clique com o botão direito do mouse no recurso ponto de acesso para cliente em **nome do servidor**e clique em **Propriedades**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Clique na guia **dependências** . Verifique se o endereço IP é uma dependência. Se não estiver, defina uma dependência no endereço IP. Se houver vários recursos listados, verifique se os endereços IP têm as dependências ou, não e. Clique em **OK**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Você pode validar que as dependências estão configuradas corretamente. Em Gerenciador de Cluster de Failover, vá para funções, clique com o botão direito do mouse no grupo de disponibilidade, clique em **mais ações**e, em seguida, clique em **Mostrar relatório de dependência**. Quando as dependências são configuradas corretamente, o grupo de disponibilidade depende do nome da rede e o nome da rede depende do endereço IP. 


1. <a name="setparam"></a>Defina os parâmetros de cluster no PowerShell.

   a. Copie o seguinte script do PowerShell para uma de suas instâncias de SQL Server. Atualize as variáveis para seu ambiente.

   - `$ListenerILBIP` é o endereço IP que você criou no Azure Load Balancer para o ouvinte do grupo de disponibilidade.
    
   - `$ListenerProbePort` é a porta configurada no balanceador de carga do Azure para o ouvinte do grupo de disponibilidade.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Defina os parâmetros de cluster executando o script do PowerShell em um dos nós de cluster.  

   > [!NOTE]
   > Se suas instâncias de SQL Server estiverem em regiões separadas, você precisará executar o script do PowerShell duas vezes. Na primeira vez, use o `$ListenerILBIP` e `$ListenerProbePort` da primeira região. Na segunda vez, use o `$ListenerILBIP` e `$ListenerProbePort` da segunda região. O nome da rede do cluster e o nome do recurso de IP do cluster também são diferentes para cada região.

1. Coloque a função de cluster do grupo de disponibilidade online. Em **Gerenciador de cluster de failover** em **funções**, clique com o botão direito do mouse na função e selecione **Iniciar função**.

Se necessário, repita as etapas acima para definir os parâmetros de cluster para o endereço IP do Cluster WSFC.

1. Obtenha o nome do endereço IP do endereço IP do Cluster WSFC. Em **Gerenciador de cluster de failover** em **recursos principais do cluster**, localize **nome do servidor**.

1. Clique com o botão direito do mouse em **endereço IP**e selecione **Propriedades**.

1. Copie o **nome** do endereço IP. Pode ser `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Defina os parâmetros de cluster no PowerShell.
  
   a. Copie o seguinte script do PowerShell para uma de suas instâncias de SQL Server. Atualize as variáveis para seu ambiente.

   - `$ClusterCoreIP` é o endereço IP que você criou no Azure Load Balancer para o recurso de cluster do WSFC Core. Ele é diferente do endereço IP para o ouvinte do grupo de disponibilidade.

   - `$ClusterProbePort` é a porta configurada no balanceador de carga do Azure para a investigação de integridade do WSFC. Ele é diferente da investigação do ouvinte do grupo de disponibilidade.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Defina os parâmetros de cluster executando o script do PowerShell em um dos nós de cluster.  

>[!WARNING]
>A porta de investigação de integridade do ouvinte de grupo de disponibilidade deve ser diferente da porta de investigação de integridade do endereço IP do núcleo do cluster. Nesses exemplos, a porta do ouvinte é 59999 e a porta de investigação de integridade do endereço IP do núcleo do cluster é 58888. Ambas as portas exigem uma regra de firewall de entrada de permissão.
