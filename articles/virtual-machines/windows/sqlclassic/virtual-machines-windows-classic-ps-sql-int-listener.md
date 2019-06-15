---
title: Configurar um serviço de escuta ILB para grupos de disponibilidade Always On no Azure | Documentos da Microsoft
description: Este tutorial utiliza recursos criados com o modelo de implementação clássica e cria um sempre no serviço de escuta no Azure que utiliza um balanceador de carga interno.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0e6a52ea2fdd05546a4da9f8cd1165b41ed27944
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097739"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurar um serviço de escuta ILB para grupos de disponibilidade Always On no Azure
> [!div class="op_single_selector"]
> * [Serviço de escuta interno](../classic/ps-sql-int-listener.md)
> * [Serviço de escuta externo](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Descrição geral

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [O Azure Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implementação clássica. Recomendamos que as implementações mais novas utilizem o modelo de Gestor de recursos.

Para configurar um serviço de escuta para um grupo de disponibilidade Always On no modelo do Resource Manager, consulte [configurar um balanceador de carga para um grupo de disponibilidade Always On no Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

O grupo de disponibilidade pode conter réplicas que estão apenas no local ou no Azure apenas ou que abrangem no local e o Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões que utilizam várias redes virtuais. Os procedimentos neste artigo partem do princípio de que já [configurado um grupo de disponibilidade](../classic/portal-sql-alwayson-availability-groups.md) , mas ainda não tiver configurado um serviço de escuta.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Diretrizes e limitações para serviços de escuta internos
A utilização de um balanceador de carga interno (ILB) com um serviço de escuta do grupo de disponibilidade no Azure está sujeitas a as seguintes diretrizes:

* O serviço de escuta do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* Apenas um serviço de escuta de grupo de disponibilidade interno é suportado para cada serviço cloud, porque o serviço de escuta é configurado para o ILB e há apenas um ILB para cada serviço em nuvem. No entanto, é possível criar várias escutas externas. Para obter mais informações, consulte [configurar um serviço de escuta externo para grupos de disponibilidade Always On no Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do serviço de escuta
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra na criação de um serviço de escuta que utiliza um ILB. Se precisar de um serviço de escuta externo ou público, consulte a versão deste artigo que aborda a definição de segurança de um [serviço de escuta externo](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos finais da VM com balanceamento de carga com direta do servidor retorno
Cria um ILB pela primeira vez ao executar o script posteriormente nesta secção.

Crie um ponto final com balanceamento de carga para cada VM que aloja uma réplica do Azure. Se tiver de réplicas em várias regiões, cada réplica para essa região tem de estar no mesmo serviço cloud na mesma rede virtual do Azure. Criação de réplicas do grupo que abrangem várias regiões do Azure de disponibilidade exige a configuração de várias redes virtuais. Para obter mais informações sobre como configurar cruzada conectividade de rede virtual, consulte [configurar a rede virtual para conectividade de rede virtual](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal do Azure, vá para cada VM que aloja uma réplica para ver os detalhes.

2. Clique nas **pontos de extremidade** separador para cada VM.

3. Certifique-se de que o **Name** e **Porta pública** do serviço de escuta do ponto final que pretende utilizar não estão já em utilização. O exemplo nesta secção, o nome é *MyEndpoint*, e a porta é *1433*.

4. No seu cliente local, transfira e instale a versão mais recente [módulo do PowerShell](https://azure.microsoft.com/downloads/).

5. Inicie o PowerShell do Azure.  
    É aberta uma nova sessão do PowerShell, com os módulos do Azure de administrativos carregados.

6. Execute `Get-AzurePublishSettingsFile`. Este cmdlet direciona-o para um browser para transferir um ficheiro de definições de publicação para um diretório local. Pode ser pedido para as credenciais de início de sessão para a sua subscrição do Azure.

7. Execute o seguinte `Import-AzurePublishSettingsFile` comando com o caminho do ficheiro de definições de publicação transferido:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Depois do ficheiro de definições de publicação foi importado, pode gerir a sua subscrição do Azure na sessão do PowerShell.

8. Para *ILB*, atribuir um endereço IP estático. Examine a configuração de rede virtual atual, executando o seguinte comando:

        (Get-AzureVNetConfig).XMLConfiguration
9. Tenha em atenção a *sub-rede* dê um nome para a sub-rede que contém as VMs que alojam as réplicas. Este nome é utilizado no parâmetro $SubnetName no script.

10. Tenha em atenção a *VirtualNetworkSite* nome e a partir de *AddressPrefix* para a sub-rede que contém as VMs que alojam as réplicas. Procurar um endereço IP disponível pela transmissão de ambos os valores para o `Test-AzureStaticVNetIP` de comandos e ao examinar a *AvailableAddresses*. Por exemplo, se a rede virtual com o nome *MyVNet* e tem um intervalo de endereços de sub-rede que começa com *172.16.0.128*, o seguinte comando apresentará endereços disponíveis:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Selecione um dos endereços disponíveis e utilizá-lo no parâmetro $ILBStaticIP do script no próximo passo.

12. Copie o seguinte script do PowerShell para um editor de texto e defina os valores das variáveis de acordo com seu ambiente. Foram fornecidas as predefinições para alguns parâmetros.  

    As implementações existentes que utilizam grupos de afinidade não é possível adicionar um ILB. Para obter mais informações sobre os requisitos de ILB, veja [descrição geral do Balanceador de carga interno](../../../load-balancer/load-balancer-internal-overview.md).

    Além disso, se o seu grupo de disponibilidade se estende por regiões do Azure, tem de executar uma vez o script em cada datacenter para o serviço em nuvem e nós que residem em que datacenter.

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

13. Depois de ter de definir as variáveis, copie o script do editor de texto à sua sessão do PowerShell para executá-lo. Se continua a mostrar a linha de comandos **>>** , pressione Enter novamente para certificar-se de que o script começa a ser executado.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Certifique-se de que o KB2854082 está instalado se for necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abrir as portas de firewall em nós do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o serviço de escuta do grupo de disponibilidade

Crie o serviço de escuta do grupo de disponibilidade em duas etapas. Primeiro, crie o recurso de cluster de ponto de acesso de cliente e configurar as dependências. Em segundo lugar, configure os recursos do cluster no PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso de cliente e configurar as dependências de cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurar os recursos de cluster no PowerShell
1. Para o ILB, tem de utilizar o endereço IP do ILB criado anteriormente. Para obter este endereço IP no PowerShell, utilize o seguinte script:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Em uma das VMs, copie o script do PowerShell para o seu sistema operativo para um editor de texto e, em seguida, defina as variáveis para valores que apontou anteriormente.

    Para o Windows Server 2012 ou posterior, utilize o seguinte script:

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

3. Depois de ter de definir as variáveis, abra uma janela elevada do Windows PowerShell, cole o script a partir do editor de texto para a sua sessão do PowerShell para executá-lo. Se continua a mostrar a linha de comandos **>>** , prima Enter novamente para certificar-se de que o script começa a ser executado.

4. Repita os passos anteriores para cada VM.  
    Este script configura o recurso de endereço IP com o endereço IP do serviço em nuvem e define outros parâmetros, tais como a porta de sonda. Quando o recurso de endereço IP é colocado online, pode responder a consulta na porta de pesquisa do ponto de final com balanceamento de carga que criou anteriormente.

## <a name="bring-the-listener-online"></a>Colocar o serviço de escuta online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de seguimento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Testar o serviço de escuta do grupo de disponibilidade (dentro da mesma rede virtual)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
