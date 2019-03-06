---
title: Análise de tráfego do Azure | Documentos da Microsoft
description: Saiba como analisar os registos da fluxo de grupo de segurança de rede do Azure com a análise de tráfego.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: yagup;jdial
ms.openlocfilehash: bc738dd93a43a1a3610323b9dd05abfd5b5c0091
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408807"
---
# <a name="traffic-analytics"></a>Análise de Tráfego

Análise de tráfego é uma solução baseada na cloud que fornece visibilidade em atividades de utilizador e da aplicação em redes na cloud. Análise de tráfego analisa registos fluxo (NSG) do grupo de segurança de rede de observador de rede para fornecer informações sobre o fluxo de tráfego na cloud do Azure. Com a análise de tráfego, pode:

- Visualize a atividade de rede nas suas subscrições do Azure e identifique hotspots.
- Identificar ameaças de segurança para e proteger a sua rede, com informações como portas abertas, aplicações de acesso à internet e máquinas virtuais (VM) de ligação não autorizados redes a tentar.
- Compreenda os padrões de fluxo de tráfego entre regiões do Azure e a internet para otimizar a sua implementação de rede para o desempenho e a capacidade.
- Identifique os erros de configuração de rede que leva a ligações falhadas na sua rede.

## <a name="why-traffic-analytics"></a>Análise de tráfego por que?

É fundamental para monitorizar, gerir e saber a sua própria rede para não comprometida de segurança, conformidade e o desempenho. Saber o seu próprio ambiente é de fundamental importância para proteger e otimizá-lo. Muitas vezes precisa saber o estado atual da rede, o que estiver a ligar, onde eles estão se conectando, que as portas estão abertas para a internet, o comportamento de rede esperado, o comportamento de rede irregulares, e aumentos repentinos no tráfego.

Redes em nuvem são diferentes de redes de empresa no local, onde tem netflow ou protocolo equivalente com capacidade de routers e comutadores, que fornecem a capacidade de recolher o tráfego de rede IP à medida que ele entra ou sai de uma interface de rede. Análise de dados de fluxo de tráfego, pode criar uma análise de fluxo de tráfego de rede e de volume.

Redes virtuais do Azure têm registos de fluxo NSG, o que lhe fornecem informações sobre a entrada e o tráfego de IP de saída através de um grupo de segurança de rede associados a sub-redes, VMs ou interfaces de rede individuais. Ao analisar registos de fluxo NSG não processados e a inserção de inteligência de segurança, a topologia e geografia, tráfego de análise pode fornecer informações sobre o fluxo de tráfego no seu ambiente. Análise de tráfego fornece informações como anfitriões mais comunicar, protocolos de aplicação mais comunicar, mais conversação de anfitrião de pares, tráfego permitido/bloqueado, o tráfego de entrada/saída, abrir portas de internet, regras de bloqueio mais, tráfego distribuição por centro de dados do Azure, rede virtual, sub-redes, ou, não autorizados redes.

## <a name="key-components"></a>Componentes principais

