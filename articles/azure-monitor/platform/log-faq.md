---
title: FAQ do log Analytics | Documentos da Microsoft
description: Respostas a perguntas mais frequentes sobre o serviço do Log Analytics do Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 1c145f100e22dbb268a7cd491eb8a43b51988c69
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315814"
---
# <a name="log-analytics-faq"></a>FAQ do Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O FAQ do Microsoft é uma lista de perguntas freqüentes sobre o Log Analytics no Microsoft Azure. Se tiver perguntas adicionais sobre o Log Analytics, avance para o [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e poste suas perguntas. Quando uma pergunta é colocada frequentemente, adicionamo-la a este artigo para que ele pode ser encontrado rapidamente e facilmente.


## <a name="new-logs-experience"></a>Nova experiência de registos

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>P: O que é a diferença entre a nova experiência de registos e Log Analytics?

R: Eles são a mesma coisa. [O log Analytics está a ser integrado como uma funcionalidade no Azure Monitor](../../azure-monitor/azure-monitor-rebrand.md) para fornecer a experiência de monitorização de unificação de um mais. A nova experiência de registos no Azure Monitor é exatamente o mesmo que as consultas do Log Analytics que muitos clientes já têm usado.

### <a name="q-can-i-still-use-log-search"></a>P: Posso continuar a utilizar pesquisa de registos? 

R: Pesquisa de registos está atualmente ainda está disponível no portal do OMS e no portal do Azure com o nome **registos (clássico)**. Portal do OMS será oficialmente retirado a 15 de Janeiro de 2019. A experiência de registos clássica no portal do Azure vai ser descontinuada gradualmente e substituído a nova experiência de registos. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>P. Posso continuar a utilizar Portal de análise avançadas? 
A nova experiência de registos no portal do Azure baseia-se no Portal da análise avançada, mas ele ainda pode ser acessado fora do portal do Azure. O mapa para extinguir este portal externo será anunciado em breve.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>P. Por que não é possível ver o Explorador de consultas e guardar os botões na nova experiência de registos?

**Explorador de consulta**, **salvar** e **definir alerta** botões não estão disponíveis ao explorar os registos no contexto de um recurso específico. Criar alertas, salvar ou carregar uma consulta, os registos devem ser confinados para um espaço de trabalho. Para abrir registos no contexto de área de trabalho, selecione **todos os serviços** > **Monitor** > **registos**. A última área de trabalho utilizada é selecionada, mas pode selecionar qualquer outra área de trabalho. Ver [visualização e análise de dados no Log Analytics](../log-query/portals.md) para obter mais informações.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>P. Como posso extrair campos personalizados na nova experiência de registos? 

R: Experiência de registos de campos personalizados extração são atualmente suportadas no clássica. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>P. Onde posso encontrar a vista de lista nos Logs de novo? 

R: Vista de lista não está disponível nos Logs de novo. Há uma seta à esquerda de cada registo na tabela de resultados. Clique nesta seta para abrir os detalhes de um registo específico. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>P. Depois de executar uma consulta, uma lista dos filtros sugeridos estão disponíveis. Como posso ver filtros? 

R: Clique em "Filtros" no painel da esquerda para ver uma pré-visualização da nova implementação de filtros. Agora isso se baseia seu resultado completo, em vez de estar limitado pelo limite de registo 10.000 da interface do Usuário. Isto é, atualmente, uma lista de filtros mais populares e os 10 valores mais comuns para cada filtro. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>P. Por que estou a receber o erro: "Registar o fornecedor de recursos"Microsoft. insights' para esta subscrição para ativar esta consulta"nos registos, após a análise de sessão de VM? 

R: Por padrão, muitos fornecedores de recursos são automaticamente registrados, no entanto, poderá ter de registar manualmente alguns fornecedores de recursos. Esta ação configura a sua subscrição para trabalhar com o fornecedor de recursos. O âmbito de registo é sempre a subscrição. Veja [Fornecedores e tipos de recursos](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal) para obter mais informações.

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>P. Por que estou eu não estou a receber nenhuma mensagem de erro de acesso ao aceder a registos de uma página VM? 

