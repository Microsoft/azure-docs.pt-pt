---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9c7c6d31b9443ee09539d4882a9e8f4c4332763b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124643"
---
O serviço de escuta do grupo de disponibilidade é um nome de rede e endereço IP que o grupo de disponibilidade do SQL Server escuta na. Para criar o serviço de escuta do grupo de disponibilidade, faça o seguinte:

1. <a name="getnet"></a>Obtenha o nome do recurso de rede do cluster.

    a. Utilize o RDP para ligar à máquina virtual do Azure que aloja a réplica primária. 

    b. Abra o Gestor de clusters de ativação pós-falha.

    c. Selecione o **redes** nó e tenha em atenção o nome de rede do cluster. Utilizar este nome no `$ClusterNetworkName` variáveis no script do PowerShell. Na imagem seguinte é o nome de rede do cluster **rede de Cluster 1**:

   ![Nome de rede do cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Adicione o ponto de acesso de cliente.  
    O ponto de acesso de cliente é o nome de rede que aplicações utilizam para ligar às bases de dados num grupo de disponibilidade. Crie o ponto de acesso de cliente no Gestor de clusters de ativação pós-falha.

    a. Expanda o nome do cluster e, em seguida, clique em **funções**.

    b. Na **funções** painel, clique com o botão direito no nome do grupo de disponibilidade e, em seguida, selecione **adicionar recursos** > **ponto de acesso de cliente**.

   ![Ponto de acesso de cliente](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Na **nome** caixa, crie um nome para este novo serviço de escuta. 
   O nome para o novo serviço de escuta é o nome de rede que aplicações utilizam para ligar a bases de dados no grupo de disponibilidade do SQL Server.

    d. Para acabar de criar o serviço de escuta, clique em **próxima** duas vezes e, em seguida, clique em **concluir**. Não inclua o serviço de escuta ou recurso online neste momento.

1. Coloque a função de cluster de grupo de disponibilidade offline. Na **Gestor de clusters de ativação pós-falha** sob **funções**, a função com o botão direito e selecione **parar função**.

1. <a name="congroup"></a>Configure o recurso IP para o grupo de disponibilidade.

    a. Clique nas **recursos** separador e, em seguida, expanda o ponto de acesso de cliente que criou.  
    O ponto de acesso de cliente está offline.

   ![Ponto de acesso de cliente](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Com o botão direito do recurso de IP e, em seguida, clique em propriedades. Tome nota do nome do endereço IP e utilizá-la no `$IPResourceName` variáveis no script do PowerShell.

    c. Sob **endereço IP**, clique em **endereço IP estático**. Defina o endereço IP como o mesmo endereço que utilizou quando definir o endereço de Balanceador de carga no portal do Azure.

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Tornar o recurso do grupo de disponibilidade do SQL Server dependentes no ponto de acesso de cliente.

    a. No Gestor de clusters de ativação pós-falha, clique em **funções**e, em seguida, clique em seu grupo de disponibilidade.

    b. Sobre o **recursos** separador, em **outros recursos**, o grupo de recursos de disponibilidade com o botão direito e, em seguida, clique em **propriedades**. 

    c. Na guia dependências, adicione o nome do recurso de ponto (o serviço de escuta) de acesso de cliente.

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Clique em **OK**.

1. <a name="listname"></a>Marca o recurso de ponto de acesso de cliente dependente no endereço IP.

    a. No Gestor de clusters de ativação pós-falha, clique em **funções**e, em seguida, clique em seu grupo de disponibilidade. 

    b. Sobre o **recursos** separador, clique com o botão direito do recurso de ponto de acesso de cliente em **nome do servidor de**e, em seguida, clique em **propriedades**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Clique nas **dependências** separador. Certifique-se de que o endereço IP é uma dependência. Se não for, defina uma dependência no endereço IP. Se existirem vários recursos listados, certifique-se de que os endereços IP têm ou, não AND, as dependências. Clique em **OK**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Pode validar que as dependências estão corretamente configuradas. No Gestor de clusters de ativação pós-falha, aceda às funções, clique com botão direito do grupo de disponibilidade, clique em **mais ações**e, em seguida, clique em **Mostrar relatório de dependência**. Quando as dependências estão corretamente configuradas, o grupo de disponibilidade está dependente do nome da rede e o nome da rede é dependente do endereço IP. 


1. <a name="setparam"></a>Defina os parâmetros de cluster no PowerShell.

   a. Copie o seguinte script do PowerShell para uma das suas instâncias do SQL Server. Atualize as variáveis para o seu ambiente.

   - `$ListenerILBIP` é o endereço IP que criou no balanceador de carga do Azure para o serviço de escuta do grupo de disponibilidade.
    
   - `$ListenerProbePort` é a porta que configurou no balanceador de carga do Azure para o serviço de escuta do grupo de disponibilidade.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Defina os parâmetros de cluster ao executar o script do PowerShell num de nós do cluster.  

   > [!NOTE]
   > Se as instâncias do SQL Server estão em diferentes regiões, terá de executar o script PowerShell duas vezes. Na primeira vez, utilize o `$ListenerILBIP` e `$ListenerProbePort` da primeira região. Na segunda vez, utilize o `$ListenerILBIP` e `$ListenerProbePort` partir da segunda região. O nome de rede do cluster e o nome de recurso IP de cluster também são diferentes para cada região.

1. Colocar a função de cluster de grupo de disponibilidade online. Na **Gestor de clusters de ativação pós-falha** sob **funções**, clique com o botão direito do rato na função e selecione **iniciar função**.

Se necessário, repita os passos acima para definir os parâmetros de cluster para o endereço IP do cluster WSFC.

1. Obtenha o nome de endereço IP do endereço IP do Cluster de WSFC. Na **Gestor de clusters de ativação pós-falha** sob **recursos principais do Cluster**, localize **nome do servidor**.

1. Com o botão direito **endereço IP**e selecione **propriedades**.

1. Copiar o **nome** do endereço IP. Pode ser `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Defina os parâmetros de cluster no PowerShell.
  
   a. Copie o seguinte script do PowerShell para uma das suas instâncias do SQL Server. Atualize as variáveis para o seu ambiente.

   - `$ClusterCoreIP` é o endereço IP que criou no balanceador de carga do Azure para o recurso de cluster do WSFC core. É diferente do endereço IP para o serviço de escuta do grupo de disponibilidade.

   - `$ClusterProbePort` é a porta que configurou no balanceador de carga do Azure para a sonda de estado de funcionamento do WSFC. É diferente da sonda para o serviço de escuta do grupo de disponibilidade.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Defina os parâmetros de cluster ao executar o script do PowerShell num de nós do cluster.  

>[!WARNING]
>Porta de sonda de estado de funcionamento de serviço de escuta de grupo de disponibilidade tem de ser diferente do que a porta de sonda de estado de funcionamento de endereço do cluster principal IP. Nestes exemplos, a porta do serviço de escuta é 59999 e o endereço IP de núcleo de cluster é 58888. Ambas as portas requerem uma regra de firewall de entrada de permissão.