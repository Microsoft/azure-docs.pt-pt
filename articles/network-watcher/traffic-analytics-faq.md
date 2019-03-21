---
title: Análise de tráfego do Azure perguntas mais frequentes | Documentos da Microsoft
description: Obtenha respostas para algumas das perguntas mais frequentes sobre a análise de tráfego.
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
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 41b80e3914c93a4baeb39f35c7281f8fe81ff37c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835321"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Perguntas mais frequentes sobre a análise de tráfego

Este artigo recolhe num único local muitas das perguntas mais frequentes sobre a análise de tráfego no observador de rede do Azure.

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Quais são os pré-requisitos para utilizar a análise de tráfego?

Análise de tráfego requer os seguintes pré-requisitos:

- Uma subscrição do observador de rede ativado.
- Registos de fluxo do grupo de segurança de rede (NSG) ativados para os NSGs que pretende monitorizar.
- Uma conta de armazenamento do Azure, para armazenar os registos de fluxo não processados.
- Um trabalho de Log Analytics do Azure, com acesso de escrita e leitura.

Sua conta tem de cumprir um dos seguintes procedimentos para ativar a análise de tráfego:

- Sua conta tem de ter qualquer uma das seguintes funções de controlo (RBAC) de acesso baseado em funções no âmbito da subscrição: proprietário, Contribuidor, leitor ou contribuinte de rede.
- Se a sua conta não está atribuída a uma das funções listadas anteriormente, tem de ser atribuído a uma função personalizada que é atribuída as seguintes ações, ao nível da subscrição.
            
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

1. Inicie sessão no Azure, utilizando **Login-AzureRmAccount**. 

2. Selecione a subscrição que se necessário, utilizando **Select-AzureRmSubscription**. 

3. Para listar todas as funções que são atribuídas a um utilizador especificado, utilize **Get-AzureRmRoleAssignment - SignInName [e-mail do utilizador] - IncludeClassicAdministrators**. 

