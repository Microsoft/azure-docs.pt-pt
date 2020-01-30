---
title: Análise de tráfego azure  Microsoft Docs
description: Saiba como analisar os registos de fluxo do grupo de segurança da rede Azure com análise de tráfego.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: damendo
ms.reviewer: vinigam
ms.openlocfilehash: 6cec7c813b0723ac770da6ebd04f4d2cf26a1409
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840592"
---
# <a name="traffic-analytics"></a>Análise de Tráfego

Traffic Analytics é uma solução baseada na nuvem que proporciona visibilidade na atividade do utilizador e aplicação em redes cloud. A análise de tráfego analisa os registos de fluxo do grupo de segurança da rede Network Watcher (NSG) para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Com análise de tráfego, pode:

- Visualize a atividade da rede através das suas subscrições do Azure e identifique pontos quentes.
- Identifique ameaças de segurança e proteja a sua rede, com informações como portas abertas, aplicações que tentem aceder à Internet e máquinas virtuais (VM) conectando-se a redes fraudulentas.
- Compreenda os padrões de fluxo de tráfego em todas as regiões de Azure e na internet para otimizar a implementação da sua rede para desempenho e capacidade.
- Identificar configurações erradas de rede que conduzam a ligações falhadas na sua rede.

> [!NOTE]
> Traffic Analytics agora suporta a recolha de dados de Registos de Fluxo NSG a uma frequência mais alta de 10 minutos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Por que análise de trânsito?

É vital monitorizar, gerir e conhecer a sua própria rede para segurança, conformidade e desempenho descomprometidos. Conhecer o seu próprio ambiente é da maior importância para protegê-lo e otimizar. Muitas vezes é preciso saber o estado atual da rede, que está a ligar-se, de onde estão a ligar-se, quais as portas abertas à internet, comportamento de rede esperado, comportamento irregular da rede e aumentos repentinos de tráfego.

As redes cloud são diferentes das redes empresariais no local, onde você tem routers e comutadores de protocolo sinuoso ou equivalente, que fornecem a capacidade de recolher tráfego de rede IP à medida que entra ou sai de uma interface de rede. Ao analisar os dados do fluxo de tráfego, pode construir uma análise do fluxo de tráfego e volume da rede.

As redes virtuais Azure têm registos de fluxo NSG, que lhe fornecem informações sobre o tráfego IP de entrada e fuga através de um Grupo de Segurança de Rede associado a interfaces de rede individuais, VMs ou subnets. Analisando registos de fluxo de NSG crus e inserindo inteligência de segurança, topologia e geografia, a análise de tráfego pode fornecer-lhe informações sobre o fluxo de tráfego no seu ambiente. O Traffic Analytics fornece informações como a maioria dos anfitriões comunicadores, a maioria dos protocolos de aplicação comunicantes, a maioria dos pares de anfitriões conversantes, tráfego permitido/bloqueado, tráfego de entrada/saída, portas de internet abertas, a maioria das regras de bloqueio, distribuição de tráfego por centro de dados Azure, rede virtual, subnets ou redes fraudulentas.

## <a name="key-components"></a>Componentes principais

