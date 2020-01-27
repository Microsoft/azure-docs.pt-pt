---
title: Implantar o modelo de solução do consórcio de prova de autoridade Ethereum no Azure
description: Usar a solução do consórcio de prova de autoridade do Ethereum para implantar e configurar uma rede Ethereum do consórcio de vários membros no Azure
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75387680"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Implantar o modelo de solução do consórcio de prova de autoridade Ethereum no Azure

Você pode usar [o modelo de solução do Ethereum de verificação do consórcio de prova de autoridade do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) para implantar, configurar e governar uma rede Ethereum do consórcio de vários membros com conhecimento mínimo do Azure e do Ethereum.

O modelo de solução pode ser usado por cada membro do consórcio para provisionar uma superfície de rede blockchain usando os serviços de computação, rede e armazenamento do Microsoft Azure. A superfície de rede de cada membro do consórcio consiste em um conjunto de nós de validador com balanceamento de carga com os quais um aplicativo ou usuário pode interagir para enviar transações Ethereum.

## <a name="choose-an-azure-blockchain-solution"></a>Escolher uma solução de Blockchain do Azure

Antes de escolher usar o modelo de solução do consórcio de prova de autoridade Ethereum, Compare seu cenário com os casos de uso comuns das opções disponíveis do Azure Blockchain.

Opção | Modelo de serviço | Caso de uso comum
-------|---------------|-----------------
Modelos de soluções | IaaS | Os modelos de solução são Azure Resource Manager modelos que você pode usar para provisionar uma topologia de rede blockchain totalmente configurada. Os modelos implantam e configuram Microsoft Azure serviços de computação, rede e armazenamento para um determinado tipo de rede blockchain.
[Serviço Blockchain do Azure](../service/overview.md) | PaaS | O Azure Blockchain Service Preview simplifica a formação, o gerenciamento e a governança de redes do consórcio Blockchain. Use o serviço Blockchain do Azure para soluções que exigem PaaS, gerenciamento de consórcio ou privacidade de contrato e transação.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | A versão prévia do Azure Blockchain Workbench é uma coleção de serviços e recursos do Azure projetados para ajudá-lo a criar e implantar aplicativos Blockchain para compartilhar dados e processos de negócios com outras organizações. Use o Azure Blockchain Workbench para o protótipo de uma solução Blockchain ou uma prova de conceito de aplicativo Blockchain.

## <a name="solution-architecture"></a>Arquitetura de soluções

Usando o modelo de solução Ethereum, você pode implantar uma rede única ou multimembro com base em várias regiões do consórcio de Ethereum de prova de autoridade.

![arquitetura de implantação](./media/ethereum-poa-deployment/deployment-architecture.png)

Cada implantação de membro do consórcio inclui:

* Máquinas virtuais para executar os validadores do PoA
* Azure Load Balancer para distribuir solicitações de DApp de RPC, emparelhamento e governança
* Azure Key Vault para proteger as identidades do validador
* Armazenamento do Azure para hospedar informações de rede persistentes e coordenar a concessão
* Azure Monitor para agregar logs e estatísticas de desempenho
* Gateway de VNet (opcional) para permitir conexões VPN entre VNets particulares

