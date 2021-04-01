---
title: Azure análise de tráfego | Microsoft Docs
description: Saiba como analisar os registos de fluxo do grupo de segurança da rede Azure com análise de tráfego.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.reviewer: vinigam
ms.custom: references_regions
ms.openlocfilehash: 87dacd28223fd88866edb90266aa76fd3cdf86cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095360"
---
# <a name="traffic-analytics"></a>Análise de Tráfego

Traffic Analytics é uma solução baseada na nuvem que proporciona visibilidade à atividade do utilizador e da aplicação em redes em nuvem. A análise de tráfego analisa os registos de fluxo do Grupo de Segurança da Rede Observador (NSG) para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Com análises de tráfego, pode:

- Visualize a atividade da rede através das suas subscrições Azure e identifique pontos quentes.
- Identifique ameaças de segurança para, e proteja a sua rede, com informações como portas abertas, aplicações que tentam aceder à Internet e máquinas virtuais (VM) conectando-se a redes fraudulentas.
- Compreenda os padrões de fluxo de tráfego em todas as regiões de Azure e na internet para otimizar a implementação da sua rede para desempenho e capacidade.
- Identifique as configurações erradas da rede que conduzam a ligações falhadas na sua rede.

> [!NOTE]
> Traffic Analytics agora suporta a recolha de dados de Registos de Fluxo NSG numa frequência mais elevada de 10 minutos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Por que análise de tráfego?

É vital monitorizar, gerir e conhecer a sua própria rede para segurança, conformidade e desempenho descompromissados. Conhecer o seu próprio ambiente é da maior importância protegê-lo e otimizá-lo. É frequente saber o estado atual da rede, quem está a ligar-se, de onde estão a ligar-se, quais as portas abertas à internet, o comportamento esperado da rede, o comportamento irregular da rede e o aumento súbito do tráfego.

As redes em nuvem são diferentes das redes empresariais no local, onde você tem fluxo de rede ou protocolo equivalente com capacidade de routers e switches, que fornecem a capacidade de recolher tráfego de rede IP à medida que entra ou sai de uma interface de rede. Ao analisar dados de fluxo de tráfego, pode construir uma análise do fluxo de tráfego de rede e volume.

As redes virtuais Azure têm registos de fluxo NSG, que lhe fornecem informações sobre o tráfego IP de entrada e saída através de um Grupo de Segurança de Rede associado a interfaces de rede individuais, VMs ou sub-redes. Ao analisar registos de fluxo de NSG crus e inserir inteligência de segurança, topologia e geografia, a análise de tráfego pode fornecer-lhe informações sobre o fluxo de tráfego no seu ambiente. Traffic Analytics fornece informações como a maioria dos anfitriões comunicadores, a maioria dos protocolos de aplicação comunicando, a maioria dos pares de anfitriões conversões, tráfego permitido/bloqueado, tráfego de entrada/saída, portas de internet abertas, a maioria das regras de bloqueio, distribuição de tráfego por datacenter Azure, rede virtual, sub-redes ou redes fraudulentas.

## <a name="key-components"></a>Componentes principais