Se não está a ver quaisquer dados, contacte o administrador de subscrição relevante para obter acesso para executar os comandos. Para obter mais detalhes, consulte [gerir o controlo de acesso baseado em funções com o Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Em que regiões do Azure é a análise de tráfego disponível?

Pode utilizar a análise de tráfego para NSGs em qualquer uma das seguintes regiões suportadas:
- Canadá Central
- EUA Centro-Oeste
- EUA Leste
- EUA Leste 2
- EUA Centro-Norte
- EUA Centro-Sul
- EUA Central
- EUA Oeste
- EUA Oeste 2
- Europa Ocidental
- Europa do Norte
- Sul do Brasil
- Reino Unido Oeste
- Reino Unido Sul
- Leste da Austrália
- Sudeste da Austrália 
- Sudeste Asiático
- Índia Central
- Sul da Índia
- Leste do Japão
- Gov (US) - Virginia

A área de trabalho do Log Analytics têm de existir nas seguintes regiões:
- Canadá Central
- EUA Centro-Oeste
- EUA Leste
- Europa Ocidental
- Reino Unido Sul
- Sudeste da Austrália
- Sudeste Asiático 
- Índia Central
- Leste do Japão
- Gov (US) - Virginia

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Pode os NSGs habilitar o fluxo de registos para estar em regiões diferentes do que a minha área de trabalho?

Sim, esses NSGs podem ser em regiões diferentes do que a área de trabalho do Log Analytics.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Podem ser configurados vários NSGs dentro de um único espaço de trabalho?

Sim.

## <a name="can-i-use-an-existing-workspace"></a>Pode utilizar uma área de trabalho existente?

Sim. Se selecionar uma área de trabalho existente, certifique-se de que foi migrada para a nova linguagem de consulta. Se não pretender atualizar a área de trabalho, terá de criar um novo. Para obter mais informações sobre a nova linguagem de consulta, consulte [do Azure Monitor registos de atualização para a nova pesquisa de registo](../log-analytics/log-analytics-log-search-upgrade.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Pode ser de minha conta de armazenamento do Azure numa subscrição e minha área de trabalho do Log Analytics ser numa subscrição diferente?

Sim, a conta de armazenamento do Azure pode estar numa subscrição, e sua área de trabalho do Log Analytics pode estar numa subscrição diferente.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Pode armazenar os registos não processados numa subscrição diferente?

Não. Pode armazenar os registos não processados em qualquer conta de armazenamento onde um NSG está ativado para os registos de fluxo. No entanto, a conta de armazenamento e os registos não processados tem de ser na mesma subscrição e região.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>E se eu não é possível configurar um NSG para análise de tráfego devido a um erro "Não encontrada"?

Selecione uma região suportada. Se selecionar uma região sem suporte, receberá um erro "Não encontrado". As regiões suportadas são apresentadas anteriormente neste artigo.

## <a name="why-am-i-getting-the-error-failed-to-update-flow-logs-settings-for--internalservererror-when-enabling-nsgs-in-us-gov-virginia"></a>Por que eu ganho o erro "Falha ao atualizar as definições de registos de fluxo para... InternalServerError..." Quando ativar NSG em Virgínia gov (US)?

Isso é devido a um erro em que o fornecedor de recursos "Microsoft. Network" não é novamente registado para obter uma subscrição em Virgínia gov (US). A equipe está trabalhando na correção. Como solução, precisaria [manualmente voltar a registar 'Microsoft. Network"RP](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-register-provider-errors). 

Contacte o suporte se o problema persistir. 

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>E se estou a obter o estado "Falha ao carregar," sob a página de registos de fluxo NSG?

O fornecedor de Microsoft. insights tem de estar registado para o flow logging funcione corretamente. Se não tiver a certeza se o fornecedor de Microsoft. insights está registado para a sua subscrição, substitua *xxxxx-xxxxx-xxxxxx-xxxx* no comando seguinte e execute os seguintes comandos do PowerShell:

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Configurei a solução. Por que não estou a ver nada no dashboard?

O dashboard pode demorar até 30 minutos a aparecer na primeira vez. A solução deve primeiro agregar dados suficientes para o mesmo derivar informações significativas. Em seguida, ela gera relatórios. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>E se receber esta mensagem: "Não foi possível encontrar quaisquer dados nesta área de trabalho para o intervalo de tempo selecionado. Tente alterar o intervalo de tempo ou selecione uma área de trabalho diferente. "?

Experimente as seguintes opções:
- Altere o intervalo de tempo na barra superior.
- Selecione uma área de trabalho do Log Analytics diferente na barra superior.
- Tente aceder a análise de tráfego após 30 minutos, se tiver sido recentemente ativada.
    
Se os problemas persistirem, criar problemas na [fórum do uservoice](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>E se receber esta mensagem: "A analisar os registos de fluxo do NSG pela primeira vez. Este processo pode demorar 20-30 minutos a concluir. Verifique novamente após algum tempo. 2) se o passo acima não funciona e sua área de trabalho está sob o SKU gratuito, em seguida, verifique a utilização da área de trabalho aqui para validar através de quota, ou consulte a FAQ para obter mais informações. "?

Poderá ver esta mensagem porque:
- Análise de tráfego tiver sido recentemente ativada e poderá não ainda ter agregados dados suficientes para o mesmo derivar informações significativas.
- Estiver a utilizar a versão gratuita da área de trabalho do Log Analytics e excedeu os limites de quota. Poderá ter de utilizar uma área de trabalho com uma capacidade maior.
    
Se os problemas persistirem, criar problemas na [fórum do uservoice](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>E se receber esta mensagem: "Parece que temos dados de recursos (topologia) e nenhuma informação de fluxos. Enquanto isso, clique aqui para ver dados de recursos e consulte FAQ para obter mais informações. "?

Está a ver as informações de recursos no dashboard; No entanto, não há estatística relacionados com o fluxo está presente. Dados não podem ser apresentados devido a não existem fluxos de comunicação entre os recursos. Aguarde durante 60 minutos e verificar o estado. Se o problema persistir, e tem a certeza de que existem fluxos de comunicação entre os recursos, criar problemas na [fórum do uservoice](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Posso configurar a análise de tráfego com o PowerShell ou um modelo Azure Resource Manager ou o cliente?

Pode configurar a análise de tráfego utilizando o Windows PowerShell da versão 6.2.1 e posteriores. Para configurar o registo do fluxo e análise de tráfego para um NSG específico utilizando o cmdlet do conjunto, consulte [Set-AzureRmNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0). Para obter o registo do fluxo e o estado de análise de tráfego para um NSG específico, consulte [Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0).

Atualmente, não é possível utilizar um modelo Azure Resource Manager para configurar a análise de tráfego.

Para configurar a análise de tráfego com um cliente do Azure Resource Manager, consulte os exemplos seguintes.

**Exemplo de cmdlet do conjunto:**
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
**Obter o exemplo de cmdlet:**
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



## <a name="how-is-traffic-analytics-priced"></a>Como é cobrada a análise de tráfego?

Análise de tráfego é medida. A medição baseia-se no processamento de fluxo de dados de registo pelo serviço e armazenar resultante avançadas registos numa área de trabalho do Log Analytics. 

Por exemplo, como pela [plano de preços](https://azure.microsoft.com/pricing/details/network-watcher/), Considerando a região e.u.a. centro-oeste, se os registos de fluxo de dados armazenados numa conta de armazenamento processada pela análise de tráfego é de 10 GB e aprimorados registos ingeridos na área de trabalho do Log Analytics é de 1 GB, em seguida, o são aplicáveis encargos: 10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Como navegar usando o teclado na vista de mapa de geográfico

A página de mapa de geográfico contém duas secções principais:
    
- **Faixa**: A faixa na parte superior do mapa geográfico fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, implementação, o tráfego de países e maliciosos). Quando seleciona um botão, o respetivo filtro é aplicado no mapa. Por exemplo, se selecionar o botão de Active Directory, o mapa realça os datacenters do Active Directory na sua implementação.
- **Mapa**: Abaixo da faixa, a seção de mapa mostra a distribuição de tráfego entre os datacenters do Azure e países.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação do teclado na faixa
    
- Por predefinição, a seleção na página de mapa geográfico para a faixa é o filtro de "Controladores de domínio do Azure".
- Para mover para outro filtro, utilize o `Tab` ou o `Right arrow` chave. Para mover para trás, utilize o `Shift+Tab` ou o `Left arrow` chave. Para navegação progressiva é deixada para a direita, seguido de cima para baixo.
- Prima `Enter` ou o `Down` tecla de seta para aplicar o filtro selecionado. Com base na seleção de filtros e a implantação, um ou vários nós sob a secção de mapa estão realçadas.
- Para alternar entre a faixa e o mapa, prima `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Navegação do teclado no mapa
    
- Depois de ter selecionado qualquer filtro na faixa e premido `Ctrl+F6`, foco move para um de nós realçado (**datacenter do Azure** ou **país/região**) na vista de mapa.
- Para mover para outros nós realçada no mapa, utilize `Tab` ou o `Right arrow` chave para movimento de avançar. Uso `Shift+Tab` ou o `Left arrow` chave para movimento com versões anteriores.
- Para selecionar qualquer nó realçada no mapa, utilize o `Enter` ou `Down arrow` chave.
- Na seleção de quaisquer nós desse tipo, o foco muda para o **caixa de ferramenta de informações** para o nó. Por predefinição, foco move para o botão fechado a **caixa de ferramenta de informações**. Ainda mais mover dentro de **caixa** ver, utilize `Right arrow` e `Left arrow` chaves para mover para frente e para trás, respectivamente. Premir `Enter` tem o mesmo efeito que selecionar o botão focado na **caixa de ferramenta de informações**.
- Quando pressiona `Tab` enquanto o foco está a ser o **caixa de ferramenta de informações**, o foco muda para os pontos finais no mesmo continente, como o nó selecionado. Utilize o `Right arrow` e `Left arrow` chaves passar por estes pontos finais.
- Para mover para outros pontos finais de fluxo ou continente clusters, utilize `Tab` para movimento de avançar e `Shift+Tab` para movimento com versões anteriores.
- Quando o foco está ativado **clusters do continente**, utilize o `Enter` ou `Down` teclas de seta para destacar os pontos finais dentro do cluster continente. Para mover-se através de pontos de extremidade e no botão Fechar na caixa de informações do cluster continente, utilize o `Right arrow` ou `Left arrow` chave para frente e para trás movimento, respectivamente. Em qualquer ponto final, pode usar `Shift+L` para mudar para a linha de ligação do nó selecionado para o ponto final. Pode premir `Shift+L` novamente para mover para o ponto de extremidade selecionado.
        
### <a name="keyboard-navigation-at-any-stage"></a>Navegação do teclado em qualquer fase
    
- `Esc` reduz a seleção expandida.
- O `Up arrow` chave executa a mesma ação como `Esc`. O `Down arrow` chave executa a mesma ação como `Enter`.
- Uso `Shift+Plus` para aplicar zoom no, e `Shift+Minus` zoom.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Como pode navegar usando o teclado na vista de topologia de rede virtual?

A página de topologia de redes virtuais contém duas secções principais:
    
- **Faixa**: A faixa na parte superior da topologia de redes virtuais fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, redes virtuais ligadas, redes virtuais desligados e IPs públicos). Quando seleciona um botão, o respetivo filtro é aplicado a topologia. Por exemplo, se selecionar o botão de Active Directory, a topologia destaca as redes virtuais Active Directory na sua implementação.
- **Topologia**: Abaixo da faixa, a secção de topologia mostra a distribuição de tráfego entre redes virtuais.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação do teclado na faixa
    
- Por predefinição, a seleção na página de topologia de redes virtuais para a faixa é o filtro de "VNets ligadas".
- Para mover para outro filtro, utilize o `Tab` chave para seguir em frente. Para mover para trás, utilize o `Shift+Tab` chave. Para navegação progressiva é deixada para a direita, seguido de cima para baixo.
- Prima `Enter` para aplicar o filtro selecionado. Um ou vários nós (rede virtual) na secção topologia com base na seleção de filtros e implementação, são realçados.
- Para alternar entre a faixa e a topologia, prima `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navegação do teclado na topologia
    
- Depois de ter selecionado qualquer filtro na faixa e premido `Ctrl+F6`, foco move para um de nós realçado (**VNet**) na vista de topologia.
- Para mover para outros nós realçado na vista de topologia, utilize o `Shift+Right arrow` chave para movimento de avançar. 
- Em nós realçado, o foco se move para o **caixa de ferramenta de informações** para o nó. Por predefinição, o foco muda para o **mais detalhes** botão a **caixa de ferramenta de informações**. Ainda mais mover dentro do **caixa** ver, utilize o `Right arrow` e `Left arrow` chaves para mover para frente e para trás, respectivamente. Premir `Enter` tem o mesmo efeito que selecionar o botão focado na **caixa de ferramenta de informações**.
- Na seleção de quaisquer nós desse tipo, pode visitar todas as suas ligações, individualmente, ao premir o `Shift+Left arrow` chave. Foco muda para o **caixa de ferramenta de informações** essa ligação. Em qualquer momento, pode ser mudou o foco novamente para o nó ao premir `Shift+Right arrow` novamente.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Como navegar usando o teclado na vista de topologia de sub-rede

A página de topologia de sub-redes virtuais contém duas secções principais:
    
- **Faixa**: A faixa na parte superior da topologia de sub-redes virtuais fornece botões para selecionar filtros de distribuição de tráfego (por exemplo, sub-redes de Active Directory, médio e Gateway). Quando seleciona um botão, o respetivo filtro é aplicado a topologia. Por exemplo, se selecionar o botão de Active Directory, a topologia realça a sub-rede virtual Active Directory na sua implementação.
- **Topologia**: Abaixo da faixa, a secção de topologia mostra a distribuição de tráfego entre sub-redes virtuais.
    
### <a name="keyboard-navigation-on-the-banner"></a>Navegação do teclado na faixa
    
- Por predefinição, a seleção na página de topologia de sub-redes virtuais para a faixa é o filtro de "Sub-redes".
- Para mover para outro filtro, utilize o `Tab` chave para seguir em frente. Para mover para trás, utilize o `Shift+Tab` chave. Para navegação progressiva é deixada para a direita, seguido de cima para baixo.
- Prima `Enter` para aplicar o filtro selecionado. Com base na seleção de filtros e a implantação, um ou vários nós (sub-rede) na secção topologia são realçadas.
- Para alternar entre a faixa e a topologia, prima `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Navegação do teclado na topologia
    
- Depois de ter selecionado qualquer filtro na faixa e premido `Ctrl+F6`, foco move para um de nós realçado (**sub-rede**) na vista de topologia.
- Para mover para outros nós realçado na vista de topologia, utilize o `Shift+Right arrow` chave para movimento de avançar. 
- Em nós realçado, o foco se move para o **caixa de ferramenta de informações** para o nó. Por predefinição, o foco muda para o **mais detalhes** botão a **caixa de ferramenta de informações**. Ainda mais mover dentro de **caixa** ver, utilize `Right arrow` e `Left arrow` chaves para mover para frente e para trás, respectivamente. Premir `Enter` tem o mesmo efeito que selecionar o botão focado na **caixa de ferramenta de informações**.
- Na seleção de quaisquer nós desse tipo, pode visitar todas as suas ligações, individualmente, ao premir `Shift+Left arrow` chave. Foco muda para o **caixa de ferramenta de informações** essa ligação. Em qualquer momento, pode ser mudou o foco novamente para o nó ao premir `Shift+Right arrow` novamente.    

