---
title: Configurar um ouvinte de ILB para grupos de disponibilidade (clássico)
description: Este tutorial usa recursos criados com o modelo de implantação clássico e cria um ouvinte de grupo de disponibilidade Always On no para uma VM SQL Server no Azure que usa um balanceador de carga interno.
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
ms.openlocfilehash: 29aaedeafb3995cc09e221d2e049dd538808904a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032663"
---
# <a name="configure-an-ilb-listener-for-availability-groups-on-azure-sql-server-vms"></a>Configurar um ouvinte de ILB para grupos de disponibilidade em VMs SQL Server do Azure
> [!div class="op_single_selector"]
> * [Ouvinte interno](../classic/ps-sql-int-listener.md)
> * [Ouvinte externo](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássico. Recomendamos que a maioria das implantações novas usem o modelo do Resource Manager.

Para configurar um ouvinte para um grupo de disponibilidade Always On no modelo do Resource Manager, consulte [configurar um balanceador de carga para um grupo de disponibilidade Always on no Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Seu grupo de disponibilidade pode conter réplicas que são somente locais ou somente no Azure, ou que abrangem locais e o Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões que usam várias redes virtuais. Os procedimentos neste artigo pressupõem que você já [configurou um grupo de disponibilidade](../classic/portal-sql-alwayson-availability-groups.md) , mas ainda não configurou um ouvinte.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Diretrizes e limitações para ouvintes internos
O uso de um ILB (balanceador de carga interno) com um ouvinte de grupo de disponibilidade no Azure está sujeito às seguintes diretrizes:

* O ouvinte do grupo de disponibilidade tem suporte no Windows Server 2008 R2, no Windows Server 2012 e no Windows Server 2012 R2.
* Somente um ouvinte de grupo de disponibilidade interno tem suporte para cada serviço de nuvem, pois o ouvinte está configurado para o ILB e há apenas um ILB para cada serviço de nuvem. No entanto, é possível criar vários ouvintes externos. Para obter mais informações, consulte [configurar um ouvinte externo para grupos de disponibilidade Always on no Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do ouvinte
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra na criação de um ouvinte que usa um ILB. Se você precisar de um ouvinte público ou externo, consulte a versão deste artigo que discute a configuração de um [ouvinte externo](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos de extremidade de VM com balanceamento de carga com retorno de servidor direto
Primeiro, crie um ILB executando o script mais adiante nesta seção.

Crie um ponto de extremidade com balanceamento de carga para cada VM que hospeda uma réplica do Azure. Se você tiver réplicas em várias regiões, cada réplica dessa região deverá estar no mesmo serviço de nuvem na mesma rede virtual do Azure. A criação de réplicas do grupo de disponibilidade que abrangem várias regiões do Azure requer a configuração de várias redes virtuais. Para obter mais informações sobre como configurar a conectividade de rede virtual cruzada, consulte [Configurar rede virtual para conectividade de rede virtual](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Na portal do Azure, acesse cada VM que hospeda uma réplica para exibir os detalhes.

2. Clique na guia **pontos de extremidade** para cada VM.

3. Verifique se o **nome** e a **porta pública** do ponto de extremidade do ouvinte que você deseja usar ainda não estão em uso. No exemplo nesta seção, o nome é *myEndpoint*e a porta é *1433*.

4. No seu cliente local, baixe e instale o módulo mais recente do [PowerShell](https://azure.microsoft.com/downloads/).

5. Iniciar Azure PowerShell.  
    Uma nova sessão do PowerShell é aberta, com os módulos administrativos do Azure carregados.

6. Execute `Get-AzurePublishSettingsFile`. Esse cmdlet direciona você para um navegador para baixar um arquivo de configurações de publicação em um diretório local. Suas credenciais de logon podem ser solicitadas para sua assinatura do Azure.

7. Execute o comando `Import-AzurePublishSettingsFile` a seguir com o caminho do arquivo de configurações de publicação que você baixou:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Depois que o arquivo de configurações de publicação for importado, você poderá gerenciar sua assinatura do Azure na sessão do PowerShell.

8. Para *ILB*, atribua um endereço IP estático. Examine a configuração de rede virtual atual executando o seguinte comando:

        (Get-AzureVNetConfig).XMLConfiguration
9. Observe o nome da *sub-rede* da sub-rede que contém as VMs que hospedam as réplicas. Esse nome é usado no parâmetro $SubnetName no script.

10. Observe o nome do *VirtualNetworkSite* e o *AddressPrefix* inicial da sub-rede que contém as VMs que hospedam as réplicas. Procure um endereço IP disponível passando os dois valores para o comando `Test-AzureStaticVNetIP` e examinando o *AvailableAddresses*. Por exemplo, se a rede virtual for nomeada *MyVNet* e tiver um intervalo de endereços de sub-rede que começa em *172.16.0.128*, o seguinte comando listará os endereços disponíveis:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Selecione um dos endereços disponíveis e use-o no parâmetro $ILBStaticIP do script na próxima etapa.

12. Copie o seguinte script do PowerShell para um editor de texto e defina os valores de variáveis para se adequar ao seu ambiente. Os padrões foram fornecidos para alguns parâmetros.  

    As implantações existentes que usam grupos de afinidades não podem adicionar um ILB. Para obter mais informações sobre os requisitos de ILB, consulte [visão geral do balanceador de carga interno](../../../load-balancer/load-balancer-internal-overview.md).

    Além disso, se o seu grupo de disponibilidade abranger regiões do Azure, você deverá executar o script uma vez em cada datacenter para o serviço de nuvem e os nós que residem nesse datacenter.

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

13. Depois de definir as variáveis, copie o script do editor de texto para a sessão do PowerShell para executá-lo. Se o prompt ainda mostrar **>>** , pressione Enter novamente para verificar se o script começa a ser executado.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verifique se o KB2854082 está instalado, se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abrir as portas do firewall em nós do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o ouvinte do grupo de disponibilidade

Crie o ouvinte do grupo de disponibilidade em duas etapas. Primeiro, crie o recurso de cluster do ponto de acesso para cliente e configure as dependências. Em segundo lugar, configure os recursos de cluster no PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso para cliente e configurar as dependências de cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurar os recursos de cluster no PowerShell
1. Para ILB, você deve usar o endereço IP do ILB que foi criado anteriormente. Para obter esse endereço IP no PowerShell, use o seguinte script:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Em uma das VMs, copie o script do PowerShell para o seu sistema operacional para um editor de texto e defina as variáveis para os valores que você anotou anteriormente.

    Para o Windows Server 2012 ou posterior, use o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Para o Windows Server 2008 R2, use o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Depois de definir as variáveis, abra uma janela do Windows PowerShell com privilégios elevados, Cole o script do editor de texto em sua sessão do PowerShell para executá-lo. Se o prompt ainda mostrar **>>** , pressione Enter novamente para ter certeza de que o script começa a ser executado.

4. Repita as etapas anteriores para cada VM.  
    Esse script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros, como a porta de investigação. Quando o recurso de endereço IP é colocado online, ele pode responder à sondagem na porta de investigação do ponto de extremidade com balanceamento de carga que você criou anteriormente.

## <a name="bring-the-listener-online"></a>Colocar o ouvinte online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de acompanhamento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testar o ouvinte do grupo de disponibilidade (dentro da mesma rede virtual)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