- **O grupo de segurança de rede (NSG)**: Contém uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos ligados a uma rede Virtual do Azure. Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou a interfaces de rede individuais (NIC) ligadas a VMs (Resource Manager). Para obter mais informações, consulte [descrição geral de grupo de segurança de rede](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Registos de fluxo de grupo (NSG) de segurança de rede**: Permitem-lhe ver informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede. Fluxo NSG registos são escritos no formato json e mostram os fluxos de saída e entrados numa base por regra, que a NIC, o fluxo de mensagens em fila aplica-se a informações de cinco cadeias de identificação sobre o fluxo (endereço IP de origem/destino, porta de origem/destino e protocolo) e se o tráfego foi permitido ou negado. Para obter mais informações sobre os registos de fluxo do NSG, consulte [registos de fluxo NSG](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: Um serviço do Azure que recolhe dados de monitorização e armazena os dados num repositório central. Estes dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos pela API do Azure. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação. Monitorização de aplicações, tais como análise de tráfego e o monitor de desempenho de rede é criada usando logs de Monitor do Azure como uma base. Para obter mais informações, consulte [registos do Azure Monitor](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Área de trabalho de análise de registo**: Uma instância de registos do Azure Monitor, onde os dados relativos a uma conta do Azure, são armazenados. Para obter mais informações sobre áreas de trabalho do Log Analytics, consulte [criar uma área de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Observador de rede**: Um serviço regional que lhe permite monitorizar e diagnosticar condições ao nível de um cenário de rede no Azure. Pode ativar registos de fluxo NSG e desativar com o observador de rede. Para obter mais informações, consulte [observador de rede](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Como funciona a análise de tráfego

Análise de tráfego examina os registos de fluxo NSG não processados e captura os registos reduzidos através da agregação fluxos comuns entre o mesmo endereço IP de origem, endereço IP de destino, porta de destino e protocolo. Por exemplo, o Host 1 (endereço IP: 10.10.10.10) comunicar com o anfitrião 2 (endereço IP: 10.10.20.10), 100 vezes num período de 1 hora, com a porta (por exemplo, 80) e protocolo (por exemplo, http). O log reduzido possui uma entrada, o que o anfitrião 1 e 2 do anfitrião comunicadas 100 vezes num período de 1 hora, utilizando a porta *80* e o protocolo *HTTP*, em vez de ter 100 entradas. Registos reduzidos são melhorados com geografia, segurança e informações de topologia e, em seguida, armazenados numa área de trabalho do Log Analytics. A imagem seguinte mostra o fluxo de dados:

![Fluxo de dados para processar registos de fluxo NSG](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Regiões suportadas

Pode utilizar a análise de tráfego para NSGs em qualquer uma das seguintes regiões suportadas:

* Canadá Central
* EUA Centro-Oeste
* EUA Leste
* EUA Leste 2
* EUA Centro-Norte
* EUA Centro-Sul
* EUA Central
* EUA Oeste
* EUA Oeste 2
* Europa Ocidental
* Europa do Norte
* Sul do Brasil
* Reino Unido Oeste
* Reino Unido Sul
* Leste da Austrália
* Sudeste da Austrália
* Sudeste Asiático
* Índia Central
* Sul da Índia
* Leste do Japão 
* Gov (US) - Virginia

A área de trabalho do Log Analytics têm de existir nas seguintes regiões:
* Canadá Central
* EUA Centro-Oeste
* EUA Leste
* Europa Ocidental
* Reino Unido Sul
* Sudeste da Austrália
* Sudeste Asiático
* Índia Central
* Leste do Japão
* Gov (US) - Virginia

## <a name="prerequisites"></a>Pré-requisitos

### <a name="user-access-requirements"></a>Requisitos de acesso de utilizador

Sua conta tem de ser um membro de um do Azure seguinte [funções incorporadas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Modelo de implementação   | Função                   |
|---------          |---------               |
|Resource Manager   | Proprietário                  |
|                   | Contribuinte            |
|                   | Leitor                 |
|                   | Contribuinte de Rede    |

Se a sua conta não está atribuída a uma das funções incorporadas, tem de ser atribuído a um [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) atribuída as seguintes ações, ao nível da subscrição:

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

Para obter informações sobre como verificar as permissões de acesso de utilizador, consulte [FAQ da análise de tráfego](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Ativar o Observador de Rede

Para analisar o tráfego, tem de ter um observador de rede existente, ou [ativar um observador de rede](network-watcher-create.md) em cada região que tem de NSGs que pretende analisar o tráfego para. Análise de tráfego pode ser ativada para NSGs alojados em qualquer uma da [regiões suportadas](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Voltar a registar o fornecedor de recursos de rede

Antes de poder utilizar a análise de tráfego, tem de voltar a registar o fornecedor de recursos de rede. Clique em **experimentar** na caixa de código seguinte para abrir o Azure Cloud Shell. O Cloud Shell automaticamente iniciar a sua sessão na sua subscrição do Azure. Depois do Cloud Shell é aberto, introduza o seguinte comando para voltar a registar o fornecedor de recursos de rede:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Selecione um grupo de segurança de rede

Antes de ativar o registo de fluxo NSG, tem de ter um grupo de segurança de rede para iniciar sessão fluxos para. Se não tiver um grupo de segurança de rede, consulte [criar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md#create-a-network-security-group) para criar um.

No lado esquerdo do portal do Azure, selecione **Monitor**, em seguida, **observador de rede**e, em seguida, selecione **registos de fluxo NSG**. Selecione o grupo de segurança de rede que pretende ativar um registo de fluxo NSG para, conforme mostrado na imagem seguinte:

![Seleção de NSGs que requerem a ativação do registo de fluxo NSG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Se tentar ativar a análise de tráfego para um NSG que está alojado em qualquer região que o [regiões suportadas](#supported-regions), receberá um erro "Não encontrada".

## <a name="enable-flow-log-settings"></a>Ativar as definições de registo de fluxo

Antes de ativar as definições de registos de fluxo, tem de concluir as seguintes tarefas:

Registe o fornecedor de informações do Azure, se já não está registado para a sua subscrição:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Se ainda não tiver uma conta de armazenamento do Azure para armazenar o fluxo NSG inicia sessão, tem de criar uma conta de armazenamento. Pode criar uma conta de armazenamento com o comando seguinte. Antes de executar o comando, substitua `<replace-with-your-unique-storage-account-name>` com um nome que seja exclusivo em todas as localizações do Azure, entre 3 e 24 carateres de comprimento, a utilizar apenas números e letras minúsculas. Também pode alterar o nome do grupo de recursos, se necessário.

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Selecione as opções seguintes, conforme mostrado na imagem:

1. Selecione *nos* para **Estado**
2. Selecione uma conta de armazenamento existente para armazenar os registos de fluxo no. Se quiser armazenar os dados para sempre, defina o valor como *0*. Incorrem em taxas de armazenamento do Azure para a conta de armazenamento.
3. Definir **retenção** para o número de dias que pretende armazenar os dados de.
4. Selecione *nos* para **estado de análise de tráfego**.
5. Selecione uma área de trabalho do Log Analytics existente ou selecione **criar nova área de trabalho** para criar um novo. Uma área de trabalho do Log Analytics é utilizada pela análise de tráfego para armazenar os dados agregados e indexados que, em seguida, são utilizados para gerar a análise. Se selecionar uma área de trabalho existente, tem de existir uma das regiões suportadas e foram atualizados para a nova linguagem de consulta. Se não desejar atualizar de uma área de trabalho existente ou não tem uma área de trabalho numa região suportada, crie um novo. Para obter mais informações sobre as linguagens de consulta, consulte [do Azure Monitor registos de atualização para a nova pesquisa de registo](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    A área de trabalho do Log Analytics que aloja a solução de análise de tráfego e os NSGs não tem de ser na mesma região. Por exemplo, pode ter a análise de tráfego numa área de trabalho na região da Europa Ocidental, embora possa ter NSGs nos E.U.A. leste e E.U.A. oeste. Vários NSGs podem ser configurados na mesma área de trabalho.
6. Selecione **Guardar**.

    ![Seleção de conta de armazenamento, área de trabalho do Log Analytics e ativação de análise de tráfego](./media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

Repita os passos anteriores para quaisquer outros NSGs para o qual pretende ativar a análise de tráfego para. Dados dos registos de fluxo são enviados para a área de trabalho, por isso, certifique-se de que as leis e regulamentações locais no seu país permitem o armazenamento de dados na região onde a área de trabalho existe.

Também pode configurar a análise de tráfego com o [Set-AzureRmNetworkWatcherConfigFlowLog](/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog) cmdlet do PowerShell no AzureRm PowerShell versão do módulo 6.2.1 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para encontrar a versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="view-traffic-analytics"></a>Ver análise de tráfego

Do lado esquerdo do portal, selecione **todos os serviços**, em seguida, introduza *Monitor* no **filtro** caixa. Quando **Monitor** aparecer nos resultados da pesquisa, selecione. Para começar a explorar a análise de tráfego e seus recursos, selecione **observador de rede**, em seguida, **análise de tráfego**.

![Aceder ao dashboard de análise de tráfego](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

O dashboard pode demorar até 30 minutos a aparecer na primeira vez, porque a análise de tráfego primeiro tem de agregar dados suficientes para o mesmo derivar informações significativas, antes de pode gerar relatórios.

## <a name="usage-scenarios"></a>Cenários de utilização

Algumas das informações que talvez queira obter após a análise de tráfego é totalmente, são as seguintes:

### <a name="find-traffic-hotspots"></a>Encontrar hotspots de tráfego

**Procurar**

- Os anfitriões, sub-redes e redes virtuais são enviar ou receber o tráfego a maioria dos, atravessando máximos de tráfego malicioso e fluxos significativos de bloqueio?
    - Verifique o gráfico comparativa para anfitriões, sub-rede e rede virtual. Compreender os anfitriões, sub-redes e redes virtuais estão a enviar ou receber mais tráfego pode ajudar a identificar os anfitriões que estão a processar mais tráfego, e se a distribuição de tráfego é feita de maneira adequada.
    - Pode avaliar se o volume de tráfego é adequado para um anfitrião. É o volume do comportamento normal de tráfego ou merecem mais investigação?
- Como grande parte do tráfego de entrada/saída existe?
    -   O anfitrião deve receber a entrada mais tráfego de saída, ou vice-versa?
- Estatísticas de tráfego bloqueado.
    - Por que motivo é que um anfitrião está bloqueando um volume significativo de tráfego benigno? Esse comportamento requer mais investigação e provavelmente a Otimização da configuração
- Estatísticas de tráfego malicioso permitido/bloqueado
    - Por que é um anfitrião recebendo tráfego malicioso e por que os fluxos de origem malicioso é permitido? Esse comportamento requer mais investigação e provavelmente a Otimização da configuração.

    Selecione **ver tudo**, em **anfitrião**, conforme mostrado na imagem seguinte:

    ![Dashboard utilizar que apresenta o anfitrião com a maioria dos detalhes de tráfego](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- A imagem seguinte mostra a tendência de tempo para os anfitriões de falar cinco principais e os detalhes relacionados com o flow (permitidos – de entrada/saída e negadas - fluxos de entrada/saída) para um anfitrião:

    ![Tendência de anfitrião de falar com a maioria de cinco principais](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Procurar**

- Quais são os pares de host mais conversação?
    - Comportamento esperado, como a comunicação de front-end ou back-end ou comportamento irregular, como o tráfego de internet de back-end.
- Estatísticas de tráfego permitido/bloqueado
    - Por que um anfitrião é permitir ou bloquear o volume de tráfego significativo
- Protocolo de aplicação entre a maioria dos pares de anfitriões de conversação de usados com mais freqüência:
    - Esses aplicativos são permitidos nesta rede?
    - Os aplicativos estão configurados corretamente? Elas estão usando o protocolo apropriado para a comunicação? Selecione **ver tudo** sob **frequentes conversação**, como mostrar na imagem seguinte:

        ![Dashboard utilizar que apresenta a conversa mais frequente](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- A imagem seguinte mostra a tendência de tempo para as conversas de cinco principais e os detalhes relacionados com o fluxo, tal como autorizadas e negadas fluxos de entrada e saídos para um par de conversa:

    ![Principais cinco detalhes de conversação chatty e tendência](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Procurar**

- O protocolo de aplicação é mais utilizado no seu ambiente e os pares de anfitriões de conversação estão a utilizar o protocolo de aplicação mais?
    - Esses aplicativos são permitidos nesta rede?
    - Os aplicativos estão configurados corretamente? Elas estão usando o protocolo apropriado para a comunicação? Comportamento esperado é portas comuns, como 80 e 443. Para a comunicação padrão, se são apresentadas as portas invulgares, eles podem exigir uma alteração de configuração. Selecione **ver tudo** sob **porta da aplicação**, na imagem seguinte:

        ![Dashboard utilizar que apresenta protocolos de aplicação principais](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- O tempo de mostrar imagens seguintes tendências para os protocolos L7 de cinco principais e os detalhes relacionados com o fluxo (por exemplo e rejeições fluxos) para um protocolo L7:

    ![Cinco principais de camada 7 detalhes de protocolos e tendência](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Detalhes para o protocolo de aplicação na pesquisa de registos do fluxo](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Procurar**

- Tendências de utilização da capacidade de um gateway VPN no seu ambiente.
    - Cada SKU de VPN permite que uma determinada quantidade de largura de banda. Os gateways de VPN estão subutilizados?
    - Os gateways Contatando capacidade? Deve atualizar para o SKU superior seguinte?
- Quais são os anfitriões de conversação mais como, por meio de que o gateway de VPN, através da qual porta?
    - Este padrão é normal? Selecione **ver tudo** sob **gateway de VPN**, conforme mostrado na imagem seguinte:

        ![Dashboard utilizar que apresenta ligações de VPN ativas principais](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- A imagem seguinte mostra a tendência de tempo para a utilização da capacidade de um Gateway de VPN do Azure e os detalhes relacionados com o fluxo (por exemplo, fluxos permitidos e portas):

    ![VPN gateway tendência e fluxo de detalhes de utilização](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualizar a distribuição de tráfego por geografia

**Procurar**

- Distribuição de tráfego por centro de dados, como fontes principais de tráfego para um centro de dados, redes de adesão principais conversação com o Centro de dados e superior conversação protocolos de aplicação.
    - Se observar mais carga no Centro de dados, pode planear para a distribuição de tráfego eficiente.
    - Se a redes de adesão são conversação no Centro de dados, corrigir, em seguida, as regras do NSG para bloqueá-los.

    Selecione **Ver mapa** sob **seu ambiente**, conforme mostrado na imagem seguinte:

    ![Distribuição de tráfego apresentando do dashboard](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- O mapa geográfico mostra a faixa de opções principal para a seleção de parâmetros, tais como a centros de dados (análise de implementado/não-implementação/ativo/inativo/tráfego ativado/análise de tráfego não ativada) e os países que contribuem com tráfego Benign/maliciosos para o Active Directory implementação:

    ![Vista do mapa geográfico que mostra a implementação ativa](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- O mapa geográfico mostra a distribuição de tráfego para um centro de dados de países e continentes comunicar a ele, em vermelho (tráfego malicioso) e azul (tráfego benigno), colorido linhas:

    ![Vista do mapa geográfico que mostra a distribuição de tráfego em países e continentes](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Detalhes para a distribuição de tráfego na pesquisa de registos do fluxo](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualizar a distribuição de tráfego através de redes virtuais

**Procurar**

- Distribuição de tráfego por rede virtual, a topologia, fontes principais de tráfego para a rede virtual, redes de adesão principais conversação para a rede virtual e de cima conversação protocolos de aplicação.
    - Saber qual a rede virtual é conversação ao qual a rede virtual. Se não for esperada a conversa, ele poderá ser corrigido.
    - Se a redes de adesão são conversação com uma rede virtual, pode corrigir as regras do NSG para bloquear as redes de adesão.
 
    Selecione **vista de VNets** sob **seu ambiente**, conforme mostrado na imagem seguinte:

    ![Dashboard que mostra a distribuição de rede virtual](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- A topologia de rede Virtual mostra a faixa de opções principal para a seleção dos parâmetros como o da rede virtual (rede virtual do Inter ligações/Active/Inactive), ligações externas, fluxos ativos e fluxos maliciosos da rede virtual.
- Pode filtrar a topologia de rede Virtual com base nas subscrições, áreas de trabalho, grupos de recursos e intervalo de tempo. Filtros adicionais que o ajudam a compreender o fluxo são: Fluxo de tipo (inter-Vnet, IntraVNET etc), direção de fluxo de mensagens em fila (entrada, saída), estado do fluxo (permitido, bloqueado) VNETs (destino e ligado), o tipo de ligação (Peering ou Gateway - P2S e S2S) e do NSG. Utilize estes filtros para se concentrar em VNets que pretende examinar em detalhes.
- A topologia de rede Virtual mostra a distribuição de tráfego para uma rede virtual com respeito a fluxos (permitido/bloqueado/entrada/saída/Benign/maliciosos), protocolo de aplicação e grupos de segurança de rede, por exemplo:

    ![Topologia de rede virtual que mostra detalhes de distribuição e o fluxo de tráfego](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologia de rede virtual que mostra o nível superior e mais filtros](./media/traffic-analytics/virtual-network-filters.png)

    ![Detalhes para a distribuição de tráfego de rede virtual na pesquisa de registos do fluxo](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Procurar**

- Distribuição por sub-rede, a topologia, as fontes principais de tráfego para a sub-rede de tráfego redes de adesão principais conversação para a sub-rede e de cima conversação protocolos de aplicação.
    - Saber qual a sub-rede é conversação ao qual a sub-rede. Se vir conversas inesperadas, pode corrigir sua configuração.
    - Se a redes de adesão são conversação com uma sub-rede, é possível corrigi-lo ao configurar regras NSG para bloquear as redes de adesão.
- A topologia de sub-redes mostra a faixa de opções principal para a seleção de parâmetros, como Active/Inactive sub-rede, ligações externas, fluxos ativos e fluxos maliciosos da sub-rede.
- A topologia de sub-rede mostra a distribuição de tráfego para uma rede virtual com respeito a fluxos (permitido/bloqueado/entrada/saída/Benign/maliciosos), protocolo de aplicação e os NSGs, por exemplo:

    ![Topologia de sub-rede que mostra a distribuição de tráfego uma sub-rede de rede virtual com respeito a fluxos](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Procurar**

Distribuição de tráfego por gateway de aplicação & Balanceador de carga, topologia, as fontes principais de tráfego, principal não autorizados redes conversação para o gateway de aplicação & Balanceador de carga e de cima conversação protocolos de aplicação. 
    
 - Saber qual a sub-rede é conversação para que o gateway de aplicação ou Balanceador de carga. Se observar conversas inesperadas, pode corrigir sua configuração.
 - Se a redes de adesão são conversação com um gateway de aplicação ou Balanceador de carga, é possível corrigi-lo ao configurar regras NSG para bloquear as redes de adesão. 

    ![subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Ver as portas e as máquinas virtuais a receber o tráfego da internet

**Procurar**

- Que portas abertas são conversação através da internet?
    - Se inesperado de portas encontram-se aberto, pode corrigir a configuração:

    ![Dashboard que mostra as portas a receber e enviar o tráfego para a internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Detalhes de anfitriões e as portas de destino do Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Procurar**

Tem tráfego malicioso no seu ambiente? Onde é ele provenientes de? Onde é destinada a ela?

![Detalhes de fluxos de tráfego malicioso na pesquisa de registos](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualize as tendências de acertos de regras NSG/NSG

**Procurar**

- As regras NSG/NSG tem a maioria dos resultados comparativos gráfico com a distribuição de fluxos?
- Quais são os pares de conversação de origem e de destino principais por regras do NSG/NSG?

    ![Estatísticas de chegar a dashboard utilizar que apresenta o NSG](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- O tempo de mostrar imagens seguintes tendências para cliques das regras do NSG e os detalhes de fluxo de origem-destino de um grupo de segurança de rede:

    - Detetar rapidamente quais os NSGs e NSG regras são atravessando fluxos maliciosos e quais são o principal IP malicioso aborda a aceder ao seu ambiente na cloud
    - Identificar quais as regras do NSG/NSG estão permitindo/a bloquear o tráfego de rede significativo
    - Parte superior selecione filtros para inspeção granular de um NSG ou o NSG de regras

    ![Que mostra a tendência de tempo para correspondências de regras do NSG e regras principais de NSG](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Detalhes de estatísticas na pesquisa de registos de regras de NSG superior](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Para obter respostas às perguntas mais frequentes, consulte [FAQ da análise de tráfego](traffic-analytics-faq.md).
