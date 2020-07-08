---
title: Configure um ouvinte externo para grupos de disponibilidade
description: Este tutorial percorre os passos da criação de um Ouvinte do Grupo Always On Availability em Azure que seja acessível externamente através do endereço IP virtual público do serviço de nuvem associado.
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
ms.openlocfilehash: 4517a600acaf581ad240d634e89bba3984f835db
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087338"
---
# <a name="configure-an-external-listener-for-availability-groups-on-azure-sql-server-vms"></a>Configure um ouvinte externo para grupos de disponibilidade em VMs do servidor Azure SQL
> [!div class="op_single_selector"]
> * [Ouvinte Interno](../classic/ps-sql-int-listener.md)
> * [Ouvinte Externa](../classic/ps-sql-ext-listener.md)
> 
> 

Este tópico mostra-lhe como configurar um ouvinte para um Grupo De Disponibilidade Sempre Acessível que seja acessível externamente na internet. Isto é possível associando o endereço **virtual IP (VIP) do** serviço de nuvem ao ouvinte.

> [!IMPORTANT] 
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../../../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

O seu Grupo de Disponibilidade pode conter réplicas que são apenas no local, apenas Azure, ou abrangem tanto no local como a Azure para configurações híbridas. As réplicas azure podem residir na mesma região ou em várias regiões usando múltiplas redes virtuais (VNets). Os passos abaixo assumem que já [configuraram um grupo de disponibilidade,](../classic/portal-sql-alwayson-availability-groups.md) mas não configuraram um ouvinte.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Diretrizes e limitações para ouvintes externos
Note as seguintes diretrizes sobre o ouvinte do grupo de disponibilidade em Azure quando estiver a implementar usando o endereço VIP público do serviço de nuvem:

