---
title: Configure um ouvinte externo para grupos de disponibilidade
description: Este tutorial percorre-o através de passos de criação de um Listener de Grupo Sempre Em Disponibilidade em Azure que é acessível externamente utilizando o endereço IP virtual público do serviço de nuvem associado.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: ca13d5e8369d007188a17352913519172ed8744e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978186"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Configure um ouvinte externo para grupos de disponibilidade em VMs de servidor Estanque
> [!div class="op_single_selector"]
> * [Ouvinte Interno](../classic/ps-sql-int-listener.md)
> * [Ouvinte Externo](../classic/ps-sql-ext-listener.md)
> 
> 

Este tópico mostra-lhe como configurar um ouvinte para um Grupo Sempre Disponível que é acessível externamente na internet. Isto é possível associando o endereço **ip virtual (VIP)** do serviço de nuvem com o ouvinte.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

O Seu Grupo de Disponibilidade pode conter réplicas que estão apenas no local, apenas azure, ou abrangetanto no local como azure para configurações híbridas. As réplicas azure podem residir na mesma região ou em várias regiões usando várias redes virtuais (VNets). Os passos abaixo assumem que já [configuraram um grupo de disponibilidade,](../classic/portal-sql-alwayson-availability-groups.md) mas não configuraram um ouvinte.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Diretrizes e limitações para ouvintes externos
Tenha em atenção as seguintes diretrizes sobre o ouvinte do grupo de disponibilidade em Azure quando estiver a implementar utilizando o endereço VIP público do serviço na nuvem:

