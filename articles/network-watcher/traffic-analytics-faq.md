---
title: A análise de tráfego azure frequentemente fez perguntas / Microsoft Docs
description: Obtenha respostas para algumas das perguntas mais frequentes sobre análise de tráfego.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: damendo
ms.openlocfilehash: 5e31ed905f05070c8715a63ef3386b0006df0a75
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840626"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Análise de Tráfego frequentemente fez perguntas

Este artigo recolhe num só local muitas das perguntas mais frequentes sobre análise de tráfego no Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Quais são os pré-requisitos para usar análises de tráfego?

A Análise de Tráfego requer os seguintes pré-requisitos:

- Um Observador de Rede ativou a subscrição.
- Os registos de fluxo do Grupo de Segurança da Rede (NSG) ativados para os NSGs que pretende monitorizar.
- Uma conta azure de armazenamento, para armazenar troncos de fluxo bruto.
- Um espaço de trabalho Azure Log Analytics, com acesso a leitura e escrita.

A sua conta deve satisfazer um dos seguintes dados para permitir análises de tráfego:

- A sua conta deve ter qualquer uma das seguintes funções de controlo de acesso (RBAC) no âmbito da subscrição: proprietário, colaborador, leitor ou colaborador de rede.
- Se a sua conta não for atribuída a uma das funções previamente listadas, deve ser atribuída a uma função personalizada que seja atribuída às seguintes ações, ao nível da subscrição.
            
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

2. Selecione a subscrição necessária utilizando **select-AzSubscription**. 

3. Para listar todas as funções atribuídas a um utilizador especificado, utilize **o Get-AzRoleAssignment -SignInName [email do utilizador] -Incluir Administradores Clássicos**. 

