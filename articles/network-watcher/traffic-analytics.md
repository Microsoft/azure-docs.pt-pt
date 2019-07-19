---
title: Análise de tráfego do Azure | Microsoft Docs
description: Saiba como analisar os logs de fluxo do grupo de segurança de rede do Azure com a análise de tráfego.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: kumud
ms.reviewer: yagup
ms.openlocfilehash: ca3174ad69185da88bf89c843f641dd2b20d9ac5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872480"
---
# <a name="traffic-analytics"></a>Análise de Tráfego

Análise de Tráfego é uma solução baseada em nuvem que fornece visibilidade da atividade de usuário e aplicativo em redes em nuvem. A análise de tráfego analisa os logs de fluxo do NSG (grupo de segurança de rede) do observador de rede para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Com a análise de tráfego, você pode:

- Visualize a atividade de rede em suas assinaturas do Azure e identifique pontos de acesso.
- Identifique ameaças à segurança e proteja sua rede, com informações como portas abertas, aplicativos tentando acesso à Internet e máquinas virtuais (VM) conectando-se a redes não autorizadas.
- Entenda os padrões de fluxo de tráfego entre regiões do Azure e a Internet para otimizar a implantação de rede para desempenho e capacidade.
- Identifique as configurações incorretas de rede que levam a conexões com falha na rede.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Por que a análise de tráfego?

É vital monitorar, gerenciar e conhecer sua própria rede para segurança, conformidade e desempenho descomprometidos. Saber seu próprio ambiente é de extrema importância para protegê-lo e otimizá-lo. Geralmente, você precisa saber o estado atual da rede, que está se conectando, onde estão se conectando, quais portas estão abertas na Internet, comportamento de rede esperado, comportamento de rede irregular e aumentos repentinos no tráfego.

As redes de nuvem são diferentes das redes corporativas locais, nas quais você tem roteadores e comutadores compatíveis com o protocolo NetFlow ou equivalentes, que fornecem a capacidade de coletar o tráfego de rede IP à medida que ele entra ou sai de um adaptador de rede. Analisando dados de fluxo de tráfego, você pode criar uma análise do volume e do fluxo de tráfego de rede.

As redes virtuais do Azure têm logs de fluxo NSG, que fornecem informações sobre o tráfego IP de entrada e saída por meio de um grupo de segurança de rede associado a interfaces de rede, VMs ou sub-redes individuais. Analisando logs de fluxo NSG brutos e inserindo inteligência de segurança, topologia e geografia, a análise de tráfego pode fornecer informações sobre o fluxo de tráfego em seu ambiente. Análise de Tráfego fornece informações como a maioria dos hosts de comunicação, a maioria dos protocolos de aplicativo de comunicação, a maioria dos pares de hosts, o tráfego permitido/bloqueado, o tráfego de entrada/saída, as portas de Internet abertas, a maioria das regras de bloqueio, o tráfego distribuição por Datacenter do Azure, rede virtual, sub-redes ou redes não autorizadas.

## <a name="key-components"></a>Componentes principais