Por padrão, os pontos de extremidade RPC e de emparelhamento são acessíveis por IP público para permitir conectividade simplificada entre assinaturas e nuvens. Para controles de acesso de nível de aplicativo, você pode usar [contratos de permissão de paridade](https://wiki.parity.io/Permissioning). As redes implantadas atrás de VPNs, que aproveitam gateways de VNet para conectividade entre assinaturas, têm suporte. Como as implantações de VPN e VNet são mais complexas, talvez você queira começar com um modelo de IP público ao fazer um protótipo de uma solução.

Os contêineres do Docker são usados para confiabilidade e modularidade. O registro de contêiner do Azure é usado para hospedar e fornecer imagens com versão como parte de cada implantação. As imagens de contêiner consistem em:

* Orchestrator – gera identidades e contratos de governança. Armazena identidades em um repositório de identidades.
* Cliente de paridade – identidades de concessão do armazenamento de identidades. Descobre e se conecta a pares.
* Agente EthStats-coleta logs e estatísticas locais via RPC e envia informações para Azure Monitor.
* Governança DApp-interface da Web para interagir com contratos de governança.

### <a name="validator-nodes"></a>Nós do validador

No protocolo de prova de autoridade, os nós do validador assumem o lugar dos nós Miner tradicionais. Cada validador tem uma identidade Ethereum exclusiva, permitindo que ele participe do processo de criação de bloco. Cada membro do consórcio pode provisionar dois ou mais nós de validador em cinco regiões, para redundância geográfica. Nós de validador se comunicam com outros nós de validador para que cheguem ao estado do razão distribuído subjacente. Para garantir a participação justa na rede, cada membro do consórcio é proibido de usar mais validadores do que o primeiro membro na rede. Por exemplo, se o primeiro membro implantar três validadores, cada membro poderá ter até três validadores.

### <a name="identity-store"></a>Repositório de identidade

Um repositório de identidades é implantado na assinatura de cada membro que mantém com segurança as identidades Ethereum geradas. Para cada validador, o contêiner de orquestração gera uma chave privada Ethereum e a armazena em Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Implantar rede do Ethereum Consortium

Nesta passagem, vamos supor que você esteja criando uma rede Ethereum Consortium de vários participantes. O seguinte fluxo é um exemplo de uma implantação de vários participantes:

1. Três membros cada um geram uma conta Ethereum usando a metamáscara
1. *O membro A* implanta Ethereum POA, fornecendo seu endereço público Ethereum
1. O *membro a* fornece a URL do consórcio para o *membro B* e o *membro C*
1. *Membro B* e implantação do *membro C* , Ethereum POA, fornecendo seu endereço público Ethereum e a URL do consórcio do *membro A*
1. *Membro A* votos no *membro B* como administrador
1. O *membro A* e o *membro B* votam o *membro C* como um administrador

As próximas seções mostram como configurar a superfície do primeiro membro na rede.

### <a name="create-resource"></a>Criar recurso

Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** no canto superior esquerdo.

Selecione **Blockchain** > **Ethereum do consórcio de prova de autoridade (versão prévia)** .

### <a name="basics"></a>Noções básicas

Em **noções básicas**, especifique valores para parâmetros padrão para qualquer implantação.

![Noções básicas](./media/ethereum-poa-deployment/basic-blade.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Criar uma nova rede ou ingressar na rede existente | Você pode criar uma nova rede Consortium ou ingressar em uma rede consórcio pré-existente. Ingressar em uma rede existente requer parâmetros adicionais. | Criar novo
Endereço de E-mail | Você receberá uma notificação por email quando sua implantação for concluída com informações sobre sua implantação. | Um endereço de email válido
Nome de usuário da VM | Nome de usuário de administrador de cada VM implantada | 1-64 caracteres alfanuméricos
Tipo de autenticação | O método para autenticar a máquina virtual. | Palavra-passe
Palavra-passe | A senha da conta de administrador para cada uma das máquinas virtuais implantadas. Todas as VMs têm inicialmente a mesma senha. Você pode alterar a senha após o provisionamento. | 12-72 caracteres 
Subscrição | A assinatura na qual implantar a rede Consortium |
Grupo de Recursos| O grupo de recursos no qual implantar a rede Consortium. | myResourceGroup
Localização | A região do Azure para o grupo de recursos. | E.U.A. Oeste 2

Selecione **OK**.

### <a name="deployment-regions"></a>Regiões de implantação

Em *regiões de implantação*, especifique o número de regiões e locais para cada um. Você pode implantar no máximo cinco regiões. A primeira região deve corresponder à localização do grupo de recursos da seção *básico* . Para redes de desenvolvimento ou teste, você pode usar uma única região por membro. Para produção, implante em duas ou mais regiões para alta disponibilidade.

![regiões de implantação](./media/ethereum-poa-deployment/deployment-regions.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de região (s)|Número de regiões para implantar a rede Consortium| 2
Primeira região | Primeira região para implantar a rede Consortium | E.U.A. Oeste 2
Segunda região | Segunda região para implantar a rede Consortium. Regiões adicionais ficam visíveis quando o número de regiões é dois ou mais. | E.U.A. Leste 2

Selecione **OK**.

### <a name="network-size-and-performance"></a>Desempenho e tamanho da rede

Em *tamanho da rede e desempenho*, especifique as entradas para o tamanho da rede do consórcio. O tamanho do armazenamento do nó do validador determina o tamanho potencial do blockchain. O tamanho pode ser alterado após a implantação.

![Desempenho e tamanho da rede](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de nós de validador com balanceamento de carga | O número de nós validadores para provisionar como parte da rede. | 2
Desempenho de armazenamento do nó do validador | O tipo de disco gerenciado para cada um dos nós de validador implantados. Para obter detalhes sobre preços, consulte [preços de armazenamento](https://azure.microsoft.com/pricing/details/managed-disks/) | SSD Standard
Tamanho da máquina virtual do nó do validador | O tamanho da máquina virtual usada para nós de validador. Para obter detalhes sobre preços, consulte [preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | D2 v3 Standard

A máquina virtual e a camada de armazenamento afetam o desempenho da rede.  Use a tabela a seguir para ajudar a escolher a eficiência de custo:

SKU da máquina virtual|Camada de armazenamento|Preço|Débito|Latência
---|---|---|---|---
F1|SSD Standard|pequena|pequena|alta
D2_v3|SSD Standard|médio|médio|médio
F16s|SSD Premium|alta|alta|pequena

Selecione **OK**.

### <a name="ethereum-settings"></a>Configurações de Ethereum

Em *configurações de Ethereum*, especifique definições de configuração relacionadas a Ethereum.

![Configurações de Ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
ID do membro do consórcio | A ID associada a cada membro que participa da rede do consórcio. Ele é usado para configurar espaços de endereço IP para evitar colisão. Para uma rede privada, a ID de membro deve ser exclusiva em diferentes organizações na mesma rede.  Uma ID de membro exclusiva é necessária mesmo quando a mesma organização é implantada em várias regiões. Anote o valor desse parâmetro, pois você precisa compartilhá-lo com outros membros de ingresso para garantir que não haja colisão. O intervalo válido é de 0 a 255. | 0
ID de rede | A ID de rede para a rede do consórcio Ethereum que está sendo implantada. Cada rede Ethereum tem sua própria ID de rede, com 1 sendo a ID da rede pública. O intervalo válido é de 5 a 999.999.999 | 10101010
Endereço Ethereum do administrador | O endereço da conta do Ethereum usado para participar da governança do PoA. Você pode usar a metamáscara para gerar um endereço Ethereum. |
Opções Avançadas | Opções avançadas para configurações de Ethereum | Ativar
Implantar usando o IP público | Se VNet privada estiver selecionada, a rede será implantada atrás de um gateway de VNet e removerá o acesso de emparelhamento. Para VNet privada, todos os membros devem usar um gateway de VNet para que a conexão seja compatível. | IP público
Limite de gás de bloqueio | O limite de gás de bloco inicial da rede. | 50000000
Bloquear o período de Reseal (s) | A frequência na qual os blocos vazios serão criados quando não houver nenhuma transação na rede. Uma frequência mais alta terá uma finalização mais rápida, mas os custos de armazenamento aumentados. | 15
Contrato de permissão de transação | Código de bytes para o contrato de permissão da transação. Restringe a implantação e a execução do contrato inteligente a uma lista permitida de contas do Ethereum. |

Selecione **OK**.

### <a name="monitoring"></a>Monitorização

O monitoramento permite que você configure um recurso de log para sua rede. O agente de monitoramento coleta e superfícies de logs e métricas úteis de sua rede, fornecendo a capacidade de verificar rapidamente a integridade da rede ou os problemas de depuração.

![Azure Monitor](./media/ethereum-poa-deployment/azure-monitor.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Monitorização | Opção para habilitar o monitoramento | Ativar
Conectar-se a logs de Azure Monitor existentes | Opção para criar uma nova instância de logs de Azure Monitor ou ingressar em uma instância existente | Criar novo
Localização | A região em que a nova instância é implantada | E.U.A. Leste
ID do espaço de trabalho do log Analytics existente (conectar a logs de Azure Monitor existentes = ingressar existente)|ID do espaço de trabalho da instância existente de logs de Azure Monitor||N/D
Chave primária do log Analytics existente (conectar a logs de Azure Monitor existentes = ingressar existente)|A chave primária usada para se conectar à instância existente de logs de Azure Monitor||N/D

Selecione **OK**.

### <a name="summary"></a>Resumo

Clique no resumo para revisar as entradas especificadas e executar a validação de pré-implantação básica. Antes de implantar o, você pode baixar o modelo e os parâmetros.

Selecione **criar** para implantar.

Se a implantação incluir gateways de VNet, a implantação poderá levar de 45 a 50 minutos.

## <a name="deployment-output"></a>Saída de implantação

Depois que a implantação for concluída, você poderá acessar os parâmetros necessários usando o portal do Azure.

### <a name="confirmation-email"></a>Email de confirmação

Se você fornecer um endereço de email ([seção noções básicas](#basics)), será enviado um email que inclui as informações de implantação e links para esta documentação.

![email de implantação](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Depois que a implantação for concluída com êxito e todos os recursos tiverem sido provisionados, você poderá exibir os parâmetros de saída em seu grupo de recursos.

1. Vá para o grupo de recursos no Portal.
1. Selecione **visão geral > implantações**.

    ![Visão geral do grupo de recursos](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Selecione a implantação **Microsoft-Azure-blockchain. Azure-blockchain-ether-...** .
1. Selecione a seção **saídas** .

    ![Saídas de implantação](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Aumentando o consórcio

Para expandir seu Consórcio, primeiro você deve conectar a rede física. Se estiver implantando por trás de uma VPN, consulte a seção [conectando o gateway de VNet](#connecting-vnet-gateways) configurar a conexão de rede como parte da implantação do novo membro. Quando a implantação for concluída, use o [DApp de governança](#governance-dapp) para se tornar um administrador de rede.

### <a name="new-member-deployment"></a>Nova implantação de membro

Compartilhe as informações a seguir com o membro de junção. As informações são encontradas em seu email de pós-implantação ou na saída de implantação do Portal.

* URL de dados do consórcio
* O número de nós implantados
* ID de recurso do gateway de VNet (se estiver usando VPN)

O membro de implantação deve usar o mesmo modelo de solução do consórcio de prova de autoridade do Ethereum ao implantar sua presença de rede usando as seguintes diretrizes:

* Selecionar **junção existente**
* Escolha o mesmo número de nós de validador que o restante dos membros na rede para garantir a representação justa
* Usar o mesmo endereço de Ethereum do administrador
* Usar a *URL de dados do consórcio* fornecida nas configurações do *Ethereum*
* Se o restante da rede estiver atrás de uma VPN, selecione **VNet privada** na seção avançado

### <a name="connecting-vnet-gateways"></a>Conectando gateways de VNet

Esta seção só será necessária se você tiver implantado usando uma VNet privada. Você pode ignorar esta seção se estiver usando endereços IP públicos.

Para uma rede privada, os diferentes membros são conectados por meio de conexões de gateway de VNet. Antes que um membro possa ingressar na rede e ver o tráfego da transação, um membro existente deve fazer uma configuração final em seu gateway de VPN para aceitar a conexão. Os nós Ethereum do membro de junção não serão executados até que uma conexão seja estabelecida. Para reduzir as chances de um único ponto de falha, crie conexões de rede redundantes no consórcio.

Depois que o novo membro for implantado, o membro existente deverá concluir a conexão bidirecional Configurando uma conexão de gateway de VNet para o novo membro. O membro existente precisa de:

* O ResourceId do gateway de VNet do membro de conexão. Consulte [saída da implantação](#deployment-output).
* A chave de conexão compartilhada.

O membro existente deve executar o seguinte script do PowerShell para concluir a conexão. Você pode usar Azure Cloud Shell localizadas na barra de navegação superior direita no Portal.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Monitorização do serviço

Você pode localizar seu portal de Azure Monitor seguindo o link no email de implantação ou localizando o parâmetro na saída de implantação [OMS_PORTAL_URL].

O portal primeiro exibirá as estatísticas de rede de alto nível e a visão geral do nó.

![Categorias de monitor](./media/ethereum-poa-deployment/monitor-categories.png)

A seleção de **visão geral do nó** mostra as estatísticas de infraestrutura por nó.

![Estatísticas de nó](./media/ethereum-poa-deployment/node-stats.png)

A seleção de **Estatísticas de rede** mostra as estatísticas de rede Ethereum.

![Estatísticas de rede](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Consultas Kusto de exemplo

Você pode consultar os logs de monitoramento para investigar falhas ou configurar alertas de limite. As consultas a seguir são exemplos que podem ser executados na ferramenta de *pesquisa de logs* :

Os blocos de lista que foram relatados por mais de uma consulta de validador podem ser úteis para ajudar a localizar bifurcações de cadeia.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Obter a contagem média de pares de um nó validador especificado com média de buckets de 5 minutos.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>Acesso SSH

Por motivos de segurança, o acesso à porta SSH é negado por uma regra de segurança de grupo de rede por padrão. Para acessar as instâncias de máquina virtual na rede PoA, você precisa alterar a seguinte segurança é a regra a *permitir*.

1. Vá para a seção **visão geral** do grupo de recursos implantado na portal do Azure.

    ![Visão geral do ssh](./media/ethereum-poa-deployment/ssh-overview.png)

1. Selecione o **grupo de segurança de rede** para a região da VM que você deseja acessar.

    ![NSG SSH](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Selecione a regra **Allow-SSH** .

    ![SSH-permitir](./media/ethereum-poa-deployment/ssh-allow.png)

1. Alterar **ação** para **permitir**

    ![permitir habilitação de SSH](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Selecione **Guardar**. As alterações podem levar alguns minutos para serem aplicadas.

Você pode se conectar remotamente às máquinas virtuais para os nós de validador via SSH com o nome de usuário do administrador fornecido e a chave de senha/SSH. O comando SSH para acessar o primeiro nó do validador é listado na saída de implantação do modelo. Por exemplo:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Para obter os nós de transação adicionais, aumente o número da porta em um.

Se você implantou em mais de uma região, altere o comando para o nome DNS ou endereço IP do balanceador de carga nessa região. Para localizar o nome DNS ou o endereço IP das outras regiões, localize o recurso com a Convenção de nomenclatura **\*\*\*\*\*-lbpip-reg\#** e exiba suas propriedades de nome DNS e endereço IP.

## <a name="azure-traffic-manager-load-balancing"></a>Balanceamento de carga do Gerenciador de tráfego do Azure

O Gerenciador de tráfego do Azure pode ajudar a reduzir o tempo de inatividade e melhorar a capacidade de resposta da rede PoA roteando o tráfego de entrada entre várias implantações em regiões diferentes. As verificações de integridade internas e o redirecionamento automático ajudam a garantir a alta disponibilidade dos pontos de extremidade RPC e do DApp de governança. Esse recurso será útil se você tiver implantado em várias regiões e estiver pronto para produção.

Use o Gerenciador de tráfego para melhorar a disponibilidade da rede PoA com failover automático. Você também pode usar o Gerenciador de tráfego para aumentar a capacidade de resposta de suas redes roteando os usuários finais para o local do Azure com menor latência de rede.

Se você decidir criar um perfil do Gerenciador de tráfego, poderá usar o nome DNS do perfil para acessar sua rede. Depois que outros membros do Consortium tiverem sido adicionados à rede, o Gerenciador de tráfego também poderá ser usado para balancear a carga entre seus validadores implantados.

### <a name="creating-a-traffic-manager-profile"></a>Criando um perfil do Gerenciador de tráfego

1. Na [portal do Azure](https://portal.azure.com), selecione **criar um recurso** no canto superior esquerdo.
1. Pesquise o **perfil do Gerenciador de tráfego**.

    ![Pesquisar o Gerenciador de tráfego do Azure](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Dê um nome exclusivo ao perfil e selecione o grupo de recursos que foi usado para a implantação do PoA.

1. Selecione **criar** para implantar.

    ![Criar Gerenciador de tráfego](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Depois de implantado, selecione a instância no grupo de recursos. O nome DNS para acessar o Gerenciador de tráfego pode ser encontrado na guia Visão geral.

    ![Localizar o DNS do Gerenciador de tráfego](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Escolha a guia **pontos de extremidade** e selecione o botão **Adicionar** .
1. Dê um nome exclusivo ao ponto de extremidade.
1. Para **tipo de recurso de destino**, escolha **endereço IP público**.
1. Escolha o endereço IP público do balanceador de carga da primeira região.

    ![Gerenciador de tráfego de roteamento](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Repita para cada região na rede implantada. Depois que os pontos de extremidade estiverem no status **habilitado** , eles serão carregados automaticamente e com a região balanceada no nome DNS do Gerenciador de tráfego. Agora você pode usar esse nome DNS no lugar do parâmetro [CONSORTIUM_DATA_URL] em outras etapas do artigo.

## <a name="data-api"></a>API de dados

Cada membro do Consortium hospeda as informações necessárias para que outras pessoas se conectem à rede. Para habilitar a facilidade de conectividade, cada membro hospeda um conjunto de informações de conexão no ponto de extremidade da API de dados.

O membro existente fornece o [CONSORTIUM_DATA_URL] antes da implantação do membro. Após a implantação, um membro de junção recuperará informações da interface JSON no seguinte ponto de extremidade:

`<CONSORTIUM_DATA_URL>/networkinfo`

A resposta contém informações úteis para ingressar Membros (bloco Genesis, ABI do contrato de conjunto de validadores, bootnodes) e informações úteis para o membro existente (endereços do validador). Você pode usar essa padronização para estender o Consórcio entre provedores de nuvem. Essa API retorna uma resposta formatada em JSON com a seguinte estrutura:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>DApp de governança

No coração da prova de autoridade está a governança descentralizada. Como a prova de autoridade depende de uma lista de autoridades de rede permitidas para manter a rede íntegra, é importante fornecer um mecanismo justo para fazer modificações nessa lista de permissões. Cada implantação vem com um conjunto de contratos inteligentes e o portal para governança na cadeia dessa lista de permissão. Depois que uma alteração proposta alcança um voto de maioria por membros do consórcio, a alteração é aplicada. A votação permite que novos participantes de consenso sejam adicionados ou comprometidos os participantes sejam removidos de uma maneira transparente que incentiva uma rede honesta.

O DApp de governança é um conjunto de [contratos inteligentes](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) previamente implantados e um aplicativo Web que é usado para controlar as autoridades na rede. As autoridades são divididas em identidades de administrador e nós de validador.
Os administradores têm a capacidade de delegar a participação em consenso a um conjunto de nós de validador. Os administradores também podem votar outros administradores dentro ou fora da rede.

![DApp de governança](./media/ethereum-poa-deployment/governance-dapp.png)

* **Governança descentralizada:** As alterações nas autoridades de rede são administradas por meio de votação na cadeia por meio de administradores selecionados.
* **Delegação de validador:** As autoridades podem gerenciar seus nós de validador que são configurados em cada implantação do PoA.
* **Histórico de alterações auditável:** Cada alteração é registrada no blockchain, fornecendo transparência e auditoria.

### <a name="getting-started-with-governance"></a>Introdução à governança

Para executar qualquer tipo de transação por meio do DApp de governança, você precisa usar uma carteira Ethereum. A abordagem mais simples é usar uma carteira no navegador, como [metamask](https://metamask.io); no entanto, como esses contratos inteligentes são implantados na rede, você também pode automatizar suas interações com o contrato de governança.

Depois de instalar a metamáscara, navegue até o DApp de governança no navegador.  Você pode localizar a URL por meio de portal do Azure na saída de implantação.  Se você não tiver uma carteira no navegador instalada, não poderá executar nenhuma ação; no entanto, você pode exibir o estado do administrador.  

### <a name="becoming-an-admin"></a>Tornando-se um administrador

Se você for o primeiro membro implantado na rede, você se tornará automaticamente um administrador e seus nós de paridade serão listados como validadores. Se você estiver ingressando na rede, precisará entrar como administrador por uma maioria (maior que 50%) do conjunto de administradores existente. Se você optar por não se tornar um administrador, seus nós ainda serão sincronizados e validará o blockchain; no entanto, eles não participam do processo de criação de blocos. Para iniciar o processo de votação para se tornar um administrador, selecione **indicado** e insira seu endereço Ethereum e alias.

![Nomear](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Candidatos

A seleção da guia **candidatos** mostra o conjunto atual de administradores candidatos.  Quando um candidato atinge um voto de maioria dos administradores atuais, o candidato é promovido a um administrador.  Para votar em um candidato, selecione a linha e selecione **votar em**. Se você mudar de ideia em um voto, selecione o candidato e selecione **rescindir voto**.

![Candidatos](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administradores

A guia **Administradores** mostra o conjunto atual de administradores e fornece a capacidade de votar.  Quando um administrador perde mais de 50% de suporte, eles são removidos como um administrador na rede. Todos os nós de validador que o administrador possui perdem o status do validador e se tornam nós de transação na rede. Um administrador pode ser removido por vários motivos; no entanto, cabe ao consórcio concordar sobre uma política com antecedência.

![Administradores](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validadores

A seleção da guia **validadores** exibe os nós de paridade atualmente implantados para a instância e seu status atual (tipo de nó). Cada membro do consórcio tem um conjunto diferente de validadores nesta lista, já que essa exibição representa o membro do consórcio implantado atual. Se a instância for implantada recentemente e você não tiver adicionado seus validadores, você obterá a opção de **Adicionar validadores**. A adição de validadores escolhe automaticamente um conjunto de nós de paridade com balanceamento de região e os atribui ao seu conjunto de validadores. Se você tiver implantado mais nós do que a capacidade permitida, os nós restantes se tornarão nós de transação na rede.

O endereço de cada validador é atribuído automaticamente por meio do [repositório de identidades](#identity-store) no Azure.  Se um nó falhar, ele cederá sua identidade, permitindo que outro nó em sua implantação assuma seu lugar. Esse processo garante que sua participação no consenso esteja altamente disponível.

![Validadores](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Nome do consórcio

Qualquer administrador pode atualizar o nome do consórcio.  Selecione o ícone de engrenagem na parte superior esquerda para atualizar o nome do consórcio.

### <a name="account-menu"></a>Menu conta

No canto superior direito, está o alias da sua conta do Ethereum e Identicon.  Se você for um administrador, terá a capacidade de atualizar seu alias.

![Conta](./media/ethereum-poa-deployment/governance-dapp-account.png)

## Desenvolvimento de Ethereum<a id="tutorials"></a>

Para compilar, implantar e testar contratos inteligentes, aqui estão algumas opções que você pode considerar para o desenvolvimento de Ethereum:
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) – ambiente de desenvolvimento Ethereum baseado em cliente
* [Ethereum remix](https://remix-ide.readthedocs.io/en/latest/index.html ) -ambiente de desenvolvimento Ethereum local e baseado em navegador

### <a name="compile-deploy-and-execute-smart-contract"></a>Compilar, implantar e executar o contrato inteligente

No exemplo a seguir, você cria um contrato inteligente simples. Você usa o Truffle para compilar e implantar o contrato inteligente em sua rede blockchain. Uma vez implantado, você chama uma função de contrato inteligente por meio de uma transação.

#### <a name="prerequisites"></a>Pré-requisitos

* Instale o [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). O Python é necessário para Truffle e Web3. Selecione a opção instalar para incluir o Python em seu caminho.
* Instale o Truffle v 5.0.5 `npm install -g truffle@v5.0.5`. O Truffle exige que várias ferramentas sejam instaladas, incluindo [node. js](https://nodejs.org), [git](https://git-scm.com/). Para obter mais informações, consulte a [documentação do Truffle](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Criar projeto Truffle

Para poder compilar e implantar um contrato inteligente, você precisa criar um projeto Truffle.

1. Abra uma linha de comandos ou shell.
1. Crie uma pasta com o nome `HelloWorld`.
1. Altere o diretório para a nova pasta `HelloWorld`.
1. Inicialize um novo projeto Truffle usando o comando `truffle init`.

    ![Criar um novo projeto Truffle](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Adicionar um contrato inteligente

Crie seus contratos inteligentes no subdiretório **Contracts** do seu projeto Truffle.

1. Crie um arquivo no `postBox.sol` nomeado no subdiretório **Contracts** de seu projeto Truffle.
1. Adicione o seguinte código de solidez a **postBox. sol**.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Implantar um contrato inteligente usando o Truffle

Os projetos Truffle contêm um arquivo de configuração para os detalhes de conexão de rede do blockchain. Modifique o arquivo de configuração para incluir as informações de conexão para sua rede.

> [!WARNING]
> Nunca envie sua chave privada Ethereum pela rede. Verifique se cada transação é assinada localmente e se a transação assinada é enviada pela rede.

1. Você precisa da frase mnemônico para a [conta de administrador Ethereum usada ao implantar sua rede blockchain](#ethereum-settings). Se você usou o metamask para criar a conta, poderá recuperar o mnemônico de metamask. Selecione o ícone de conta de administrador no canto superior direito da extensão de metamáscara e selecione **configurações > segurança & privacidade > revelar palavras de propagação**.
1. Substitua o conteúdo de `truffle-config.js` em seu projeto Truffle pelo conteúdo a seguir. Substitua os valores de ponto de extremidade de espaço reservado e mnemônico.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Como estamos usando o provedor de carteira de HD Truffle, instale o módulo em seu projeto usando o comando `npm install truffle-hdwallet-provider --save`.

O Truffle usa scripts de migração para implantar contratos inteligentes em uma rede blockchain. Você precisa de um script de migração para implantar seu novo contrato inteligente.

1. Adicione uma nova migração para implantar o novo contrato. Crie `2_deploy_contracts.js` de arquivo no subdiretório de **migrações** do projeto Truffle.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Implante na rede PoA usando o comando Truffle Migrate. No prompt de comando no diretório do projeto Truffle, execute:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Chamar uma função de contrato inteligente

Agora que o seu contrato inteligente está implantado, você pode enviar uma transação para chamar uma função.

1. No diretório do projeto Truffle, crie um novo arquivo chamado `sendtransaction.js`.
1. Adicione o conteúdo a seguir a **sendtransaction. js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Execute o script usando o comando Truffle execute.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Executar script para chamar a função por meio de transação](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Suporte a Webassembly (WASM)

O suporte ao Webassembly já está habilitado para você em redes PoA implantadas recentemente. Ele permite o desenvolvimento de um contrato inteligente em qualquer linguagem que seja transformada em assembly da Web (Rust, C++C,). Para obter mais informações, consulte: [visão geral de paridade do Webassembly](https://wiki.parity.io/WebAssembly-Home) e [tutorial da tecnologia de paridade](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>FAQ

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Observe que há muitas transações na rede que eu não enviei. De onde eles estão vindos?

Não é seguro desbloquear a [API pessoal](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Os bots escutam contas desbloqueadas do Ethereum e tentam drenar os fundos. O bot pressupõe que essas contas contenham real-ether e tente ser a primeira a desviarr o saldo. Não habilite a API pessoal na rede. Em vez disso, assine manualmente as transações usando uma carteira, como metamask ou programaticamente.

### <a name="how-to-ssh-onto-a-vm"></a>Como fazer SSH em uma VM?

A porta SSH não é exposta por motivos de segurança. Siga [este guia para habilitar a porta SSH](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Como fazer configurar um membro de auditoria ou nós de transação?

Os nós de transação são um conjunto de clientes de paridade emparelhados com a rede, mas que não estão participando de consenso. Esses nós ainda podem ser usados para enviar transações Ethereum e ler o estado do contrato inteligente. Esse mecanismo funciona para fornecer a auditoria para membros de não autoridade da rede. Para conseguir isso, siga as etapas em [aumentando o consórcio](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Por que as transações de metamáscara estão demorando muito tempo?

Para garantir que as transações sejam recebidas na ordem correta, cada transação Ethereum vem com um nonce de incremento. Se você usou uma conta em metamask em uma rede diferente, precisará redefinir o valor de nonce. Clique no ícone de configurações (três barras), configurações, redefinir conta. O histórico de transações será limpo e agora você poderá reenviar a transação.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>É necessário especificar a taxa de gás em metamask?

O Ether não atende a uma finalidade no consórcio de prova de autoridade. Portanto, não é necessário especificar a taxa de gás ao enviar transações na metamáscara.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>O que devo fazer se a minha implantação falhar devido à falha ao provisionar o OMS do Azure?

O monitoramento é um recurso opcional. Em alguns casos raros em que a implantação falha devido à incapacidade de provisionar com êxito Azure Monitor recurso, você pode reimplantar sem Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>As implantações de IP público são compatíveis com implantações de rede privada?

Não. O emparelhamento requer comunicação bidirecional para que toda a rede seja pública ou privada.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Qual é a taxa de transferência de transação esperada de prova de autoridade?

A taxa de transferência da transação será altamente dependente dos tipos de transações e da topologia de rede. Usando transações simples, fizemos um benchmark de uma média de 400 transações por segundo com uma rede implantada em várias regiões.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Como fazer assinar eventos de contrato inteligente?

A prova de autoridade do Ethereum agora dá suporte a soquetes da Web.  Verifique a saída da implantação para localizar a URL e a porta do soquete da Web.

## <a name="next-steps"></a>Passos seguintes

Para obter mais soluções de Blockchain do Azure, consulte a [documentação do Blockchain do Azure](https://docs.microsoft.com/azure/blockchain/).
