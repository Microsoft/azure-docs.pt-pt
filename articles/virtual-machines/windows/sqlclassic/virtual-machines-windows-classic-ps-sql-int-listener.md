---
title: Configure um ouvinte ILB para grupos de disponibilidade (Clássico)
description: Este tutorial utiliza recursos criados com o modelo de implementação clássico, e cria um ouvinte de grupo always on para um SQL Server VM em Azure que usa um balanceor de carga interna.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77j
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f26c5a6c6fc2774d19beaa021015357a1991f0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978161"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Configure um ouvinte ILB para grupos de disponibilidade em VMs de servidor Estoque Azure SQL
> [!div class="op_single_selector"]
> * [Ouvinte interno](../classic/ps-sql-int-listener.md)
> * [Ouvinte externo](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e classic](../../../azure-resource-manager/management/deployment-models.md). Este artigo abrange a utilização do modelo clássico de implantação. Recomendamos que a maioria das novas implementações utilizem o modelo De Gestor de Recursos.

Para configurar um ouvinte para um grupo de disponibilidade Always On no modelo Derecursos Manager, consulte [Configure um balancer de carga para um grupo sempre em funcionação no Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

O seu grupo de disponibilidade pode conter réplicas que estão apenas no local ou apenas no Azure, ou que abrangem tanto no local como no Azure para configurações híbridas. As réplicas azure podem residir na mesma região ou em várias regiões que usam várias redes virtuais. Os procedimentos deste artigo assumem que já [configuraram um grupo de disponibilidade,](../classic/portal-sql-alwayson-availability-groups.md) mas ainda não configuraram um ouvinte.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Diretrizes e limitações para os ouvintes internos
A utilização de um equilibrista interno de carga (ILB) com um ouvinte de grupo de disponibilidade em Azure está sujeita às seguintes orientações:

* O ouvinte do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* Apenas um ouvinte de grupo de disponibilidade interna é suportado para cada serviço na nuvem, porque o ouvinte está configurado para o ILB, e há apenas um ILB para cada serviço na nuvem. No entanto, é possível criar múltiplos ouvintes externos. Para mais informações, consulte [Configure um ouvinte externo para grupos de disponibilidade Always On em Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do ouvinte
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo centra-se na criação de um ouvinte que usa um ILB. Se precisar de um ouvinte público ou externo, consulte a versão deste artigo que discute a criação de um [ouvinte externo](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos finais VM equilibrados com retorno direto do servidor
Primeiro cria-se um ILB executando o guião mais tarde nesta secção.

Crie um ponto final equilibrado para cada VM que acolhe uma réplica Azure. Se tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço de nuvem na mesma rede virtual Azure. Criar réplicas de grupo de disponibilidade que abrangem várias regiões do Azure requer configurar várias redes virtuais. Para obter mais informações sobre a configuração da conectividade da rede virtual, consulte a [rede virtual Configure para a conectividade virtual da rede](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal Azure, vá a cada VM que acolhe uma réplica para ver os detalhes.

2. Clique no separador **Pontos Finais** para cada VM.

3. Verifique se o **Nome** e o **Porto Público** do ponto final do ouvinte que pretende utilizar ainda não estão a ser utilizados. No exemplo desta secção, o nome é *MyEndpoint*, e a porta é *1433*.

4. No seu cliente local, faça o download e instale o mais recente [módulo PowerShell.](https://azure.microsoft.com/downloads/)

5. Inicie a Azure PowerShell.  
    Abre-se uma nova sessão powerShell, com os módulos administrativos Azure carregados.

6. Execute `Get-AzurePublishSettingsFile`. Este cmdlet direciona-o para um navegador para descarregar um ficheiro de definições de publicação para um diretório local. Pode ser solicitado para as suas credenciais de entrada para a sua subscrição Azure.

7. Execute `Import-AzurePublishSettingsFile` o seguinte comando com o caminho do ficheiro de definições de publicação que descarregou:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Depois de importado o ficheiro de definições de publicação, pode gerir a subscrição do Azure na sessão PowerShell.

8. Para *ILB,* atribua um endereço IP estático. Examine a configuração da rede virtual atual executando o seguinte comando:

        (Get-AzureVNetConfig).XMLConfiguration
9. Note o nome *Subnet* para a subnet que contém os VMs que acolhem as réplicas. Este nome é usado no parâmetro $SubnetName no script.

10. Note o nome *VirtualNetworkSite* e o *endereço inicialPrefix* para a sub-rede que contém os VMs que acolhem as réplicas. Procure um endereço IP disponível, `Test-AzureStaticVNetIP` passando ambos os valores para o comando e examinando os *Endereços Disponíveis*. Por exemplo, se a rede virtual for chamada *MyVNet* e tiver uma gama de endereços de sub-rede que começa em *172.16.0.128,* o seguinte comando listaria endereços disponíveis:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Selecione um dos endereços disponíveis e use-o no parâmetro $ILBStaticIP do script no passo seguinte.

12. Copie o seguinte guião PowerShell para um editor de texto e detetete os valores variáveis de acordo com o seu ambiente. Foram previstos incumprimentos para alguns parâmetros.  

    As implementações existentes que utilizam grupos de afinidade não podem adicionar um ILB. Para obter mais informações sobre os requisitos do ILB, consulte a [visão geral do equilibrador](../../../load-balancer/load-balancer-internal-overview.md)de carga interna .

    Além disso, se o seu grupo de disponibilidade abrange regiões de Azure, você deve executar o script uma vez em cada datacenter para o serviço de nuvem e nós que residem nesse datacenter.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Depois de definir as variáveis, copie o script do editor de texto para a sua sessão PowerShell para executá-lo. Se a solicitação ainda aparecer, **>>** prima Enter novamente para se certificar de que o script começa a funcionar.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verifique se kB2854082 está instalado se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas de firewall em nódeos do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o ouvinte do grupo de disponibilidade

Crie o ouvinte do grupo de disponibilidade em dois passos. Em primeiro lugar, crie o recurso de cluster de pontos de acesso ao cliente e configure dependências. Em segundo lugar, configure os recursos de cluster na PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso ao cliente e configurar as dependências do cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configure os recursos de cluster no PowerShell
1. Para o ILB, deve utilizar o endereço IP do ILB que foi criado anteriormente. Para obter este endereço IP no PowerShell, utilize o seguinte script:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Num dos VMs, copie o script PowerShell para o seu sistema operativo para um editor de texto e, em seguida, detete as variáveis para os valores que observou anteriormente.

    Para windows Server 2012 ou posterior, utilize o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Para o Windows Server 2008 R2, utilize o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Depois de definir as variáveis, abra uma janela elevada do Windows PowerShell, colhe o script do editor de texto para a sua sessão PowerShell para executá-la. Se o pedido **>>** ainda aparecer, pressione O Enter novamente para se certificar de que o script começa a funcionar.

4. Repita os passos anteriores para cada VM.  
    Este script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros, como a porta da sonda. Quando o recurso ip address é colocado on-line, ele pode responder às sondagens na porta da sonda a partir do ponto final equilibrado que você criou anteriormente.

## <a name="bring-the-listener-online"></a>Traga o ouvinte on-line
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de acompanhamento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Teste o ouvinte do grupo de disponibilidade (dentro da mesma rede virtual)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