* O ouvinte do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* A aplicação do cliente deve residir num serviço na nuvem diferente daquele que contém os seus VMs do grupo de disponibilidade. O Azure não suporta a devolução do servidor direto com o cliente e o servidor no mesmo serviço de cloud.
* Por padrão, os passos deste artigo mostram como configurar um ouvinte para usar o endereço IP virtual (VIP) do serviço na nuvem. No entanto, é possível reservar e criar vários endereços VIP para o seu serviço na nuvem. Isto permite-lhe usar os passos deste artigo para criar múltiplos ouvintes que estão associados a um VIP diferente. Para obter informações sobre como criar vários endereços VIP, consulte [Múltiplos VIPs por serviço na nuvem](../../../load-balancer/load-balancer-multivip.md).
* Se está a criar um ouvinte para um ambiente híbrido, a rede no local deve ter conectividade com a Internet pública, para além do site-to-site VPN com a rede virtual Azure. Quando na sub-rede Azure, o ouvinte do grupo de disponibilidade só é acessível pelo endereço IP público do respetivo serviço na nuvem.
* Não é suportado para criar um ouvinte externo no mesmo serviço de nuvem onde também tem um ouvinte interno usando o Balancer de Carga Interna (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do ouvinte
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo centra-se na criação de um ouvinte que utilize o **equilíbrio de carga externa.** Se quiser um ouvinte privado da sua rede virtual, consulte a versão deste artigo que fornece passos para a criação de um [ouvinte com o ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos finais VM equilibrados com retorno direto do servidor
O equilíbrio de carga externa utiliza o endereço VIRTUAL IP virtual virtual do serviço de nuvem que acolhe os seus VMs. Portanto, não precisa de criar ou configurar o equilibrador de carga neste caso.

Deve criar um ponto final equilibrado para cada VM que acolhe uma réplica Azure. Se tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço de nuvem no mesmo VNet. Criar réplicas do Grupo de Disponibilidade que abrangem várias regiões do Azure requer configurar vários VNets. Para obter mais informações sobre a configuração da conectividade VNet, consulte [Configure VNet para Conectividade VNet](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal Azure, navegue para cada VM hospedando uma réplica e veja os detalhes.
2. Clique no separador **Pontos Finais** para cada um dos VMs.
3. Verifique se o **Nome** e o **Porto Público** do ponto final do ouvinte que pretende utilizar ainda não estão a ser utilizados. No exemplo abaixo, o nome é "MyEndpoint" e o porto é "1433".
4. No seu cliente local, faça o download e instale [o mais recente módulo PowerShell.](https://azure.microsoft.com/downloads/)
5. Lançamento **Azure PowerShell**. É aberta uma nova sessão powerShell com os módulos administrativos Azure carregados.
6. Executar **Get-AzurePublishSettingsDefiniçõesFile**. Este cmdlet direciona-o para um navegador para descarregar um ficheiro de definições de publicação para um diretório local. Pode ser solicitado para as suas credenciais de login para a sua subscrição Azure.
7. Executar o comando **Import-AzurePublishSettingsFile** com o caminho do ficheiro de definições de publicação que descarregou:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Assim que o ficheiro de definições de publicação for importado, pode gerir a subscrição do Azure na sessão PowerShell.
    
1. Copie o script PowerShell abaixo num editor de texto e defina os valores variáveis de acordo com o seu ambiente (foram previstos predefinições para alguns parâmetros). Note que se o seu grupo de disponibilidade abrange regiões de Azure, deve executar o script uma vez em cada datacenter para o serviço de nuvem e nós que residem nesse datacenter.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Depois de definir as variáveis, copie o guião do editor de texto para a sua sessão Azure PowerShell para executá-lo. Se a solicitação ainda mostrar >>, digite o ENTER novamente para se certificar de que o script começa a funcionar.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verifique se kB2854082 está instalado se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas de firewall em nódeos do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o ouvinte do grupo de disponibilidade

Crie o ouvinte do grupo de disponibilidade em dois passos. Em primeiro lugar, crie o recurso de cluster de pontos de acesso ao cliente e configure dependências. Em segundo lugar, configure os recursos de cluster com powerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso ao cliente e configurar as dependências do cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configure os recursos de cluster no PowerShell
1. Para equilibrar a carga externa, deve obter o endereço IP virtual público do serviço de nuvem que contém as suas réplicas. Inicie sessão no portal do Azure. Navegue para o serviço de nuvem que contenha o seu grupo de disponibilidade VM. Abra a vista **do Dashboard.**
2. Note o endereço mostrado no **endereço IP Virtual Público (VIP)**. Se a sua solução abranger VNets, repita este passo para cada serviço na nuvem que contenha um VM que acolhe uma réplica.
3. Num dos VMs, copie o script PowerShell abaixo num editor de texto e deteteas as variáveis para os valores que observou anteriormente.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Depois de definir as variáveis, abra uma janela elevada do Windows PowerShell e, em seguida, copie o script do editor de texto e cole na sua sessão Azure PowerShell para executá-lo. Se a solicitação ainda mostrar >>, digite o ENTER novamente para se certificar de que o script começa a funcionar.
5. Repita isto em cada VM. Este script configura o recurso IP Address com o endereço IP do serviço de nuvem e define outros parâmetros como a porta da sonda. Quando o recurso IP Address é colocado on-line, pode então responder às sondagens na porta da sonda a partir do ponto final equilibrado criado anteriormente neste tutorial.

## <a name="bring-the-listener-online"></a>Traga o ouvinte on-line
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de acompanhamento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Teste o ouvinte do grupo de disponibilidade (dentro do mesmo VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Teste o ouvinte do grupo de disponibilidade (através da internet)
Para aceder ao ouvinte de fora da rede virtual, deve utilizar o equilíbrio de carga externa/pública (descrito neste tópico) em vez do ILB, que só é acessível dentro do mesmo VNet. Na cadeia de ligação, especifique o nome do serviço na nuvem. Por exemplo, se tivesse um serviço na nuvem com o nome *mycloudservice,* a declaração sqlcmd seria a seguinte:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Ao contrário do exemplo anterior, a autenticação SQL deve ser utilizada, uma vez que o chamador não pode utilizar a autenticação do windows através da internet. Para mais informações, consulte [Sempre Em Grupo de Disponibilidade em Azure VM: Cenários](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)de Conectividade do Cliente . Ao utilizar a autenticação SQL, certifique-se de que cria o mesmo login em ambas as réplicas. Para obter mais informações sobre logins de resolução de problemas com Grupos de Disponibilidade, consulte [como mapear logins ou utilizar utilizadores de bases de dados SQL contidos para se conectar a outras réplicas e mapear para](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)bases de dados de disponibilidade .

Se as réplicas Always On estiverem em subnets diferentes, os clientes devem especificar **MultisubnetFailover=True** na cadeia de ligação. Isto resulta em tentativas de ligação paralela seleções a réplicas nas diferentes subredes. Note que este cenário inclui uma implantação transversal do Grupo Always On Availability Group.

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