- Grupo de segurança de **rede (NSG)** : Contém uma lista de regras de segurança que permitem ou negam o tráfego de rede a recursos ligados a uma Rede Virtual Azure. Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou a interfaces de rede individuais (NIC) ligadas a VMs (Resource Manager). Para mais informações, consulte a visão geral do grupo de [segurança da rede](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Registos de fluxo do grupo de segurança da **rede (NSG)** : Permita-lhe visualizar informações sobre o tráfego IP de entrada e fuga através de um grupo de segurança de rede. Os registos de fluxo NSG são escritos em formato JSON e mostram fluxos de saída e de entrada por regra, o NIC o fluxo aplica-se a informações de cinco tuple sobre o fluxo (endereço IP de origem/destino, porta de origem/destino e protocolo), e se o tráfego foi permitido ou negado. Para obter mais informações sobre os registos de fluxo da NSG, consulte os registos de [fluxo nsg](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: Um serviço Azure que recolhe dados de monitorização e armazena os dados num repositório central. Estes dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos através da API Azure. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação. Aplicações de monitorização, tais como o monitor de desempenho da rede e a análise de tráfego são construídas usando os registos do Monitor Azure como base. Para mais informações, consulte os [registos do Monitor Azure](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics workspace**: Uma instância de registos do Monitor Azure, onde os dados relativos a uma conta Azure são armazenados. Para mais informações sobre os espaços de trabalho do Log Analytics, consulte Criar um espaço de [trabalho de Log Analytics](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: Um serviço regional que lhe permite monitorizar e diagnosticar condições ao nível do cenário da rede em Azure. Pode ligar e desligar os registos de fluxo nsg com o Observador da Rede. Para mais informações, consulte [O Observador da Rede.](network-watcher-monitoring-overview.md)

## <a name="how-traffic-analytics-works"></a>Como funciona a análise de tráfego

A análise de tráfego examina os registos de fluxo de NSG brutos e captura registos reduzidos, agregando fluxos comuns entre o mesmo endereço IP de origem, endereço IP de destino, porta de destino e protocolo. Por exemplo, anfitrião 1 (endereço IP: 10.10.10.10.10) comunicando ao Anfitrião 2 (endereço IP: 10.10.20.10), 100 vezes durante um período de 1 hora utilizando o porto (por exemplo, 80) e o protocolo (por exemplo, http). O registo reduzido tem uma entrada, que o Anfitrião 1 & Host 2 comunicou 100 vezes durante um período de 1 hora utilizando a porta *80* e o protocolo *HTTP,* em vez de ter 100 entradas. Os registos reduzidos são melhorados com informações de geografia, segurança e topologia, e depois armazenados num espaço de trabalho de Log Analytics. A imagem seguinte mostra o fluxo de dados:

![Fluxo de dados para processamento de registos de fluxo de NSG](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Regiões apoiadas: NSG 

Pode utilizar análises de tráfego para NSGs em qualquer uma das seguintes regiões apoiadas:

* Canadá Central
* E.U.A. Centro-Oeste
* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Centro-Norte
* E.U.A. Centro-Sul
* E.U.A. Central
* E.U.A. Oeste
* E.U.A. Oeste 2
* França Central
* Europa Ocidental
* Europa do Norte
* Sul do Brasil
* Oeste do Reino Unido
* Sul do Reino Unido
* Leste da Austrália
* Sudeste da Austrália
* Ásia Oriental
* Sudeste Asiático
* Coreia do Sul Central
* Índia Central
* Sul da Índia
* Leste do Japão 
* Oeste do Japão
* US Gov - Virginia
* Leste da China 2

## <a name="supported-regions-log-analytics-workspaces"></a>Regiões apoiadas: Log Analytics Workspaces

O espaço de trabalho log Analytics deve existir nas seguintes regiões:
* Canadá Central
* E.U.A. Centro-Oeste
* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Centro-Norte
* E.U.A. Centro-Sul
* E.U.A. Central
* E.U.A. Oeste
* E.U.A. Oeste 2
* E.U.A. Central
* França Central
* Europa Ocidental
* Europa do Norte
* Sul do Brasil
* Oeste do Reino Unido
* Sul do Reino Unido
* Leste da Austrália
* Sudeste da Austrália
* Ásia Oriental
* Sudeste Asiático
* Coreia do Sul Central
* Índia Central
* Leste do Japão
* US Gov - Virginia
* Leste da China 2

## <a name="prerequisites"></a>Pré-requisitos

### <a name="user-access-requirements"></a>Requisitos de acesso ao utilizador

A sua conta deve ser membro de uma das seguintes [funções azure incorporadas:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

|Modelo de implementação   | Função                   |
|---------          |---------               |
|Resource Manager   | Proprietário                  |
|                   | Contribuinte            |
|                   | Leitor                 |
|                   | Colaborador de rede    |

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

Para obter informações sobre como verificar as permissões de acesso do utilizador, consulte [o Traffic analytics FAQ](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Ativar o Observador de Rede

Para analisar o tráfego, é necessário ter um observador de rede existente, ou [ativar um observador](network-watcher-create.md) de rede em cada região para o qual tem NSGs para os qual pretende analisar o tráfego. A análise do tráfego pode ser ativada para os NSGs alojados em qualquer uma das [regiões apoiadas](#supported-regions-nsg).

### <a name="select-a-network-security-group"></a>Selecione um grupo de segurança de rede

Antes de permitir o registo de fluxo sanções nsg, deve ter um grupo de segurança de rede para registar fluxos. Se não tiver um grupo de segurança de rede, consulte [Criar um grupo](../virtual-network/manage-network-security-group.md#create-a-network-security-group) de segurança de rede para criar um.

No lado esquerdo do portal Azure, selecione **Monitor**, em **seguida, observador**de rede, e, em seguida, selecione registos de **fluxo NSG**. Selecione o grupo de segurança da rede para o que pretende ativar um registo de fluxo NSG, como mostra a seguinte imagem:

![Seleção de NSGs que requerem a ativação do registo de fluxo sNG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Se tentar ativar a análise de tráfego para um NSG que esteja hospedado em qualquer região que não as [regiões apoiadas,](#supported-regions-nsg)recebe um erro "Não encontrado".

## <a name="enable-flow-log-settings"></a>Ativar definições de registo de fluxo

Antes de ativar as definições de registo de fluxo, deve completar as seguintes tarefas:

Registe o fornecedor Azure Insights, caso ainda não esteja registado para a sua subscrição:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Se ainda não tem uma conta de Armazenamento Azure para armazenar registos de fluxo snSG, tem de criar uma conta de armazenamento. Pode criar uma conta de armazenamento com o comando que se segue. Antes de executar o comando, substitua `<replace-with-your-unique-storage-account-name>` por um nome único em todos os locais do Azure, entre 3-24 caracteres de comprimento, usando apenas números e letras minúsculas. Também pode alterar o nome do grupo de recursos, se necessário.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Selecione as seguintes opções, como mostrado na imagem:

1. Selecione *on* for **Status**
2. Selecione *versão 2* para **versão Flow Logs**. A versão 2 contém estatísticas de sessão de fluxo (bytes e pacotes)
3. Selecione uma conta de armazenamento existente para armazenar os registos de fluxo. Se quiser armazenar os dados para sempre, detete o valor para *0*. Incorre nas taxas de armazenamento azure para a conta de armazenamento. Certifique-se de que o seu armazenamento não tem "Data Lake Storage Gen2 Hierarchical Namespace Enabled" definido como verdadeiro. Além disso, os registos de fluxo NSG não podem ser armazenados numa Conta de Armazenamento com uma Firewall. 
4. Detete **a Retenção** no número de dias para os dias que pretende armazenar.
> [!IMPORTANT]
> Atualmente, existe um problema em que os registos de fluxo do grupo de segurança de [rede (NSG)](network-watcher-nsg-flow-logging-overview.md) para O Observador de Rede não são automaticamente eliminados do armazenamento blob com base em definições de política de retenção. Se tiver uma política de retenção não nula existente, recomendamos que apague periodicamente as bolhas de armazenamento que já passaram do seu período de retenção para evitar quaisquer encargos incorridos. Para obter mais informações sobre como eliminar o blog de armazenamento de registo de fluxo NSG, consulte [Delete NSG flow log storage blobs](network-watcher-delete-nsg-flow-log-blobs.md).

5. Selecione *para* o **estado de análise**de tráfego .
6. Selecione intervalo de processamento. Com base na sua escolha, os registos de fluxo serão recolhidos a partir da conta de armazenamento e processados pela Traffic Analytics. Pode escolher o intervalo de processamento de cada 1 hora ou a cada 10 minutos. 
7. Selecione um espaço de trabalho existente no Log Analytics (OMS) ou selecione **Create New Workspace** para criar um novo. Um espaço de trabalho log Analytics é usado pela Traffic Analytics para armazenar os dados agregados e indexados que são então usados para gerar a análise. Se selecionar um espaço de trabalho existente, deve existir numa das [regiões apoiadas](#supported-regions-log-analytics-workspaces) e ter sido atualizado para a nova linguagem de consulta. Se não quiser atualizar um espaço de trabalho existente, ou não tiver um espaço de trabalho numa região apoiada, crie um novo espaço de trabalho. Para mais informações sobre idiomas de consulta, consulte [o upgrade do Azure Log Analytics para uma nova pesquisa](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)de registo .

> [!NOTE]
>O espaço de trabalho de análise de registoque que acolhe a solução de análise de tráfego e os NSGs não têm de estar na mesma região. Por exemplo, você pode ter análises de tráfego em um espaço de trabalho na região da Europa Ocidental, enquanto você pode ter NSGs nos EUA Leste e Oeste dos EUA. Vários NSGs podem ser configurados no mesmo espaço de trabalho.

8. Selecione **Guardar**.

    ![Seleção de conta de armazenamento, espaço de trabalho log Analytics e habilitação traffic analytics](./media/traffic-analytics/ta-customprocessinginterval.png)

Repita os passos anteriores para quaisquer outros NSGs para os quais deseje ativar a análise do tráfego. Os dados dos registos de fluxo são enviados para o espaço de trabalho, por isso certifique-se de que as leis e regulamentos locais do seu país permitem o armazenamento de dados na região onde existe o espaço de trabalho. Se tiver definido diferentes intervalos de processamento para diferentes NSGs, os dados serão recolhidos em intervalos diferentes. Por exemplo: Pode optar por ativar o intervalo de processamento de 10 minutos para VNETs críticos e 1 hora para VNETs não críticos.

Também pode configurar análises de tráfego utilizando o [cmdlet Set-AzNetworkWatcherConfigFlowPowerShell](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) powerShell no Azure PowerShell. Execute `Get-Module -ListAvailable Az` para encontrar a sua versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Ver análise de tráfego

No lado esquerdo do portal, selecione **Todos os serviços**e, em seguida, introduza o *Monitor* na caixa **de filtro.** Quando o **Monitor** aparecer nos resultados da pesquisa, selecione-o. Para começar a explorar a análise de tráfego e as suas capacidades, selecione **O Observador da Rede,** em **seguida, Traffic Analytics**.

![Acesso ao dashboard Traffic Analytics](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

O dashboard pode demorar até 30 minutos a aparecer pela primeira vez, porque o Traffic Analytics deve primeiro agregar dados suficientes para que obtenha insights significativos, antes de poder gerar quaisquer relatórios.

## <a name="usage-scenarios"></a>Cenários de utilização

Algumas das ideias que pode querer obter depois do Traffic Analytics estiver totalmente configurado, são as seguintes:

### <a name="find-traffic-hotspots"></a>Encontre pontos de tráfego

**Procurar**

- Quais os anfitriões, subredes e redes virtuais que estão a enviar ou a receber mais tráfego, atravessando o tráfego máximo malicioso e bloqueando fluxos significativos?
    - Verifique a tabela comparativa para o hospedeiro, subnet e rede virtual. Compreender quais os anfitriões, subredes e redes virtuais que estão a enviar ou a receber mais tráfego pode ajudá-lo a identificar os anfitriões que estão a processar mais tráfego, e se a distribuição de tráfego é feita corretamente.
    - Pode avaliar se o volume de tráfego é apropriado para um hospedeiro. O volume de tráfego é normal, ou merece mais investigação?
- Quanto tráfego de entrada/saída existe?
    -   Espera-se que o hospedeiro receba mais tráfego de entrada do que saída, ou vice-versa?
- Estatísticas do tráfego bloqueado.
    - Porque é que um hospedeiro está a bloquear um volume significativo de tráfego benigno? Este comportamento requer mais investigação e provavelmente otimização da configuração
- Estatísticas do tráfego malicioso permitido/bloqueado
  - Porque é que é permitido um hospedeiro que recebe tráfego malicioso e porque é que é permitido um fluxo de fonte maliciosa? Este comportamento requer mais investigação e provavelmente otimização da configuração.

    Selecione **Ver tudo,** em **Anfitrião,** como mostra a seguinte imagem:

    ![Dashboard mostrando anfitrião com a maioria dos detalhes de tráfego](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- A imagem seguinte mostra a tendência do tempo para os cinco principais anfitriões falantes e os detalhes relacionados com o fluxo (permitidos – entrada/saída e negados - fluxos de entrada/saída) para um hospedeiro:

    ![Top 5 tendência anfitriã mais falante](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Procurar**

- Quais são os pares de anfitriões mais conversadores?
    - Comportamento esperado como comunicação frontal ou back-end ou comportamento irregular, como tráfego de internet de back-end.
- Estatísticas do tráfego permitido/bloqueado
    - Por que um hospedeiro está permitindo ou bloqueando volume de tráfego significativo
- Protocolo de aplicação mais utilizado com mais frequência entre a maioria dos pares de anfitriões:
    - Estas aplicações são permitidas nesta rede?
    - As aplicações estão configuradas corretamente? Estão a usar o protocolo adequado para a comunicação? Selecione **Ver tudo** sob **conversação frequente,** como mostrar na seguinte imagem:

        ![Dashboard mostrando a conversa mais frequente](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- A imagem seguinte mostra a tendência do tempo para as cinco melhores conversas e os detalhes relacionados com o fluxo, tais como fluxos de entrada e saída permitidos e negados para um par de conversações:

    ![Os cinco principais detalhes e tendências de conversação tagarela](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Procurar**

- Qual o protocolo de aplicação mais utilizado no seu ambiente, e qual o que os pares de anfitriões estão a usar mais o protocolo de aplicação?
    - Estas aplicações são permitidas nesta rede?
    - As aplicações estão configuradas corretamente? Estão a usar o protocolo adequado para a comunicação? O comportamento esperado são portos comuns como 80 e 443. Para uma comunicação padrão, se forem apresentadas portas incomuns, podem necessitar de uma alteração de configuração. Selecione **Ver tudo** sob **a porta aplicação,** na seguinte imagem:

        ![Dashboard mostrando protocolos de aplicação de topo](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- As seguintes imagens mostram o tempo de tendência para os cinco principais protocolos L7 e os detalhes relacionados com o fluxo (por exemplo, fluxos permitidos e negados) para um protocolo L7:

    ![Detalhes e tendências dos protocolos de camada 7](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Detalhes de fluxo para protocolo de aplicação em pesquisa de registo](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Procurar**

- Tendências de utilização da capacidade de um gateway VPN no seu ambiente.
    - Cada VPN SKU permite uma certa largura de banda. Os portões VPN estão subutilizados?
    - Os seus portais estão a atingir a capacidade? Deve atualizar para o próximo SKU mais alto?
- Quais são os anfitriões mais conversadores, através do qual a porta VPN, sobre qual porto?
    - Este padrão é normal? Selecione **Ver tudo** sob **gateway VPN,** como mostrado na seguinte imagem:

        ![Dashboard mostrando as principais ligações VPN ativas](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- A imagem seguinte mostra a tendência do tempo para a utilização da capacidade de um Gateway VPN Azure e os detalhes relacionados com o fluxo (tais como fluxos e portas permitidos):

    ![Tendência de utilização de gateway VPN e detalhes de fluxo](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualizar a distribuição de tráfego por geografia

**Procurar**

- Distribuição de tráfego por centro de dados, tais como fontes de tráfego de topo para um datacenter, redes de topo fraudulentas conversando com o data center, e protocolos de aplicação de conversões de topo.
  - Se observar mais carga num centro de dados, pode planear uma distribuição eficiente do tráfego.
  - Se as redes fraudulentas estiverem a conversar no centro de dados, corrija as regras do NSG para bloqueá-las.

    Selecione **ver mapa** sob **o seu ambiente,** como mostrado na seguinte imagem:

    ![Dashboard mostrando distribuição de tráfego](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- O geo-mapa mostra a fita superior para a seleção de parâmetros tais como centros de dados (Deployed/No-deployment/Ative/Inative/Traffic Analytics Enabled/Traffic Analytics Não Ativado) e países/regiões que contribuem com tráfego benigno/malicioso para a implantação ativa:

    ![Visão de mapa geo mostrando implantação ativa](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- O geo-mapa mostra a distribuição de tráfego para um centro de dados de países/regiões e continentes que lhe comunicam em linhas coloridas azuis (benignas) e vermelhas (tráfego malicioso):

    ![Geo map view mostrando distribuição de tráfego para países/regiões e continentes](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Detalhes de fluxo para distribuição de tráfego na pesquisa de registo](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualizar a distribuição de tráfego por redes virtuais

**Procurar**

- Distribuição de tráfego por rede virtual, topologia, principais fontes de tráfego para a rede virtual, redes fraudulentas de topo conversando para a rede virtual, e protocolos de aplicação de topo conversindo.
  - Saber qual rede virtual está a conversar com a rede virtual. Se a conversa não for esperada, pode ser corrigida.
  - Se as redes fraudulentas estiverem a conversar com uma rede virtual, pode corrigir as regras do NSG para bloquear as redes fraudulentas.
 
    Selecione **Ver VNets** sob **o seu ambiente,** como mostra a seguinte imagem:

    ![Dashboard mostrando distribuição de rede virtual](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- A Topologia da Rede Virtual mostra a fita superior para a seleção de parâmetros como a de uma rede virtual (Conexões inter virtuais/ativas/inativas), Conexões Externas, Fluxos Ativos e fluxos maliciosos da rede virtual.
- Pode filtrar a Topologia da Rede Virtual com base em subscrições, espaços de trabalho, grupos de recursos e intervalo de tempo. Filtros adicionais que o ajudam a compreender o fluxo são: Type de fluxo (InterVNet, IntraVNET, e assim por diante), Direção de Fluxo (Entrada, Saída), Estado de Fluxo (Permitido, Bloqueado), VNETs (Direcionado e Conectado), Tipo de Ligação (Peering ou Gateway - P2S e S2S) e NSG. Utilize estes filtros para se concentrar em VNets que pretende examinar em detalhe.
- A Topologia da Rede Virtual mostra a distribuição de tráfego a uma rede virtual no que diz respeito aos fluxos (Permitido/Bloqueado/Entrada/Saída/Benigno/Malicioso), protocolo de aplicação e grupos de segurança de rede, por exemplo:

    ![Topologia de rede virtual que mostra detalhes de distribuição de tráfego e fluxo](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologia de rede virtual mostrando nível superior e mais filtros](./media/traffic-analytics/virtual-network-filters.png)

    ![Detalhes de fluxo para distribuição de tráfego de rede virtual na pesquisa de registo](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Procurar**

- Distribuição de tráfego por sub-rede, topologia, principais fontes de tráfego para a subnet, redes de topo desonestos conversando com a subnet, e protocolos de aplicação de conversões de topo.
    - Saber qual sub-rede está a conversar com a subrede. Se vir conversas inesperadas, pode corrigir a sua configuração.
    - Se as redes fraudulentas estiverem a conversar com uma subnet, é possível corrigi-la configurando regras de NSG para bloquear as redes fraudulentas.
- A Topologia subnets mostra a fita superior para a seleção de parâmetros como subnet Ativo/Inativo, Conexões Externas, Fluxos Ativos e Fluxos Maliciosos da subnet.
- A Subnet Topology mostra a distribuição de tráfego a uma rede virtual no que diz respeito aos fluxos (Permitido/Bloqueado/Entrada/Saída/Benigno/Malicioso), protocolo de aplicação e NSGs, por exemplo:

    ![Topologia subnet mostrando distribuição de tráfego uma subnet de rede virtual no que diz respeito aos fluxos](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Procurar**

Distribuição de tráfego por Gateway & Load Balancer, topologia, fontes de tráfego de topo, redes de topo fraudulentas conversando com o gateway & Load Balancer de aplicação e protocolos de aplicação de topo. 
    
 - Saber qual a subnet a conversinar com que gateway de aplicação ou Balancer de Carga. Se observar conversas inesperadas, pode corrigir a sua configuração.
 - Se as redes fraudulentas estiverem a conversar com um gateway de aplicação ou um Balancer de Carga, é possível corrigi-lo configurando regras de NSG para bloquear as redes fraudulentas. 

    ![subnet-topologia-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Ver portas e máquinas virtuais que recebem tráfego da internet

**Procurar**

- Que portas abertas estão a conversar pela internet?
  - Se portas inesperadas forem encontradas abertas, pode corrigir a sua configuração:

    ![Dashboard mostrando portas recebendo e enviando tráfego para a internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Detalhes dos portos e anfitriões de destino Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Procurar**

Tem tráfego malicioso no seu ambiente? De onde vem? Para onde está destinado?

![Tráfego malicioso flui detalhes na pesquisa de registo](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualizar as tendências das regras NSG/NSG

**Procurar**

- Quais as regras NSG/NSG que têm mais acessos em gráfico comparativo com distribuição de fluxos?
- Quais são os principais pares de conversas de origem e destino por regras NSG/NSG?

    ![Dashboard mostrando NSG atinge estatísticas](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- As seguintes imagens mostram o tempo a tendência para os acessos das regras do NSG e detalhes de fluxo de destino de origem para um grupo de segurança de rede:

  - Detete rapidamente quais nsgs e regras de NSG estão atravessando fluxos maliciosos e quais são os principais endereços IP maliciosos que acedem ao seu ambiente em nuvem
  - Identifique quais as regras NSG/NSG que permitem/bloquear tráfego significativo de rede
  - Selecione filtros superiores para inspeção granular de regras NSG ou NSG

    ![Tendência de tempo para a regra nsg atinge e as melhores regras da NSG](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![As principais regras do NSG detalham as estatísticas na pesquisa de registos](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Para obter respostas a perguntas frequentes, consulte [o Traffic analytics FAQ](traffic-analytics-faq.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber como habilitar os logs de fluxo, consulte [habilitando o log de fluxo do NSG](network-watcher-nsg-flow-logging-portal.md).
- Para compreender o esquema e processar detalhes da Análise de Tráfego, consulte o esquema de [análise de tráfego.](traffic-analytics-schema.md)
