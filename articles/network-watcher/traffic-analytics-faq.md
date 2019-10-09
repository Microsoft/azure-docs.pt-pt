---
title: Perguntas frequentes sobre a análise de tráfego do Azure | Microsoft Docs
description: Obtenha respostas para algumas das perguntas mais frequentes sobre a análise de tráfego.
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
ms.date: 03/08/2018
ms.author: kumud
ms.openlocfilehash: b232df010190a95d12d5a57f076a4c1bf336cea4
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026594"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Análise de Tráfego perguntas frequentes

Este artigo coleta em um só lugar muitas das perguntas mais frequentes sobre a análise de tráfego no observador de rede do Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Quais são os pré-requisitos para usar a análise de tráfego?

Análise de Tráfego requer os seguintes pré-requisitos:

- Uma assinatura habilitada do observador de rede.
- Logs de fluxo do NSG (grupo de segurança de rede) habilitados para o NSGs que você deseja monitorar.
- Uma conta de armazenamento do Azure para armazenar logs de fluxo brutos.
- Um espaço de trabalho Log Analytics do Azure, com acesso de leitura e gravação.

Sua conta deve atender a um dos seguintes para habilitar a análise de tráfego:

- Sua conta deve ter uma das seguintes funções de RBAC (controle de acesso baseado em função) no escopo da assinatura: proprietário, colaborador, leitor ou colaborador de rede.
- Se sua conta não estiver atribuída a uma das funções listadas anteriormente, ela deverá ser atribuída a uma função personalizada atribuída às ações a seguir, no nível de assinatura.
            
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
        
Para verificar as funções atribuídas a um usuário para uma assinatura:

1. Entre no Azure usando **login-AzAccount**. 

2. Selecione a assinatura necessária usando **Select-AzSubscription**. 

3. Para listar todas as funções atribuídas a um usuário especificado, use **Get-AzRoleAssignment-SignInName [email do usuário]-IncludeClassicAdministrators**. 

