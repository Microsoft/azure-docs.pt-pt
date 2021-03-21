---
title: A azure traffic analytics frequentemente fez perguntas | Microsoft Docs
description: Obtenha respostas a algumas das perguntas mais frequentes sobre análise de tráfego.
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
ms.openlocfilehash: f4d97b81bf8f11b8380f04dcbfdb72bd658805ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579239"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Traffic Analytics frequentemente fez perguntas

Este artigo recolhe num local muitas das perguntas mais frequentes sobre análise de tráfego no Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Quais são os pré-requisitos para usar a análise do tráfego?

A Análise de Tráfego requer os seguintes pré-requisitos:

- Uma subscrição ativada pelo Observador de Rede.
- Registos de fluxo do Grupo de Segurança de Rede (NSG) ativados para os NSGs que pretende monitorizar.
- Uma conta de armazenamento Azure, para armazenar troncos de fluxo brutos.
- Um espaço de trabalho Azure Log Analytics, com acesso de leitura e escrita.

A sua conta deve encontrar uma das seguintes para permitir análises de tráfego:

- A sua conta deve ter qualquer uma das seguintes funções Azure no âmbito de subscrição: proprietário, colaborador, leitor ou colaborador de rede.
- Se a sua conta não for atribuída a uma das funções anteriormente listadas, deve ser atribuída a uma função personalizada que seja atribuída às seguintes ações, ao nível da subscrição.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Para verificar as funções atribuídas a um utilizador para uma subscrição:

1. Inicie sessão no Azure utilizando **o Login-AzAccount**. 

2. Selecione a subscrição necessária utilizando **Select-AzSubscription**. 

3. Para listar todas as funções atribuídas a um utilizador especificado, utilize **o Get-AzRoleAssignment -SignInName [e-mail do utilizador] - Inclua os administradores da Classe.** 

