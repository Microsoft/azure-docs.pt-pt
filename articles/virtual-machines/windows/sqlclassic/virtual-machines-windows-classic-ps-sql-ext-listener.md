---
title: Configurar um serviço de escuta externo para grupos de Disponibilidade AlwaysOn | Documentos da Microsoft
description: Este tutorial orienta-o através de passos para criar um sempre no serviço de escuta no Azure que é acessível externamente utilizando o endereço Virtual IP público do serviço na nuvem associadas.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 89623adbddce07cbc3c3ead811f5174d108c9b0e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444791"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurar um serviço de escuta externo para grupos de Disponibilidade AlwaysOn no Azure
> [!div class="op_single_selector"]
> * [Serviço de escuta interno](../classic/ps-sql-int-listener.md)
> * [Serviço de escuta externo](../classic/ps-sql-ext-listener.md)
> 
> 

Este tópico mostra-lhe como configurar um serviço de escuta para um grupo de Disponibilidade AlwaysOn que seja acessível externamente na internet. Isso se tornou possível por meio da associação do serviço cloud **IP Virtual público (VIP)** endereço com o serviço de escuta.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

O grupo de disponibilidade pode conter as réplicas que estão no local apenas, apenas, Azure ou abrangem no local e o Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões com várias redes virtuais (VNets). Os passos abaixo partem do princípio de que já [configurado um grupo de disponibilidade](../classic/portal-sql-alwayson-availability-groups.md) , mas não tiver configurado um serviço de escuta.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Diretrizes e limitações para as escutas externas
Quando estiver a implementar com o endereço de VIP público do cloud service, tenha em atenção as seguintes diretrizes sobre o serviço de escuta do grupo de disponibilidade no Azure:

* O serviço de escuta do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* A aplicação cliente tem de residir num serviço cloud diferente daquela que contém o grupo de disponibilidade de VMs. Azure não suporta a devolução direta do servidor com o cliente e servidor no mesmo serviço cloud.
* Por predefinição, os passos neste artigo mostram como configurar um serviço de escuta para utilizar o endereço de IP Virtual (VIP) do serviço de nuvem. No entanto, é possível reservar e criar vários endereços de VIP do serviço em nuvem. Isto permite-lhe utilizar os passos neste artigo para criar vários serviços de escuta que estejam associados um VIP diferente. Para obter informações sobre como criar vários endereços de VIP, consulte [múltiplos VIPs por serviço cloud](../../../load-balancer/load-balancer-multivip.md).
* Se estiver a criar um serviço de escuta para um ambiente híbrido, a rede no local tem de ter conectividade à Internet pública, além da VPN de site a site com a rede virtual do Azure. Quando a sub-rede do Azure, o serviço de escuta do grupo de disponibilidade é acessível apenas pelo endereço IP público do serviço cloud correspondentes.
* Não é suportada para criar um serviço de escuta externo no mesmo serviço cloud em que também tem um serviço de escuta interno com o Balanceador de carga interno (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do serviço de escuta
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra na criação de um serviço de escuta que utiliza **balanceamento de carga externo**. Se pretender que um serviço de escuta é privado à sua rede virtual, consulte a versão deste artigo, que fornece os passos para configurar um [serviço de escuta com o ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos finais da VM com balanceamento de carga com direta do servidor retorno
Balanceamento de carga externo utiliza virtual o endereço de Virtual IP público do serviço em nuvem que aloja as suas VMs. Portanto, não é necessário criar ou configurar o Balanceador de carga neste caso.

Tem de criar um ponto final com balanceamento de carga para cada VM que aloja uma réplica do Azure. Se tiver de réplicas em várias regiões, cada réplica para essa região tem de estar no mesmo serviço cloud na mesma VNet. Réplicas de criação de grupo de disponibilidade que abrangem várias regiões do Azure requer a configuração de várias VNets. Para obter mais informações sobre como configurar a conectividade de VNet cruzada, veja [configurar a conectividade VNet a VNet](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal do Azure, navegue para cada VM que aloja uma réplica e ver os detalhes.
2. Clique nas **pontos de extremidade** separador para cada uma das VMs.
3. Certifique-se de que o **Name** e **Porta pública** do serviço de escuta do ponto final que pretende utilizar não está já em utilização. No exemplo abaixo, o nome é "MyEndpoint" e a porta é "1433".
4. No seu cliente local, transfira e instale [o módulo do PowerShell mais recente](https://azure.microsoft.com/downloads/).
5. Inicie **do Azure PowerShell**. Uma nova sessão do PowerShell é aberta com os módulos do Azure de administrativos carregados.
6. Run **Get-AzurePublishSettingsFile**. Este cmdlet direciona-o para um browser para transferir um ficheiro de definições de publicação para um diretório local. Poderá ser solicitado a suas credenciais de início de sessão para a sua subscrição do Azure.
7. Executar o **importação AzurePublishSettingsFile** comando com o caminho do ficheiro de definições de publicação transferido:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Depois do ficheiro de definições de publicação foi importado, pode gerir a sua subscrição do Azure na sessão do PowerShell.
    
1. Copie o script do PowerShell abaixo para um editor de texto e defina os valores das variáveis de acordo com seu ambiente (predefinições foram fornecidas para alguns parâmetros). Tenha em atenção que se o seu grupo de disponibilidade se estende por regiões do Azure, tem de executar o script de uma vez em cada datacenter para o serviço em nuvem e nós que residem em que datacenter.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Depois de definir as variáveis, copie o script do editor de texto para a sua sessão do Azure PowerShell para executá-lo. Se a linha de comandos ainda mostra >>, escreva ENTER novamente para se certificar de que o script começa a ser executado.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Certifique-se de que o KB2854082 está instalado se for necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abrir as portas de firewall em nós do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o serviço de escuta do grupo de disponibilidade

Crie o serviço de escuta do grupo de disponibilidade em duas etapas. Primeiro, crie o recurso de cluster de ponto de acesso de cliente e configurar as dependências. Em segundo lugar, configure os recursos do cluster com o PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso de cliente e configurar as dependências de cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurar os recursos de cluster no PowerShell
1. Para o balanceamento de carga externo, tem de obter o endereço IP virtual público do serviço cloud que contenha as réplicas. Inicie sessão no portal do Azure. Navegue para o serviço cloud que contém o grupo de disponibilidade VM. Abra o **Dashboard** vista.
2. Tome nota do endereço apresentado em **endereço IP Virtual público (VIP)**. Se sua solução abrange VNets, repita este passo para cada serviço em nuvem que contém uma VM que aloja uma réplica.
3. Em uma das VMs, copie o script do PowerShell abaixo para um editor de texto e defina as variáveis para valores que apontou anteriormente.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Uma vez que definiu as variáveis, abra uma janela elevada do Windows PowerShell, em seguida, copie o script do editor de texto e cole na sua sessão do Azure PowerShell para executá-lo. Se a linha de comandos ainda mostra >>, escreva ENTER novamente para se certificar de que o script começa a ser executado.
5. Repetir isso em cada VM. Este script configura o recurso de endereço IP com o endereço IP do serviço em nuvem e define outros parâmetros, como a porta de sonda. Quando o recurso de endereço IP é colocado online, em seguida, pode responder a consulta na porta de pesquisa do ponto final com balanceamento de carga criado anteriormente no tutorial.

## <a name="bring-the-listener-online"></a>Colocar o serviço de escuta online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de seguimento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testar o serviço de escuta do grupo de disponibilidade (na mesma VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testar o serviço de escuta do grupo de disponibilidade (através da internet)
Para acessar o serviço de escuta de fora da rede virtual, tem de utilizar o balanceamento de carga de externo/público (descrito neste tópico) em vez de ILB, que só é acessível na mesma VNet. Na cadeia de ligação, especifique o nome do serviço cloud. Por exemplo, se tivesse um serviço cloud com o nome *mycloudservice*, a instrução de sqlcmd seria da seguinte forma:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Ao contrário do exemplo anterior, deve ser utilizada autenticação do SQL, uma vez que o chamador não é possível utilizar a autenticação do windows através da internet. Para obter mais informações, consulte [grupo de Disponibilidade AlwaysOn na VM do Azure: Cenários de conectividade do cliente](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Ao utilizar a autenticação de SQL, certifique-se de que criar o mesmo início de sessão em ambas as réplicas. Para obter mais informações sobre resolução de problemas de inícios de sessão com grupos de disponibilidade, consulte [como mapear os inícios de sessão ou utilizar contidos utilizador de base de dados SQL para ligar a outras réplicas e mapear para bases de dados de disponibilidade](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se as réplicas Always On estiverem em sub-redes diferentes, os clientes têm de especificar **MultisubnetFailover = True** na cadeia de ligação. Isso resulta nas tentativas de ligação paralela réplicas em diferentes sub-redes. Tenha em atenção que este cenário inclui uma implementação de grupo de Disponibilidade AlwaysOn em várias regiões.

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