- **NSG (grupo de segurança de rede)** : Contém uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos conectados a uma rede virtual do Azure. Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou a interfaces de rede individuais (NIC) ligadas a VMs (Resource Manager). Para obter mais informações, consulte [visão geral do grupo de segurança de rede](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Logs de fluxo do NSG (grupo de segurança de rede)** : Permite que você exiba informações sobre o tráfego IP de entrada e saída por meio de um grupo de segurança de rede. Os logs de fluxo do NSG são gravados no formato JSON e mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, informações de cinco tuplas sobre o fluxo (endereço IP de origem/destino, porta de origem/destino e protocolo) e se o tráfego foi permitido ou negado. Para obter mais informações sobre logs de fluxo do NSG, consulte [logs de fluxo do NSG](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: Um serviço do Azure que coleta dados de monitoramento e armazena os dados em um repositório central. Esses dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos por meio da API do Azure. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação. Os aplicativos de monitoramento, como o monitor de desempenho de rede e a análise de tráfego, são criados usando logs de Azure Monitor como base. Para obter mais informações, consulte [Azure monitor logs](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Espaço de trabalho log Analytics**: Uma instância de logs de Azure Monitor, onde os dados pertencentes a uma conta do Azure são armazenados. Para obter mais informações sobre espaços de trabalho do Log Analytics, consulte [criar um espaço de trabalho do log Analytics](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Observador de rede**: Um serviço regional que permite monitorar e diagnosticar condições em um nível de cenário de rede no Azure. Você pode ativar e desativar os logs de fluxo do NSG com o observador de rede. Para obter mais informações, consulte observador de [rede](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Como funciona a análise de tráfego

A análise de tráfego examina os logs de fluxo NSG brutos e captura logs reduzidos agregando fluxos comuns entre o mesmo endereço IP de origem, endereço IP de destino, porta de destino e protocolo. Por exemplo, host 1 (endereço IP: 10.10.10.10) se comunicando com o host 2 (endereço IP: 10.10.20.10), 100 vezes durante um período de 1 hora usando a porta (por exemplo, 80) e o protocolo (por exemplo, http). O log reduzido tem uma entrada, que o host 1 & host 2 enviou 100 vezes durante um período de 1 hora usando a porta *80* e o protocolo *http*, em vez de ter entradas de 100. Os logs reduzidos são aprimorados com informações de Geografia, segurança e topologia e, em seguida, armazenados em um espaço de trabalho do Log Analytics. A figura a seguir mostra o fluxo de dados:

![Fluxo de dados para processamento de logs de fluxo NSG](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Regiões suportadas

Você pode usar a análise de tráfego para NSGs em qualquer uma das seguintes regiões com suporte:

* Canadá Central
* EUA Centro-Oeste
* East US
* EUA Leste 2
* EUA Centro-Norte
* EUA Centro-Sul
* EUA Central
* EUA Oeste
* EUA Oeste 2
* França Central
* Europa Ocidental
* Europa do Norte
* Sul do Brasil
* Reino Unido Oeste
* Reino Unido Sul
* Leste da Austrália
* Sudeste da Austrália
* Ásia Oriental
* Sudeste Asiático
* Coreia do Sul Central
* Índia Central
* Sul da Índia
* Leste do Japão 
* Oeste do Japão
* Gov (US) - Virginia

O espaço de trabalho Log Analytics deve existir nas seguintes regiões:
* Canadá Central
* EUA Centro-Oeste
* East US
* EUA Leste 2
* EUA Centro-Sul
* E.U.A. Oeste
* EUA Oeste 2
* EUA Central
* França Central
* Europa do Norte
* Europa Ocidental
* Reino Unido Sul
* Leste da Austrália
* Sudeste da Austrália
* Ásia Oriental
* Sudeste Asiático
* Coreia do Sul Central
* Índia Central
* Leste do Japão
* Gov (US) - Virginia

## <a name="prerequisites"></a>Pré-requisitos

### <a name="user-access-requirements"></a>Requisitos de acesso do usuário

Sua conta deve ser um membro de uma das seguintes [funções internas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)do Azure:

|Modelo de implementação   | Role                   |
|---------          |---------               |
|Resource Manager   | Owner                  |
|                   | Contribuinte            |
|                   | Leitor                 |
|                   | Colaborador de rede    |

Se sua conta não estiver atribuída a uma das funções internas, ela deverá ser atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) atribuída às seguintes ações, no nível da assinatura:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft. Network/Connections/Read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"

Para obter informações sobre como verificar as permissões de acesso do usuário, consulte [perguntas frequentes sobre análise de tráfego](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Ativar o Observador de Rede

Para analisar o tráfego, você precisa ter um observador de rede existente ou [habilitar um observador de rede](network-watcher-create.md) em cada região em que você tenha NSGs para o qual deseja analisar o tráfego. A análise de tráfego pode ser habilitada para NSGs hospedados em qualquer uma das [regiões com suporte](#supported-regions).

### <a name="select-a-network-security-group"></a>Selecionar um grupo de segurança de rede

Antes de habilitar o log de fluxo do NSG, você deve ter um grupo de segurança de rede para registrar fluxos. Se você não tiver um grupo de segurança de rede, consulte [criar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md#create-a-network-security-group) para criar um.

No lado esquerdo da portal do Azure, selecione **Monitor**, o observador de **rede**e, em seguida, selecione logs de **fluxo de NSG**. Selecione o grupo de segurança de rede para o qual você deseja habilitar um log de fluxo do NSG, conforme mostrado na figura a seguir:

![Seleção de NSGs que requer a habilitação do log de fluxo do NSG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Se você tentar habilitar a análise de tráfego para um NSG hospedado em qualquer região diferente das [regiões com suporte](#supported-regions), você receberá um erro "não encontrado".

## <a name="enable-flow-log-settings"></a>Habilitar configurações de log de fluxo

Antes de habilitar as configurações de log de fluxo, você deve concluir as seguintes tarefas:

Registre o provedor do Azure insights, se ele ainda não estiver registrado para sua assinatura:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Se você ainda não tiver uma conta de armazenamento do Azure para armazenar logs de fluxo de NSG, você deve criar uma conta de armazenamento. Você pode criar uma conta de armazenamento com o comando a seguir. Antes de executar o comando, `<replace-with-your-unique-storage-account-name>` substitua por um nome que seja exclusivo em todos os locais do Azure, entre 3-24 caracteres de comprimento, usando apenas números e letras minúsculas. Você também pode alterar o nome do grupo de recursos, se necessário.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Selecione as seguintes opções, conforme mostrado na imagem:

1. Selecione *ativado* para **status**
2. Selecione a *versão 2* para a **versão dos logs de fluxo**. A versão 2 contém estatísticas de sessão de fluxo (bytes e pacotes)
3. Selecione uma conta de armazenamento existente para armazenar os logs de fluxo. Se você quiser armazenar os dados para sempre, defina o valor como *0*. Você incorre em taxas de armazenamento do Azure para a conta de armazenamento.
4. Defina a **retenção** para o número de dias para os quais você deseja armazenar dados.
5. Selecione *ativado* para obter **análise de tráfego status**.
6. Selecione um espaço de trabalho existente do Log Analytics (OMS) ou selecione **criar novo espaço de trabalho** para criar um novo. Um espaço de trabalho Log Analytics é usado pelo Análise de Tráfego para armazenar os dados agregados e indexados que são usados para gerar a análise. Se você selecionar um espaço de trabalho existente, ele deverá existir em uma das [regiões com suporte](#supported-regions) e ter sido atualizado para a nova linguagem de consulta. Se você não quiser atualizar um espaço de trabalho existente ou não tiver um espaço de trabalho em uma região com suporte, crie um novo. Para obter mais informações sobre as linguagens de consulta, consulte [atualização de log Analytics do Azure para nova pesquisa de logs](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    O espaço de trabalho do log Analytics que hospeda a solução de análise de tráfego e o NSGs não precisa estar na mesma região. Por exemplo, você pode ter análise de tráfego em um espaço de trabalho na região Europa Ocidental, embora você possa ter NSGs no leste dos EUA e oeste dos EUA. Vários NSGs podem ser configurados no mesmo espaço de trabalho.
7. Selecione **Guardar**.

    ![Seleção de conta de armazenamento, espaço de trabalho de Log Analytics e habilitação de Análise de Tráfego](./media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement-nsg-flowlogs-v2.png)

Repita as etapas anteriores para qualquer outro NSGs para o qual você deseja habilitar a análise de tráfego. Os dados dos logs de fluxo são enviados para o espaço de trabalho, portanto, verifique se as leis e regulamentos locais em seu país/região permitem o armazenamento de dados na região onde o espaço de trabalho existe.

Você também pode configurar a análise de tráfego usando o cmdlet do PowerShell [set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) no Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar sua versão instalada. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Exibir análise de tráfego

No lado esquerdo do portal, selecione **todos os serviços**e, em seguida, digite *Monitor* na caixa de **filtro** . Quando o **Monitor** aparecer nos resultados da pesquisa, selecione-o. Para começar a explorar a análise de tráfego e seus recursos, selecione observador de **rede**e, em seguida, **análise de tráfego**.

![Acessando o painel do Análise de Tráfego](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

O painel pode levar até 30 minutos para aparecer na primeira vez porque Análise de Tráfego deve primeiro agregar dados suficientes para que ele derive informações significativas, antes que possa gerar qualquer relatório.

## <a name="usage-scenarios"></a>Cenários de utilização

Algumas das informações que você pode querer obter depois que Análise de Tráfego estiver totalmente configurado, são as seguintes:

### <a name="find-traffic-hotspots"></a>Localizar pontos de HotSpot de tráfego

**Procurar**

- Quais hosts, sub-redes e redes virtuais estão enviando ou recebendo mais tráfego, atravessando o tráfego máximo mal-intencionado e bloqueando fluxos significativos?
    - Verifique o gráfico comparativa para host, sub-rede e rede virtual. Entender quais hosts, sub-redes e redes virtuais estão enviando ou recebendo mais tráfego pode ajudá-lo a identificar os hosts que estão processando mais tráfego e se a distribuição de tráfego é feita corretamente.
    - Você pode avaliar se o volume de tráfego é apropriado para um host. É o volume do comportamento normal do tráfego ou ele merece mais investigação?
- Quanto tráfego de entrada/saída existe?
    -   O host deve receber mais tráfego de entrada do que o de saída, ou vice-versa?
- Estatísticas de tráfego bloqueado.
    - Por que um host que está bloqueando um volume significativo de tráfego benigno? Esse comportamento requer mais investigação e, provavelmente, a otimização da configuração
- Estatísticas de tráfego mal-intencionado permitido/bloqueado
  - Por que um host recebe tráfego mal-intencionado e por que os fluxos de origem mal-intencionada são permitidos? Esse comportamento requer uma investigação mais detalhada e provavelmente a otimização da configuração.

    Selecione **ver tudo**, em **host**, conforme mostrado na figura a seguir:

    ![Painel mostrando o host com a maioria dos detalhes de tráfego](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- A imagem a seguir mostra a tendência de tempo para os cinco principais hosts de conversa e os detalhes relacionados ao fluxo (permitidos – fluxos de entrada/saída e negados-entrada/saída) para um host:

    ![Cinco principais tendências de host mais falantes](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Procurar**

- Quais são os pares de hosts mais inversos?
    - Comportamento esperado como comunicação de front-end ou back-end ou comportamento irregular, como tráfego de Internet de back-end.
- Estatísticas de tráfego permitido/bloqueado
    - Por que um host está permitindo ou bloqueando um volume de tráfego significativo
- Protocolo de aplicativo usado com mais frequência entre os pares de hosts mais inversos:
    - Esses aplicativos são permitidos nesta rede?
    - Os aplicativos estão configurados corretamente? Eles estão usando o protocolo apropriado para comunicação? Selecione **ver tudo** sob **conversa frequente**, conforme mostrado na figura a seguir:

        ![Painel mostrando a conversa mais frequente](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- A imagem a seguir mostra a tendência de tempo para as cinco principais conversas e os detalhes relacionados ao fluxo, como fluxos de entrada e saída permitidos e negados para um par de conversa:

    ![Os cinco principais detalhes de conversa informativos e a tendência](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Procurar**

- Qual protocolo de aplicativo é mais usado em seu ambiente e quais pares de hosts estão usando o protocolo de aplicativo mais?
    - Esses aplicativos são permitidos nesta rede?
    - Os aplicativos estão configurados corretamente? Eles estão usando o protocolo apropriado para comunicação? O comportamento esperado são portas comuns, como 80 e 443. Para comunicação padrão, se qualquer porta incomum for exibida, elas poderão exigir uma alteração de configuração. Selecione **ver tudo** em **porta do aplicativo**, na seguinte imagem:

        ![Painel mostrando os principais protocolos de aplicativo](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- As imagens a seguir mostram a tendência de tempo para os cinco protocolos L7 principais e os detalhes relacionados ao fluxo (por exemplo, fluxos permitidos e negados) para um protocolo L7:

    ![Detalhes e tendências dos cinco principais protocolos da camada 7](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Detalhes de fluxo para protocolo de aplicativo na pesquisa de logs](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Procurar**

- Tendências de utilização da capacidade de um gateway de VPN em seu ambiente.
    - Cada SKU de VPN permite uma determinada quantidade de largura de banda. Os gateways de VPN estão subutilizados?
    - Os gateways estão atingindo a capacidade? Você deve atualizar para o próximo SKU mais alto?
- Quais são os hosts mais inversos, por meio do gateway de VPN, sobre qual porta?
    - Esse padrão é normal? Selecione **ver tudo** em **Gateway de VPN**, conforme mostrado na figura a seguir:

        ![Painel mostrando as principais conexões VPN ativas](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- A imagem a seguir mostra a tendência de tempo de utilização de capacidade de um gateway de VPN do Azure e os detalhes relacionados ao fluxo (como portas e fluxos permitidos):

    ![Detalhes de fluxo e tendência de utilização do gateway de VPN](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualizar a distribuição de tráfego por geografia

**Procurar**

- A distribuição de tráfego por data center como as principais fontes de tráfego para um datacenter, as principais redes não autorizadas que continham o data center e os principais protocolos de aplicativo que convertem.
  - Se você observar mais carga em um data center, poderá planejar a distribuição de tráfego eficiente.
  - Se as redes não autorizadas estiverem convertidas na data center, corrija as regras NSG para bloqueá-las.

    Selecione **exibir mapa** em **seu ambiente**, conforme mostrado na figura a seguir:

    ![Painel mostrando a distribuição de tráfego](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- O mapa geográfico mostra a faixa de opção superior para seleção de parâmetros, como data centers (implantado/não-implantação/ativo/inativo/Análise de Tráfego habilitado/Análise de Tráfego não habilitado) e países/regiões que contribuem com tráfego benigno/mal-intencionado para o ativo planta

    ![Exibição de mapa geográfico mostrando a implantação ativa](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- O mapa geográfico mostra a distribuição de tráfego para um data center de países/regiões e continentes que se comunicam com ele em linhas azuis (tráfego benigno) e vermelho (tráfego mal-intencionado):

    ![Exibição de mapa geográfico mostrando a distribuição de tráfego para países/regiões e continentes](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Detalhes de fluxo para distribuição de tráfego na pesquisa de log](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualizar a distribuição de tráfego por redes virtuais

**Procurar**

- Distribuição de tráfego por rede virtual, topologia, principais fontes de tráfego para a rede virtual, principais redes não autorizadas que se desvertem para a rede virtual e os principais protocolos de aplicativo que convertem.
  - Saber qual rede virtual está convertida em qual rede virtual. Se a conversa não for esperada, ela poderá ser corrigida.
  - Se as redes não autorizadas estiverem convertidas em uma rede virtual, você poderá corrigir as regras de NSG para bloquear as redes não autorizadas.
 
    Selecione **Exibir VNets** em **seu ambiente**, conforme mostrado na figura a seguir:

    ![Painel mostrando a distribuição de rede virtual](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- A topologia de rede virtual mostra a faixa de opção superior para seleção de parâmetros como uma rede virtual (entre conexões de rede virtual/ativas/inativas), conexões externas, fluxos ativos e fluxos mal-intencionados da rede virtual.
- Você pode filtrar a topologia de rede virtual com base em assinaturas, espaços de trabalho, grupos de recursos e intervalo de tempo. Filtros adicionais que ajudam a entender o fluxo são: Tipo de fluxo (InterVNet, IntraVNET e assim por diante), direção do fluxo (entrada, saída), status do fluxo (permitido, bloqueado), VNETs (direcionado e conectado), tipo de conexão (emparelhamento ou gateway-P2S e S2S) e NSG. Use esses filtros para se concentrar em VNets que você deseja examinar em detalhes.
- A topologia de rede virtual mostra a distribuição de tráfego para uma rede virtual com relação aos fluxos (permitido/bloqueado/entrada/saída/benigno/mal-intencionado), protocolo de aplicativo e grupos de segurança de rede, por exemplo:

    ![Topologia de rede virtual mostrando detalhes de fluxo e distribuição de tráfego](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologia de rede virtual mostrando o nível superior e mais filtros](./media/traffic-analytics/virtual-network-filters.png)

    ![Detalhes de fluxo para distribuição de tráfego de rede virtual na pesquisa de log](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Procurar**

- Distribuição de tráfego por sub-rede, topologia, principais fontes de tráfego para a sub-rede, as principais redes não autorizadas que se desvertem à sub-rede e os principais protocolos de aplicativo que convertem.
    - Saber qual sub-rede está convertida em qual sub-rede. Se você vir conversas inesperadas, você pode corrigir sua configuração.
    - Se as redes não autorizadas estiverem convertidas em uma sub-rede, você poderá corrigi-las Configurando regras de NSG para bloquear as redes não autorizadas.
- A topologia sub-redes mostra a faixa de opção superior para seleção de parâmetros, como sub-rede ativa/inativa, conexões externas, fluxos ativos e fluxos mal-intencionados da sub-rede.
- A topologia de sub-rede mostra a distribuição de tráfego para uma rede virtual com relação aos fluxos (permitido/bloqueado/entrada/saída/benigno/mal-intencionado), protocolo de aplicativo e NSGs, por exemplo:

    ![Topologia de sub-rede mostrando a distribuição de tráfego uma sub-rede de rede virtual com relação aos fluxos](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Procurar**

A distribuição de tráfego por gateway de aplicativo & Load Balancer, topologia, principais fontes de tráfego, principais redes não autorizadas que continham o gateway de aplicativo & Load Balancer e os principais protocolos de aplicativo convertidas. 
    
 - Saber qual sub-rede está convertida para qual gateway de aplicativo ou Load Balancer. Se você observar conversas inesperadas, você pode corrigir sua configuração.
 - Se as redes não autorizadas estiverem convertidas com um gateway de aplicativo ou Load Balancer, você poderá corrigi-las Configurando regras de NSG para bloquear as redes não autorizadas. 

    ![sub-rede-topologia-apresentação-tráfego-distribuição-para-a-aplicativo-gateway-subnet-com-refere-se a fluxos](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Exibir portas e máquinas virtuais que recebem tráfego da Internet

**Procurar**

- Quais portas abertas estão convertidas pela Internet?
  - Se portas inesperadas forem encontradas abertas, você poderá corrigir sua configuração:

    ![Painel mostrando portas que recebem e enviam tráfego para a Internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Detalhes de hosts e portas de destino do Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Procurar**

Você tem tráfego mal-intencionado em seu ambiente? De onde ele se origina? Para onde ele está destinado?

![Detalhes de fluxos de tráfego mal-intencionado na pesquisa de logs](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualizar as tendências em ocorrências de regras de NSG/NSG

**Procurar**

- Quais regras de NSG/NSG têm mais ocorrências no gráfico comparativa com distribuição de fluxos?
- Quais são os principais pares de conversa de origem e destino por regras de NSG/NSG?

    ![Painel mostrando estatísticas de ocorrências NSG](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- As imagens a seguir mostram a tendência de tempo para ocorrências de regras NSG e detalhes de fluxo de destino de origem para um grupo de segurança de rede:

  - Detectar rapidamente quais regras NSGs e NSG estão atravessando fluxos mal-intencionados e quais são os principais endereços IP mal-intencionados que acessam seu ambiente de nuvem
  - Identificar quais regras de NSG/NSG estão permitindo/bloqueando o tráfego de rede significativo
  - Selecione os principais filtros para a inspeção granular de uma regra de NSG ou NSG

    ![Mostrando a tendência de tempo para ocorrências de regra NSG e as principais regras de NSG](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Principais detalhes de estatísticas de regras de NSG na pesquisa de logs](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Para obter respostas para perguntas frequentes, consulte [perguntas frequentes sobre análise de tráfego](traffic-analytics-faq.md).

## <a name="next-steps"></a>Passos Seguintes

- Para saber como habilitar os logs de fluxo, consulte Habilitando o [log de fluxo do NSG](network-watcher-nsg-flow-logging-portal.md).
- Para entender o esquema e os detalhes de processamento de Análise de Tráfego, consulte [esquema de análise de tráfego](traffic-analytics-schema.md).