R: Para ver os registos de VM, terá de ser concedida permissão de leitura para as áreas de trabalho que armazena os registos VM. Nestes casos, o administrador deve conceder-lhe com as permissões no Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>P. Por isso que posso pode aceder à minha área de trabalho no portal do OMS, mas recebo a mensagem de erro "não têm acesso" no portal do Azure?  

R: Para aceder a uma área de trabalho no Azure, tem de ter permissões do Azure atribuídas. Existem alguns casos em que pode não ter permissões de acesso adequado. Nestes casos, o administrador deve conceder-lhe com permissões no Azure.See [portal do OMS para o Azure](oms-portal-transition.md) para obter mais informações.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>P. Por que não é possível não é possível ver entrada de estruturador de vistas nos registos? 
R: Estruturador de vistas só está disponível nos registos para os utilizadores com permissões de Contribuidor ou superior.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>P. Posso continuar a utilizar o portal do Analytics fora do Azure?
R. Sim, a página de registos no Azure e o portal da análise avançada baseiam-se no mesmo código. O log Analytics está a ser integrado como uma funcionalidade no Azure Monitor para fornecer uma experiência de monitorização mais unificada. Pode continuar a aceder através do URL do portal da análise: https://portal.loganalytics.io/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/workspaces/{workspaceName}.



## <a name="general"></a>Geral

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>P. Como posso ver meu vistas e soluções no portal do Azure? 

R: A lista de vistas e soluções instaladas estão disponíveis no portal do Azure. Clique em **Ver todos os serviços**. Na lista de recursos, selecione **Monitor**, em seguida, clique em **... Mais**. A última área de trabalho utilizada é selecionada, mas pode selecionar qualquer outra área de trabalho. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>P. Por que motivo não consigo criar áreas de trabalho na região e.u.a. centro-oeste? 

R: Esta região atingiu o limite de capacidade temporariamente. Está previsto corrigir o limite na primeira metade de 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>P. O Log Analytics usa o mesmo agente como centro de segurança do Azure?