Se não estiver a ver qualquer saída, contacte o respetivo administrador de subscrição para ter acesso à execução dos comandos. Para mais detalhes, consulte Gerir o [controlo de acesso baseado em papéis com o Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Em que as regiões azure estão disponíveis na Analytics de Tráfego?

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
- Ásia Sudeste
- Coreia do Sul Central
- Índia Central
- Sul da Índia
- Leste do Japão
- Oeste do Japão
- US Gov - Virginia
- Leste da China 2

O espaço de trabalho log Analytics deve existir nas seguintes regiões:
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
- Ásia Sudeste 
- Coreia do Sul Central
- Índia Central
- Leste do Japão
- US Gov - Virginia
- Leste da China 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Os NSGs posso permitir que os registos de fluxo estejam em diferentes regiões do que o meu espaço de trabalho?

Sim, estes NSGs podem estar em diferentes regiões do que o seu espaço de trabalho Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Podem ser configurados vários NSGs num único espaço de trabalho?

Sim.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um espaço de trabalho existente?

Sim. Se selecionar um espaço de trabalho existente, certifique-se de que foi migrado para a nova linguagem de consulta. Se não quiser atualizar o espaço de trabalho, precisa de criar um novo. Para mais informações sobre a nova linguagem de consulta, consulte o upgrade de [registos do Monitor Azure para nova pesquisa](../log-analytics/log-analytics-log-search-upgrade.md)de registos .

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>A minha Conta de Armazenamento Azure pode estar numa subscrição e o meu espaço de trabalho log Analytics pode estar numa subscrição diferente?

Sim, a sua conta de Armazenamento Azure pode estar numa subscrição, e o seu espaço de trabalho Log Analytics pode estar numa subscrição diferente.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Posso armazenar registos crus numa subscrição diferente?

Não. Pode armazenar registos brutos em qualquer conta de armazenamento onde um NSG esteja habilitado para registos de fluxo. No entanto, tanto a conta de armazenamento como os registos brutos devem estar na mesma subscrição e região.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>E se eu não conseguir configurar um NSG para análise de tráfego devido a um erro "não encontrado"?

Selecione uma região apoiada. Se selecionar uma região não apoiada, receberá um erro "Não encontrado". As regiões apoiadas estão listadas no início deste artigo.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>E se eu estiver a receber o estatuto, "Falhou em carregar", sob a página de registos de fluxo da NSG?

O fornecedor Microsoft.Insights deve ser registado para que o registo de fluxos funcione corretamente. Se não tiver a certeza se o fornecedor Microsoft.Insights está registado para a sua subscrição, substitua *xxxxx-xxxxx-xxxxxx-xxxx* no seguinte comando e execute os seguintes comandos da PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Configurei a solução. Porque é que não estou a ver nada no tablier?

O painel pode demorar até 30 minutos a aparecer da primeira vez. A solução deve primeiro agregar dados suficientes para que obtenha insights significativos. Depois gera relatórios. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>E se eu receber esta mensagem: "Não conseguimos encontrar nenhum dado neste espaço de trabalho para intervalo de tempo selecionado. Tente alterar o intervalo de tempo ou selecione um espaço de trabalho diferente."?

Experimente as seguintes opções:
- Mude o intervalo de tempo na barra superior.
- Selecione um espaço de trabalho de Log Analytics diferente na barra superior.
- Tente aceder à análise de tráfego após 30 minutos, se foi ativado recentemente.
    
Se persistirem problemas, levante preocupações no fórum de [voz do Utilizador](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>E se eu receber esta mensagem: "Analisando os seus registos de fluxo nsg pela primeira vez. Este processo pode demorar 20 a 30 minutos a ser concluído. Volte depois de algum tempo. 2) Se o passo acima não funcionar e o seu espaço de trabalho estiver sob o SKU gratuito, então verifique o seu uso do espaço de trabalho aqui para validar sobre quota, então consulte as FAQs para mais informações."?

Pode ver esta mensagem porque:
- O Traffic Analytics foi recentemente ativado, e pode ainda não ter dados agregados suficientes para que obtenha insights significativos.
- Está a utilizar a versão gratuita do espaço de trabalho Log Analytics e ultrapassou os limites de quota. Pode ser necessário usar um espaço de trabalho com uma capacidade maior.
    
Se persistirem problemas, levante preocupações no fórum de [voz do Utilizador](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>E se eu receber esta mensagem: "Parece que temos dados de recursos (Topologia) e nenhuma informação de fluxos. Entretanto, clique aqui para ver os dados de recursos e consulte as FAQs para mais informações."?

Está a ver a informação sobre os recursos no painel de instrumentos; no entanto, não existem estatísticas relacionadas com o fluxo. Os dados podem não estar presentes devido a nenhum fluxo de comunicação entre os recursos. Espere 60 minutos e volte a verificar o estado. Se o problema persistir, e tiver a certeza de que existem fluxos de comunicação entre recursos, levante preocupações no fórum de [voz do Utilizador](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Posso configurar análises de tráfego usando powerShell ou um modelo de Gestor de Recursos Azure ou cliente?

Pode configurar a análise de tráfego utilizando o Windows PowerShell a partir da versão 6.2.1. Para configurar a exploração de fluxos e análises de tráfego para um NSG específico utilizando o conjunto cmdlet, consulte [Set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Para obter o estado de registo de fluxoe análise de tráfego para um NSG específico, consulte [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Atualmente, não pode usar um modelo de Gestor de Recursos Azure para configurar análises de tráfego.

Para configurar a análise de tráfego utilizando um cliente do Gestor de Recursos Azure, consulte os seguintes exemplos.

**Definir exemplo cmdlet:**
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


## <a name="how-is-traffic-analytics-priced"></a>Como é que o Traffic Analytics tem preços?

O Traffic Analytics está medido. A medição baseia-se no processamento de dados de registo de fluxo pelo serviço e armazenando os registos melhorados resultantes num espaço de trabalho de Log Analytics. 

Por exemplo, de acordo com o plano de [preços](https://azure.microsoft.com/pricing/details/network-watcher/), considerando a região do Centro-Oeste dos EUA, se os dados de registos de fluxo armazenados numa conta de armazenamento processada pela Traffic Analytics forem de 10 GB e os registos melhorados ingeridos no espaço de trabalho do Log Analytics são de 1 GB então os encargos aplicáveis são: 10 x 2,3$ + 1 x 2,76$ = 25,76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Com que frequência o Traffic Analytics processa dados?

Consulte a secção de [agregação](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) de dados no Esquema de Análise de Tráfego e documento de agregação de dados

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Como é que o Traffic Analytics decide que um IP é malicioso? 

O Traffic Analytics conta com sistemas de inteligência de ameaças internas da Microsoft para considerá-lo um IP como malicioso. Estes sistemas aproveitam diversas fontes de telemetria, como produtos e serviços da Microsoft, a Microsoft Digital Crimes Unit (DCU), o Microsoft Security Response Center (MSRC) e feeds externos e constroem muita inteligência em cima dele. Alguns destes dados são da Microsoft Internal. Se um IP conhecido está a ser sinalizado como malicioso, por favor, levante um bilhete de apoio para saber os detalhes.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Como posso definir alertas sobre os dados do Traffic Analytics?

A Análise de Tráfego não tem suporte incorporado para alertas. No entanto, uma vez que os dados do Traffic Analytics são armazenados no Log Analytics, pode escrever consultas personalizadas e definir alertas sobre os mesmos. Passos:
- Pode utilizar o shortlink para Log Analytics no Traffic Analytics. 
- Use o [esquema documentado aqui](traffic-analytics-schema.md) para escrever as suas consultas 
- Clique em "Nova regra de alerta" para criar o alerta
- Consulte a documentação de [alertas de registo](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) para criar o alerta

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premise-traffic"></a>Como posso verificar quais vMs estão recebendo mais tráfego no local

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            | where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
             | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
            | render timechart

  Para os IPs:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
            | render timechart

Para o tempo, use o formato : yyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-recieved-by-my-vms-from-on-premise-machines"></a>Como posso verificar o desvio padrão no tráfego recebido pelos meus VMs de máquinas no local

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by vm


Para os IPs:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by IP
            
## <a name="how-do-i-check-which-ports-are-reachable-or-bocked-between-ip-pairs-with-nsg-rules"></a>Como verificar quais portas são acessíveis (ou bocked) entre pares IP com regras NSG

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
            | extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
            destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
            | mvexpand SourceIp = sourceIPs to typeof(string)
            | mvexpand DestIp = destIPs to typeof(string)
            | project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
            | summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Como posso navegar usando o teclado na vista do mapa geográfico?

A página geomap contém duas secções principais:
    
- **Banner**: O banner na parte superior do geomapa fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, Implantação, Tráfego de países/regiões e Maliciosos). Quando se leciona um botão, o respetivo filtro é aplicado no mapa. Por exemplo, se selecionar o botão Ative, o mapa destaca os datacenters ativos na sua implementação.
- **Mapa**: Abaixo do banner, a secção do mapa mostra a distribuição de tráfego entre centros de dados Azure e países/regiões.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação de teclado no banner
    
- Por padrão, a seleção na página geo-mapa para o banner é o filtro "DCs Azure".
- Para se deslocar para outro filtro, utilize o `Tab` ou a tecla `Right arrow`. Para andar para trás, utilize a `Shift+Tab` ou a chave `Left arrow`. A navegação para a frente é da esquerda para a direita, seguida de cima para baixo.
- Prima `Enter` ou a tecla de seta `Down` para aplicar o filtro selecionado. Com base na seleção e implantação do filtro, destacam-se os nódosos sob a secção do mapa.
- Para alternar entre banner e mapa, prima `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Navegação de teclado no mapa
    
- Depois de ter selecionado qualquer filtro no banner e pressionado `Ctrl+F6`, o foco move-se para um dos nós destacados ( Centro de**dados Azure** ou **País/Região**) na vista do mapa.
- Para passar para outros nós destacados no mapa, use `Tab` ou a chave `Right arrow` para o movimento para a frente. Utilize `Shift+Tab` ou a chave `Left arrow` para movimentos retrógrados.
- Para selecionar qualquer nó realçado no mapa, utilize a tecla `Enter` ou `Down arrow`.
- Na seleção de tais nós, o foco move-se para a Caixa de Ferramentas de **Informação** para o nó. Por predefinição, o foco move-se para o botão fechado na Caixa de Ferramentas de **Informação**. Para se mover ainda mais dentro da vista **Box,** utilize `Right arrow` e `Left arrow` chaves para avançar e recuar, respectivamente. Premir `Enter` tem o mesmo efeito que selecionar o botão focado na Caixa de Ferramentas de **Informação**.
- Quando pressiona `Tab` enquanto o foco está na Caixa de Ferramentas de **Informação,** o foco move-se para os pontos finais no mesmo continente que o nó selecionado. Utilize as chaves `Right arrow` e `Left arrow` para passar por estes pontos finais.
- Para se mover para outros pontos finais de fluxo ou aglomerados de continente, use `Tab` para movimentos avançados e `Shift+Tab` para o movimento retrógrado.
- Quando o foco estiver nos **clusters do Continente,** utilize as teclas de seta `Enter` ou `Down` para destacar os pontos finais dentro do aglomerado do continente. Para passar por pontos finais e pelo botão de fecho na caixa de informação do cluster do continente, utilize a chave `Right arrow` ou `Left arrow` para movimentos para a frente e para trás, respectivamente. Em qualquer ponto final, pode utilizar `Shift+L` para mudar para a linha de ligação do nó selecionado para o ponto final. Pode pressionar `Shift+L` novamente para passar ao ponto final selecionado.
        
### <a name="keyboard-navigation-at-any-stage"></a>Navegação de teclado em qualquer fase
    
- `Esc` colapsa a seleção expandida.
- A chave `Up arrow` realiza a mesma ação que `Esc`. A chave `Down arrow` realiza a mesma ação que `Enter`.
- Use `Shift+Plus` para ampliar e `Shift+Minus` para ampliar.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Como posso navegar usando o teclado na vista de topologia da rede virtual?

A página de topologia das redes virtuais contém duas secções principais:
    
- **Banner**: O banner na parte superior das redes virtuais topologia fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, redes virtuais conectadas, redes virtuais desligadas e IPs públicos). Quando se leciona um botão, o respetivo filtro é aplicado na topologia. Por exemplo, se selecionar o botão Ative, a topologia destaca as redes virtuais ativas na sua implementação.
- **Topologia**: Abaixo do banner, a secção de topologia mostra distribuição de tráfego entre redes virtuais.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação de teclado no banner
    
- Por padrão, a seleção na página de topologia das redes virtuais para o banner é o filtro "VNets Conectados".
- Para se mover para outro filtro, use a chave `Tab` para avançar. Para andar para trás, use a chave `Shift+Tab`. A navegação para a frente é da esquerda para a direita, seguida de cima para baixo.
- Prima `Enter` para aplicar o filtro selecionado. Com base na seleção e implantação do filtro, destacam-se os nódos os nós (rede virtual) sob a secção de topologia.
- Para alternar entre o banner e a topologia, prima `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navegação de teclado na topologia
    
- Depois de ter selecionado qualquer filtro no banner e pressionado `Ctrl+F6`, o foco move-se para um dos nós em destaque **(VNet**) na vista topologia.
- Para passar para outros nós destacados na vista topologia, use a chave `Shift+Right arrow` para o movimento para a frente. 
- Nos nós em destaque, o foco move-se para a Caixa de Ferramentas de **Informação** para o nó. Por predefinição, o foco move-se para o botão **Mais detalhes** na Caixa de Ferramentas de **Informação**. Para se mover ainda mais dentro da vista **Box,** utilize as chaves `Right arrow` e `Left arrow` para avançar e recuar, respectivamente. Premir `Enter` tem o mesmo efeito que selecionar o botão focado na Caixa de Ferramentas de **Informação**.
- Na seleção de tais nós, pode visitar todas as suas ligações, uma a uma, pressionando a chave `Shift+Left arrow`. O foco move-se para a Caixa de Ferramentas de **Informação** dessa ligação. Em qualquer momento, o foco pode ser deslocado de volta para o nó pressionando `Shift+Right arrow` novamente.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Como posso navegar usando o teclado na vista de topologia subnet?

A página de topologia das subredes virtuais contém duas secções principais:
    
- **Banner**: O banner na parte superior da topofologia das subredes virtuais fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, subnets Ativas, Médias e Gateway). Quando se leciona um botão, o respetivo filtro é aplicado na topologia. Por exemplo, se selecionar o botão Ative, a topologia destaca a subrede virtual ativa na sua implantação.
- **Topologia**: Abaixo do banner, a secção de topologia mostra distribuição de tráfego entre subredes virtuais.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação de teclado no banner
    
- Por padrão, a seleção na página de topologia das subredes virtuais para o banner é o filtro "Subnets".
- Para se mover para outro filtro, use a chave `Tab` para avançar. Para andar para trás, use a chave `Shift+Tab`. A navegação para a frente é da esquerda para a direita, seguida de cima para baixo.
- Prima `Enter` para aplicar o filtro selecionado. Com base na seleção e implantação do filtro, destacam-se os nódosos (Subnet) sob a secção de topologia.
- Para alternar entre o banner e a topologia, prima `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navegação de teclado na topologia
    
- Depois de ter selecionado qualquer filtro no banner e pressionado `Ctrl+F6`, o foco move-se para um dos nós destacados **(Subnet)** na vista de topologia.
- Para passar para outros nós destacados na vista topologia, use a chave `Shift+Right arrow` para o movimento para a frente. 
- Nos nós em destaque, o foco move-se para a Caixa de Ferramentas de **Informação** para o nó. Por predefinição, o foco move-se para o botão **Mais detalhes** na Caixa de Ferramentas de **Informação**. Para se mover ainda mais dentro da vista **Box,** utilize `Right arrow` e `Left arrow` chaves para avançar e recuar, respectivamente. Premir `Enter` tem o mesmo efeito que selecionar o botão focado na Caixa de Ferramentas de **Informação**.
- Na seleção de tais nós, pode visitar todas as suas ligações, uma a uma, pressionando `Shift+Left arrow` chave. O foco move-se para a Caixa de Ferramentas de **Informação** dessa ligação. Em qualquer momento, o foco pode ser deslocado de volta para o nó pressionando `Shift+Right arrow` novamente.    