Se não estiver a ver nenhuma saída, contacte o respetivo administrador de subscrição para ter acesso à execução dos comandos. Para obter mais detalhes, consulte [Adicionar ou remover atribuições de funções Azure utilizando a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Em que regiões de Azure está disponível a Traffic Analytics?

Pode utilizar análises de tráfego para NSGs em qualquer uma das seguintes regiões apoiadas:
- Canadá Central
- E.U.A. Centro-Oeste
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Centro-Norte
- E.U.A. Centro-Sul
- E.U.A. Central
- E.U.A. Oeste
- E.U.A. Oeste 2
- França Central
- Europa Ocidental
- Europa do Norte
- Sul do Brasil
- Oeste do Reino Unido
- Sul do Reino Unido
- Leste da Austrália
- Austrália Sudeste 
- Ásia Leste
- Sudeste Asiático
- Coreia do Sul Central
- Índia Central
- Sul da Índia
- Leste do Japão
- Oeste do Japão
- US Gov - Virginia
- China Leste 2

O espaço de trabalho Log Analytics deve existir nas seguintes regiões:
- Canadá Central
- E.U.A. Centro-Oeste
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Centro-Norte
- E.U.A. Centro-Sul
- E.U.A. Central
- E.U.A. Oeste
- E.U.A. Oeste 2
- França Central
- Europa Ocidental
- Europa do Norte
- Oeste do Reino Unido
- Sul do Reino Unido
- Leste da Austrália
- Austrália Sudeste
- Ásia Leste
- Sudeste Asiático 
- Coreia do Sul Central
- Índia Central
- Leste do Japão
- US Gov - Virginia
- China Leste 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Os NSGs posso permitir que os registos de fluxo sejam em regiões diferentes do meu espaço de trabalho?

Sim, estes NSGs podem estar em regiões diferentes do seu espaço de trabalho Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Vários NSGs podem ser configurados dentro de um único espaço de trabalho?

Sim.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um espaço de trabalho existente?

Sim. Se selecionar um espaço de trabalho existente, certifique-se de que foi migrado para a nova linguagem de consulta. Se não quiser atualizar o espaço de trabalho, tem de criar um novo. Para obter mais informações sobre o novo idioma de consulta, consulte [o upgrade de registos do Azure Monitor para nova pesquisa de registos](../azure-monitor/logs/log-query-overview.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>A minha Conta de Armazenamento Azure pode estar numa subscrição e o meu espaço de trabalho Log Analytics está numa subscrição diferente?

Sim, a sua conta de Armazenamento Azure pode estar numa subscrição, e o seu espaço de trabalho Log Analytics pode estar numa subscrição diferente.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Posso armazenar registos crus numa subscrição diferente?

Sim. Pode configurar registos de fluxo NSG para ser enviado para uma conta de armazenamento localizada numa subscrição diferente, desde que tenha os privilégios adequados, e que a conta de armazenamento esteja localizada na mesma região que o NSG. A NSG e a conta de armazenamento de destino também devem partilhar o mesmo Azure Ative Directory Tenanty.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>E se eu não conseguir configurar um NSG para análise de tráfego devido a um erro de "Não encontrado"?

Selecione uma região apoiada. Se selecionar uma região não suportada, receberá um erro "Não encontrado". As regiões apoiadas constam anteriormente neste artigo.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>E se eu estiver a obter o estado, "Falhou em carregar", na página de registos de fluxo da NSG?

O fornecedor Microsoft.Insights deve estar registado para que a exploração de fluxo funcione corretamente. Se não tiver a certeza se o fornecedor Microsoft.Insights está registado para a sua subscrição, substitua *xxxxx-xxxxx-xxxxx-xxxxx-xxxxx* no seguinte comando, e execute os seguintes comandos da PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Configurei a solução. Por que não vejo nada no painel?

O painel pode demorar até 30 minutos a aparecer pela primeira vez. A solução deve primeiro agregar dados suficientes para obter insights significativos. Depois gera relatórios. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>E se eu receber esta mensagem: "Não conseguimos encontrar nenhum dado neste espaço de trabalho para intervalos de tempo selecionados. Tente alterar o intervalo de tempo ou selecione um espaço de trabalho diferente."?

Experimente as seguintes opções:
- Altere o intervalo de tempo na barra superior.
- Selecione um espaço de trabalho de Log Analytics diferente na barra superior.
- Tente aceder à análise de tráfego após 30 minutos, se foi recentemente ativado.
    
Se os problemas persistirem, levante preocupações no fórum de voz do [utilizador](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>E se eu receber esta mensagem: "Analisar os seus registos de fluxo NSG pela primeira vez. Este processo pode demorar 20 a 30 minutos a ser concluído. Volte depois de algum tempo. 2) Se o passo acima não funcionar e o seu espaço de trabalho estiver sob o SKU gratuito, então verifique o seu uso do espaço de trabalho aqui para validar mais de quota, caso contrário consulte as FAQs para mais informações."?

Pode ver esta mensagem porque:
- A Traffic Analytics foi recentemente ativada, e pode ainda não ter agregado dados suficientes para obter insights significativos.
- Está a utilizar a versão gratuita do espaço de trabalho Log Analytics e excedeu os limites de quota. Pode ser necessário utilizar um espaço de trabalho com uma capacidade maior.
    
Se os problemas persistirem, levante preocupações no fórum de voz do [utilizador](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>E se eu receber esta mensagem: "Parece que temos dados de recursos (Topologia) e nenhuma informação de fluxo. Entretanto, clique aqui para ver os dados dos recursos e consulte as PERGUNTAS Frequentes para mais informações."?

Está a ver a informação sobre recursos no painel de instrumentos; no entanto, não existem estatísticas relacionadas com o fluxo. Os dados podem não estar presentes devido à falta de fluxos de comunicação entre os recursos. Espere 60 minutos e remarcar o estado. Se o problema persistir, e tem a certeza de que os fluxos de comunicação entre os recursos existem, levantem preocupações no fórum de voz do [Utilizador](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Posso configurar análises de tráfego usando o PowerShell ou um modelo ou cliente do Azure Resource Manager?

Pode configurar análises de tráfego utilizando o Windows PowerShell a partir da versão 6.2.1. Para configurar a registo de fluxo e a análise do tráfego para um NSG específico utilizando o cmdlet set, consulte [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Para obter o estado de registo de fluxo e análise de tráfego para um NSG específico, consulte [Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Atualmente, não é possível utilizar um modelo de Gestor de Recursos Azure para configurar análises de tráfego.

Para configurar a análise de tráfego utilizando um cliente Azure Resource Manager, consulte os seguintes exemplos.

**Definir exemplo de cmdlet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Obtenha o exemplo do cmdlet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Como é que a Traffic Analytics tem o preço?

A Análise de Tráfego está medido. A medição baseia-se no processamento de dados de registo de fluxo pelo serviço e no armazenamento dos registos melhorados resultantes num espaço de trabalho do Log Analytics. 

Por exemplo, de acordo com o [plano de preços](https://azure.microsoft.com/pricing/details/network-watcher/), considerando a região centro-oeste dos EUA, se os dados de fluxo armazenados numa conta de armazenamento processados pelo Traffic Analytics forem de 10 GB e os registos melhorados ingeridos no espaço de trabalho Log Analytics forem de 1 GB, então os encargos aplicáveis são: 10 x 2,3$ + 1 x 2,76$ = 25,76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Com que frequência o Traffic Analytics processa os dados?

Consulte a [secção de agregação de dados](./traffic-analytics-schema.md#data-aggregation) no Esquema de Análise de Tráfego e Documento de Agregação de Dados

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Como é que a Traffic Analytics decide que um IP é malicioso? 

O Traffic Analytics conta com sistemas de inteligência de ameaças internas da Microsoft para considerar um IP como malicioso. Estes sistemas aproveitam diversas fontes de telemetria, como produtos e serviços da Microsoft, a Unidade de Crimes Digitais da Microsoft (DCU), o Microsoft Security Response Center (MSRC) e feeds externos e constroem muita inteligência em cima dela. Alguns destes dados são da Microsoft Internal. Se um IP conhecido estiver a ser sinalizado como malicioso, por favor, levante um bilhete de apoio para saber os detalhes.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Como posso definir alertas sobre os dados do Traffic Analytics?

A Traffic Analytics não tem suporte incorporado para alertas. No entanto, uma vez que os dados de Traffic Analytics são armazenados no Log Analytics, pode escrever consultas personalizadas e definir alertas sobre os mesmos. Passos:
- Pode utilizar o shortlink para Log Analytics in Traffic Analytics. 
- Use o [esquema documentado aqui](traffic-analytics-schema.md) para escrever as suas consultas 
- Clique em "Nova regra de alerta" para criar o alerta
- Consulte a [documentação de alertas](../azure-monitor/alerts/alerts-log.md) de registo para criar o alerta

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>Como verifico quais os VMs que estão a receber mais tráfego no local?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
| render timechart
```

  Para IPs:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
| render timechart
```

Para o tempo, use o formato : yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>Como verifico o desvio padrão no tráfego recebido pelos meus VMs de máquinas no local?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + utboundBytes_d
| summarize deviation = stdev(traffic)  by vm
```

Para IPs:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
| summarize deviation = stdev(traffic)  by IP
```

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>Como verifico quais portas são alcançáveis (ou bloqueadas) entre pares DE IP com regras NSG?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
| extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
| mvexpand SourceIp = sourceIPs to typeof(string)
| mvexpand DestIp = destIPs to typeof(string)
| project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
| summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s
```

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Como posso navegar usando o teclado na vista do mapa geoparque?

A página do mapa geo contém duas secções principais:
    
- **Banner**: A bandeira no topo do mapa geológico fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, Implantação, Tráfego de países/regiões e Malicious). Quando seleciona um botão, o respetivo filtro é aplicado no mapa. Por exemplo, se selecionar o botão Ative, o mapa realça os centros de dados ativos na sua implementação.
- **Mapa**: Abaixo da bandeira, a secção do mapa mostra a distribuição de tráfego entre datacenters do Azure e países/regiões.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação de teclado no banner
    
- Por predefinição, a seleção na página do mapa geoparque para o banner é o filtro "Azure DCs".
- Para passar para outro filtro, utilize a `Tab` chave ou a `Right arrow` chave. Para andar para trás, use a `Shift+Tab` chave ou a `Left arrow` chave. A navegação para a frente é da esquerda para a direita, seguida de cima para baixo.
- Prima `Enter` ou a tecla de `Down` seta para aplicar o filtro selecionado. Com base na seleção e implantação do filtro, destacam-se um ou vários nós na secção do mapa.
- Para alternar entre banner e mapa, prima `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-map"></a>Navegação de teclado no mapa
    
- Depois de ter selecionado qualquer filtro no banner e `Ctrl+F6` pressionado, o foco move-se para um dos nós realçados (Centro de **dados Azure** ou **País/Região)** na vista do mapa.
- Para mover-se para outros nós destacados no mapa, use `Tab` ou a chave para o movimento para a `Right arrow` frente. Utilize `Shift+Tab` ou a chave para `Left arrow` movimentos retrógradas.
- Para selecionar qualquer nó realçado no mapa, utilize a `Enter` `Down arrow` ou a chave.
- Na seleção de tais nós, o foco passa para a **Caixa de Ferramentas de Informação** para o nó. Por predefinição, o foco move-se para o botão fechado na **Caixa de Ferramentas de Informação**. Para avançar ainda mais dentro da vista **Caixa,** utilize `Right arrow` e as `Left arrow` teclas para avançar e recuar, respectivamente. A prensagem `Enter` tem o mesmo efeito que selecionar o botão focado na Caixa de **Ferramentas de Informação**.
- Quando pressiona `Tab` enquanto o foco está na **Caixa de Ferramentas de Informação,** o foco move-se para os pontos finais no mesmo continente que o nó selecionado. Use as `Right arrow` chaves e as chaves para passar por estes `Left arrow` pontos finais.
- Para passar para outros pontos finais de fluxo ou aglomerados de continente, use `Tab` para movimentos para a frente e `Shift+Tab` para movimentos retrógradas.
- Quando o foco estiver nos **clusters continentes,** use as `Enter` `Down` teclas ou setas para destacar os pontos finais dentro do aglomerado do continente. Para passar pelos pontos finais e pelo botão de fecho na caixa de informações do cluster continente, utilize a `Right arrow` ou a chave para `Left arrow` movimentos para a frente e para trás, respectivamente. Em qualquer ponto final, pode utilizar `Shift+L` para mudar para a linha de ligação do nó selecionado para o ponto final. Pode `Shift+L` pressionar novamente para passar para o ponto final selecionado.
        
### <a name="keyboard-navigation-at-any-stage"></a>Navegação de teclado em qualquer fase
    
- `Esc` colapsa a seleção expandida.
- A `Up-arrow` chave executa a mesma ação que . `Esc` . A `Down arrow` chave executa a mesma ação que . `Enter` .
- Use `Shift+Plus` para ampliar e `Shift+Minus` ampliar.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Como posso navegar usando o teclado na vista de topologia da rede virtual?

A página de topologia das redes virtuais contém duas secções principais:
    
- **Banner**: O banner no topo da topologia das redes virtuais fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, redes virtuais conectadas, redes virtuais desligadas e IPs públicos). Quando seleciona um botão, o respetivo filtro é aplicado na topologia. Por exemplo, se selecionar o botão Ative, a topologia realça as redes virtuais ativas na sua implementação.
- **Topologia**: Abaixo do banner, a secção de topologia mostra a distribuição de tráfego entre redes virtuais.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação de teclado no banner
    
- Por predefinição, a seleção na página de topologia de redes virtuais para o banner é o filtro "Connected VNets".
- Para passar para outro filtro, use a `Tab` chave para avançar. Para andar para trás, use a `Shift+Tab` chave. A navegação para a frente é da esquerda para a direita, seguida de cima para baixo.
- Prima `Enter` para aplicar o filtro selecionado. Com base na seleção e implantação do filtro, destacam-se um ou vários nós (rede virtual) sob a secção topologia.
- Para alternar entre o banner e a topologia, prima `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Navegação de teclado na topologia
    
- Depois de ter selecionado qualquer filtro no banner e `Ctrl+F6` pressionado, o foco move-se para um dos nós realçados **(VNet)** na vista topologia.
- Para passar para outros nós destacados na vista topologia, use a chave para o `Shift+Right arrow` movimento para a frente. 
- Nos nós realçados, o foco move-se para a **Caixa de Ferramentas de Informação** para o nó. Por predefinição, o foco passa para o botão **Mais detalhes** na **Caixa de Ferramentas de Informação**. Para se mover ainda mais dentro da vista **Caixa,** utilize as `Right arrow` `Left arrow` teclas e as teclas para avançar e recuar, respectivamente. A prensagem `Enter` tem o mesmo efeito que selecionar o botão focado na Caixa de **Ferramentas de Informação**.
- Na seleção de tais nós, pode visitar todas as suas ligações, uma a uma, premindo a `Shift+Left arrow` chave. O foco desloca-se para a Caixa de Ferramentas de **Informação** dessa ligação. Em qualquer ponto, o foco pode ser deslocado de volta para o nó pressionando `Shift+Right arrow` novamente.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Como posso navegar usando o teclado na vista de topologia da sub-rede?

A página de topologia de sub-redes virtuais contém duas secções principais:
    
- **Banner**: A banner no topo da topologia de sub-redes virtuais fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, sub-redes Ative, Medium e Gateway). Quando seleciona um botão, o respetivo filtro é aplicado na topologia. Por exemplo, se selecionar o botão Ative, a topologia realça a sub-rede virtual ativa na sua implantação.
- **Topologia**: Abaixo do banner, a secção de topologia mostra a distribuição de tráfego entre sub-redes virtuais.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação de teclado no banner
    
- Por predefinição, a seleção na página de topologia de sub-redes virtuais para o banner é o filtro "Subnets".
- Para passar para outro filtro, use a `Tab` chave para avançar. Para andar para trás, use a `Shift+Tab` chave. A navegação para a frente é da esquerda para a direita, seguida de cima para baixo.
- Prima `Enter` para aplicar o filtro selecionado. Com base na seleção e implantação de filtros, destacam-se um ou vários nós (Subnet) sob a secção topologia.
- Para alternar entre o banner e a topologia, prima `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Navegação de teclado na topologia
    
- Depois de ter selecionado qualquer filtro no banner e `Ctrl+F6` pressionado, o foco move-se para um dos nós realçados **(Subnet)** na vista topologia.
- Para passar para outros nós destacados na vista topologia, use a chave para o `Shift+Right arrow` movimento para a frente. 
- Nos nós realçados, o foco move-se para a **Caixa de Ferramentas de Informação** para o nó. Por predefinição, o foco passa para o botão **Mais detalhes** na **Caixa de Ferramentas de Informação**. Para avançar ainda mais dentro da vista **Caixa,** utilize `Right arrow` e as `Left arrow` teclas para avançar e recuar, respectivamente. A prensagem `Enter` tem o mesmo efeito que selecionar o botão focado na Caixa de **Ferramentas de Informação**.
- Na seleção de tais nós, pode visitar todas as suas ligações, uma a uma, premindo `Shift+Left arrow` a tecla. O foco desloca-se para a Caixa de Ferramentas de **Informação** dessa ligação. Em qualquer ponto, o foco pode ser deslocado de volta para o nó pressionando `Shift+Right arrow` novamente.

## <a name="are-classic-nsgs-supported"></a>Os NSGs clássicos são suportados?
Não, a Traffic Analytics não suporta o NSG clássico. Recomenda-se a migração dos recursos iaaS do clássico para o Azure Resource Manager, uma vez que os recursos clássicos serão [depreciados.](../virtual-machines/classic-vm-deprecation.md) Consulte este artigo para entender [como migrar.](../virtual-machines/migration-classic-resource-manager-overview.md)