R: No início de Junho de 2017, o Centro de segurança do Azure começou com o Microsoft Monitoring Agent para recolher e armazenar dados. Para obter mais informações, consulte [FAQ de migração de plataforma de centro de segurança do Azure](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>P. Quais verificações são efetuadas pelo AD e soluções de avaliação do SQL?

R: A consulta seguinte mostra uma descrição de todas as verificações executadas atualmente:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Os resultados podem ser exportados para o Excel para examinar detalhadamente.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>P. Por que vejo algo diferente de OMS na consola do System Center Operations Manager?

R: Consoante o Update Rollup do Operations Manager estiver a utilizar, pode ver um nó para *System Center Advisor*, *das informações operacionais*, ou *do Log Analytics*.

A atualização de seqüência de caracteres de texto *OMS* está incluído num pacote de gestão, que tem de ser importado manualmente. Para ver o texto atual e a funcionalidade, siga as instruções do artigo mais recente do System Center Operations Manager Update Rollup KB e atualize a consola.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>P: Existe uma versão no local do Log Analytics?

R: Não. O log Analytics é um serviço de cloud escalável que processa e armazena grandes quantidades de dados. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>P. Como posso resolver problemas se o Log Analytics já não está a recolher dados?

R: Para uma subscrição e o espaço de trabalho criadas antes de 2 de Abril de 2018 no *gratuito* escalão de preço se mais de 500 MB de dados é enviada num dia, deixa de recolha de dados para o resto do dia. Atingir o limite diário é um motivo comum que o Log Analytics interrompe a recolha de dados ou dados parecem estar em falta.  

O log Analytics cria um evento do tipo *Heartbeat* e pode ser utilizado para determinar se a recolha de dados será interrompido. 

Execute a seguinte consulta na pesquisa para verificar se estiver a atingir o limite diário e dados em falta: `Heartbeat | summarize max(TimeGenerated)`

Para verificar um computador específico, execute a seguinte consulta: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Quando parar a recolha de dados, consoante o período de tempo selecionado, não verá quaisquer registos devolvidos.   

A tabela seguinte descreve as razões que pára de recolha de dados e uma ação sugerida para retomar a recolha de dados:

| Interrompe a recolha de dados do razão                       | Para retomar a recolha de dados |
| -------------------------------------------------- | ----------------  |
| Foi atingido o limite de dados gratuitos<sup>1</sup>       | Aguarde até ao mês seguinte para a coleção reiniciar automaticamente, ou<br> Alterar para um escalão de preço pago |
| Subscrição do Azure está num estado suspenso devido a: <br> Versão de avaliação gratuita terminada <br> Passagem do Azure expirou <br> Atingido o limite de gastos mensal (por exemplo, numa assinatura do MSDN ou o Visual Studio)                          | Converter numa subscrição paga <br> Converter numa subscrição paga <br> Remover o limite, ou aguarde até que o limite é zerado |

<sup>1</sup> se a sua área de trabalho no *gratuito* escalão de preço, está limitado ao envio de 500 MB de dados por dia para o serviço. Quando atingir o limite diário, deixa de recolha de dados até ao dia seguinte. Dados enviados, enquanto a recolha de dados está parada não indexados e não estão disponíveis para pesquisa. Quando a retoma a recolha de dados, o processamento ocorre apenas para novos dados enviados. 

O log Analytics utiliza hora UTC e cada dia começa à meia-noite UTC. Se a área de trabalho atinge o limite diário, o processamento continua durante a primeira hora do dia seguinte UTC.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>P. Como posso ser notificado da quando parar a recolha de dados?

R: Utilize os passos descritos em [criar um novo alerta de registo](../../azure-monitor/platform/alerts-metric.md) para ser notificado quando parar a recolha de dados.

Quando criar o alerta para quando para a recolha de dados, defina o:

- **Definir condição de alerta** especifique a sua área de trabalho do Log Analytics como o destino de recursos.
- **Critérios de alerta** especifique o seguinte:
   - **Sinalizar o nome** selecionar **pesquisa de registos personalizado**.
   - A **consulta de pesquisa** como `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - A **Lógica de alerta** é **Baseada no** *número de resultados* e a **Condição** é *Maior do que* um **Limiar** de *0*
   - **Período de tempo** dos *30* minutos e **frequência do alerta** a todo *10* minutos
- **Definir detalhes do alerta** especifique o seguinte:
   - **Nome** para *parada a recolha de dados*
   - A **gravidade** como *Aviso*

Especifique um existente ou crie um novo [grupo de ação](../../azure-monitor/platform/action-groups.md) , de modo que quando o alerta de registo corresponde aos critérios, é notificado se tiver um heartbeat em falta para a mais de 15 minutos.

## <a name="configuration"></a>Configuração
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>P. Pode alterar o nome do contentor de blob/tabela usado para ler a partir do diagnóstico do Azure (WAD)?

R. Não, não é atualmente possível ler a partir de tabelas arbitrárias ou de contentores no armazenamento do Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>P. O que os endereços IP é que a utilização de serviço do Log Analytics? Como garantir que a minha firewall permite apenas tráfego para o serviço do Log Analytics?

R. O serviço Log Analytics se baseia no Azure. Endereços de IP de análise de registo estão no [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

Como as implementações de serviço são feitas, alterar os endereços IP reais do serviço do Log Analytics. Os nomes DNS para permitir através da firewall estão documentados em [requisitos de rede](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>P. Utilizar o ExpressRoute para ligar ao Azure. O tráfego de meu Log Analytics usa a minha ligação do ExpressRoute?

R. Os diferentes tipos de tráfego do ExpressRoute são descritos a [documentação do ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

O tráfego para o Log Analytics utiliza o circuito de ExpressRoute de peering público.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>P. Existe uma forma simple e fácil de mover uma área de trabalho do Log Analytics existente para outra subscrição de área de trabalho/Azure Log Analytics?

R. O `Move-AzResource` cmdlet permite-lhe mover uma área de trabalho do Log Analytics bem como uma conta de automatização de uma subscrição do Azure para outra. Para obter mais informações, consulte [movimentação AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Esta alteração também pode ser feita no portal do Azure.

Não é possível mover dados de uma área de trabalho do Log Analytics para outro, ou alterar a região de dados do Log Analytics são armazenados no.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>P: Como posso adicionar o Log Analytics para o System Center Operations Manager?

R:  A atualização para o update rollup mais recente e importar pacotes de gestão permite-lhe ligar o Operations Manager ao Log Analytics.

>[!NOTE]
>A ligação do Operations Manager ao Log Analytics está disponível apenas para o System Center Operations Manager 2012 SP1 e posterior.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>P: Como posso confirmar que um agente é capaz de comunicar com o Log Analytics?

R: Para garantir que o agente consegue comunicar com o OMS, aceda a: Controlar o painel, segurança e as definições, **Microsoft Monitoring Agent**.

Sob o **do Azure Log Analytics (OMS)** separador, procure uma marca de verificação verde. Um ícone de marca de verificação verde confirma que o agente é capaz de comunicar com o serviço do Azure.

Um ícone de aviso amarelo significa que o agente está a ter problemas de comunicação com o Log Analytics. Uma das razões comuns é que o serviço Microsoft Monitoring Agent parou. Utilize o Gestor de controlo de serviço para reiniciar o serviço.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>P: Como faço para interromper um agente de comunicar com o Log Analytics?

R: No System Center Operations Manager, remova o computador da lista de computadores geridos de OMS. Do Operations Manager atualiza a configuração do agente já não é o relatório para o Log Analytics. Para agentes ligados diretamente ao Log Analytics, pode pará-los de comunicar através de: Controlar o painel, segurança e as definições, **Microsoft Monitoring Agent**.
Sob **do Azure Log Analytics (OMS)**, remover todas as áreas de trabalho listadas.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>P: Por que eu ganho um erro ao tentar mover a minha área de trabalho de uma subscrição do Azure para outra?

R: Para mover uma área de trabalho para uma subscrição diferente ou um grupo de recursos, tem primeiro de desassociar a conta de automatização na área de trabalho. Desassociar uma conta de Automatização requer que as seguintes soluções sejam removidas, caso estejam instaladas na área de trabalho: Gestão de atualizações, controlo de alterações ou iniciar/parar VMs fora do horário comercial são removidas. Depois destas soluções são removidas, desassociar a conta de automatização, selecionando **ligado áreas de trabalho** no painel da esquerda a automação de conta de recurso e clique em **desassociar área de trabalho** da faixa de opções.
 > Removido de soluções precisam de ser reinstalados na área de trabalho e a ligação de automatização à área de trabalho tem de ser expressas novamente após a mudança.

Certifique-se de que tem permissão em ambas as subscrições do Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>P: Por que eu ganho um erro ao tentar atualizar um SavedSearch?

R: Tem de adicionar "etag" no corpo da API ou de propriedades do modelo do Azure Resource Manager:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Dados do agente
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>P. A quantidade de dados posso enviar através do agente para o Log Analytics? Existe uma quantidade máxima de dados por cliente?
R. O plano gratuito define um limite diário de 500 MB por área de trabalho. Os planos standard e premium não têm limite na quantidade de dados que são carregados. Como um serviço cloud, do Log Analytics foi concebido para dimensionar automaticamente até o identificador do volume proveniente de um cliente – mesmo se for terabytes por dia.

O agente Log Analytics foi concebido para garantir que ele ocupa pouco espaço. O volume de dados varia consoante as soluções que ativa. Pode encontrar informações detalhadas sobre o volume de dados e ver a divisão pela solução no [utilização](../../azure-monitor/platform/data-usage.md) página.

Para obter mais informações, leia um [blog clientes](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) que mostra os resultados depois de avaliar a utilização de recursos (requisitos de espaço) do agente do OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>P. Quanta largura de banda de rede é utilizada pelo agente de gestão da Microsoft (MMA) ao enviar dados para o Log Analytics?

R. Largura de banda é uma função na quantidade de dados enviados. Dados são comprimidos à medida que ele é enviado através da rede.

### <a name="q-how-much-data-is-sent-per-agent"></a>P. A quantidade de dados é enviado por agente?

R. A quantidade de dados enviados por agente depende de:

* As soluções que tiver ativado
* O número de registos e contadores de desempenho recolhidos
* O volume de dados nos registos

O escalão de preço gratuito é uma boa forma de carregar vários servidores e o volume de dados típico do medidor. Geral de utilização é apresentada no [utilização](../../azure-monitor/platform/data-usage.md) página.

Para computadores que são capazes de executar o agente de WireData, utilize a seguinte consulta para ver a quantidade de dados está sendo enviada:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Log Analytics](../../azure-monitor/overview.md) para saber mais sobre o Log Analytics e começar a trabalhar em minutos.
