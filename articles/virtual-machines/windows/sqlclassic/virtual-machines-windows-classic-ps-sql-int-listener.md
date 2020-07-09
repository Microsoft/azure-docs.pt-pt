---
title: Configure um ouvinte ILB para grupos de disponibilidade (Clássico)
description: Este tutorial utiliza recursos criados com o modelo de implementação clássico, e cria um ouvinte de grupo de disponibilidade Always On para um SQL Server VM em Azure que utiliza um equilibrador de carga interno.
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
ms.openlocfilehash: b677821ae32d4d916b6235228ae2807397c9fc60
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078498"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Configure um ouvinte ILB para grupos de disponibilidade em VMs do Servidor Azure SQL
> [!div class="op_single_selector"]
> * [Ouvinte interno](../classic/ps-sql-int-listener.md)
> * [Ouvinte externo](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo abrange a utilização do modelo clássico de implantação. Recomendamos que a maioria das novas implementações utilizem o modelo Gestor de Recursos.

Para configurar um ouvinte para um grupo de disponibilidade Always On no modelo Gestor de Recursos, consulte [configurar um equilibrador de carga para um grupo de disponibilidade Always On em Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).

O seu grupo de disponibilidade pode conter réplicas que são apenas no local ou apenas Azure, ou que se estendem tanto no local como no Azure para configurações híbridas. As réplicas azure podem residir na mesma região ou em várias regiões que utilizam várias redes virtuais. Os procedimentos deste artigo pressupõem que já [configuraste um grupo de disponibilidade,](../classic/portal-sql-alwayson-availability-groups.md) mas ainda não configuraste um ouvinte.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Diretrizes e limitações para os ouvintes internos
A utilização de um equilibrador de carga interno (ILB) com um ouvinte de grupo de disponibilidade em Azure está sujeita às seguintes orientações:

* O ouvinte do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* Apenas um ouvinte de grupo de disponibilidade interna é suportado para cada serviço na nuvem, porque o ouvinte está configurado para o ILB, e há apenas um ILB para cada serviço na nuvem. No entanto, é possível criar vários ouvintes externos. Para obter mais informações, consulte [configurar um ouvinte externo para sempre em grupos de disponibilidade em Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do ouvinte
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo centra-se na criação de um ouvinte que usa um ILB. Se precisar de um ouvinte público ou externo, consulte a versão deste artigo que discute a criação de um [ouvinte externo](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos finais VM equilibrados em carga com retorno direto do servidor
Primeiro cria-se um ILB executando o script mais tarde nesta secção.

Crie um ponto final equilibrado para cada VM que acolhe uma réplica Azure. Se tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço de nuvem na mesma rede virtual Azure. A criação de réplicas de grupos de disponibilidade que abrangem várias regiões do Azure requer a configuração de várias redes virtuais. Para obter mais informações sobre a configuração da conectividade da rede virtual transversal, consulte a [rede virtual Configure para a conectividade da rede virtual.](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

1. No portal Azure, vá a cada VM que acolhe uma réplica para ver os detalhes.

2. Clique no **separador Pontos finais** para cada VM.

3. Verifique se o **Nome** e **o Porto Público** do ponto final do ouvinte que pretende utilizar ainda não estão a ser utilizados. No exemplo desta secção, o nome é *MyEndpoint,* e a porta é *1433*.

4. No seu cliente local, descarregue e instale o mais recente [módulo PowerShell](https://azure.microsoft.com/downloads/).

5. Inicie a Azure PowerShell.  
    Abre uma nova sessão PowerShell, com os módulos administrativos Azure carregados.

6. Execute `Get-AzurePublishSettingsFile`. Este cmdlet direciona-o para um navegador para descarregar um ficheiro de configurações de publicação para um diretório local. Pode ser solicitado para as suas credenciais de inscrição para a sua assinatura Azure.

7. Execute o seguinte `Import-AzurePublishSettingsFile` comando com o caminho do ficheiro de definições de publicação que descarregou:

    ```powershell
    Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
    ```

    Depois de importado o ficheiro de definições de publicação, pode gerir a sua subscrição Azure na sessão PowerShell.

8. Para *o ILB,* atribua um endereço IP estático. Examine a configuração atual da rede virtual executando o seguinte comando:

    ```powershell
    (Get-AzureVNetConfig).XMLConfiguration
    ```

9. Note o nome *sub-rede* para a sub-rede que contém os VMs que acolhem as réplicas. Este nome é usado no parâmetro $SubnetName no script.

10. Note o nome *VirtualNetworkSite* e o *Endereço InicialPrefixo* para a sub-rede que contém os VMs que acolhem as réplicas. Procure um endereço IP disponível, passando os dois valores para o `Test-AzureStaticVNetIP` comando e examinando os *Endereços Existentes*. Por exemplo, se a rede virtual for denominada *MyVNet* e tiver uma gama de endereços de sub-rede que começa em *172.16.0.128,* o seguinte comando listaria endereços disponíveis:

    ```powershell
    (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
    ```

11. Selecione um dos endereços disponíveis e use-o no parâmetro $ILBStaticIP do script no passo seguinte.

12. Copie o seguinte script PowerShell para um editor de texto e desaprote os valores variáveis de acordo com o seu ambiente. Foram previstos incumprimentos para alguns parâmetros.  

    As implementações existentes que utilizam grupos de afinidade não podem adicionar um ILB. Para obter mais informações sobre os requisitos do ILB, consulte [a visão geral do balançador de carga interna](../../../load-balancer/load-balancer-internal-overview.md).

    Além disso, se o seu grupo de disponibilidade abrange regiões Azure, deve executar o script uma vez em cada datacenter para o serviço de nuvem e nós que residem nesse datacenter.

    ```powershell
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
    ```

13. Depois de definir as variáveis, copie o script do editor de texto para a sessão PowerShell para executá-lo. Se a indicação ainda **>>** aparecer, prima Enter novamente para se certificar de que o script começa a funcionar.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verifique se o KB2854082 está instalado se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas de firewall em nosmos de grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o ouvinte do grupo de disponibilidade

Crie o ouvinte do grupo de disponibilidade em dois passos. Em primeiro lugar, crie o recurso de cluster de ponto de acesso ao cliente econfigure dependências. Segundo, configurar os recursos de cluster na PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Crie o ponto de acesso ao cliente e configuure as dependências do cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configure os recursos de cluster na PowerShell
1. Para o ILB, deve utilizar o endereço IP do ILB que foi criado anteriormente. Para obter este endereço IP no PowerShell, utilize o seguinte script:

    ```powershell
    # Define variables
    $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
    (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress
    ```

2. Num dos VMs, copie o script PowerShell para o seu sistema operativo para um editor de texto e, em seguida, defina as variáveis para os valores que observou anteriormente.

    Para o Windows Server 2012 ou mais tarde, utilize o seguinte script:

    ```powershell
    # Define variables
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP address resource name
    $ILBIP = "<X.X.X.X>" # the IP address of the ILB

    Import-Module FailoverClusters

    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    Para Windows Server 2008 R2, utilize o seguinte script:

    ```powershell
    # Define variables
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP address resource name
    $ILBIP = "<X.X.X.X>" # the IP address of the ILB

    Import-Module FailoverClusters

    cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
    ```

3. Depois de definir as variáveis, abra uma janela elevada do Windows PowerShell, cole o script do editor de texto na sua sessão PowerShell para executá-lo. Se a solicitação ainda **>>** aparecer, prima Introduza novamente para se certificar de que o script começa a funcionar.

4. Repita os passos anteriores para cada VM.  
    Este script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros, como a porta da sonda. Quando o recurso de endereço IP é trazido on-line, pode responder à votação na porta da sonda a partir do ponto final equilibrado de carga que criou anteriormente.

## <a name="bring-the-listener-online"></a>Traga o ouvinte on-line
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Artigos de acompanhamento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Teste o ouvinte do grupo de disponibilidade (dentro da mesma rede virtual)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Próximos passos
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
