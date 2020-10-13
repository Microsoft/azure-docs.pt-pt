---
title: Implementar o modelo de solução de consórcio Ethereum Proof-of-Authority no Azure
description: Utilize a solução do consórcio Ethereum Proof-of-Authority para implantar e configurar uma rede de consórcio multi-membroS Ethereum na Azure
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: devx-track-js
ms.openlocfilehash: d1d3ad94957e791b2178b6c60d4c7debdec2b391
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91283433"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Implementar o modelo de solução de consórcio de prova de autoridade do Ethereum no Azure

Você pode usar [o modelo de solução de pré-visualização do Consórcio Ethereum Proof-of-Authority](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) para implementar, configurar e governar uma rede de prova de autoridade de consórcio multi-membro com o mínimo conhecimento Azure e Ethereum.

O modelo de solução pode ser usado por cada membro do consórcio para forragem de uma pegada de rede blockchain usando os serviços de computação, rede e armazenamento do Microsoft Azure. A pegada de rede de cada membro do consórcio consiste num conjunto de nós validadores equilibrados em carga com os quais uma aplicação ou utilizador pode interagir para submeter transações do Ethereum.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Escolha uma solução Azure Blockchain

Antes de optar por utilizar o modelo de solução de consórcio de prova de autoridade do Ethereum, compare o seu cenário com os casos de uso comum das opções disponíveis do Azure Blockchain.

