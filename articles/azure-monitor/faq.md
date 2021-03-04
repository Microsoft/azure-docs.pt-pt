---
title: Azure Monitor FAQ | Microsoft Docs
description: Respostas a perguntas frequentes sobre o Azure Monitor.
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2020
ms.openlocfilehash: 5b9b0c6a0fe08ccff9da59539b926270cd0e1d44
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032859"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor perguntas frequentes

Este Microsoft FAQ é uma lista de perguntas comumente feitas sobre o Azure Monitor. Se tiver alguma pergunta adicional, vá ao [fórum de discussão](/answers/questions/topics/single/24223.html) e publique as suas perguntas. Quando uma pergunta é frequentemente feita, adicionamo-la a este artigo para que possa ser encontrada de forma rápida e fácil.


## <a name="general"></a>Geral

### <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
[O Azure Monitor](overview.md) é um serviço em Azure que fornece monitorização de desempenho e disponibilidade para aplicações e serviços em Azure, outros ambientes em nuvem ou no local. O Azure Monitor recolhe dados de múltiplas fontes numa plataforma comum de dados onde pode ser analisado para tendências e anomalias. As funcionalidades ricas no Azure Monitor ajudam-no a identificar e responder rapidamente a situações críticas que podem afetar a sua aplicação.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Qual é a diferença entre O Monitor Azure, Log Analytics e Application Insights?
Em setembro de 2018, a Microsoft combinou o Azure Monitor, o Log Analytics e o Application Insights num único serviço para fornecer uma monitorização de ponta a ponta poderosa das suas aplicações e dos componentes em que confiam. As funcionalidades em Log Analytics e Application Insights não foram alteradas, embora algumas funcionalidades tenham sido remarcadas para o Azure Monitor de forma a refletir melhor o seu novo âmbito. O motor de dados de registo e a linguagem de consulta do Log Analytics são agora referidos como Registos monitores Azure. Consulte [as atualizações da terminologia do Azure Monitor](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Quanto custa o Azure Monitor?
As funcionalidades do Azure Monitor que são automaticamente ativadas, tais como a recolha de métricas e registos de atividades são fornecidas sem custos. Há um custo associado a outras funcionalidades, tais como consultas de log e alerta. Consulte a página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter informações detalhadas sobre os preços.

### <a name="how-do-i-enable-azure-monitor"></a>Como posso ativar o Azure Monitor?
O Azure Monitor está ativado no momento em que cria uma nova subscrição do Azure, e as métricas [de registo](./essentials/platform-logs-overview.md) de atividade e plataforma são automaticamente [recolhidas.](essentials/data-platform-metrics.md) Crie [configurações de diagnóstico](essentials/diagnostic-settings.md) para recolher informações mais detalhadas sobre o funcionamento dos seus recursos Azure e adicione soluções de monitorização e [insights](./monitor-reference.md) para fornecer [análises](insights/solutions.md) adicionais sobre dados recolhidos para determinados serviços. 

### <a name="how-do-i-access-azure-monitor"></a>Como acesso ao Monitor Azure?
Aceda a todas as funcionalidades e dados do Azure Monitor **no** menu Azure. A secção de **Monitorização** do menu de diferentes serviços Azure fornece acesso às mesmas ferramentas com dados filtrados a um determinado recurso. Os dados do Azure Monitor também estão acessíveis para uma variedade de cenários usando CLI, PowerShell e uma API REST.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Existe uma versão no local do Azure Monitor?
N.º O Azure Monitor é um serviço de nuvem escalável que processa e armazena grandes quantidades de dados, embora o Azure Monitor possa monitorizar recursos que estão no local e em outras nuvens.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>O Azure Monitor pode monitorizar os recursos no local?
Sim, além de recolher dados de monitorização a partir de recursos Azure, o Azure Monitor pode recolher dados de máquinas virtuais e aplicações em outras nuvens e no local. Consulte [as fontes de monitorização dos dados do Azure Monitor](agents/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>O Azure Monitor integra-se com o Gestor de Operações do Centro de Sistema?
Pode ligar o seu grupo de gestão de operações do System Center ao Azure Monitor para recolher dados de agentes em Registos do Monitor Azure. Isto permite-lhe utilizar consultas de registo e solução para analisar dados recolhidos de agentes. Também pode configurar os agentes gestores de operações do Centro de Sistema existentes para enviar dados diretamente para o Azure Monitor. Consulte [o Gestor de Operações de Ligação ao Monitor Azure.](agents/om-agents.md)

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Que endereços IP utiliza o Azure Monitor?
Consulte [endereços IP utilizados por Insights de Aplicação e Log Analytics](app/ip-addresses.md) para uma listagem dos endereços IP e portas necessárias para que agentes e outros recursos externos acedam ao Monitor Azure. 

## <a name="monitoring-data"></a>Monitorizar dados

### <a name="where-does-azure-monitor-get-its-data"></a>Onde é que o Azure Monitor obtém os seus dados?
O Azure Monitor recolhe dados de uma variedade de fontes, incluindo registos e métricas da plataforma e recursos Azure, aplicações personalizadas e agentes que executam em máquinas virtuais. Outros serviços, como o Azure Security Center e o Network Watcher, recolhem dados num espaço de trabalho do Log Analytics para que possam ser analisados com dados do Azure Monitor. Também pode enviar dados personalizados para o Azure Monitor utilizando a API REST para registos ou métricas. Consulte [as fontes de monitorização dos dados do Azure Monitor](agents/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Que dados são recolhidos pelo Azure Monitor? 
O Azure Monitor recolhe dados de uma variedade de fontes em [registos](logs/data-platform-logs.md) ou [métricas.](essentials/data-platform-metrics.md) Cada tipo de dados tem as suas vantagens relativas, e cada um suporta um conjunto particular de funcionalidades no Azure Monitor. Existe uma única base de dados de métricas para cada subscrição do Azure, enquanto pode criar vários espaços de trabalho do Log Analytics para recolher registos dependendo dos seus requisitos. Consulte [a plataforma de dados Azure Monitor](data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Há uma quantidade máxima de dados que posso recolher no Azure Monitor?
Não há limite para a quantidade de dados métricos que pode recolher, mas estes dados são armazenados por um máximo de 93 dias. Ver [Retenção de Métricas](essentials/data-platform-metrics.md#retention-of-metrics). Não existe limite para a quantidade de dados de registo que pode recolher, mas pode ser afetado pelo nível de preços que escolher para o espaço de trabalho Log Analytics. Consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/monitor/)

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Como acedo aos dados recolhidos pelo Azure Monitor?
Insights e soluções proporcionam uma experiência personalizada para trabalhar com dados armazenados no Azure Monitor. Pode trabalhar diretamente com dados de registo utilizando uma consulta de registo escrita em Língua de Consulta de Kusto (KQL). No portal Azure, pode escrever e executar consultas e analisar interativamente dados usando o Log Analytics. Analise as métricas no portal Azure com o Explorador de Métricas. Consulte [os dados de registo de análise no Azure Monitor](logs/log-query-overview.md) e começar com o [Azure Metrics Explorer](essentials/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Soluções e insights

### <a name="what-is-an-insight-in-azure-monitor"></a>O que é uma visão no Azure Monitor?
Os insights fornecem uma experiência de monitorização personalizada para determinados serviços Azure. Eles usam as mesmas métricas e registos que outras funcionalidades no Azure Monitor, mas podem recolher dados adicionais e fornecer uma experiência única no portal Azure. Ver [Insights no Monitor Azure](./monitor-reference.md).

Para visualizar informações no portal Azure, consulte a secção **insights** do menu **Monitor** ou a secção de **Monitorização** do menu do serviço.

### <a name="what-is-a-solution-in-azure-monitor"></a>O que é uma solução no Azure Monitor?
As soluções de monitorização são conjuntos de lógica embalados para monitorizar uma determinada aplicação ou serviço com base nas funcionalidades do Azure Monitor. Recolhem dados de registo no Azure Monitor e fornecem consultas de registo e visualizações para a sua análise utilizando uma experiência comum no portal Azure. Consulte [as soluções de monitorização no Azure Monitor](insights/solutions.md).

Para ver soluções no portal Azure, clique em **Mais** na secção **Insights** do menu **Monitor.** Clique **em Adicionar** para adicionar soluções adicionais ao espaço de trabalho.

## <a name="logs"></a>Registos

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Qual é a diferença entre os Registos monitores Azure e o Azure Data Explorer?
O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. Os Registos Azure Monitor são construídos em cima do Azure Data Explorer e utilizam a mesma linguagem de consulta Kusto (KQL) com algumas diferenças menores. Consulte [as diferenças linguísticas de consulta de registo do Azure Monitor](/azure/data-explorer/kusto/query/).

### <a name="how-do-i-retrieve-log-data"></a>Como recupero dados de registo?
Todos os dados são obtidos a partir de um espaço de trabalho Log Analytics utilizando uma consulta de log escrita usando a Linguagem de Consulta de Kusto (KQL). Pode escrever as suas próprias consultas ou utilizar soluções e insights que incluam consultas de registo para uma determinada aplicação ou serviço. Consulte [a visão geral das consultas de registo no Azure Monitor](logs/log-query-overview.md).
p
### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>Posso eliminar dados de um espaço de trabalho do Log Analytics?
Os dados são removidos de um espaço de trabalho de acordo com o seu [período de retenção.](logs/manage-cost-storage.md#change-the-data-retention-period) Pode eliminar dados específicos por razões de privacidade ou conformidade. Ver [Como exportar e apagar dados privados](logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) para obter mais informações.

### <a name="is-log-analytics-storage-immutable"></a>O armazenamento do Log Analytics é imutável?
Os dados relativos ao armazenamento da base de dados não podem ser alterados uma vez ingeridos, mas podem ser eliminados através da [ *via expurgação* da API para a eliminação de dados privados](./logs/personal-data-mgmt.md#delete). Embora os dados não possam ser alterados, algumas certificações exigem que os dados sejam mantidos imutáveis e não possam ser alterados ou eliminados no armazenamento. A imutabilidade de dados pode ser alcançada usando a exportação de [dados](./logs/logs-data-export.md) para uma conta de armazenamento configurada como [armazenamento imutável](../storage/blobs/storage-blob-immutability-policies-manage.md).

### <a name="what-is-a-log-analytics-workspace"></a>O que é uma área de trabalho do Log Analytics?
Todos os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho log analytics. Um espaço de trabalho é essencialmente um recipiente onde os dados de registo são recolhidos de uma variedade de fontes. Pode ter um único espaço de trabalho log Analytics para todos os seus dados de monitorização ou pode ter requisitos para vários espaços de trabalho. Consulte [a implementação dos registos do monitor Azure](logs/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Pode mover um espaço de trabalho do Log Analytics existente para outra subscrição do Azure?
Pode mover um espaço de trabalho entre grupos de recursos ou subscrições, mas não para uma região diferente. Consulte [um espaço de trabalho Log Analytics para diferentes grupos de subscrição ou recursos](logs/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Por que não posso ver os botões Desíduo Explorador e Guardar em Log Analytics?

Os botões **de regra** de alerta  **Desembarantes** , Save e New alert não estão disponíveis quando o [âmbito de consulta](logs/scope.md) é definido para um recurso específico. Para criar alertas, guardar ou carregar uma consulta, o Log Analytics deve ser analisado para um espaço de trabalho. Para abrir o Log Analytics no contexto do espaço de trabalho, selecione **Logs** do menu **Azure Monitor.** O último espaço de trabalho usado é selecionado, mas pode selecionar qualquer outro espaço de trabalho. Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](logs/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Porque estou a obter o erro: "Registar fornecedor de recursos 'Microsoft.Insights' para esta subscrição para permitir esta consulta" ao abrir o Log Analytics a partir de um VM? 
Muitos fornecedores de recursos estão automaticamente registados, mas pode ser necessário registar manualmente alguns fornecedores de recursos. A margem de inscrição é sempre a subscrição. Veja [Fornecedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) para obter mais informações.

### <a name="why-am-i-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Porque é que não estou a receber nenhuma mensagem de erro de acesso ao abrir o Log Analytics a partir de um VM? 
Para visualizar registos VM, é necessário conceder-lhe permissão de leitura para os espaços de trabalho que armazenam os registos VM. Nestes casos, o seu administrador deve conceder-lhe permissões em Azure.

## <a name="metrics"></a>Métricas

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Porque é que as métricas do so convidado da minha máquina virtual Azure não aparecem no explorador de Métricas?
[As métricas da plataforma](essentials/monitor-azure-resource.md#monitoring-data) são recolhidas automaticamente para os recursos do Azure. No entanto, deve executar alguma configuração para recolher métricas do so convidado de uma máquina virtual. Para um Windows VM, instale a extensão de diagnóstico e configuure o lavatório Azure Monitor, conforme descrito na [instalação e configurar a extensão de diagnóstico do Windows Azure (WAD)](agents/diagnostics-extension-windows-install.md). Para o Linux, instale o agente Telegraf como descrito na [Recolha de métricas personalizadas para um Linux VM com o agente InfluxData Telegraf](essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="alerts"></a>Alertas

### <a name="what-is-an-alert-in-azure-monitor"></a>O que é um alerta no Azure Monitor?
Os alertas notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. Existem vários tipos de alertas:

- Métrica - Valor métrico excede um limiar.
- Consulta de registo - Resultados de uma consulta de registo correspondem a critérios definidos.
- Registo de atividades - O evento de registo de atividade corresponde a critérios definidos.
- Teste web - Resultados dos critérios definidos do teste de disponibilidade.


Consulte [a visão geral dos alertas no Microsoft Azure](alerts/alerts-overview.md).


### <a name="what-is-an-action-group"></a>O que é um grupo de ação?
Um grupo de ação é uma coleção de notificações e ações que podem ser desencadeadas por um alerta. Vários alertas podem usar um único grupo de ação que lhe permite aproveitar conjuntos comuns de notificações e ações. Ver [Criar e gerir grupos de ação no portal Azure.](alerts/action-groups.md)


### <a name="what-is-an-action-rule"></a>O que é uma regra de ação?
Uma regra de ação permite modificar o comportamento de um conjunto de alertas que correspondem a determinados critérios. Isto permite-lhe executar tais requisitos como desativar ações de alerta durante uma janela de manutenção. Também pode aplicar um grupo de ação a um conjunto de alertas em vez de os aplicar diretamente às regras de alerta. Ver [regras de ação.](alerts/alerts-action-rules.md)

## <a name="agents"></a>Agentes

### <a name="does-azure-monitor-require-an-agent"></a>O Monitor Azure precisa de um agente?
Um agente só é obrigado a recolher dados do sistema operativo e cargas de trabalho em máquinas virtuais. As máquinas virtuais podem ser localizadas em Azure, outro ambiente em nuvem, ou no local. Consulte [a visão geral dos agentes do Monitor Azure](agents/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Qual é a diferença entre os agentes do Monitor Azure?
A extensão de Diagnóstico Azure destina-se a máquinas virtuais Azure e recolhe dados para Azure Monitor Metrics, Azure Storage e Azure Event Hubs. O agente Log Analytics destina-se a máquinas virtuais em Azure, outro ambiente em nuvem ou no local e recolhe dados para os Registos do Monitor Azure. O agente de dependência requer o agente Log Analytics e recolheu detalhes e dependências do processo. Consulte [a visão geral dos agentes do Monitor Azure](agents/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>O tráfego do meu agente usa a minha ligação ExpressRoute?
O tráfego para o Azure Monitor utiliza o circuito ExpressRoute que espreita pela Microsoft. Consulte [a documentação ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) para uma descrição dos diferentes tipos de tráfego ExpressRoute. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Como posso confirmar que o agente Log Analytics é capaz de comunicar com o Azure Monitor?
Do Painel de Controlo no computador do agente, selecione **Definições de & de Segurança**, **Microsoft Monitoring Agent. No **separador Azure Log Analytics (OMS),** um ícone de marca de verificação verde confirma que o agente é capaz de comunicar com o Azure Monitor. Um ícone amarelo significa que o agente está com problemas. Uma razão comum é que o serviço **de monitorização da Microsoft** parou. Utilize o gestor de controlo de serviços para reiniciar o serviço.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Como posso impedir o agente do Log Analytics de comunicar com o Azure Monitor?
Para agentes ligados diretamente ao Log Analytics, abra o Painel de Controlo e selecione **Definições de & de Segurança**, Agente de **Monitorização da Microsoft**. No **separador Azure Log Analytics (OMS),** remova todos os espaços de trabalho listados. No Gestor de Operações do Centro de Sistema, remova o computador da lista de computadores geridos por Log Analytics. O Gestor de Operações atualiza a configuração do agente para deixar de reportar ao Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Quantos dados são enviados por agente?
A quantidade de dados enviados por agente depende de:

* As soluções que permitiu
* O número de registos e contadores de desempenho a serem recolhidos
* O volume de dados nos registos

Consulte [Gerir a utilização e os custos com os Registos do Monitor Azure](logs/manage-cost-storage.md) para obter mais detalhes.

Para computadores capazes de executar o agente WireData, utilize a seguinte consulta para ver quantos dados estão a ser enviados:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Quanto largura de banda de rede é utilizada pelo Microsoft Management Agent (MMA) ao enviar dados para o Azure Monitor?
Largura de banda é uma função sobre a quantidade de dados enviados. Os dados são comprimidos à medida que são enviados pela rede.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Como posso ser notificado quando a recolha de dados do agente Log Analytics para?

Utilize os passos descritos para [criar um novo alerta de registo](alerts/alerts-metric.md) para ser notificado quando a recolha de dados parar. Utilize as seguintes definições para a regra de alerta:

- **Defina a condição de alerta**: Especifique o seu espaço de trabalho Log Analytics como o alvo do recurso.
- **Critérios de alerta** 
   - **Nome do sinal**: *pesquisa de registo personalizado*
   - **Consulta de pesquisa:**`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Lógica de alerta**: **Com base no** número de *resultados,* **Condição** Maior *do que*, **Valor limiar** *0*
   - **Avaliado com base em:** **Período (em minutos)** *30*, **Frequência (em minutos)** *10*
- **Definir detalhes do alerta** 
   - **Nome**: *A recolha de dados parou*
   - **Gravidade**: *Aviso*

Especifique um Grupo de [Ação](alerts/action-groups.md) existente ou novo de modo a que, quando o alerta de registo corresponder aos critérios, seja notificado se tiver um batimento cardíaco em falta por mais de 15 minutos.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Quais são os requisitos de firewall para os agentes do Azure Monitor?
Consulte [os requisitos de firewall da rede](agents/log-analytics-agent.md#network-requirements)para obter detalhes sobre os requisitos de firewall.


## <a name="visualizations"></a>Visualizações

### <a name="why-cant-i-see-view-designer"></a>Por que não posso ver o Designer De Visão?

O View Designer só está disponível para utilizadores afetados com permissões de Contribuinte ou superior no espaço de trabalho Log Analytics.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problemas de configuração
*Estou a ter problemas em montar o meu:*

* [Aplicação .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitorização de uma aplicação já em execução](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnósticos Azure](agents/diagnostics-extension-to-application-insights.md)
* [Aplicação Web Java](app/java-troubleshoot.md)

*Não obtê nenhum dado do meu servidor:*

* [Definir exceções de firewall](app/ip-addresses.md)
* [Configurar um servidor ASP.NET](app/monitor-performance-live-website-now.md)
* [Configurar um servidor Java](app/java-agent.md)

*Quantos recursos de Insights de Aplicação devo implementar:*

* [Como conceber a sua implementação de Insights de Aplicação: Um versus muitos recursos de Insights de Aplicação?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Posso usar insights de aplicação com ...?

* [Aplicativos web em um servidor IIS em Azure VM ou Azure conjunto de escala de máquina virtual](app/azure-vm-vmss-apps.md)
* [Aplicativos web em um servidor IIS - no local ou em um VM](app/asp-net.md)
* [Aplicações Web Java](app/java-get-started.md)
* [Aplicações em Node.js](app/nodejs.md)
* [Aplicativos web em Azure](app/azure-web-apps.md)
* [Serviços cloud em Azure](app/cloudservices.md)
* [Servidores de aplicativos em execução em Docker](./azure-monitor-app-hub.yml)
* [Aplicativos web de página única](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Aplicativo de ambiente de trabalho do Windows](app/windows-desktop.md)
* [Outras plataformas](app/platforms.md)

### <a name="is-it-free"></a>É de graça?

Sim, para uso experimental. No plano de preços básicos, a sua aplicação pode enviar gratuitamente um determinado subsídio de dados por mês. O subsídio gratuito é suficientemente grande para cobrir o desenvolvimento e a publicação de uma aplicação para um pequeno número de utilizadores. Pode definir uma tampa para evitar que mais de uma quantidade especificada de dados sejam processados.

Volumes maiores de telemetria são cobrados pela Gb. Fornecemos algumas dicas sobre como limitar os [seus encargos.](app/pricing.md)

O plano Da Enterprise incorre numa taxa por cada dia que cada nó do servidor web envia telemetria. É adequado se quiser utilizar a Exportação Contínua em larga escala.

[Leia o plano de preços.](https://azure.microsoft.com/pricing/details/application-insights/)

### <a name="how-much-does-it-cost"></a>Quanto custa?

* Abra a **página de utilização e custos estimados** num recurso Application Insights. Há um gráfico de uso recente. Pode definir uma tampa de volume de dados, se quiser.
* Abra a [lâmina de faturação Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) para ver as suas contas em todos os recursos.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>O que modifica o Application Insights no meu projeto?
Os detalhes dependem do tipo de projeto. Para uma aplicação web:

* Adiciona estes ficheiros ao seu projeto:
  * ApplicationInsights.config
  * ai.js
* Instala estes pacotes NuGet:
  * *Aplicações Insights API* - a API central
  * *Application Insights API para aplicações web* - usado para enviar telemetria a partir do servidor
  * *Application Insights API para Aplicações JavaScript* - usado para enviar telemetria do cliente
* Os pacotes incluem estes conjuntos:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insere itens em:
  * Web.config
  * packages.config
* (Apenas novos projetos - se [adicionar Insights de Aplicação a um projeto existente,][start]tem de o fazer manualmente.) Insere snippets no código do cliente e do servidor para inicializá-los com o ID do recurso Application Insights. Por exemplo, numa aplicação MVC, o código é inserido na página principal Views/Shared/ \_ Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como faço o upgrade a partir de versões SDK mais antigas?
Consulte as [notas de lançamento](app/release-notes.md) do SDK adequadas ao seu tipo de aplicação.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Como posso alterar para que recurso Azure o meu projeto envia dados?
No Solution Explorer, clique à direita `ApplicationInsights.config` e escolha Insights de **Aplicação de Atualização**. Pode enviar os dados para um recurso existente ou novo em Azure. O assistente de atualização altera a tecla de instrumentação em ApplicationInsights.config, que determina para onde o servidor SDK envia os seus dados. A menos que desmarca "Update all", também mudará a chave onde aparece nas suas páginas web.

### <a name="do-new-azure-regions-require-the-use-of-connection-strings"></a>As novas regiões de Azure requerem a utilização de cordas de ligação?

As novas regiões de Azure **requerem** a utilização de cordas de ligação em vez de teclas de instrumentação. [A cadeia de ligação](./app/sdk-connection-string.md) identifica o recurso com o que pretende associar os seus dados de telemetria. Também permite modificar os pontos finais que o seu recurso utilizará como destino para a sua telemetria. Terá de copiar o fio de ligação e adicioná-lo ao código da sua aplicação ou a uma variável ambiental.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>Posso usar `providers('Microsoft.Insights', 'components').apiVersions[0]` nas minhas implementações do Azure Resource Manager?

Não recomendamos a utilização deste método de povoamento da versão API. A versão mais recente pode representar versões de pré-visualização que podem conter alterações de rutura. Mesmo com as versões não pré-visualização mais recentes, as versões API nem sempre são compatíveis com os modelos existentes, ou em alguns casos a versão API pode não estar disponível para todas as subscrições.

### <a name="what-is-status-monitor"></a>O que é o Monitor de Estado?

Uma aplicação de ambiente de trabalho que pode utilizar no seu servidor web IIS para ajudar a configurar o Application Insights em aplicações web. Não recolhe telemetria: pode detê-la quando não está a configurar uma aplicação. 

[Saiba mais](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Que telemetria é recolhida pela Application Insights?

A partir de aplicativos web de servidor:

* Pedidos HTTP
* [Dependências](app/asp-net-dependencies.md). Chamadas para: SQL Databases; CHAMADAS HTTP para serviços externos; Azure Cosmos DB, mesa, armazenamento de bolhas e fila. 
* [Exceções](app/asp-net-exceptions.md) e traços de pilha.
* [Contadores de Desempenho](app/performance-counters.md) - Se utilizar [o Status Monitor](app/monitor-performance-live-website-now.md), [monitorização Azure para Serviços de Aplicações](app/azure-web-apps.md), [monitorização Azure para conjunto de escala de VM ou máquina virtual](app/azure-vm-vmss-apps.md), ou o escritor [colecionado Application Insights](app/java-collectd.md).
* [Eventos e métricas personalizados](app/api-custom-events-metrics.md) que codifica.
* [Trace registos](app/asp-net-trace-logs.md) se configurar o colecionador apropriado.

A partir de [páginas web do cliente:](app/javascript.md)

* [Contagens de visualizações de página](app/usage-overview.md)
* [Chamadas do AJAX](app/asp-net-dependencies.md) Pedidos feitos a partir de um roteiro de execução.
* Ver dados de carga de visualização de página
* Contagens de utilizador e sessão
* [IDs de utilizador autenticados](app/api-custom-events-metrics.md#authenticated-users)

De outras fontes, se as configurar:

* [Diagnósticos Azure](agents/diagnostics-extension-to-application-insights.md)
* [Importação para Analytics](logs/data-collector-api.md)
* [Log Analytics](logs/data-collector-api.md)
* [Logstash](logs/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Posso filtrar ou modificar alguma telemetria?

Sim, no servidor pode escrever:

* Processador de telemetria para filtrar ou adicionar propriedades a itens de telemetria selecionados antes de serem enviados da sua aplicação.
* Inicializador de telemetria para adicionar propriedades a todos os itens de telemetria.

Saiba mais para [ASP.NET](app/api-filtering-sampling.md) ou [Java.](app/java-filter-telemetry.md)

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Como são calculados os dados da cidade, país/região e outros dados de localização geológico?

Procuramos o endereço IP (IPv4 ou IPv6) do cliente web utilizando a [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria do navegador: Recolhemos o endereço IP do remetente.
* Telemetria do servidor: O módulo Application Insights recolhe o endereço IP do cliente. Não é recolhido se `X-Forwarded-For` estiver definido.
* Para saber mais sobre como o endereço IP e os dados de geolocalização são recolhidos no Application Insights consulte este [artigo.](./app/ip-collection.md)

Pode configurar o `ClientIpHeaderTelemetryInitializer` endereço IP para tirar o endereço IP de um cabeçalho diferente. Em alguns sistemas, por exemplo, é movido por um proxy, balanceador de carga ou CDN para `X-Originating-IP` . [Saiba mais](https://apmtips.com/posts/2016-07-05-client-ip-address/).

Pode [utilizar o Power BI](app/export-power-bi.md ) para mostrar a telemetria do seu pedido num mapa.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Quanto tempo os dados são retidos no portal? É seguro?
Veja a [retenção de dados e a privacidade.][data]

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>O que acontece com a telemetria da Application Insight quando um servidor ou dispositivo perde a ligação com o Azure?

Todos os nossos SDKs, incluindo a web SDK, inclui "transporte fiável" ou "transporte robusto". Quando o servidor ou dispositivo perde a ligação com o Azure, a telemetria é [armazenada localmente no sistema de ficheiros](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (Server SDKs) ou no HTML5 Session Storage (Web SDK). O SDK tentará periodicamente enviar esta telemetria até que o nosso serviço de ingestão o considere "velho" (48 horas para registos, 30 minutos para métricas). A telemetria velha será largada. Em alguns casos, como quando o armazenamento local está cheio, não ocorrerá novamente.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Os dados pessoais podem ser enviados na telemetria?

Isto é possível se o seu código enviar esses dados. Também pode acontecer se as variáveis em stack traces incluem dados pessoais. A sua equipa de desenvolvimento deve realizar avaliações de risco para garantir que os dados pessoais são devidamente tratados. [Saiba mais sobre a retenção de dados e privacidade.](app/data-retention-privacy.md)

**Todos os** octetos do endereço web do cliente estão sempre definidos para 0 após os atributos de localização geoparque serem analisados.

A [Aplicação Insights JavaScript SDK](app/javascript.md) não inclui quaisquer dados pessoais na sua auto-preconção por padrão. No entanto, alguns dados pessoais utilizados na sua aplicação podem ser recolhidos pelo SDK (por exemplo, `window.title` nomes completos ou IDs de conta nos parâmetros de consulta de URL XHR). Para mascarar dados pessoais personalizados, adicione um [inicializador de telemetria](app/api-filtering-sampling.md#javascript-web-applications).

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>A minha chave de instrumentação está visível na minha fonte de página web.

* Trata-se de uma prática comum no controlo de soluções.
* Não pode ser usado para roubar os seus dados.
* Pode ser usado para distorcer os seus dados ou desencadear alertas.
* Não ouvimos dizer que nenhum cliente tenha tido tais problemas.

Pode:

* Utilize duas teclas de instrumentação separadas (recursos separados de Insights de Aplicação), para dados de clientes e servidores. Ou
* Escreva um representante que é executado no seu servidor e faça com que o cliente web envie dados através desse representante.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Como vejo os dados post na pesquisa de diagnóstico?
Não registamos automaticamente os dados POST, mas pode utilizar uma chamada TrackTrace: colocar os dados no parâmetro da mensagem. Isto tem um limite de tamanho mais longo do que os limites das propriedades das cordas, embora não possa filtrar nele.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Devo utilizar recursos individuais ou múltiplos de Insights de Aplicação?

Utilize um único recurso para todos os componentes ou funções num único sistema de negócio. Utilize recursos separados para versões de desenvolvimento, teste e lançamento e para aplicações independentes.

* [Veja a discussão aqui](app/separate-resources.md)
* [Exemplo - serviço em nuvem com funções de trabalhador e web](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Como posso alterar dinamicamente a chave de instrumentação?

* [Discussão aqui](app/separate-resources.md)
* [Exemplo - serviço em nuvem com funções de trabalhador e web](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>O que conta o Utilizador e a Sessão?

* O JavaScript SDK define um cookie de utilizador no cliente web, para identificar os utilizadores que regressam e um cookie de sessão para agrupar atividades.
* Se não houver script do lado do cliente, pode [definir cookies no servidor](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/).
* Se um utilizador real utilizar o seu site em diferentes navegadores, ou utilizar navegação insógina/incógnita, ou máquinas diferentes, então serão contados mais de uma vez.
* Para identificar um utilizador com sessão registada através de máquinas e navegadores, adicione uma chamada para [definir OTexto de Utilizador/Gravador de Dados()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="how-does-application-insights-generate-device-information-browser-os-language-model"></a>Como é que o Application Insights gera informações sobre dispositivos (Browser, OS, Idioma, Modelo)?

O navegador passa a cadeia de Agente utilizador no cabeçalho HTTP do pedido, e o serviço de ingestão de Insights de Aplicação utiliza [o UA Parser](https://github.com/ua-parser/uap-core) para gerar os campos que vê nas tabelas e experiências de dados. Como resultado, os utilizadores do Application Insights não conseguem alterar estes campos.

Ocasionalmente, estes dados podem estar em falta ou imprecisos se o utilizador ou empresa desativar o envio de Agente de Utilizador nas definições do Navegador. Além disso, os [regexes UA Parser](https://github.com/ua-parser/uap-core/blob/master/regexes.yaml) podem não incluir todas as informações do dispositivo ou o Application Insights pode não ter adotado as últimas atualizações.

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Já capacitei tudo no Application Insights?
| O que deve ver | Como obtê-lo | Por que quer? |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes web](app/monitor-web-app-availability.md) |Saiba que a sua aplicação web subiu |
| Servidor app perf: tempos de resposta, ... |[Adicione Insights de Aplicação ao seu projeto](app/asp-net.md) ou [instale o Monitor de Estado de IA no servidor](app/monitor-performance-live-website-now.md) (ou escreva o seu próprio código para rastrear as [dependências)](app/api-custom-events-metrics.md#trackdependency) |Detetar problemas perf |
| Telemetria de dependência |[Instale o Monitor de Estado da IA no servidor](app/monitor-performance-live-website-now.md) |Diagnosticar problemas com bases de dados ou outros componentes externos |
| Obtenha vestígios de pilhas de exceções |[Insira chamadas TrackException no seu código](app/asp-net-exceptions.md) (mas algumas são reportadas automaticamente) |Detetar e diagnosticar exceções |
| Rastreios de registo de pesquisa |[Adicione um adaptador de registo](app/asp-net-trace-logs.md) |Diagnosticar exceções, problemas perf |
| Básicos de utilização do cliente: vistas de página, sessões, ... |[Inicializador JavaScript em páginas web](app/javascript.md) |Análise de utilização |
| Métricas personalizadas do cliente |[Rastreio de chamadas em páginas web](app/api-custom-events-metrics.md) |Melhorar a experiência do utilizador |
| Métricas personalizadas do servidor |[Rastreio de chamadas no servidor](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Por que as contagens nos gráficos de Pesquisa e Métricas são desiguais?

[A amostragem](app/sampling.md) reduz o número de itens de telemetria (pedidos, eventos personalizados, e assim por diante) que são realmente enviados da sua app para o portal. Em Search, vê o número de itens realmente recebidos. Em gráficos métricos que exibem uma contagem de eventos, você vê o número de eventos originais que ocorreram. 

Cada item que é transmitido transporta uma `itemCount` propriedade que mostra quantos eventos originais que o item representa. Para observar a amostragem em funcionamento, pode executar esta consulta em Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```

### <a name="how-do-i-move-an-application-insights-resource-to-a-new-region"></a>Como posso mover um recurso Application Insights para uma nova região?

**Atualmente, não** é suportado o movimento dos recursos de Insights de Aplicação existentes de uma região para outra. Os dados históricos que recolheu **não podem ser migrados** para uma nova região. A única solução parcial é:

1. Crie um novo recurso Application Insights[(clássico](app/create-new-resource.md) ou [baseado no espaço de trabalho)](./app/create-workspace-resource.md)na nova região.
2. Recrie todas as personalizações únicas específicas ao recurso original no novo recurso.
3. Modifique a sua aplicação para utilizar a chave de [instrumentação](app/create-new-resource.md#copy-the-instrumentation-key) ou a cadeia de ligação do novo recurso da [região.](app/sdk-connection-string.md)  
4. Teste para confirmar que tudo continua a funcionar como esperado com o seu novo recurso Application Insights. 
5. Neste momento pode eliminar o recurso original que resultará na perda de **todos os dados históricos**. Ou reter o recurso original para fins históricos de reporte durante a duração das suas definições de retenção de dados.

Personalizações únicas que normalmente precisam de ser recriadas manualmente ou atualizadas para o recurso na nova região incluem, mas não se limitam a:

- Recrie os painéis e livros personalizados. 
- Recrie ou atualize o âmbito de quaisquer alertas de registo/métrica personalizados. 
- Recrie os alertas de disponibilidade.
- Recrie quaisquer definições personalizadas de controlo de acesso baseado em funções (Azure RBAC) que sejam necessárias para que os seus utilizadores acedam ao novo recurso. 
- Replicar configurações envolvendo amostragem de ingestão, retenção de dados, tampa diária e ativação de métricas personalizadas. Estas definições são controladas através do **painel de utilização e custos estimados.**
- Qualquer integração que dependa de chaves API, tais como [anotações de libertação,](./app/annotations.md) [métricas ao vivo, canal de controlo seguro,](app/live-stream.md#secure-the-control-channel) etc. Terá de gerar novas teclas API e atualizar a integração associada. 
- As exportações contínuas de recursos clássicos teriam de ser configuradas novamente.
- As definições de diagnóstico dos recursos baseados no espaço de trabalho teriam de ser configuradas novamente.

> [!NOTE]
> Se o recurso que está a criar numa nova região está a substituir um recurso clássico, recomendamos explorar os benefícios de [criar um novo recurso baseado no espaço de trabalho](app/create-workspace-resource.md) ou, em alternativa, [migrar o seu recurso existente para o espaço de trabalho baseado.](app/convert-classic-resource.md) 

### <a name="automation"></a>Automatização

#### <a name="configuring-application-insights"></a>Configurar insights de aplicações

Pode [escrever scripts PowerShell](app/powershell.md) utilizando o Monitor de Recursos Azure para:

* Criar e atualizar recursos de Insights de Aplicação.
* Desajei o plano de preços.
* Pegue a chave de instrumentação.
* Adicione um alerta métrico.
* Adicione um teste de disponibilidade.

Não é possível configurar um relatório do Metric Explorer ou configurar uma exportação contínua.

#### <a name="querying-the-telemetry"></a>Consulta da telemetria

Utilize a [API REST](https://dev.applicationinsights.io/) para executar consultas de [Analytics.](./logs/log-query-overview.md)

### <a name="how-can-i-set-an-alert-on-an-event"></a>Como posso alertar um evento?

Os alertas de azul são apenas em métricas. Crie uma métrica personalizada que cruze um limiar de valor sempre que o seu evento ocorra. Em seguida, coloque um alerta na métrica. Receberá uma notificação sempre que a métrica cruze o limiar em qualquer direção; não receberá uma notificação até à primeira travessia, independentemente de o valor inicial ser elevado ou baixo; há sempre uma latência de alguns minutos.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Existem taxas de transferência de dados entre uma aplicação web Azure e Application Insights?

* Se a sua aplicação web Azure estiver hospedada num centro de dados onde existe um ponto final de recolha de Insights de Aplicação, não há qualquer custo. 
* Se não houver um ponto final de recolha no centro de dados do anfitrião, então a telemetria da sua aplicação incorrerá em [taxas de saída do Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Isto não depende de onde o seu recurso Application Insights está hospedado. Só depende da distribuição dos nossos pontos finais.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Posso enviar telemetria para o portal Application Insights?

Recomendamos que utilize os nossos SDKs e utilize o [API SDK.](app/api-custom-events-metrics.md) Existem variantes do SDK para [várias plataformas.](app/platforms.md) Estes SDKs lidam com tampão, compressão, estrangulamento, retrações, e assim por diante. No entanto, o [esquema de ingestão](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) e [o protocolo de ponto final](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) são públicos.

### <a name="can-i-monitor-an-intranet-web-server"></a>Posso monitorizar um servidor web intranet?

Sim, mas você precisará permitir o tráfego para os nossos serviços através de exceções de firewall ou redirecionamentos de procuração.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- Canal da Telemetria `https://dc.services.visualstudio.com:443` 


Reveja [aqui](app/ip-addresses.md)a nossa lista completa de serviços e endereços IP.

#### <a name="firewall-exception"></a>Exceção firewall

Permita que o seu servidor web envie telemetria para os nossos pontos finais. 

#### <a name="gateway-redirect"></a>Redirecionamento de gateway

Encaminhe o tráfego do seu servidor para um portal na sua intranet, sobressaindo pontos de final na sua configuração. Se estas propriedades "Endpoint" não estiverem presentes no seu config, estas classes utilizarão os valores predefinidos apresentados abaixo no exemplo ApplicationInsights.config. 

O seu portal deve encaminhar o tráfego para o endereço da base do nosso ponto final. Na sua configuração, substitua os valores predefinidos por `http://<your.gateway.address>/<relative path>` .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Exemplo ApplicationInsights.config com pontos finais predefinidos:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider está disponível a partir de v2.6.0.



#### <a name="proxy-passthrough"></a>Passagem por procuração

A passagem por procuração pode ser conseguida configurando um nível de máquina ou um proxy de nível de aplicação.
Para mais informações consulte o artigo da Dotnet sobre [OProxy Padrão.](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)
 
 Exemplo Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Posso executar testes web de disponibilidade num servidor intranet?

Os [nossos testes web](app/monitor-web-app-availability.md) funcionam em pontos de presença que são distribuídos por todo o mundo. Existem duas soluções:

* Porta de firewall - Permita pedidos ao seu servidor a partir da [longa e mutável lista de agentes de teste web](app/ip-addresses.md).
* Escreva o seu próprio código para enviar pedidos periódicos para o seu servidor a partir de dentro da sua intranet. Podes fazer testes web do Visual Studio para este fim. O teste pode enviar os resultados para Application Insights usando a API trackAvailability().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Quanto tempo demora a telemetria ser recolhida?

A maioria dos dados de Insights de Aplicação tem uma latência inferior a 5 minutos. Alguns dados podem demorar mais tempo; ficheiros de registo tipicamente maiores. Para mais informações, consulte o [SLA Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>HTTP 502 e 503 respostas nem sempre são captadas por Insights de Aplicação

Os erros de "502 bad gateway" e "503 serviços indisponíveis" nem sempre são capturados pela Application Insights. Se apenas o JavaScript do lado do cliente estiver a ser utilizado para monitorizar este comportamento seria esperado, uma vez que a resposta de erro é devolvida antes da página que contém o cabeçalho HTML com o corte JavaScript de monitorização a ser prestado. 

Se a resposta 502 ou 503 fosse enviada de um servidor com monitorização do lado do servidor ativada, os erros seriam recolhidos pelo Application Insights SDK. 

No entanto, ainda existem casos em que, mesmo quando a monitorização do lado do servidor é ativada no servidor web de uma aplicação, um erro de 502 ou 503 não será capturado pelo Application Insights. Muitos servidores web modernos não permitem que um cliente se comunique diretamente, mas em vez disso empregam soluções como proxies invertidos para passar informações de um lado para o outro entre o cliente e os servidores web front-end. 

Neste cenário, uma resposta 502 ou 503 poderia ser devolvida a um cliente devido a um problema na camada de procuração inversa e esta não seria capturada fora de caixa pela Application Insights. Para ajudar a detetar problemas nesta camada, poderá necessitar de encaminhar registos do seu representante inverso para o Log Analytics e criar uma regra personalizada para verificar se há respostas 502/503. Para saber mais sobre causas comuns de 502 e 503 erros consulte o artigo de resolução de problemas do Serviço de Aplicações Azure [para "502 bad gateway" e "503 serviço indisponíveis".](../app-service/troubleshoot-http-502-http-503.md)     


## <a name="opentelemetry"></a>OpenTelemetry

### <a name="what-is-opentelemetry"></a>O que é OpenTelemetry

Um novo padrão de código aberto para a observabilidade. Saiba mais em [https://opentelemetry.io/](https://opentelemetry.io/) .

### <a name="why-is-microsoft--azure-monitor-investing-in-opentelemetry"></a>Porque é que a Microsoft / Azure Monitor está a investir na OpenTelemetry?

Acreditamos que melhor serve os nossos clientes por três razões:
   1. Ativar o suporte para mais cenários de clientes.
   2. Instrumento sem medo do bloqueio do fornecedor.
   3. Aumentar a transparência e o envolvimento do cliente.

Também alinha com a estratégia da Microsoft de abraçar a [open source](https://opensource.microsoft.com/).

### <a name="what-additional-value-does-opentelemetry-give-me"></a>Que valor adicional a OpenTelemetry me dá?

Além das razões acima referidas, a OpenTelemetry é mais eficiente à escala e fornece design/configurações consistentes em todos os idiomas.

### <a name="how-can-i-test-out-opentelemetry"></a>Como posso testar a OpenTelemetry?

Inscreva-se para se juntar à nossa comunidade adotante Azure Monitor Application Insights em [https://aka.ms/AzMonOtel](https://aka.ms/AzMonOtel) .

### <a name="what-does-ga-mean-in-the-context-of-opentelemetry"></a>O que significa GA no contexto da OpenTelemetry?

A comunidade OpenTelemetry define geralmente disponível (GA) [aqui.](https://medium.com/opentelemetry/ga-planning-f0f6d7b5302) No entanto, a OpenTelemetry "GA" não significa paridade de características com os SDKs de Insights de Aplicação existentes. O Azure Monitor continuará a recomendar os nossos atuais SDKs de Insights de Aplicação para clientes que necessitem de funcionalidades como [métricas pré-agregadas,](app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) [métricas ao vivo,](app/live-stream.md) [amostragem adaptativa,](app/sampling.md#adaptive-sampling) [profiler,](app/profiler-overview.md)e [depurador de instantâneos](app/snapshot-debugger.md) até que os SDKs openTelemetry atinjam a maturidade.

### <a name="can-i-use-preview-builds-in-production-environments"></a>Posso utilizar as construções de pré-visualização em ambientes de produção?

Não é recomendado. Consulte [os Termos De Utilização Suplementares para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

### <a name="whats-the-difference-between-opentelemetry-sdk-and-auto-instrumentation"></a>Qual é a diferença entre a OpenTelemetry SDK e a auto-instrumentação?

A especificação OpenTelemetry define [SDK](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/glossary.md#telemetry-sdk). Em suma, "SDK" é um pacote específico da linguagem que recolhe dados de telemetria em todos os vários componentes da sua aplicação e envia os dados para o Azure Monitor através de um exportador.

O conceito de auto-instrumentação (por vezes referido como injeção por código, codeless ou baseado em agente) refere-se à capacidade de instrumentar a sua aplicação sem alterar o seu código. Por exemplo, consulte a [Leitura de Auto-instrumentação DeMarcaria Java OpenTelemetry](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/master/README.md) para obter mais informações.

### <a name="whats-the-opentelemetry-collector"></a>O que é o Colecionador de Telequírias Abertas?

O Colecionador DeTelemetria Aberta é descrito no seu [lido GitHub.](https://github.com/open-telemetry/opentelemetry-collector#opentelemetry-collector) Atualmente, a Microsoft não utiliza o Colecionador de Teleteleméticos Abertos e depende de exportadores diretos que enviam para o Azure Monitor's Application Insights.

### <a name="whats-the-difference-between-opencensus-and-opentelemetry"></a>Qual é a diferença entre OpenCensus e OpenTelemetry?

[OpenCensus](https://opencensus.io/) é o precursor da [OpenTelemetry](https://opentelemetry.io/). A Microsoft ajudou a reunir [o OpenTracing](https://opentracing.io/) e o OpenCensus para criar o OpenTelemetry, um padrão de observabilidade único para o mundo. O [Python SDK recomendado pela produção](app/opencensus-python.md) atual do Azure Monitor é baseado no OpenCensus, mas eventualmente todos os SDKs do Azure Monitor serão baseados na OpenTelemetry.


## <a name="container-insights"></a>Insights de contentores

### <a name="what-does-other-processes-represent-under-the-node-view"></a>O que *os Outros Processos* representam sob a vista nó?

**Outros processos** destinam-se a ajudá-lo a compreender claramente a causa principal do uso elevado de recursos no seu nó. Isto permite-lhe distinguir o uso entre processos contentorizados vs processos não-contentorizados.

O que são estes **Outros Processos?** 

Estes são processos não-contentorizados que funcionam no seu nó.  

Como calculamos isto?

**Outros Processos**  =  *Utilização total do CAdvisor*  -  *Utilização do processo contentorizado*

Os **outros processos** incluem:

- Processos não-contentorizados de Kubernetes geridos ou geridos por si próprios 

- Processos de tempo de funcionamento do contentor  

- Kubelet  

- Processos do sistema em execução no seu nó 

- Outras cargas de trabalho não-Kubernetes em execução em hardware de nó ou VM 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Não vejo valores de propriedade de Imagem e Nome povoados quando consulta a tabela ContainerLog.

Para a versão do agente ciprod12042019 e posteriormente, por padrão, estas duas propriedades não são povoadas para cada linha de registo para minimizar os custos incorridos nos dados de registo recolhidos. Existem duas opções para consultar a tabela que incluem estas propriedades com os seus valores:

#### <a name="option-1"></a>Opção 1 

Junte-se a outras tabelas para incluir estes valores de propriedade nos resultados.

Modifique as suas consultas para incluir propriedades de Imagem e ImageTag a partir da ```ContainerInventory``` tabela, juntando-se na propriedade ContainerID. Pode incluir a propriedade Name (tal como apareceu anteriormente na ```ContainerLog``` tabela) do campo De Nome da mesa KubepodInventory, juntando-se à propriedade ContainerID. Esta é a opção recomendada.

O exemplo a seguir é uma consulta detalhada que explica como obter estes valores de campo com juntas.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Opção 2

Reestibilar a recolha destas propriedades para cada linha de registo de contentores.

Se a primeira opção não for conveniente devido às alterações de consulta envolvidas, pode voltar a ativar a recolha destes campos, permitindo a definição ```log_collection_settings.enrich_container_logs``` no mapa de configuração do agente, conforme descrito nas definições de configuração da [recolha de dados](containers/container-insights-agent-config.md).

> [!NOTE]
> A segunda opção não é recomendada com grandes clusters que têm mais de 50 nós porque gera chamadas de servidor API de todos os nós do cluster para realizar este enriquecimento. Esta opção também aumenta o tamanho dos dados para cada linha de registo recolhida.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Posso ver as métricas recolhidas em Grafana?

As informações do contentor suportam métricas de visualização armazenadas no seu espaço de trabalho Log Analytics nos dashboards grafana. Fornecemos um modelo que você pode baixar do [repositório](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) do painel de grafana para começar e referência para ajudá-lo a aprender a consultar dados adicionais dos seus clusters monitorizados para visualizar em dashboards grafana personalizados. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-container-insights"></a>Posso monitorizar o meu cluster de motores AKS com insights de contentores?

Os conhecimentos dos contentores suportam a monitorização das cargas de trabalho dos contentores implantadas no cluster(s) do motor AKS (anteriormente conhecido como acs-engine) alojado em Azure. Para mais detalhes e uma visão geral das etapas necessárias para permitir a monitorização deste cenário, consulte [a utilização de insights do recipiente para o motor AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Por que não vejo dados no meu espaço de trabalho log Analytics?

Se não conseguir ver quaisquer dados na área de trabalho do Log Analytics num determinado horário todos os dias, pode ter atingido o limite predefinido de 500 MB ou o limite diário especificado para controlar a quantidade de dados recolhidos diariamente. Quando o limite for atingido para o dia, a recolha de dados para e é retomada apenas no dia seguinte. Para rever a utilização dos seus dados e atualizar para um nível de preços diferente com base nos seus padrões de utilização previstos, consulte [a utilização dos dados do Registo e o custo](logs/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quais são os estados do contentor especificados na tabela ContentorInventory?

A tabela ContainerInventory contém informações sobre contentores parados e em funcionamento. A tabela é povoada por um fluxo de trabalho dentro do agente que consulta o estivador para todos os contentores (em execução e parada), e encaminha esse espaço de trabalho log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Como resolvo o erro *de registo de assinatura em falta?*

Se receber o **registo de subscrição em falta para o Microsoft.OperationsManagement,** pode resolvê-lo registando o fornecedor de recursos **Microsoft.OperationsManagement** na subscrição onde o espaço de trabalho é definido. A documentação para como fazê-lo pode ser encontrada [aqui.](../azure-resource-manager/templates/error-register-resource-provider.md)

### <a name="is-there-support-for-kubernetes-rbac-enabled-aks-clusters"></a>Existe suporte para clusters AKS ativados pela Kubernetes?

A solução de monitorização do contentor não suporta o RBAC de Kubernetes, mas é suportada com insights do Contentor. A página de detalhes da solução pode não mostrar as informações certas nas lâminas que mostram dados para estes clusters.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Como posso permitir a recolha de registos para contentores no espaço de nomes do sistema kube através do Helm?

A recolha de registos a partir de contentores no espaço de nomes do sistema kube é desativada por padrão. A recolha de registos pode ser ativada através da definição de uma variável ambiental no omsagent. Para mais informações, consulte a página [GitHub de insights de informação](https://aka.ms/azuremonitor-containers-helm-chart) do Recipiente. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Como atualizei o omsagent para a versão mais recente lançada?

Para aprender a atualizar o agente, consulte a [gestão do Agente.](containers/container-insights-manage-agent.md)

### <a name="how-do-i-enable-multi-line-logging"></a>Como posso permitir a exploração de madeira em várias linhas?

Atualmente, as informações do Contentor não suportam a exploração de madeira em várias linhas, mas existem soluções alternativas disponíveis. Você pode configurar todos os serviços para escrever em formato JSON e, em seguida, Docker/Moby irá escrevê-los como uma única linha.

Por exemplo, pode embrulhar o seu registo como um objeto JSON, como mostrado no exemplo abaixo para uma amostra node.js aplicação:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Estes dados serão o seguinte exemplo no Azure Monitor para registos quando consultar os mesmos:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para uma análise detalhada do problema, reveja o seguinte [link GitHub](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Como resolvo erros de Ad Azure quando posso ativar registos ao vivo? 

Pode ver o seguinte erro: **O url de resposta especificado no pedido não corresponde aos urls de resposta configurados para o pedido: '<ID de aplicação' \>**. A solução para resolvê-lo pode ser encontrada no artigo [Como visualizar os dados](containers/container-insights-livedata-setup.md#configure-ad-integrated-authentication)dos recipientes em tempo real com insights do Contentor . 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Por que não posso atualizar o cluster depois de embarcar?

Se depois de ativar as informações do Container para um cluster AKS, eliminar o espaço de trabalho Log Analytics para o quais o cluster estava a enviar os seus dados, quando tentava atualizar o cluster, falhará. Para contornar isto, terá de desativar a monitorização e, em seguida, reativá-la referindo-se a um espaço de trabalho diferente e válido na sua subscrição. Quando tentar efetuar novamente o upgrade do cluster, este deve processar e completar com sucesso.  

### <a name="which-ports-and-domains-do-i-need-to-openallow-for-the-agent"></a>Que portas e domínios preciso para abrir/permitir o agente?

Consulte os [requisitos de firewall da rede](containers/container-insights-onboard.md#network-firewall-requirements) para as informações de configuração proxy e firewall necessárias para o agente contentorizado com nuvens Azure, Azure US e Azure China 21Vianet.


## <a name="vm-insights"></a>Insights VM

### <a name="can-i-onboard-to-an-existing-workspace"></a>Posso embarcar num espaço de trabalho existente?
Se as suas máquinas virtuais já estiverem ligadas a um espaço de trabalho log Analytics, poderá continuar a utilizar esse espaço de trabalho ao embarcar em conhecimentos VM, desde que se encontra numa das [regiões suportadas.](vm/vminsights-configure-workspace.md#supported-regions)


### <a name="can-i-onboard-to-a-new-workspace"></a>Posso embarcar num novo espaço de trabalho? 
Se os seus VMs não estiverem atualmente ligados a um espaço de trabalho log analytics existente, precisa de criar um novo espaço de trabalho para armazenar os seus dados. A criação de um novo espaço de trabalho predefinido é feita automaticamente se configurar um único Azure VM para insights VM através do portal Azure.

Se optar por utilizar o método baseado no script, estes passos estão cobertos nos insights de Enable VM utilizando o artigo [de modelo Azure PowerShell ou Resource Manager.](./vm/vminsights-enable-powershell.md) 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>O que faço se o meu VM já está a reportar a um espaço de trabalho existente?
Se já está a recolher dados das suas máquinas virtuais, pode já tê-lo configurado para reportar dados a um espaço de trabalho do Log Analytics existente.  Enquanto esse espaço de trabalho estiver numa das nossas regiões apoiadas, você pode ativar informações vM para esse espaço de trabalho pré-existente.  Se o espaço de trabalho que já está a utilizar não estiver numa das nossas regiões apoiadas, não poderá embarcar em conhecimentos de VM neste momento.  Estamos a trabalhar ativamente para apoiar regiões adicionais.


### <a name="why-did-my-vm-fail-to-onboard"></a>Porque é que o meu VM não conseguiu embarcar?
Ao embarcar um Azure VM a partir do portal Azure, ocorrem os seguintes passos:

* É criado um espaço de trabalho de Log Analytics predefinido, se essa opção for selecionada.
* O agente Log Analytics é instalado em VMs Azure utilizando uma extensão VM, se for determinado que é necessário.  
* O agente VM insights Map Dependency é instalado em VMs Azure usando uma extensão, se determinado que é necessário. 

Durante o processo de bordo, verificamos o estado de cada um dos acima referidos para lhe devolver um estado de notificação no portal. A configuração do espaço de trabalho e da instalação do agente normalmente demora 5 a 10 minutos. A visualização dos dados de monitorização no portal demora mais 5 a 10 minutos.  

Se tiver iniciado o embarque e visto as mensagens indicando que o VM precisa de ser a bordo, deixe até 30 minutos para que o VM complete o processo. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Não vejo alguns ou quaisquer dados nos gráficos de desempenho para o meu VM
Os nossos gráficos de desempenho foram atualizados para utilizar os dados armazenados na tabela *InsightsMetrics.*  Para ver dados nestes gráficos, terá de atualizar para utilizar a nova solução VM Insights.  Consulte a nossa [GA FAQ](vm/vminsights-ga-release-faq.md) para obter informações adicionais.

Se não vir dados de desempenho na tabela de discos ou em algumas das tabelas de desempenho, os seus contadores de desempenho podem não estar configurados no espaço de trabalho. Para resolver, executar o seguinte [script PowerShell](./vm/vminsights-enable-powershell.md).


### <a name="how-is-vm-insights-map-feature-different-from-service-map"></a>Como é que as informações do VM O mapa é diferente do Mapa de Serviços?
A funcionalidade de mapa de insights VM baseia-se no Mapa de Serviços, mas tem as seguintes diferenças:

* A vista do Mapa pode ser acedida a partir da lâmina VM e a partir de insights VM sob O Monitor Azure.
* As ligações no Mapa são agora clicáveis e exibem uma visão dos dados métricos de ligação no painel lateral para a ligação selecionada.
* Há uma nova API que é usada para criar os mapas para melhor suportar mapas mais complexos.
* Os VMs monitorizados estão agora incluídos no nó do grupo cliente, e o gráfico de donuts mostra a proporção de máquinas virtuais monitorizadas vs não monitorizadas no grupo.  Também pode ser usado para filtrar a lista de máquinas quando o grupo é expandido.
* As máquinas virtuais monitorizadas estão agora incluídas nos nós do grupo de portas do servidor, e o gráfico de donuts mostra a proporção de máquinas monitorizadas vs não monitorizadas no grupo.  Também pode ser usado para filtrar a lista de máquinas quando o grupo é expandido.
* O estilo do mapa foi atualizado para ser mais consistente com o Mapa de Aplicações a partir de insights de aplicação.
* Os painéis laterais foram atualizados e não têm o conjunto completo de integrações que foram suportados no Mapa de Serviços - Gestão de Atualização, Rastreio de Alterações, Segurança e Serviço. 
* A opção de escolher grupos e máquinas para mapear foi atualizada e agora suporta Subscrições, Grupos de Recursos, conjuntos de escala de máquinas virtuais Azure e serviços cloud.
* Não é possível criar novos grupos de máquinas de mapa de serviço na funcionalidade de mapa de insights VM.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Porque é que os meus gráficos de desempenho mostram linhas pontilhadas?
Isto pode ocorrer por algumas razões.  Nos casos em que há uma lacuna na recolha de dados, retratamos as linhas como pontilhadas.  Se tiver modificado a frequência de amostragem de dados para os contadores de desempenho ativados (a definição predefinida é recolher dados a cada 60 segundos), pode ver linhas pontilhadas na tabela se escolher um intervalo de tempo estreito para o gráfico e a sua frequência de amostragem é inferior ao tamanho do balde utilizado na tabela (por exemplo, a frequência de amostragem é a cada 10 minutos e cada balde na tabela é de 5 minutos).  Escolher um intervalo de tempo mais amplo para visualizar deve fazer com que as linhas de gráfico apareçam como linhas sólidas em vez de pontos neste caso.

### <a name="are-groups-supported-with-vm-insights"></a>Os grupos são apoiados com insights VM?
Sim, assim que instalar o agente Dependency recolhemos informações dos VMs para exibir grupos baseados na subscrição, grupo de recursos, conjuntos de escala de máquinas virtuais e serviços na nuvem.  Se esteve a utilizar o Mapa de Serviço e criou grupos de máquinas, estes também são exibidos.  Os grupos de computador também aparecerão no filtro de grupos se os tiver criado para o espaço de trabalho que está a visualizar. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Como vejo os detalhes do que está a conduzir a linha percentil 95 nas tabelas de desempenho agregadas?
Por predefinição, a lista é ordenada para mostrar-lhe os VMs que têm o valor mais alto para o percentil 95 para a métrica selecionada, exceto para o gráfico de memória disponível, que mostra as máquinas com o menor valor do percentil 5.  Clicar na tabela abrirá a vista **da Lista N superior**  com a métrica escolhida.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Como é que a funcionalidade Do Mapa lida com iPs duplicados em diferentes redes e sub-redes?
Se estiver a duplicar os intervalos de IP com conjuntos de escala de máquinas virtuais VM ou Azure em sub-redes e vnets, pode fazer com que os insights VM Map apresentem informações incorretas. Esta é uma questão conhecida e estamos a investigar opções para melhorar esta experiência.

### <a name="does-map-feature-support-ipv6"></a>O Mapa tem suporte iPv6?
A funcionalidade do mapa suporta atualmente apenas o IPv4 e estamos a investigar o suporte para o IPv6. Também apoiamos o IPv4 que está dentro do IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Quando carrego um mapa para um Grupo de Recursos ou outro grupo grande, o mapa é difícil de ver
Embora tenhamos feito melhorias no Map para lidar com configurações grandes e complexas, percebemos que um mapa pode ter muitos nós, ligações e nó funcionando como um cluster.  Estamos empenhados em continuar a reforçar o apoio para aumentar a escalabilidade.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Porque é que o gráfico de rede no separador Desempenho parece diferente do gráfico de rede na página de visão geral do Azure VM?

A página geral de um VM Azure exibe gráficos com base na medição da atividade do anfitrião no VM convidado.  Para o gráfico de rede na Visão Geral do Azure VM, apenas apresenta o tráfego de rede que será faturado.  Isto não inclui o tráfego de rede inter-virtual.  Os dados e gráficos mostrados para insights VM baseiam-se em dados do VM convidado e o gráfico de rede exibe todo o tráfego TCP/IP que está de entrada e saída para esse VM, incluindo a rede inter-virtual.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Como é medido o tempo de resposta para os dados armazenados em VMConnection e apresentados no painel de ligação e nos livros de trabalho?

O tempo de resposta é uma aproximação. Uma vez que não instrumentamos o código da aplicação, não sabemos realmente quando um pedido começa e quando a resposta chega. Em vez disso, observamos que os dados são enviados numa ligação e, em seguida, os dados voltam a essa ligação. O nosso agente acompanha estes envios e receções e tenta emparelhá-los: uma sequência de envios, seguida de uma sequência de receções é interpretada como um par de pedidos/respostas. O tempo entre estas operações é o tempo de resposta. Incluirá a latência da rede e o tempo de processamento do servidor.

Esta aproximação funciona bem para protocolos que são baseados em pedidos/respostas: um único pedido sai na ligação, e uma única resposta chega. É o caso do HTTP(S) (sem canalização), mas não está satisfeito com outros protocolos.

### <a name="are-there-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Existem limitações se eu estiver no plano de preços gratuitos do Log Analytics?
Se tiver configurado o Azure Monitor com um espaço de trabalho Log Analytics utilizando o nível de preços *gratuitos,* a funcionalidade de insights VM Map apenas suportará cinco máquinas ligadas ligadas ao espaço de trabalho. Se tiver cinco VM ligados a um espaço de trabalho gratuito, desligue um dos VMs e, em seguida, ligue um novo VM, o novo VM não é monitorizado e refletido na página do Mapa.  

Nesta condição, será solicitado com a opção **Try Now** quando abrir o VM e selecionar **Insights** a partir do painel esquerdo, mesmo depois de já ter sido instalado no VM.  No entanto, não é solicitado com opções como normalmente ocorreria se este VM não estivesse a bordo de insights VM. 


## <a name="next-steps"></a>Passos seguintes
Se a sua pergunta não for respondida aqui, pode consultar os seguintes fóruns para perguntas e respostas adicionais.

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Para obter feedback geral sobre o Azure Monitor, visite o [fórum de comentários](https://feedback.azure.com/forums/34192--general-feedback).