* O ouvinte do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* A aplicação do cliente deve residir num serviço de nuvem diferente daquele que contém os VM do seu grupo de disponibilidade. O Azure não suporta a devolução direta do servidor com o cliente e o servidor no mesmo serviço de cloud.
* Por predefinição, os passos deste artigo mostram como configurar um ouvinte para utilizar o endereço Virtual IP (VIP) do serviço de nuvem. No entanto, é possível reservar e criar vários endereços VIP para o seu serviço na nuvem. Isto permite-lhe usar os passos deste artigo para criar vários ouvintes que estão cada um associado a um VIP diferente. Para obter informações sobre como criar vários endereços VIP, consulte [Vários VIPs por serviço na nuvem](../../../load-balancer/load-balancer-multivip.md).
* Se estiver a criar um ouvinte para um ambiente híbrido, a rede no local deve ter conectividade com a Internet pública, para além da VPN site-to-site com a rede virtual Azure. Quando na sub-rede Azure, o ouvinte do grupo de disponibilidade só é acessível pelo endereço IP público do respetivo serviço em nuvem.
* Não é suportado para criar um ouvinte externo no mesmo serviço de nuvem onde também tem um ouvinte interno usando o Balançador de Carga Interna (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade do ouvinte
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo centra-se na criação de um ouvinte que utilize **o equilíbrio de carga externa.** Se quiser um ouvinte privado da sua rede virtual, consulte a versão deste artigo que fornece passos para a configuração de um [ouvinte com ILB](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos finais VM equilibrados em carga com retorno direto do servidor
O equilíbrio de carga externa utiliza o endereço VIRTUAL IP virtual virtual do serviço de nuvem que hospeda os seus VMs. Por isso, não é necessário criar ou configurar o equilibrador de carga neste caso.

Deve criar um ponto final equilibrado para cada VM que hospeda uma réplica Azure. Se tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço de nuvem no mesmo VNet. Criar réplicas do Grupo Availability que abrangem várias regiões do Azure requer a configuração de vários VNets. Para obter mais informações sobre a configuração da conectividade cross VNet, consulte [Configure VNet para VNet Conectividade](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal Azure, navegue para cada VM hospedando uma réplica e veja os detalhes.
2. Clique no **separador Pontos finais** para cada um dos VMs.
3. Verifique se o **Nome** e **a Porta Pública** do ponto final do ouvinte que pretende utilizar ainda não está a ser utilizado. No exemplo abaixo, o nome é "MyEndpoint" e o porto é "1433".
4. No seu cliente local, descarregue e instale [o mais recente módulo PowerShell](https://azure.microsoft.com/downloads/).
5. Lançamento **Azure PowerShell**. Abre-se uma nova sessão PowerShell com os módulos administrativos Azure carregados.
6. Executar **Get-AzurePublishSettingsFile**. Este cmdlet direciona-o para um navegador para descarregar um ficheiro de configurações de publicação para um diretório local. Pode ser solicitado para as suas credenciais de login para a sua assinatura Azure.
7. Executar o comando **Import-AzurePublishSettingsFile** com o caminho do ficheiro de definições de publicação que descarregou:

    ```powershell
    Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
    ```

    Uma vez importado o ficheiro de definições de publicação, pode gerir a sua subscrição Azure na sessão PowerShell.
    
1. Copie o script PowerShell abaixo num editor de texto e desaprova os valores variáveis de acordo com o seu ambiente (foram fornecidos padrão para alguns parâmetros). Note que se o seu grupo de disponibilidade abrange regiões Azure, deve executar o script uma vez em cada datacenter para o serviço de nuvem e nós que residem nesse datacenter.

    ```powershell
    # Define variables
    $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
    $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
    # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
    ForEach ($node in $AGNodes)
    {
        Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
    }
    ```

2. Depois de definir as variáveis, copie o script do editor de texto para a sua sessão Azure PowerShell para executá-lo. Se a solicitação ainda mostrar >>, escreva ENTER novamente para se certificar de que o script começa a funcionar.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verifique se o KB2854082 está instalado se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas de firewall em nosmos de grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar o ouvinte do grupo de disponibilidade

Crie o ouvinte do grupo de disponibilidade em dois passos. Em primeiro lugar, crie o recurso de cluster de ponto de acesso ao cliente econfigure dependências. Segundo, configurar os recursos do cluster com o PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Crie o ponto de acesso ao cliente e configuure as dependências do cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configure os recursos de cluster na PowerShell
1. Para equilibrar cargas externas, deve obter o endereço IP virtual público do serviço de nuvem que contém as suas réplicas. Inicie sessão no portal do Azure. Navegue para o serviço de nuvem que contém o seu grupo de disponibilidade VM. Abra a vista **do painel de instrumentos.**
2. Note o endereço indicado no **Endereço IP Virtual Público (VIP).** Se a sua solução abranger VNets, repita este passo para cada serviço de nuvem que contenha um VM que acolhe uma réplica.
3. Num dos VMs, copie o script PowerShell abaixo num editor de texto e defina as variáveis para os valores que observou anteriormente.

    ```powershell
    # Define variables
    $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
    Import-Module FailoverClusters
   
    # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
    # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
    # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
    ```

4. Depois de definir as variáveis, abra uma janela elevada do Windows PowerShell e, em seguida, copie o script do editor de texto e cole-o na sessão Azure PowerShell para executá-lo. Se a solicitação ainda mostrar >>, escreva ENTER novamente para se certificar de que o script começa a funcionar.
5. Repita isto em cada VM. Este script configura o recurso IP Address com o endereço IP do serviço de nuvem e define outros parâmetros como a porta da sonda. Quando o recurso IP Address é trazido on-line, pode então responder à votação na porta da sonda a partir do ponto final equilibrado de carga criado anteriormente neste tutorial.

## <a name="bring-the-listener-online"></a>Traga o ouvinte on-line
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Artigos de acompanhamento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Teste o ouvinte do grupo de disponibilidade (dentro do mesmo VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Teste o ouvinte do grupo de disponibilidade (através da internet)
Para aceder ao ouvinte de fora da rede virtual, deve utilizar o equilíbrio de carga externa/pública (descrito neste tópico) em vez do ILB, que só é acessível dentro do mesmo VNet. Na cadeia de ligação, especifique o nome do serviço de assistência na nuvem. Por exemplo, se tivesse um serviço de nuvem com o nome *mycloudservice,* a declaração sqlcmd seria a seguinte:

```console
sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15
```

Ao contrário do exemplo anterior, a autenticação SQL deve ser utilizada, uma vez que o autor da chamada não pode utilizar a autenticação do Windows através da internet. Para mais informações, consulte [Sempre On Availability Group em Azure VM: Cenários de Conectividade do Cliente](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Ao utilizar a autenticação SQL, certifique-se de que cria o mesmo login em ambas as réplicas. Para obter mais informações sobre a resolução de logins com grupos de disponibilidade, consulte [como mapear logins ou utilizar o utilizador de base de dados SQL contido para ligar a outras réplicas e mapear bases de dados de disponibilidade.](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)

Se as réplicas Always On estiverem em sub-redes diferentes, os clientes devem especificar **MultisubnetFailover=True** na cadeia de ligação. Isto resulta em tentativas de conexão paralelas a réplicas nas diferentes sub-redes. Note que este cenário inclui uma implantação do Grupo Always On Availability.

## <a name="next-steps"></a>Próximos passos
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