> [!IMPORTANT]
> Considere usar [o Serviço Azure Blockchain](../service/overview.md) em vez do Ethereum no modelo de solução Azure. O Azure Blockchain Service é um Serviço Azure gerido com suporte. Paridade Ethereum transitou para o desenvolvimento e manutenção orientados pela comunidade. Para obter mais informações, consulte [a Transição Parity Ethereum para OpenEthereum DAO](https://www.parity.io/parity-ethereum-openethereum-dao/).

Opção | Modelo de serviço | Caso de uso comum
-------|---------------|-----------------
Modelos de solução | IaaS | Os modelos de solução são modelos de Gestor de Recursos Azure que pode utilizar para providenciar uma topologia de rede blockchain totalmente configurada. Os modelos implementam e configuram os serviços de computação, networking e armazenamento do Microsoft Azure para um determinado tipo de rede blockchain. Os modelos de solução são fornecidos sem um acordo de nível de serviço. Utilize a [página de perguntas&Microsoft Q](/answers/topics/azure-blockchain-workbench.html) para suporte.
[Azure Blockchain Service](../service/overview.md) | PaaS | A antevisão do serviço Azure Blockchain simplifica a formação, gestão e governação das redes blockchain do consórcio. Utilize o Serviço Azure Blockchain para soluções que exijam paaS, gestão de consórcios ou privacidade de contratos e transações.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | A Azure Blockchain Workbench Preview é uma coleção de serviços e capacidades da Azure projetadas para ajudá-lo a criar e implementar aplicações blockchain para partilhar processos de negócio e dados com outras organizações. Utilize a bancada Azure Blockchain workbench para prototipar uma solução blockchain ou uma aplicação blockchain comprovativo de conceito. O Azure Blockchain Workbench é fornecido sem contrato de nível de serviço. Utilize a [página de perguntas&Microsoft Q](/answers/topics/azure-blockchain-workbench.html) para suporte.

## <a name="solution-architecture"></a>Arquitetura de soluções

Utilizando o modelo de solução Ethereum, pode implementar uma rede de consórcio de prova de autoridade de ethereum com base em vários membros ou multi-regiões.

![arquitetura de implantação](./media/ethereum-poa-deployment/deployment-architecture.png)

Cada destacamento de membro do consórcio inclui:

* Máquinas Virtuais para executar os validadores PoA
* Azure Load Balancer para distribuir pedidos de RPC, peering e governação DApp
* Cofre de Chaves Azure para garantir as identidades validadoras
* Armazenamento Azure para hospedar informações persistentes de rede e coordenar o leasing
* Monitor Azure para agregação de registos e estatísticas de desempenho
* VNet Gateway (opcional) para permitir ligações VPN em VNets privados

Por padrão, os pontos finais de RPC e peering são acessíveis através do IP público para permitir a conectividade simplificada entre subscrições e nuvens. Para controlos de acesso ao nível de aplicação, pode utilizar [os contratos de permissão da Parity.](https://wiki.parity.io/Permissioning) As redes implementadas atrás de VPNs, que alavancam os gateways VNet para a conectividade de subscrição cruzada são suportadas. Uma vez que as implementações de VPN e VNet são mais complexas, é melhor começar com um modelo IP público ao prototipar uma solução.

Os recipientes estivadores são utilizados para a fiabilidade e modularidade. O Registo de Contentores Azure é utilizado para hospedar e servir imagens versadas como parte de cada implantação. As imagens do contentor consistem em:

* Orquestrador - Gera identidades e contratos de governação. Armazena identidades numa loja de identidades.
* Cliente de paridade - Arrendamento identidade da loja de identidade. Descobre e liga-se aos pares.
* EthStats Agent - Recolhe registos e estatísticas locais via RPC e empurra a informação para o Azure Monitor.
* Governance DApp - Interface web para interagir com contratos de Governação.

### <a name="validator-nodes"></a>Nódoiadores validadores

No protocolo de prova de autoridade, os nós validadores tomam o lugar dos nós mineiros tradicionais. Cada validador tem uma identidade Ethereum única que lhe permite participar no processo de criação de blocos. Cada membro do consórcio pode prever dois ou mais nós validadores em cinco regiões, para geo-redundância. Os nós validadores comunicam com outros nós validadores para chegar a um consenso sobre o estado do livro de contabilidade distribuído subjacente. Para garantir uma participação justa na rede, cada membro do consórcio está proibido de utilizar mais validadores do que o primeiro membro da rede. Por exemplo, se o primeiro membro implementar três validadores, cada membro só pode ter até três validadores.

### <a name="identity-store"></a>Loja de identidades

Uma loja de identidade é implantada na subscrição de cada membro que detém de forma segura as identidades geradas pelo Ethereum. Para cada validador, o recipiente de orquestração gera uma chave privada Ethereum e armazena-a no Cofre da Chave Azure.

## <a name="deploy-ethereum-consortium-network"></a>Implementar rede de consórcios Ethereum

Nesta caminhada, vamos supor que está a criar uma rede multi-partidária de consórcios Ethereum. O seguinte fluxo é um exemplo de uma implantação multi-partidária:

1. Três membros cada geram uma conta Ethereum usando MetaMask
1. *Membro A* implanta Ethereum PoA, fornecendo o seu endereço público Ethereum
1. *O membro A* fornece o URL do consórcio aos *membros B* e *C*
1. *Membro B* e *Membro C* implementam, Ethereum PoA, fornecendo o seu Endereço Público Ethereum e URL do consórcio do *membro A*
1. *Membro A* vota no *Membro B* como administrador
1. *Membro A* e *Membro B* votam *membro C* como administrador

As próximas secções mostram-lhe como configurar a pegada do primeiro membro na rede.

### <a name="create-resource"></a>Criar um recurso

No [portal Azure,](https://portal.azure.com) **selecione Criar um recurso** no canto superior esquerdo.

Selecione **Blockchain**  >  **Ethereum Proof-of-Authority Consortium (pré-visualização)**.

### <a name="basics"></a>Noções básicas

De acordo com **os Básicos,** especifique os valores para os parâmetros padrão para qualquer implantação.

![Noções básicas](./media/ethereum-poa-deployment/basic-blade.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Criar uma nova rede ou aderir à rede existente | Pode criar uma nova rede de consórcios ou aderir a uma rede de consórcios pré-existente. A adesão a uma rede existente requer parâmetros adicionais. | Criar novo
Endereço de E-mail | Recebe uma notificação por e-mail quando a sua implementação termina com informações sobre a sua implementação. | Um endereço de e-mail válido
Nome de utilizador VM | Nome de utilizador do administrador de cada VM implantado | 1-64 caracteres alfanuméricos
Tipo de autenticação | O método para autenticar a máquina virtual. | Palavra-passe
Palavra-passe | A palavra-passe para a conta do administrador para cada uma das máquinas virtuais implantadas. Todos os VMs têm inicialmente a mesma senha. Pode alterar a palavra-passe após o provisionamento. | 12-72 caracteres 
Subscrição | A subscrição para implantar a rede do consórcio |
Grupo de Recursos| O grupo de recursos para implantar a rede de consórcios. | myResourceGroup
Localização | A região de Azure para o grupo de recursos. | E.U.A. Oeste 2

Selecione **OK**.

### <a name="deployment-regions"></a>Regiões de implantação

Nas *regiões de implantação,* especifique o número de regiões e locais para cada um. Pode implantar-se no máximo de cinco regiões. A primeira região deve coincidir com a localização do grupo de recursos da secção *Basics.* Para redes de desenvolvimento ou teste, pode utilizar uma única região por membro. Para a produção, implantar em duas ou mais regiões para uma elevada disponibilidade.

![regiões de implantação](./media/ethereum-poa-deployment/deployment-regions.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de regiões ou de regiões|Número de regiões para implantar a rede de consórcios| 2
Primeira região | Primeira região a implantar a rede de consórcios | E.U.A. Oeste 2
Segunda região | Segunda região a implantar a rede de consórcios. Regiões adicionais são visíveis quando o número de regiões é duas ou maiores. | E.U.A. Leste 2

Selecione **OK**.

### <a name="network-size-and-performance"></a>Tamanho e desempenho da rede

No *tamanho e desempenho da rede,* especifique as entradas para o tamanho da rede do consórcio. O tamanho do nó validador dita o tamanho potencial da blockchain. O tamanho pode ser alterado após a colocação.

![Tamanho e desempenho da rede](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Número de nós validadores equilibrados de carga | O número de nós validadores à provisão como parte da rede. | 2
Desempenho do armazenamento do nó de validador | O tipo de disco gerido para cada um dos nós validadores implantados. Para obter detalhes sobre os preços, consulte [os preços de armazenamento](https://azure.microsoft.com/pricing/details/managed-disks/) | SSD Standard
Tamanho da máquina virtual do nó validador | O tamanho da máquina virtual utilizado para validar nós. Para mais detalhes sobre os preços, consulte [os preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | D2 padrão v3

A máquina virtual e o nível de armazenamento afetam o desempenho da rede.  Utilize a seguinte tabela para ajudar a escolher a eficiência de custos:

Máquina virtual SKU|Nível de armazenamento|Preço|Débito|Latência
---|---|---|---|---
F1|SSD Standard|baixo|baixo|alta
D2_v3|SSD Standard|médio|médio|médio
F16s|SSD Premium|alta|alta|baixo

Selecione **OK**.

### <a name="ethereum-settings"></a>Definições de Ethereum

Nas *Definições do Ethereum*, especifique as definições de configuração relacionadas com o Ethereum.

![Definições de Ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
ID membro do consórcio | O ID associado a cada membro que participa na rede do consórcio. É usado para configurar espaços de endereço IP para evitar colisão. Para uma rede privada, o ID dos membros deve ser único entre diferentes organizações na mesma rede.  Um ID de membro único é necessário mesmo quando a mesma organização se desdobra em várias regiões. Tome nota do valor deste parâmetro, uma vez que precisa partilhá-lo com outros membros que se juntam para garantir que não há colisão. O intervalo válido é de 0 a 255. | 0
ID de rede | A rede ID para a rede Ethereum do consórcio está a ser implantada. Cada rede Ethereum tem o seu próprio ID de rede, sendo 1 o ID para a rede pública. A gama válida é de 5 a 999.999.999 | 10101010
Endereço Admin Ethereum | O endereço da conta Ethereum usado para participar na governação do PoA. Pode utilizar o MetaMask para gerar um endereço Ethereum. |
Opções Avançadas | Opções avançadas para configurações do Ethereum | Ativar
Implementar usando IP público | Se o VNet Privado for selecionado, a rede é implantada atrás de um Gateway VNet e remove o acesso de peering. Para o VNet Privado, todos os membros devem utilizar um Gateway VNet para que a ligação seja compatível. | IP público
Limite de gás de bloqueio | O limite de gás de bloco de partida da rede. | 50000000
Período de Reseal do Bloco (seg) | A frequência em que serão criados blocos vazios quando não houver transações na rede. Uma frequência mais alta terá uma finalidade mais rápida, mas aumentou os custos de armazenamento. | 15
Contrato de Autorização de Transação | Bytecode para o contrato de Permissão de Transações. Restringe a implementação e execução de contratos inteligentes a uma lista permitida de contas Ethereum. |

Selecione **OK**.

### <a name="monitoring"></a>Monitorização

A monitorização permite-lhe configurar um recurso de registo para a sua rede. O agente de monitorização recolhe e superfícies métricas e troncos úteis da sua rede, proporcionando a capacidade de verificar rapidamente os problemas de saúde da rede ou depurar.

![Monitor azul](./media/ethereum-poa-deployment/azure-monitor.png)

Parâmetro | Descrição | Valor de exemplo
----------|-------------|--------------
Monitorização | Opção para permitir a monitorização | Ativar
Ligar aos registos do Monitor Azure existentes | Opção de criar uma nova instância de registos do Azure Monitor ou aderir a uma instância existente | Criar novo
Localização | A região onde a nova instância é implantada | E.U.A. Leste
ID do espaço de trabalho de analítica de registo existente (Ligar aos registos existentes do Monitor Azure = Unir-se existente)|ID do espaço de trabalho da instância de registos do Azure Monitor existente||ND
Chave primária de analítica de registo existente (Ligar aos registos do Monitor Azure existentes = Unir-se existente)|A chave primária usada para ligar à instância de registos do Monitor Azure existente||ND

Selecione **OK**.

### <a name="summary"></a>Resumo

Clique no resumo para rever as entradas especificadas e executar a validação básica de pré-implantação. Antes de implementar, pode descarregar o modelo e os parâmetros.

Selecione **Criar** para implementar.

Se a implementação incluir gateways VNet, a implementação pode demorar entre 45 a 50 minutos.

## <a name="deployment-output"></a>Saída de implantação

Uma vez concluída a implementação, pode aceder aos parâmetros necessários utilizando o portal Azure.

### <a name="confirmation-email"></a>E-mail de confirmação

Se fornecer um endereço de e-mail[(Secção Básica),](#basics)é enviado um e-mail que inclui as informações de implementação e ligações a esta documentação.

![e-mail de implementação](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Uma vez concluída a implementação com sucesso e todos os recursos foram aprovisionados, pode ver os parâmetros de saída no seu grupo de recursos.

1. Vá ao seu grupo de recursos no portal.
1. Selecione **visão geral > implementações**.

    ![Visão geral do grupo de recursos](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Selecione a implementação **microsoft-azure-blockchain.azure-blockchain-...** implementação.
1. Selecione a secção **Saídas.**

    ![Saídas de implantação](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Crescimento do consórcio

Para expandir o seu consórcio, tem primeiro de ligar a rede física. Se estiver a ser implantado atrás de uma VPN, consulte a secção [Connecting VNet Gateway configurar](#connecting-vnet-gateways) a ligação de rede como parte da nova implementação do membro. Assim que a sua implementação estiver concluída, utilize o [DApp de Governação](#governance-dapp) para se tornar um administrador de rede.

### <a name="new-member-deployment"></a>Nova implantação de membros

Partilhe as seguintes informações com o membro da união. A informação encontra-se no seu email pós-implantação ou na saída de implementação do portal.

* URL de dados do consórcio
* O número de nós que lançou
* VNet Gateway Resource ID (se utilizar VPN)

O membro de implantação deve utilizar o mesmo modelo de solução de consórcio Ethereum Proof-of-Authority ao implementar a sua presença na rede utilizando as seguintes orientações:

* Selecione **Aderir existente**
* Escolha o mesmo número de nós validadores que os restantes membros da rede para garantir uma representação justa
* Use o mesmo endereço Admin Ethereum
* Utilize o url de *dados do consórcio* fornecido nas definições do *Ethereum*
* Se o resto da rede estiver por trás de uma VPN, selecione **Private VNet** sob a secção avançada

### <a name="connecting-vnet-gateways"></a>Ligação de gateways VNet

Esta secção só é necessária se tiver sido implantada utilizando um VNet privado. Pode saltar esta secção se estiver a utilizar endereços IP públicos.

Para uma rede privada, os diferentes membros estão ligados através de ligações de gateway VNet. Antes que um membro possa entrar na rede e ver o tráfego de transações, um membro existente deve fazer uma configuração final no seu gateway VPN para aceitar a ligação. Os nós Ethereum do membro de união não funcionarão até que uma ligação seja estabelecida. Para reduzir as hipóteses de um único ponto de falha, crie ligações de rede redundantes no consórcio.

Após a implantação do novo membro, o membro existente deve completar a ligação biducional, estabelecendo uma ligação de gateway VNet ao novo membro. O membro existente precisa:

* O recurso de gateway VNet DO membro de ligação. Ver [saída de implantação](#deployment-output).
* A chave de conexão partilhada.

O membro existente deve executar o seguinte script PowerShell para completar a ligação. Você pode usar Azure Cloud Shell localizado na barra de navegação superior direita no portal.

![casca de nuvem](./media/ethereum-poa-deployment/cloud-shell.png)

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

Pode localizar o seu portal Azure Monitor seguindo o link no e-mail de implantação ou localizando o parâmetro na saída de implementação [OMS_PORTAL_URL].

O portal apresentará primeiro estatísticas de rede de alto nível e uma visão geral do nó.

![Categorias de monitor](./media/ethereum-poa-deployment/monitor-categories.png)

A seleção da **visão geral do nó**  mostra estatísticas de infraestruturas por nó.

![Estatísticas de nó](./media/ethereum-poa-deployment/node-stats.png)

A seleção de **estatísticas da rede** mostra-lhe estatísticas de rede Ethereum.

![Estatísticas de rede](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Experimente consultas kusto

Pode consultar os registos de monitorização para investigar falhas ou alertas de limiar de configuração. As seguintes consultas são exemplos que pode executar na ferramenta *'Procurar* registos':

Os blocos de listas que tenham sido relatados por mais de uma consulta validador podem ser úteis para ajudar a encontrar garfos em cadeia.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Obtenha uma contagem média de pares para um nó validador especificado em média mais de 5 minutos de baldes.

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

Por razões de segurança, o acesso à porta SSH é negado por padrão por uma regra de segurança do grupo de rede. Para aceder às instâncias da máquina virtual na rede PoA, é necessário alterar a seguinte norma de segurança para *permitir*.

1. Aceda à secção **de visão geral** do grupo de recursos implantado no portal Azure.

    ![visão geral de ssh](./media/ethereum-poa-deployment/ssh-overview.png)

1. Selecione o **Grupo de Segurança da Rede** para a região do VM a que pretende aceder.

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Selecione a regra **de permitir ssh.**

    ![A captura do ecrã mostra uma janela de visão geral da ssh-allow selecionada.](./media/ethereum-poa-deployment/ssh-allow.png)

1. Alterar **ação** para **permitir**

    ![ssh permitir](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Selecione **Guardar**. As alterações podem demorar alguns minutos a ser aplicadas.

Pode ligar-se remotamente às máquinas virtuais para os nós validadores via SSH com o nome de utilizador e palavra-passe/SSH fornecidos. O comando SSH para aceder ao primeiro nó validador está listado na saída de implementação do modelo. Por exemplo:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Para chegar a nós de transação adicionais, incremente o número da porta por um.

Se se deslocar para mais de uma região, altere o comando para o nome DNS ou endereço IP do esquilibrador de carga nessa região. Para encontrar o nome DNS ou endereço IP das outras regiões, encontre o recurso com a convenção de nomeação ** \* \* \* \* \* -lbpip-reg \# ** e veja o seu nome DNS e propriedades de endereço IP.

## <a name="azure-traffic-manager-load-balancing"></a>Balanço de carga do Gestor de Tráfego Azure

O Azure Traffic Manager pode ajudar a reduzir o tempo de inatividade e melhorar a capacidade de resposta da rede PoA, encaminhando o tráfego de entrada através de várias implementações em diferentes regiões. Os controlos de saúde incorporados e o reencaminhamento automático ajudam a garantir uma elevada disponibilidade dos pontos finais do RPC e do DApp de Governação. Esta funcionalidade é útil se tiver implementado para várias regiões e estiver pronto para a produção.

Utilize o Traffic Manager para melhorar a disponibilidade da rede PoA com falha automática. Também pode utilizar o Traffic Manager para aumentar a capacidade de resposta das suas redes, encaminhando os utilizadores finais para a localização Azure com a latência de rede mais baixa.

Se decidir criar um perfil de Gestor de Tráfego, pode utilizar o nome DNS do perfil para aceder à sua rede. Uma vez adicionados outros membros do consórcio à rede, o Gestor de Tráfego também pode ser usado para carregar o equilíbrio através dos seus validadores implantados.

### <a name="creating-a-traffic-manager-profile"></a>Criar um perfil de Gestor de Tráfego

1. No [portal Azure,](https://portal.azure.com) **selecione Criar um recurso** no canto superior esquerdo.
1. Procure **o perfil do Gestor de Tráfego.**

    ![Pesquisa rumo ao Gestor de Tráfego da Azure](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Dê ao perfil um nome único e selecione o Grupo de Recursos que foi utilizado para a implantação do PoA.

1. Selecione **Criar** para implementar.

    ![Criar Gestor de Tráfego](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Uma vez implantado, selecione a instância no grupo de recursos. O nome DNS para aceder ao gestor de tráfego pode ser encontrado no separador Visão Geral.

    ![Localizar o gestor de tráfego DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Escolha o **separador Pontos finais** e selecione o botão **Adicionar.**
1. Dê ao ponto final um nome único.
1. Para **o tipo de recurso-alvo,** escolha o endereço IP **público.**
1. Escolha o endereço IP público do balanceador de carga da primeira região.

    ![Gestor de tráfego de encaminhamento](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Repita para cada região na rede implantada. Uma vez que os pontos finais estejam no estado **de ativação,** são automaticamente carregados e região equilibradas no nome DNS do gestor de tráfego. Agora pode usar este nome DNS no lugar do parâmetro [CONSORTIUM_DATA_URL] em outros passos do artigo.

## <a name="data-api"></a>API de Dados

Cada membro do consórcio acolhe as informações necessárias para que outros se conectem à rede. Para permitir a facilidade de conectividade, cada membro acolhe um conjunto de informações de ligação no ponto final da API de dados.

O membro existente fornece o [CONSORTIUM_DATA_URL] antes da colocação do membro. Após a implementação, um membro de união irá obter informações da interface JSON no seguinte ponto final:

`<CONSORTIUM_DATA_URL>/networkinfo`

A resposta contém informações úteis para juntar membros (bloco Génesis, Contrato de Conjunto validador ABI, bootnodes) e informações úteis para o membro existente (endereços validadores). Você pode usar esta normalização para estender o consórcio através de fornecedores de nuvem. Esta API devolve uma resposta formatada JSON com a seguinte estrutura:

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

## <a name="governance-dapp"></a>DApp de governação

No centro da prova de autoridade está a governação descentralizada. Uma vez que a prova de autoridade conta com uma lista permitida de autoridades de rede para manter a rede saudável, é importante fornecer um mecanismo justo para fazer modificações nesta lista de permissões. Cada implementação vem com um conjunto de contratos inteligentes e portal para a governação em cadeia desta lista permitida. Uma vez que uma alteração proposta atinge a maioria dos membros do consórcio, a mudança é promulgada. A votação permite que novos participantes de consenso sejam adicionados ou comprometidos participantes a serem removidos de uma forma transparente que incentive uma rede honesta.

O DApp de governação é um conjunto de [contratos inteligentes](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) pré-implementados e uma aplicação web que são usadas para governar as autoridades da rede. As autoridades estão divididas em identidades administradas e nómadas validados.
Os administradores têm o poder de delegar participação consensual num conjunto de nós validadores. Os administradores também podem votar outros administradores dentro ou fora da rede.

![DApp de governação](./media/ethereum-poa-deployment/governance-dapp.png)

* **Governação Descentralizada:** As alterações nas autoridades de rede são administradas através do voto em cadeia por administradores selecionados.
* **Delegação validador:** As autoridades podem gerir os seus nós validadores que são criados em cada implantação da PoA.
* **Histórico de Alterações Auditáveis:** Cada alteração é registada na blockchain proporcionando transparência e auditabilidade.

### <a name="getting-started-with-governance"></a>Começar com a governação

Para realizar qualquer tipo de transações através do DApp de governação, você precisa usar uma carteira Ethereum. A abordagem mais simples é usar uma carteira no navegador como [MetaMask;](https://metamask.io) no entanto, uma vez que estes contratos inteligentes são implementados na rede, também pode automatizar as suas interações com o contrato de Governação.

Depois de instalar o MetaMask, navegue para o DApp de governação no navegador.  Pode localizar o URL através do portal Azure na saída de implementação.  Se não tiver uma carteira no navegador instalada, não poderá realizar quaisquer ações; no entanto, pode ver o estado do administrador.  

### <a name="becoming-an-admin"></a>Tornando-se um administrador

Se for o primeiro membro que foi implantado na rede, torna-se automaticamente um administrador e os seus nós de paridade são listados como validadores. Se está a aderir à rede, tem de ser votado como administrador por maioria (superior a 50%) do conjunto de administração existente. Se optar por não se tornar administrador, os seus nós ainda sincronizam e validam a blockchain; no entanto, não participam no processo de criação de blocos. Para iniciar o processo de votação para se tornar administrador, **selecione Nomear** e insira o seu endereço Ethereum e pseudónimo.

![Nomeie](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Candidatos

A seleção do separador **Candidatos** mostra-lhe o conjunto atual de administradores candidatos.  Uma vez que um candidato obtém a maioria dos votos pelos atuais administradores, o candidato é promovido a administrador.  Para votar num candidato, selecione a fila e **selecione Vote in**. Se mudar de opinião numa votação, selecione o candidato e selecione **rescindir o voto**.

![Candidatos](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administradores

O separador **Admins** mostra o conjunto atual de administradores e proporciona-lhe a capacidade de votar contra.  Uma vez que um administrador perde mais de 50% de suporte, eles são removidos como administrador na rede. Quaisquer nós validadores que o administrador detém perdem o estatuto de validador e tornam-se nós de transação na rede. Um administrador pode ser removido por várias razões; no entanto, cabe ao consórcio chegar a acordo sobre uma política antecipadamente.

![Administradores](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validadores

A seleção do **separador Validadores** apresenta os nó de paridade implantados atuais, por exemplo, e o seu estado atual (tipo nó). Cada membro do consórcio tem um conjunto diferente de validadores nesta lista, uma vez que esta visão representa o atual membro do consórcio implantado. Se a instância for recentemente implementada e não tiver adicionado os seus validadores, terá a opção **de Adicionar Validadores**. A adição de validadores escolhe automaticamente um conjunto de nós de paridade regionalmente equilibrado e atribui-os ao seu conjunto validador. Se tiver implantado mais nós do que a capacidade permitida, os nós restantes tornam-se nós de transação na rede.

O endereço de cada validador é automaticamente atribuído através da [loja de identidade](#identity-store) em Azure.  Se um nó cair, renuncia à sua identidade, permitindo que outro nó no seu destacamento tome o seu lugar. Este processo garante que a sua participação de consenso está altamente disponível.

![Validadores](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Nome do consórcio

Qualquer administrador pode atualizar o nome do consórcio.  Selecione o ícone de engrenagem na parte superior esquerda para atualizar o nome do consórcio.

### <a name="account-menu"></a>Menu de conta

No topo direito, está o seu pseudónimo e identicon da sua conta Ethereum.  Se for um administrador, tem a capacidade de atualizar o seu pseudónimo.

![Conta](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Desenvolvimento do Ethereum<a id="tutorials"></a>

Para compilar, implementar e testar contratos inteligentes, aqui estão algumas opções que pode considerar para o desenvolvimento do Ethereum:
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) - Ambiente de desenvolvimento do Ethereum baseado no cliente
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - Ambiente de desenvolvimento do Ethereum baseado no navegador e local

### <a name="compile-deploy-and-execute-smart-contract"></a>Compilar, implementar e executar contrato inteligente

No exemplo seguinte, cria-se um contrato inteligente simples. Usas a Truffle para compilar e implementar o contrato inteligente na tua rede blockchain. Uma vez implantado, você chama uma função de contrato inteligente através de uma transação.

#### <a name="prerequisites"></a>Pré-requisitos

* Instale [python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python é necessário para Trufas e Web3. Selecione a opção de instalação para incluir Python no seu caminho.
* Instalar trufas v5.0.5 `npm install -g truffle@v5.0.5` . A trufa requer várias ferramentas para ser instalada, incluindo [Node.js, ](https://nodejs.org) [Git.](https://git-scm.com/) Para mais informações, consulte [a documentação da Truffle.](https://github.com/trufflesuite/truffle)

### <a name="create-truffle-project"></a>Criar projeto Truffle

Antes de poder compilar e implementar um contrato inteligente, precisa criar um projeto Truffle.

1. Abra uma solicitação de comando ou uma concha.
1. Crie uma pasta com o nome `HelloWorld`.
1. Mude o diretório para a nova `HelloWorld` pasta.
1. Inicialize um novo projeto Truffle usando o `truffle init` comando.

    ![Criar um novo projeto Truffle](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Adicione um contrato inteligente

Crie os seus contratos inteligentes na subdiretória de **contratos** do seu projeto Truffle.

1. Crie um ficheiro no nome `postBox.sol` na subdiretória de **contratos** do seu projeto Truffle.
1. Adicione o seguinte código solidity ao **postBox.sol**.

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

### <a name="deploy-smart-contract-using-truffle"></a>Implementar contrato inteligente usando Trufa

Os projetos de trufas contêm um ficheiro de configuração para detalhes de ligação à rede blockchain. Modifique o ficheiro de configuração para incluir as informações de ligação da sua rede.

> [!WARNING]
> Nunca envie a sua chave privada Ethereum para a rede. Certifique-se de que cada transação é assinada localmente primeiro e que a transação assinada é enviada pela rede.

1. Você precisa da frase mnemónica para a conta de [administração Ethereum usada ao implementar a sua rede blockchain](#ethereum-settings). Se usou o MetaMask para criar a conta, pode recuperar o mnemónico da MetaMask. Selecione o ícone da conta do administrador no direito superior da extensão MetaMask e selecione **Definições > Segurança & Privacidade > Revelar Palavras de Semente**.
1. Substitua o conteúdo do `truffle-config.js` seu projeto Truffle pelo seguinte conteúdo. Substitua o ponto de terminação do espaço reservado e os valores mnemónicos.

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

1. Uma vez que estamos a utilizar o fornecedor Truffle HD Wallet, instale o módulo no seu projeto utilizando o `npm install truffle-hdwallet-provider --save` comando.

Truffle usa scripts de migração para implementar contratos inteligentes para uma rede blockchain. Precisas de um guião de migração para implementar o teu novo contrato inteligente.

1. Adicione uma nova migração para implementar o novo contrato. Criar ficheiro `2_deploy_contracts.js` na subdiretória de **migrações** do projeto Truffle.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Implemente para a rede PoA usando o comando de migração de Trufas. No comando do diretório do projeto Truffle, corra:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Chame uma função de contrato inteligente

Agora que o seu contrato inteligente está implementado, pode enviar uma transação para convocar uma função.

1. No diretório do projeto Truffle, crie um novo ficheiro chamado `sendtransaction.js` .
1. Adicione o seguinte conteúdo a **sendtransaction.js**.

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

1. Execute o script usando o comando de execução de Trufas.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Execute o script para chamar a função através de transação](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Suporte webAssembly (WASM)

O suporte webAssembly já está ativado para si em redes PoA recém-implantadas. Permite o desenvolvimento de contratos inteligentes em qualquer idioma que transpile para Web-Assembly (Rust, C, C++). Para mais informações, consulte: [Parity Overview of WebAssembly](https://wiki.parity.io/WebAssembly-Home) e [Tutorial da Parity Tech](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>FAQ

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Reparei que há muitas transações na rede que não enviei. De onde vêm estes?

É inseguro desbloquear a [API pessoal.](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html) Os bots ouvem as contas desbloqueadas do Ethereum e tentam drenar os fundos. O bot assume que estas contas contêm éter real e tentam ser as primeiras a sifonar o saldo. Não ative a API pessoal na rede. Em vez disso, pré-assine as transações manualmente usando uma carteira como metaMask ou programáticamente.

### <a name="how-to-ssh-onto-a-vm"></a>Como sSH em um VM?

A porta SSH não está exposta por razões de segurança. Siga [este guia para ativar a porta SSH](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Como posso configurar um membro de auditoria ou nós de transações?

Os nós de transação são um conjunto de clientes de paridade que são espreitados com a rede, mas não estão a participar em consensos. Estes nós ainda podem ser usados para submeter transações Ethereum e ler o estado do contrato inteligente. Este mecanismo funciona para proporcionar a auditoria aos membros do consórcio não-autoridade na rede. Para isso, siga os passos em [Growing the Consortium.](#growing-the-consortium)

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Porque é que as transações metaMask estão a demorar muito tempo?

Para garantir que as transações são recebidas na ordem correta, cada transação Ethereum vem com um nonce incrementante. Se utilizou uma conta no MetaMask numa rede diferente, tem de redefinir o valor nonce. Clique no ícone de definições (três barras), Definições, Conta Reset. O histórico de transações será apurado e agora pode reenviar a transação.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Preciso de especificar a taxa de gás no MetaMask?

Éter não serve um propósito no consórcio de prova de autoridade. Por conseguinte, não há necessidade de especificar a taxa de gás ao submeter transações no MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>O que devo fazer se a minha implantação falhar devido à falta de provisão Azure OMS?

A monitorização é uma característica opcional. Em alguns casos raros em que a sua implantação falha devido à incapacidade de obter com sucesso o recurso Azure Monitor, pode ser reenviado sem o Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>As implementações ip públicas são compatíveis com implementações de rede privada?

N.º O despresogromento requer comunicação bidirecional, pelo que toda a rede deve ser pública ou privada.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Qual é o resultado da transação esperada da Prova de Autoridade?

A produção de transações será altamente dependente dos tipos de transações e da topologia da rede. Usando transações simples, comparamos uma média de 400 transações por segundo com uma rede implantada em várias regiões.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Como subscrevo eventos de contratos inteligentes?

A Ethereum Proof-of-Authority suporta agora as tomadas web.  Verifique a sua saída de implantação para localizar o URL e a porta da tomada web.

## <a name="support-and-feedback"></a>Suporte e comentários

Para notícias da Azure Blockchain, visite o [blog Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/) para se manter atualizado sobre ofertas de serviços blockchain e informações da equipa de engenharia Azure Blockchain.

Para fornecer feedback do produto ou para solicitar novas funcionalidades, publicar ou votar por uma ideia através do [fórum de feedback Azure para blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Suporte da comunidade

Envolva-se com engenheiros da Microsoft e especialistas da comunidade Azure Blockchain.

* [Microsoft Q&Uma página de perguntas](/answers/topics/azure-blockchain-workbench.html). O suporte de engenharia para modelos blockchain está limitado a problemas de implantação.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Passos seguintes

Para obter mais soluções Azure Blockchain, consulte a documentação do [Azure Blockchain.](../index.yml)