- Grupo de **segurança de rede (NSG)**: Contém uma lista de regras de segurança que permitem ou negam o tráfego de rede a recursos ligados a uma Rede Virtual Azure. Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou a interfaces de rede individuais (NIC) ligadas a VMs (Resource Manager). Para obter mais informações, consulte [a visão geral do grupo de segurança da rede.](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- **Registos de fluxo do grupo de segurança da rede (NSG):** Permita-lhe visualizar informações sobre o tráfego IP de entrada e saída através de um grupo de segurança de rede. Os registos de fluxo NSG são escritos em formato json e mostram fluxos de saída e de entrada por regra, o NIC o fluxo aplica-se a, cinco tuple informações sobre o fluxo (endereço IP de origem/destino, porta de origem/destino e protocolo), e se o tráfego foi permitido ou negado. Para obter mais informações sobre os registos de fluxo NSG, consulte [os registos de fluxo NSG](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: Um serviço Azure que recolhe dados de monitorização e armazena os dados num repositório central. Estes dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos através da AZure API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação. Aplicações de monitorização como monitor de desempenho de rede e análise de tráfego são construídas usando registos do Azure Monitor como uma fundação. Para obter mais informações, consulte [os registos do Monitor Azure](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics workspace**: Uma instância de registos do Azure Monitor, onde os dados relativos a uma conta Azure, são armazenados. Para obter mais informações sobre os espaços de trabalho do Log Analytics, consulte [Criar um espaço de trabalho Log Analytics](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Monitor de rede**: Um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário de rede em Azure. Pode ligar e desligar os registos de fluxo NSG com o Network Watcher. Para mais informações, consulte [o Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Como funciona a análise de tráfego

A análise do tráfego examina os registos de fluxo de NSG brutos e captura registos reduzidos agregando fluxos comuns entre o mesmo endereço IP de origem, endereço IP de destino, porta de destino e protocolo. Por exemplo, o Anfitrião 1 (endereço IP: 10.10.10.10) comunicando ao Anfitrião 2 (endereço IP: 10.10.20.10), 100 vezes durante um período de 1 hora usando a porta (por exemplo, 80) e o protocolo (por exemplo, http). O registo reduzido tem uma entrada, que o Anfitrião 1 & Anfitrião 2 comunicou 100 vezes durante um período de 1 hora usando a porta *80* e o protocolo *HTTP*, em vez de ter 100 entradas. Os registos reduzidos são melhorados com informações de geografia, segurança e topologia, e depois armazenados num espaço de trabalho log analytics. A imagem a seguir mostra o fluxo de dados:

![Fluxo de dados para o processamento de registos de fluxo de NSG](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Regiões apoiadas: NSG 

Pode utilizar análises de tráfego para NSGs em qualquer uma das seguintes regiões apoiadas:
:::row:::
   :::column span="":::
      Austrália Central  
      Leste da Austrália  
      Austrália Sudeste  
      Sul do Brasil  
      Canadá Central  
      Leste do Canadá  
      Índia Central  
      E.U.A. Central  
      China Leste 2  
      China Norte 2  
      Ásia Leste       
   :::column-end:::
   :::column span="":::
      E.U.A. Leste  
      E.U.A. Leste 2  
      Leste DOS EUA 2  
      França Central  
      Alemanha Centro-Oeste Japão Leste  
      Oeste do Japão  
      Coreia do Sul Central  
      Sul da Coreia do Sul  
      E.U.A. Centro-Norte  
      Europa do Norte    
   :::column-end:::
   :::column span="":::
      Norte da África do Sul  
      E.U.A. Centro-Sul  
      Sul da Índia  
      Sudeste Asiático  
      Suíça Norte  
      Suíça Oeste  
      Uae Norte  
      Sul do Reino Unido  
      Oeste do Reino Unido   
      USGov Arizona  
      USGov Texas
   :::column-end:::
   :::column span="":::
      USGov Virginia  
      USNat Leste  
      USNat Oeste  
      USSec Leste  
      USSec West  
      E.U.A. Centro-Oeste  
      Europa Ocidental  
      E.U.A. Oeste  
      E.U.A. Oeste 2  
   :::column-end:::
:::row-end:::

## <a name="supported-regions-log-analytics-workspaces"></a>Regiões apoiadas: Log Analytics Workspaces

O espaço de trabalho Log Analytics deve existir nas seguintes regiões:
:::row:::
   :::column span="":::
      Austrália Central  
      Leste da Austrália  
      Austrália Sudeste  
      Sul do Brasil  
      Canadá Central  
      Índia Central  
      E.U.A. Central  
      China Leste 2  
      Ásia Leste  
      E.U.A. Leste  
   :::column-end:::
   :::column span="":::
      E.U.A. Leste 2  
      Leste DOS EUA 2  
      França Central  
      Alemanha Centro-Oeste  
      Leste do Japão  
      Coreia do Sul Central  
      E.U.A. Centro-Norte  
      Europa do Norte  
      Norte da África do Sul  
      E.U.A. Centro-Sul  
   :::column-end:::
   :::column span="":::
      Sudeste Asiático  
      Suíça Norte  
      Suíça Oeste  
      Centro dos Emirados Árabes Unidos  
      Uae Norte  
      Sul do Reino Unido  
      Oeste do Reino Unido   
      USGov Arizona  
      USGov Virginia  
      USNat Leste   
   :::column-end:::
   :::column span="":::
      USNat Oeste   
      USSec Leste  
      USSec West  
      E.U.A. Centro-Oeste  
      Europa Ocidental  
      E.U.A. Oeste  
      E.U.A. Oeste 2  
   :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Pré-requisitos

### <a name="user-access-requirements"></a>Requisitos de acesso ao utilizador

A sua conta deve ser membro de uma das seguintes [funções incorporadas do Azure:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

|Modelo de implementação   | Função                   |
|---------          |---------               |
|Resource Manager   | Proprietário                  |
|                   | Contribuinte            |
|                   | Leitor                 |
|                   | Contribuidor de Rede    |

Se a sua conta não for atribuída a uma das funções incorporadas, deve ser atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) que seja atribuída às seguintes ações, ao nível da subscrição:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"
- "Microsoft.Network/expressRouteCircuits/read"

Para obter informações sobre como verificar as permissões de acesso ao utilizador, consulte [as FAQ de análise de tráfego.](traffic-analytics-faq.md)

### <a name="enable-network-watcher"></a>Ativar o Observador de Rede

Para analisar o tráfego, precisa de ter um observador de rede existente, ou [ativar um observador](network-watcher-create.md) de rede em cada região para onde tem NSGs para os que pretende analisar o tráfego. A análise do tráfego pode ser ativada para os NSGs alojados em qualquer uma das [regiões apoiadas.](#supported-regions-nsg)

### <a name="select-a-network-security-group"></a>Selecione um grupo de segurança de rede

Antes de permitir a registo de fluxos NSG, tem de ter um grupo de segurança de rede para registar fluxos. Se não tiver um grupo de segurança de rede, consulte [criar um grupo de segurança](../virtual-network/manage-network-security-group.md#create-a-network-security-group) de rede para criar um.

No portal Azure, vá ao **Observador de Rede** e, em seguida, selecione **registos de fluxo NSG**. Selecione o grupo de segurança de rede para o quais pretende ativar um registo de fluxo NSG, como mostra a seguinte imagem:

![Seleção de NSGs que requerem ativação do registo de fluxo NSG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Se tentar ativar a análise de tráfego para um NSG que esteja hospedado em qualquer região que não seja as [regiões apoiadas,](#supported-regions-nsg)receberá um erro "Não encontrado".

## <a name="enable-flow-log-settings"></a>Ativar as definições de registo de fluxo

Antes de ativar as definições de registo de fluxo, deve completar as seguintes tarefas:

Registe-se no fornecedor Azure Insights, se ainda não estiver registado para a sua subscrição:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Se ainda não tiver uma conta de Armazenamento Azure para armazenar registos de fluxo NSG, tem de criar uma conta de armazenamento. Pode criar uma conta de armazenamento com o comando que se segue. Antes de executar o comando, `<replace-with-your-unique-storage-account-name>` substitua por um nome único em todos os locais do Azure, entre 3-24 caracteres de comprimento, utilizando apenas números e letras minúsculas. Pode também alterar o nome do grupo de recursos, se necessário.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Selecione as seguintes opções, como mostra a imagem:

1. Selecione *On* para **o estado**
2. Selecione *a versão 2* para **a versão Flow Logs**. A versão 2 contém estatísticas de sessão de fluxo (Bytes e Pacotes)
3. Selecione uma conta de armazenamento existente para armazenar os registos de fluxo. Certifique-se de que o seu armazenamento não tem "Data Lake Storage Gen2 Hierarchical Namespace Enabled" definido para ser verdadeiro.
4. Desabar **a Retenção** para o número de dias para os dias para os que pretende armazenar os dados. Se quiser armazenar os dados para sempre, desa um valor para *0*. Incorre nas taxas de armazenamento do Azure para a conta de armazenamento. 
5. Selecione *on* for **Traffic Analytics Status**.
6. Selecione o intervalo de processamento. Com base na sua escolha, os registos de fluxo serão recolhidos a partir da conta de armazenamento e processados pela Traffic Analytics. Pode escolher o intervalo de processamento de cada 1 hora ou a cada 10 minutos. 
7. Selecione um espaço de trabalho de Log Analytics (OMS) existente ou selecione **Criar novo espaço de trabalho** para criar um novo. Um espaço de trabalho Log Analytics é utilizado pela Traffic Analytics para armazenar os dados agregados e indexados que são depois utilizados para gerar a análise. Se selecionar um espaço de trabalho existente, deve existir numa das [regiões apoiadas](#supported-regions-log-analytics-workspaces) e ter sido atualizado para a nova linguagem de consulta. Se não pretender atualizar um espaço de trabalho existente, ou não tiver um espaço de trabalho numa região apoiada, crie um novo espaço de trabalho. Para obter mais informações sobre idiomas de consulta, consulte [o upgrade do Azure Log Analytics para nova pesquisa de registo](../azure-monitor/logs/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>O espaço de trabalho de análise de log analytics que acolhe a solução de análise de tráfego e os NSGs não têm de estar na mesma região. Por exemplo, você pode ter análises de tráfego em um espaço de trabalho na região da Europa Ocidental, enquanto você pode ter NSGs em Leste dos EUA e Eua Ocidental. Vários NSGs podem ser configurados no mesmo espaço de trabalho.

8. Selecione **Guardar**.

    ![Seleção de conta de armazenamento, espaço de trabalho Log Analytics e Ativação de Análise de Tráfego](./media/traffic-analytics/ta-customprocessinginterval.png)

Repita os passos anteriores para quaisquer outros NSGs para os quais deseja ativar a análise do tráfego. Os dados dos registos de fluxo são enviados para o espaço de trabalho, de modo a garantir que as leis e regulamentos locais no seu país/região permitem o armazenamento de dados na região onde o espaço de trabalho existe. Se tiver definido diferentes intervalos de processamento para diferentes NSGs, os dados serão recolhidos em intervalos diferentes. Por exemplo: Pode optar por ativar um intervalo de processamento de 10 minutos para VNETs críticos e 1 hora para VNETs não críticos.

Também pode configurar análises de tráfego utilizando o [cmdlet Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell em Azure PowerShell. Corra `Get-Module -ListAvailable Az` para encontrar a sua versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Ver análise de tráfego

Para ver Traffic Analytics, procure **o Observador de Redes** na barra de pesquisa do portal. Uma vez dentro do Network Watcher, para explorar a análise de tráfego e as suas capacidades, selecione **Traffic Analytics** a partir do menu esquerdo. 

![Aceder ao painel traffic Analytics](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

O painel de instrumentos pode demorar até 30 minutos a aparecer pela primeira vez porque o Traffic Analytics deve primeiro agregar dados suficientes para obter insights significativos, antes que possa gerar quaisquer relatórios.

## <a name="usage-scenarios"></a>Cenários de utilização

Algumas das ideias que pode querer obter depois de a Traffic Analytics estar totalmente configurada, são as seguintes:

### <a name="find-traffic-hotspots"></a>Encontre pontos de trânsito

**Procurar**

- Que anfitriões, sub-redes e redes virtuais estão a enviar ou a receber mais tráfego, atravessando o tráfego malicioso máximo e bloqueando fluxos significativos?
    - Verifique o gráfico comparativo para o anfitrião, sub-rede e rede virtual. Compreender quais os anfitriões, sub-redes e redes virtuais que estão a enviar ou receber o maior tráfego pode ajudá-lo a identificar os anfitriões que estão a processar mais tráfego, e se a distribuição de tráfego é feita corretamente.
    - Pode avaliar se o volume de tráfego é apropriado para um hospedeiro. O volume de tráfego é um comportamento normal, ou merece uma investigação mais aprofundada?
- Quanto tráfego de entrada/saída existe?
    -   Espera-se que o hospedeiro receba mais tráfego de entrada do que de saída, ou vice-versa?
- Estatísticas do tráfego bloqueado.
    - Porque é que um hospedeiro está a bloquear um volume significativo de tráfego benigno? Este comportamento requer mais investigação e, provavelmente, otimização da configuração
- Estatísticas de tráfego malicioso permitido/bloqueado
  - Porque é que um hospedeiro está a receber tráfego malicioso e porque é que os fluxos de fontes maliciosas são permitidos? Este comportamento requer mais investigação e provavelmente otimização da configuração.

    Selecione **Ver tudo,** em **Host**, como mostra a seguinte imagem:

    ![Painel de instrumentos mostrando anfitrião com a maioria dos detalhes do tráfego](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- A imagem a seguir mostra a tendência do tempo para os cinco anfitriões falantes e os detalhes relacionados com o fluxo (permitidos - entradas/saídas e negados - fluxos de entrada/saída) para um hospedeiro:

    ![Top 5 da tendência de anfitrião mais falante](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Procurar**

- Quais são os pares de anfitriões mais conversadores?
    - Comportamento esperado como comunicação frontal ou back-end ou comportamento irregular, como tráfego de internet back-end.
- Estatísticas do tráfego permitido/bloqueado
    - Porque é que um hospedeiro está a permitir ou a bloquear um volume significativo de tráfego
- Protocolo de aplicação mais frequentemente usado entre a maioria dos pares de anfitriões conversantes:
    - Estas aplicações são permitidas nesta rede?
    - As aplicações estão configuradas corretamente? Estão a utilizar o protocolo adequado para a comunicação? Selecione **Ver tudo** em **Conversação Frequente,** como mostra na seguinte imagem:

        ![Painel de instrumentos mostrando a conversa mais frequente](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- A imagem que se segue mostra a tendência do tempo para as cinco melhores conversas e os detalhes relacionados com o fluxo, tais como fluxos de entrada e saída permitidos e negados para um par de conversação:

    ![Top cinco detalhes de conversa tagarela e tendência](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Procurar**

- Qual o protocolo de aplicação mais utilizado no seu ambiente e quais os pares de anfitriões que mais usam o protocolo de aplicação?
    - Estas aplicações são permitidas nesta rede?
    - As aplicações estão configuradas corretamente? Estão a utilizar o protocolo adequado para a comunicação? O comportamento esperado é em portos comuns como 80 e 443. Para a comunicação padrão, se forem apresentadas portas incomuns, poderão necessitar de uma alteração de configuração. Selecione **Ver tudo** na **porta aplicação,** na seguinte imagem:

        ![Dashboard mostrando protocolos de aplicação superiores](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- As seguintes imagens mostram a tendência do tempo para os cinco principais protocolos L7 e os detalhes relacionados com o fluxo (por exemplo, fluxos permitidos e negados) para um protocolo L7:

    ![Detalhes e tendência dos protocolos de 7 camadas 7](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Detalhes do fluxo para protocolo de aplicação na pesquisa de registo](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Procurar**

- Tendências de utilização da capacidade de uma porta de entrada VPN no seu ambiente.
    - Cada VPN SKU permite uma certa quantidade de largura de banda. Os gateways VPN estão subutilizados?
    - Os seus portais estão a atingir a capacidade? Deve fazer upgrade para o próximo SKU superior?
- Quais são os anfitriões mais conversadores, através do portal VPN, sobre qual porto?
    - Este padrão é normal? Selecione **Veja tudo** sob **o gateway VPN,** como mostra a seguinte imagem:

        ![Painel de instrumentos mostrando ligações VPN ativas de topo](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- A imagem a seguir mostra a tendência do tempo para a utilização da capacidade de um Gateway Azure VPN e os detalhes relacionados com o fluxo (tais como fluxos permitidos e portas):

    ![Tendência de utilização de gateway VPN e detalhes do fluxo](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualizar a distribuição de tráfego por geografia

**Procurar**

- Distribuição de tráfego por centro de dados, como as principais fontes de tráfego para um datacenter, redes fraudulentas de topo conversando com o centro de dados, e protocolos de aplicação de conversação de topo.
  - Se observar mais carga num centro de dados, pode planear uma distribuição eficiente do tráfego.
  - Se as redes fraudulentas estiverem a conversar no centro de dados, corrija as regras do NSG para as bloquear.

    Selecione **Ver mapa** no **seu ambiente,** como mostra a seguinte imagem:

    ![Painel de instrumentos mostrando distribuição de tráfego](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- O geo-mapa mostra a fita superior para a seleção de parâmetros como centros de dados (Implantado/Não-implantação/Ative/Inative/Traffic Analytics Enabled/Traffic Analytics Não Habilitado) e países/regiões que contribuem com tráfego benigno/malicioso para a implementação ativa:

    ![Vista de mapa geo mostrando implementação ativa](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- O geo-mapa mostra a distribuição de tráfego para um centro de dados de países/regiões e continentes que comunicam a ele em linhas coloridas azuis (tráfego benigno) e vermelhas (tráfego malicioso):

    ![Vista de mapa geo que mostra distribuição de tráfego para países/regiões e continentes](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Detalhes do fluxo para distribuição de tráfego na pesquisa de registos](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualizar a distribuição de tráfego por redes virtuais

**Procurar**

- Distribuição de tráfego por rede virtual, topologia, principais fontes de tráfego para a rede virtual, redes fraudulentas de topo que conversam com a rede virtual e protocolos de aplicação de conversação de topo.
  - Saber qual rede virtual está a conversar com que rede virtual. Se a conversa não for esperada, pode ser corrigida.
  - Se as redes fraudulentas estiverem a conversar com uma rede virtual, pode corrigir as regras NSG para bloquear as redes fraudulentas.
 
    Selecione **Ver VNets** no **seu ambiente,** como mostra a seguinte imagem:

    ![Painel de instrumentos mostrando distribuição de rede virtual](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- A Topologia da Rede Virtual mostra a fita superior para a seleção de parâmetros como o de uma rede virtual (Conexões de rede virtual Inter/Ative/Inative), Conexões Externas, Fluxos Ativos e Fluxos Maliciosos da rede virtual.
- Pode filtrar a Topologia da Rede Virtual com base em subscrições, espaços de trabalho, grupos de recursos e intervalo de tempo. Filtros adicionais que o ajudam a compreender o fluxo são: Flow Type (InterVNet, IntraVNET, e assim por diante), Flow Direction (Entrada, Saída), Estado do Fluxo (Permitido, Bloqueado), VNETs (Direcionado e Conectado), Tipo de Ligação (Peering ou Gateway - P2S e S2S) e NSG. Utilize estes filtros para se concentrar em VNets que pretende examinar em detalhe.
- A Topologia da Rede Virtual mostra a distribuição de tráfego para uma rede virtual no que diz respeito aos fluxos (Permitido/Bloqueado/Entrada/Saída/Benign/Malicious), protocolo de aplicação e grupos de segurança de rede, por exemplo:

    ![Topologia de rede virtual mostrando detalhes de distribuição de tráfego e fluxo](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologia de rede virtual mostrando o nível superior e mais filtros](./media/traffic-analytics/virtual-network-filters.png)

    ![Detalhes do fluxo para distribuição de tráfego de rede virtual na pesquisa de registo](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Procurar**

- Distribuição de tráfego por sub-rede, topologia, principais fontes de tráfego para a sub-rede, redes fraudulentas de topo que convergem para a sub-rede, e protocolos de aplicação de conversação de topo.
    - Saber qual sub-rede está a falar com qual sub-rede. Se vir conversas inesperadas, pode corrigir a sua configuração.
    - Se as redes fraudulentas estiverem a conversar com uma sub-rede, é possível corrigi-la configurando as regras NSG para bloquear as redes fraudulentas.
- A Topologia das Subnetas mostra a fita superior para a seleção de parâmetros tais como sub-rede Ativa/Inativa, Conexões Externas, Fluxos Ativos e Fluxos Maliciosos da sub-rede.
- A Topologia da Sub-rede mostra a distribuição de tráfego a uma rede virtual no que diz respeito aos fluxos (Permitido/Bloqueado/Entrada/Saída/Benign/Malicioso), protocolo de aplicação e NSGs, por exemplo:

    ![Topologia sub-rede que mostra distribuição de tráfego uma sub-rede de rede virtual no que diz respeito aos fluxos](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Procurar**

Distribuição de tráfego por Gateway de aplicação & Balancer de Carga, topologia, principais fontes de tráfego, redes fraudulentas de topo que convergem para o gateway de aplicação & Load Balancer e protocolos de aplicação conversores de topo. 
    
 - Sabendo qual sub-rede está a conversar com que porta de entrada de aplicação ou balanceador de carga. Se observar conversas inesperadas, pode corrigir a sua configuração.
 - Se as redes fraudulentas estiverem a conversar com um gateway de aplicação ou um Balancer de Carga, é possível corrigi-lo configurando as regras NSG para bloquear as redes fraudulentas. 

    ![O Screenshot mostra uma topologia de sub-rede com distribuição de tráfego para uma sub-rede de gateway de aplicação no que diz respeito aos fluxos.](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Ver portas e máquinas virtuais que recebem tráfego da internet

**Procurar**

- Que portas abertas estão a conversar pela internet?
  - Se forem encontradas portas inesperadas abertas, pode corrigir a sua configuração:

    ![Painel de instrumentos que mostra portas que recebem e enviam tráfego para a internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Detalhes dos portos e anfitriões de destino Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Procurar**

Tem tráfego malicioso no seu ambiente? De onde vem? Para onde está destinado?

![Fluxos de tráfego maliciosos detalhes na pesquisa de registo](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualizar as tendências nas regras NSG/NSG atinge

**Procurar**

- Quais as regras NSG/NSG que têm mais sucessos em gráficos comparativos com distribuição de fluxos?
- Quais são os pares de conversação de primeira fonte e destino de acordo com as regras NSG/NSG?

    ![Painel de instrumentos que mostra NSG atinge estatísticas](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- As seguintes imagens mostram a tendência do tempo para os acessos das regras NSG e detalhes do fluxo de destino de origem para um grupo de segurança de rede:

  - Deteta rapidamente quais as regras de NSGs e NSG que atravessam fluxos maliciosos e quais são os principais endereços IP maliciosos que acedem ao seu ambiente em nuvem
  - Identificar quais as regras NSG/NSG que permitem/bloqueiam tráfego significativo de rede
  - Selecione filtros de topo para inspeção granular de regras NSG ou NSG

    ![Apresentando tendências de tempo para os sucessos de regras NSG e as principais regras do NSG](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Top NSG regras estatísticas detalhes na pesquisa de registo](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Para obter respostas a perguntas frequentes, consulte [as FAQ de análise de tráfego.](traffic-analytics-faq.md)

## <a name="next-steps"></a>Passos seguintes

- Para aprender a ativar os registos de fluxo, consulte [ativar a registo de fluxo nSG](network-watcher-nsg-flow-logging-portal.md).
- Para compreender os detalhes do esquema e processamento da Traffic Analytics, consulte [o esquema de análise de tráfego.](traffic-analytics-schema.md)