Se você não estiver vendo nenhuma saída, entre em contato com o administrador da respectiva assinatura para obter acesso para executar os comandos. Para obter mais detalhes, consulte [gerenciar o controle de acesso baseado em função com o Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Em que as regiões do Azure estão Análise de Tráfego disponíveis?

Você pode usar a análise de tráfego para NSGs em qualquer uma das seguintes regiões com suporte:
- Canadá Central
- EUA Centro-Oeste
- East US
- EUA Leste 2
- EUA Centro-Norte
- EUA Centro-Sul
- EUA Central
- EUA Oeste
- EUA Oeste 2
- França Central
- Europa Ocidental
- Europa do Norte
- Sul do Brasil
- Reino Unido Oeste
- Reino Unido Sul
- Leste da Austrália
- Sudeste da Austrália 
- Ásia Oriental
- Sudeste Asiático
- Coreia do Sul Central
- Índia Central
- Sul da Índia
- Leste do Japão
- Oeste do Japão
- Gov (US) - Virginia

O espaço de trabalho Log Analytics deve existir nas seguintes regiões:
- Canadá Central
- EUA Centro-Oeste
- EUA Oeste
- EUA Oeste 2
- EUA Centro-Sul
- EUA Central
- East US
- EUA Leste 2
- França Central
- Europa Ocidental
- Europa do Norte
- Reino Unido Sul
- Leste da Austrália
- Sudeste da Austrália
- Ásia Oriental
- Sudeste Asiático 
- Coreia do Sul Central
- Índia Central
- Leste do Japão
- Gov (US) - Virginia

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>O NSGs permite que os logs de fluxo estejam em regiões diferentes do meu espaço de trabalho?

Sim, esses NSGs podem estar em regiões diferentes de seu espaço de trabalho Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Vários NSGs podem ser configurados em um único espaço de trabalho?

Sim.

## <a name="can-i-use-an-existing-workspace"></a>Posso usar um espaço de trabalho existente?

Sim. Se você selecionar um espaço de trabalho existente, certifique-se de que ele foi migrado para a nova linguagem de consulta. Se não quiser atualizar o espaço de trabalho, você precisará criar um novo. Para obter mais informações sobre a nova linguagem de consulta, consulte [Azure monitor logs atualizar para a nova pesquisa de logs](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Minha conta de armazenamento do Azure pode estar em uma assinatura e o meu espaço de trabalho Log Analytics estar em uma assinatura diferente?

Sim, sua conta de armazenamento do Azure pode estar em uma assinatura e seu espaço de trabalho Log Analytics pode estar em uma assinatura diferente.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Posso armazenar logs brutos em uma assinatura diferente?

Não. Você pode armazenar logs brutos em qualquer conta de armazenamento em que um NSG esteja habilitado para logs de fluxo. No entanto, tanto a conta de armazenamento quanto os logs brutos devem estar na mesma assinatura e região.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>E se eu não conseguir configurar um NSG para análise de tráfego devido a um erro "não encontrado"?

Selecione uma região com suporte. Se você selecionar uma região sem suporte, receberá um erro "não encontrado". As regiões com suporte são listadas anteriormente neste artigo.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>E se eu estiver recebendo o status, "falha ao carregar", na página de logs de fluxo do NSG?

O provedor Microsoft. insights deve ser registrado para que o log de fluxo funcione corretamente. Se você não tiver certeza se o provedor Microsoft. insights está registrado para sua assinatura, substitua *xxxxx-xxxxx-XXXXXX-XXXX* no comando a seguir e execute os seguintes comandos do PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Configurei a solução. Por que não vejo nada no painel?

O painel pode levar até 30 minutos para aparecer na primeira vez. A solução deve primeiro agregar dados suficientes para que ele derive informações significativas. Em seguida, ele gera relatórios. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>E se eu receber esta mensagem: "Não foi possível encontrar nenhum dado neste espaço de trabalho para o intervalo de tempo selecionado. Tente alterar o intervalo de tempo ou selecionar um espaço de trabalho diferente. "?

Tente as seguintes opções:
- Altere o intervalo de tempo na barra superior.
- Selecione um espaço de trabalho de Log Analytics diferente na barra superior.
- Tente acessar a análise de tráfego após 30 minutos, se ela tiver sido habilitada recentemente.
    
Se os problemas persistirem, aumente as preocupações no [Fórum de voz do usuário](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>E se eu receber esta mensagem: "Analisando os logs de fluxo do NSG pela primeira vez. Esse processo pode levar de 20-30 a minutos para ser concluído. Verifique novamente após algum tempo. 2) se a etapa acima não funcionar e o espaço de trabalho estiver sob a SKU gratuita, verifique o uso do espaço de trabalho aqui para validar sobre a cota, caso contrário, consulte as perguntas frequentes para obter mais informações. "?

Você pode ver esta mensagem porque:
- O Análise de Tráfego foi recentemente habilitado e talvez ainda não tenha agregado dados suficientes para que ele derive informações significativas.
- Você está usando a versão gratuita do espaço de trabalho Log Analytics e excedeu os limites de cota. Talvez seja necessário usar um espaço de trabalho com uma capacidade maior.
    
Se os problemas persistirem, aumente as preocupações no [Fórum de voz do usuário](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>E se eu receber esta mensagem: "Parece que temos dados de recursos (Topologia) e nenhuma informação de fluxo. Enquanto isso, clique aqui para ver os dados de recursos e consulte as perguntas frequentes para obter mais informações. "?

Você está vendo as informações de recursos no painel; no entanto, nenhuma estatística relacionada ao fluxo está presente. Os dados podem não estar presentes devido a nenhum fluxo de comunicação entre os recursos. Aguarde 60 minutos e verifique novamente o status. Se o problema persistir, e você tiver certeza de que os fluxos de comunicação entre os recursos existem, aumente as preocupações no [Fórum de voz do usuário](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Posso configurar a análise de tráfego usando o PowerShell ou um modelo ou cliente Azure Resource Manager?

Você pode configurar a análise de tráfego usando o Windows PowerShell da versão 6.2.1 em diante. Para configurar o log de fluxo e a análise de tráfego para um NSG específico usando o cmdlet Set, consulte [set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Para obter o log de fluxo e o status da análise de tráfego para um NSG específico, consulte [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

No momento, você não pode usar um modelo de Azure Resource Manager para configurar a análise de tráfego.

Para configurar a análise de tráfego usando um cliente Azure Resource Manager, consulte os exemplos a seguir.

**Exemplo do cmdlet Set:**
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
**Exemplo de Get cmdlet:**
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


## <a name="how-is-traffic-analytics-priced"></a>Como o preço é Análise de Tráfego?

Análise de Tráfego é medido. A medição é baseada no processamento de dados de log de fluxo pelo serviço e no armazenamento dos logs aprimorados resultantes em um espaço de trabalho Log Analytics. 

Por exemplo, de acordo com o [plano de preços](https://azure.microsoft.com/pricing/details/network-watcher/), considerando a região Oeste EUA Central, se o fluxo registra os dados armazenados em uma conta de armazenamento processada pelo análise de tráfego é de 10 GB e os logs aprimorados ingeridos no espaço de trabalho log Analytics são 1 GB, os encargos aplicáveis são: 10 x $2.03 + 1 x $2.76 = $25.76

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Com que frequência Análise de Tráfego processar dados?

Consulte a [seção de agregação de dados](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) no esquema de análise de tráfego e no documento de agregação de dados

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Como Análise de Tráfego decidir que um IP é mal-intencionado? 

Análise de Tráfego se baseia nos sistemas internos de inteligência contra ameaças da Microsoft para considerar um IP como mal-intencionado. Esses sistemas aproveitam fontes de telemetria diferentes, como produtos e serviços da Microsoft, a DCU (unidade de crimes digitais da Microsoft), o MSRC (Microsoft Security Response Center) e os feeds externos e criam uma grande quantidade de inteligência sobre ele. Alguns desses dados são internos da Microsoft. Se um IP conhecido estiver sendo sinalizado como mal-intencionado, gere um tíquete de suporte para saber os detalhes.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Como posso definir alertas em Análise de Tráfego dados?

Análise de Tráfego não tem suporte interno para alertas. No entanto, como Análise de Tráfego dados são armazenados em Log Analytics você pode escrever consultas personalizadas e definir alertas neles. Tarefas
- Você pode usar o Shortlink para Log Analytics no Análise de Tráfego. 
- Use o [esquema documentado aqui](traffic-analytics-schema.md) para escrever suas consultas 
- Clique em "nova regra de alerta" para criar o alerta
- Consulte a [documentação de alertas de log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) para criar o alerta

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Como posso navegar usando o teclado na exibição de mapa geográfico?

A página de mapa geográfico contém duas seções principais:
    
- **Faixa**: A faixa na parte superior do mapa geográfico fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, implantação, tráfego de países/regiões e mal-intencionado). Quando você seleciona um botão, o respectivo filtro é aplicado no mapa. Por exemplo, se você selecionar o botão ativo, o mapa realçará os data centers ativos em sua implantação.
- **Mapa**: Abaixo da faixa, a seção de mapa mostra a distribuição de tráfego entre os data centers do Azure e os países/regiões.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação de teclado na faixa
    
- Por padrão, a seleção na página de mapa geográfico para a faixa é o filtro "Azure DCs".
- Para mover para outro filtro, use a chave `Tab` ou `Right arrow`. Para retroceder, use a chave `Shift+Tab` ou `Left arrow`. A navegação progressiva é da esquerda para a direita, seguida de cima para baixo.
- Pressione `Enter` ou a tecla de seta `Down` para aplicar o filtro selecionado. Com base na seleção e implantação de filtro, um ou vários nós na seção mapa são realçados.
- Para alternar entre faixa e mapa, pressione `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Navegação de teclado no mapa
    
- Depois de selecionar qualquer filtro na faixa e pressionar `Ctrl+F6`, o foco será movido para um dos nós realçados (**Datacenter do Azure** ou **país/região**) na exibição do mapa.
- Para mover para outros nós realçados no mapa, use `Tab` ou a chave `Right arrow` para movimento progressivo. Use `Shift+Tab` ou a chave `Left arrow` para movimento regressivo.
- Para selecionar qualquer nó realçado no mapa, use a chave `Enter` ou `Down arrow`.
- Na seleção de qualquer um desses nós, o foco é movido para a **caixa de ferramentas de informações** para o nó. Por padrão, o foco é movido para o botão fechado na **caixa ferramenta de informações**. Para mover-se ainda mais dentro do modo de exibição de **caixa** , use as teclas `Right arrow` e `Left arrow` para avançar e retroceder, respectivamente. Pressionar `Enter` tem o mesmo efeito que selecionar o botão focalizado na **caixa ferramenta de informações**.
- Quando você pressiona `Tab` enquanto o foco está na **caixa de ferramentas de informações**, o foco é movido para os pontos de extremidade no mesmo continente que o nó selecionado. Use as chaves `Right arrow` e `Left arrow` para percorrer esses pontos de extremidade.
- Para mover para outros pontos de extremidade do fluxo ou para os clusters continentes, use `Tab` para movimento progressivo e `Shift+Tab` para movimento regressivo.
- Quando o foco estiver em **clusters continentes**, use as teclas de seta `Enter` ou `Down` para realçar os pontos de extremidade dentro do cluster continente. Para percorrer os pontos de extremidade e o botão fechar na caixa de informações do cluster continente, use a chave `Right arrow` ou `Left arrow` para movimento para frente e para trás, respectivamente. Em qualquer ponto de extremidade, você pode usar `Shift+L` para alternar para a linha de conexão do nó selecionado para o ponto de extremidade. Você pode pressionar `Shift+L` novamente para mover para o ponto de extremidade selecionado.
        
### <a name="keyboard-navigation-at-any-stage"></a>Navegação por teclado em qualquer estágio
    
- `Esc` recolhe a seleção expandida.
- A chave `Up arrow` executa a mesma ação que `Esc`. A chave `Down arrow` executa a mesma ação que `Enter`.
- Use `Shift+Plus` para ampliar e `Shift+Minus` para reduzir.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Como posso navegar usando o teclado na exibição de topologia de rede virtual?

A página topologia de redes virtuais contém duas seções principais:
    
- **Faixa**: A faixa na parte superior da topologia de redes virtuais fornece botões para selecionar os filtros de distribuição de tráfego (por exemplo, redes virtuais conectadas, redes virtuais desconectadas e IPs públicos). Quando você seleciona um botão, o respectivo filtro é aplicado na topologia. Por exemplo, se você selecionar o botão ativo, a topologia destacará as redes virtuais ativas em sua implantação.
- **Topologia**: Abaixo da faixa, a seção topologia mostra a distribuição de tráfego entre redes virtuais.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação de teclado na faixa
    
- Por padrão, a seleção na página de topologia de redes virtuais para a faixa é o filtro "conectado VNets".
- Para mover para outro filtro, use a chave `Tab` para avançar. Para retroceder, use a chave `Shift+Tab`. A navegação progressiva é da esquerda para a direita, seguida de cima para baixo.
- Pressione `Enter` para aplicar o filtro selecionado. Com base na seleção e implantação de filtro, um ou vários nós (rede virtual) na seção topologia são realçados.
- Para alternar entre a faixa e a topologia, pressione `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navegação de teclado na topologia
    
- Depois de selecionar qualquer filtro na faixa e pressionar `Ctrl+F6`, o foco é movido para um dos nós realçados (**VNet**) no modo de exibição de topologia.
- Para mover para outros nós realçados no modo de exibição de topologia, use a chave `Shift+Right arrow` para movimento progressivo. 
- Em nós realçados, o foco é movido para a **caixa de ferramentas de informações** para o nó. Por padrão, o foco é movido para o botão **mais detalhes** na **caixa de ferramentas de informações**. Para mover-se ainda mais dentro do modo de exibição de **caixa** , use as teclas `Right arrow` e `Left arrow` para avançar e retroceder, respectivamente. Pressionar `Enter` tem o mesmo efeito que selecionar o botão focalizado na **caixa ferramenta de informações**.
- Na seleção de qualquer um desses nós, você pode visitar todas as suas conexões, uma a uma, pressionando a tecla `Shift+Left arrow`. O foco é movido para a **caixa de ferramentas de informações** dessa conexão. A qualquer momento, o foco pode ser deslocado de volta para o nó pressionando `Shift+Right arrow` novamente.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Como posso navegar usando o teclado na exibição de topologia de sub-rede?

A página topologia de sub-redes virtuais contém duas seções principais:
    
- **Faixa**: A faixa na parte superior da topologia de sub-redes virtuais fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, sub-redes ativas, médias e de gateway). Quando você seleciona um botão, o respectivo filtro é aplicado na topologia. Por exemplo, se você selecionar o botão ativo, a topologia destacará a sub-rede virtual ativa em sua implantação.
- **Topologia**: Abaixo da faixa, a seção topologia mostra a distribuição de tráfego entre sub-redes virtuais.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação de teclado na faixa
    
- Por padrão, a seleção na página topologia de sub-redes virtuais para a faixa é o filtro "sub-redes".
- Para mover para outro filtro, use a chave `Tab` para avançar. Para retroceder, use a chave `Shift+Tab`. A navegação progressiva é da esquerda para a direita, seguida de cima para baixo.
- Pressione `Enter` para aplicar o filtro selecionado. Com base na seleção e implantação de filtro, um ou vários nós (sub-rede) na seção topologia são realçados.
- Para alternar entre a faixa e a topologia, pressione `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navegação de teclado na topologia
    
- Depois de selecionar qualquer filtro na faixa e pressionar `Ctrl+F6`, o foco será movido para um dos nós realçados (**sub-rede**) no modo de exibição de topologia.
- Para mover para outros nós realçados no modo de exibição de topologia, use a chave `Shift+Right arrow` para movimento progressivo. 
- Em nós realçados, o foco é movido para a **caixa de ferramentas de informações** para o nó. Por padrão, o foco é movido para o botão **mais detalhes** na **caixa de ferramentas de informações**. Para mover-se ainda mais dentro do modo de exibição de **caixa** , use as teclas `Right arrow` e `Left arrow` para avançar e retroceder, respectivamente. Pressionar `Enter` tem o mesmo efeito que selecionar o botão focalizado na **caixa ferramenta de informações**.
- Na seleção de qualquer um desses nós, você pode visitar todas as suas conexões, uma a uma, pressionando a tecla `Shift+Left arrow`. O foco é movido para a **caixa de ferramentas de informações** dessa conexão. A qualquer momento, o foco pode ser deslocado de volta para o nó pressionando `Shift+Right arrow` novamente.    

