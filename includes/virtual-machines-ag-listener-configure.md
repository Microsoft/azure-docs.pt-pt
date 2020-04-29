---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 73ba78eca710f0b98b2a209494519cb8003e554b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75467554"
---
O ouvinte do grupo de disponibilidade é um endereço IP e nome de rede que o grupo de disponibilidade do SQL Server ouve. Para criar o ouvinte do grupo de disponibilidade, faça o seguinte:

1. <a name="getnet"></a>Obtenha o nome do recurso da rede de cluster.

    a. Utilize RDP para ligar à máquina virtual Azure que acolhe a réplica primária. 

    b. Open Failover Cluster Manager.

    c. Selecione o nó **de Redes** e note o nome da rede de cluster. Utilize este nome `$ClusterNetworkName` na variável no script PowerShell. Na imagem seguinte, o nome da rede cluster é **Cluster Network 1:**

   ![Nome da rede cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Adicione o ponto de acesso ao cliente.  
    O ponto de acesso ao cliente é o nome de rede que as aplicações usam para se conectarem às bases de dados num grupo de disponibilidade. Crie o ponto de acesso ao cliente no Failover Cluster Manager.

    a. Expanda o nome do cluster e, em seguida, clique em **Papéis**.

    b. No painel **Roles,** clique no nome do grupo de disponibilidade e, em seguida, selecione Adicionar Ponto de Acesso ao**Cliente**de **Recursos** > .

   ![Ponto de acesso ao cliente](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Na caixa **nome,** crie um nome para este novo ouvinte. 
   O nome do novo ouvinte é o nome de rede que as aplicações usam para se conectarem a bases de dados no grupo de disponibilidade do Servidor SQL.

    d. Para terminar a criação do ouvinte, clique em **Seguinte** duas vezes e, em seguida, clique em **Terminar**. Não deixe o ouvinte ou o recurso online neste momento.

1. Tome o papel de cluster do grupo de disponibilidade offline. No **Failover Cluster Manager** em **Funções,** clique na função e selecione **Stop Role**.

1. <a name="congroup"></a>Configure o recurso IP para o grupo de disponibilidade.

    a. Clique no separador **Recursos** e, em seguida, expanda o ponto de acesso ao cliente que criou.  
    O ponto de acesso ao cliente está offline.

   ![Ponto de acesso ao cliente](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Clique no recurso IP e, em seguida, clique em propriedades. Note o nome do endereço IP e `$IPResourceName` use-o na variável no script PowerShell.

    c. No **endereço IP,** clique no **endereço IP estático**. Defino o endereço IP como o mesmo endereço utilizado quando definir o endereço do equilibrador de carga no portal Azure.

   ![Recurso IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Tornar o recurso do grupo de disponibilidade do SQL Server dependente do ponto de acesso ao cliente.

    a. No Failover Cluster Manager, clique em **Funções**e clique no seu grupo de disponibilidade.

    b. No separador **Recursos,** sob **Outros Recursos,** clique à direita no grupo de recursos de disponibilidade e, em seguida, clique em **Propriedades**. 

    c. No separador de dependências, adicione o nome do recurso do ponto de acesso ao cliente (o ouvinte).

   ![Recurso IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Clique em **OK**.

1. <a name="listname"></a>Tornar o recurso do ponto de acesso ao cliente dependente do endereço IP.

    a. No Failover Cluster Manager, clique em **Funções**e clique no seu grupo de disponibilidade. 

    b. No separador **Recursos,** clique no recurso do ponto de acesso do cliente em nome do **servidor**e, em seguida, clique em **Propriedades**. 

   ![Recurso IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Clique no separador **Dependências.** Verifique se o endereço IP é uma dependência. Caso contrário, estabeleça uma dependência do endereço IP. Se houver vários recursos listados, verifique se os endereços IP têm or, não dependências. Clique em **OK**. 

   ![Recurso IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Pode validar que as dependências estão corretamente configuradas. No Failover Cluster Manager, vá a Papéis, clique no grupo de disponibilidade, clique em **Mais Ações**e, em seguida, clique em Mostrar Relatório **de Dependência**. Quando as dependências estão corretamente configuradas, o grupo de disponibilidade depende do nome da rede, e o nome da rede depende do endereço IP. 


1. <a name="setparam"></a>Defina os parâmetros do cluster no PowerShell.

   a. Copie o seguinte script PowerShell para uma das instâncias do Seu Servidor SQL. Atualize as variáveis para o seu ambiente.

   - `$ListenerILBIP`é o endereço IP que criou no equilibrador de carga Azure para o ouvinte do grupo de disponibilidade.
    
   - `$ListenerProbePort`é a porta configurada no equilibrador de carga Azure para o ouvinte do grupo de disponibilidade.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Defina os parâmetros do cluster executando o script PowerShell num dos nós do cluster.  

   > [!NOTE]
   > Se os seus casos de Servidor SQL estiverem em regiões separadas, precisa de executar o script PowerShell duas vezes. A primeira vez, `$ListenerILBIP` `$ListenerProbePort` use o e a partir da primeira região. Pela segunda vez, `$ListenerILBIP` `$ListenerProbePort` use a e a partir da segunda região. O nome da rede cluster e o nome do recurso IP do cluster também são diferentes para cada região.

1. Traga online o papel de cluster do grupo de disponibilidade. No **Failover Cluster Manager** em **Funções,** clique na função e selecione **Função inicial**.

Se necessário, repita os passos acima para definir os parâmetros do cluster para o endereço IP do cluster WSFC.

1. Obtenha o nome de endereço IP do endereço IP do Cluster WSFC. No **Failover Cluster Manager** em recursos core de **cluster,** localize o **nome do servidor**.

1. Clique no **endereço IP**do clique direito, e selecione **Propriedades**.

1. Copie o **nome** do endereço IP. Pode ser `Cluster IP Address`que. 

1. <a name="setwsfcparam"></a>Defina os parâmetros do cluster no PowerShell.
  
   a. Copie o seguinte script PowerShell para uma das instâncias do Seu Servidor SQL. Atualize as variáveis para o seu ambiente.

   - `$ClusterCoreIP`é o endereço IP que criou no equilibrador de carga Azure para o recurso de cluster de núcleo WSFC. É diferente do endereço IP para o ouvinte do grupo de disponibilidade.

   - `$ClusterProbePort`é a porta que configuraste no equilibrador de carga Azure para a sonda de saúde WSFC. É diferente da sonda para o ouvinte do grupo de disponibilidade.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Defina os parâmetros do cluster executando o script PowerShell num dos nós do cluster.  

>[!WARNING]
>A porta de sonda de saúde do grupo de disponibilidade tem de ser diferente da porta de sonda de saúde IP do núcleo de cluster. Nestes exemplos, a porta de escuta é 59999 e o núcleo de cluster IP porta de sonda de saúde é 58888. Ambas as portas requerem uma regra de firewall de entrada.
