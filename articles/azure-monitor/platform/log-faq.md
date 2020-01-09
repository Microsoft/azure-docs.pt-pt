---
title: FAQ do log Analytics | Documentos da Microsoft
description: Respostas para perguntas frequentes sobre o serviço de análise de logs de Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/01/2019
ms.openlocfilehash: 77159e0fa73a1f56688c867c55ae46f28016992c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75394788"
---
# <a name="log-analytics-faq"></a>FAQ do Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre o Azure Monitor Log Analytics espaço de trabalho. Se tiver perguntas adicionais sobre o Log Analytics, avance para o [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e poste suas perguntas. Quando uma pergunta é colocada frequentemente, adicionamo-la a este artigo para que ele pode ser encontrado rapidamente e facilmente.


## <a name="new-logs-experience"></a>Nova experiência de registos

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>P: qual é a diferença entre a nova experiência de registos e Log Analytics?

R: eles são a mesma coisa. [O log Analytics está a ser integrado como uma funcionalidade no Azure Monitor](../../azure-monitor/azure-monitor-rebrand.md) para fornecer a experiência de monitorização de unificação de um mais. A nova experiência de registos no Azure Monitor é exatamente o mesmo que as consultas do Log Analytics que muitos clientes já têm usado.

### <a name="q-can-i-still-use-log-search"></a>P: Posso continuar a utilizar pesquisa de registos? 

R: pesquisa de registos de atualmente, está ainda disponível no portal do OMS e no portal do Azure com o nome **registos (clássico)** . Portal do OMS será oficialmente retirado a 15 de Janeiro de 2019. A experiência de registos clássica no portal do Azure vai ser descontinuada gradualmente e substituído a nova experiência de registos. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>P. Posso continuar a utilizar Portal de análise avançadas? 
A nova experiência de registos no portal do Azure baseia-se no Portal da análise avançada, mas ele ainda pode ser acessado fora do portal do Azure. O mapa para extinguir este portal externo será anunciado em breve.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>P. Por que não é possível ver o Explorador de consultas e guardar os botões na nova experiência de registos?

**Explorador de consulta**, **salvar** e **definir alerta** botões não estão disponíveis ao explorar os registos no contexto de um recurso específico. Criar alertas, salvar ou carregar uma consulta, os registos devem ser confinados para um espaço de trabalho. Para abrir registos no contexto de área de trabalho, selecione **todos os serviços** > **Monitor** > **registos**. A última área de trabalho utilizada é selecionada, mas pode selecionar qualquer outra área de trabalho. Ver [visualização e análise de dados no Log Analytics](../log-query/portals.md) para obter mais informações.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>P. Como posso extrair campos personalizados na nova experiência de registos? 

R: extração de campos personalizada de atualmente são suportados no clássica a experiência de registos. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>P. Onde posso encontrar a vista de lista nos Logs de novo? 

R: vista de lista de não está disponível nos Logs de novo. Há uma seta à esquerda de cada registo na tabela de resultados. Clique nesta seta para abrir os detalhes de um registo específico. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>P. Depois de executar uma consulta, uma lista de filtros sugeridos está disponível. Como posso ver os filtros? 

R: clique em ' filtros ' no painel esquerdo para ver uma visualização da implementação de novos filtros. Agora isso se baseia seu resultado completo, em vez de estar limitado pelo limite de registo 10.000 da interface do Usuário. Isto é, atualmente, uma lista de filtros mais populares e os 10 valores mais comuns para cada filtro. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>P. Por que estou recebendo o erro: "Registar o fornecedor de recursos"Microsoft. insights' para esta subscrição para ativar esta consulta"nos registos, após a análise de sessão de VM? 

R: por predefinição, muitos fornecedores de recursos são automaticamente registrados, no entanto, poderá ter de registar manualmente alguns fornecedores de recursos. Esta ação configura a sua subscrição para trabalhar com o fornecedor de recursos. O âmbito de registo é sempre a subscrição. Veja [Fornecedores e tipos de recursos](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) para obter mais informações.

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>P. Por que estou eu não estou a receber nenhuma mensagem de erro de acesso ao aceder a registos de uma página VM? 

R: para ver os registos de VM, terá de ser concedida permissão de leitura para as áreas de trabalho que armazena os registos VM. Nestes casos, o administrador deve conceder-lhe com as permissões no Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>P. Por isso que posso pode aceder à minha área de trabalho no portal do OMS, mas recebo a mensagem de erro "não têm acesso" no portal do Azure?  

R: para aceder a uma área de trabalho no Azure, tem de ter permissões do Azure atribuídas. Existem alguns casos em que pode não ter permissões de acesso adequado. Nestes casos, o administrador deve conceder-lhe com permissões no Azure.See [portal do OMS para o Azure](oms-portal-transition.md) para obter mais informações.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>P. Por que não é possível não é possível ver entrada de estruturador de vistas nos registos?

R: estruturador de vistas de só está disponível nos registos para os utilizadores com permissões de Contribuidor ou superior.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>P. Posso continuar a utilizar o portal do Analytics fora do Azure?

R. Sim, a página de registos no Azure e o portal da análise avançada baseiam-se no mesmo código. O log Analytics está a ser integrado como uma funcionalidade no Azure Monitor para fornecer uma experiência de monitorização mais unificada. Você ainda pode acessar o portal de análise usando a URL: https:\/\/Portal. loganalytics. Io/inscrições/{SubscriptionId}/resourcegroups/{resourceGroupName}/espaços de trabalho/{WorkspaceName}.



## <a name="general"></a>Geral

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>P. Como posso ver meu vistas e soluções no portal do Azure? 

R: a lista de vistas e soluções instaladas estão disponíveis no portal do Azure. Clique em **Ver todos os serviços**. Na lista de recursos, selecione **Monitor**, em seguida, clique em **... Mais**. A última área de trabalho utilizada é selecionada, mas pode selecionar qualquer outra área de trabalho. 

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>P. O Log Analytics usa o mesmo agente como centro de segurança do Azure?

R: no início de Junho de 2017, o Centro de segurança do Azure começou com o Microsoft Monitoring Agent para recolher e armazenar dados. Para obter mais informações, consulte [FAQ de migração de plataforma de centro de segurança do Azure](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>P. Quais verificações são efetuadas pelo AD e soluções de avaliação do SQL?

R: a consulta seguinte mostra uma descrição de todas as verificações executadas atualmente:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Os resultados podem ser exportados para o Excel para examinar detalhadamente.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>P. Por que vejo algo diferente de OMS na consola do System Center Operations Manager?

R: dependendo em quais Update Rollup do Operations Manager estiver a utilizar, poderá ver um nó para *System Center Advisor*, *das informações operacionais*, ou *do Log Analytics*.

A atualização de seqüência de caracteres de texto *OMS* está incluído num pacote de gestão, que tem de ser importado manualmente. Para ver o texto atual e a funcionalidade, siga as instruções do artigo mais recente do System Center Operations Manager Update Rollup KB e atualize a consola.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>P: existe uma versão no local do Log Analytics?

R: não. O log Analytics é um serviço de cloud escalável que processa e armazena grandes quantidades de dados. 

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

R. O `Move-AzResource` cmdlet permite-lhe mover uma área de trabalho do Log Analytics bem como uma conta de automatização de uma subscrição do Azure para outra. Para obter mais informações, consulte [move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Esta alteração também pode ser feita no portal do Azure.

Não é possível mover dados de uma área de trabalho do Log Analytics para outro, ou alterar a região de dados do Log Analytics são armazenados no.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>P: como posso adicionar o Log Analytics para o System Center Operations Manager?

R: a atualização para o update rollup mais recente e importar pacotes de gestão permite-lhe ligar o Operations Manager ao Log Analytics.

>[!NOTE]
>A ligação do Operations Manager ao Log Analytics está disponível apenas para o System Center Operations Manager 2012 SP1 e posterior.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>P: como posso confirmar que um agente é capaz de comunicar com o Log Analytics?

R: para garantir que o agente possa se comunicar com o espaço de trabalho Log Analytics, acesse: painel de controle, configurações de & de segurança **Microsoft Monitoring Agent**.

Sob o **do Azure Log Analytics (OMS)** separador, procure uma marca de verificação verde. Um ícone de marca de verificação verde confirma que o agente é capaz de comunicar com o serviço do Azure.

Um ícone de aviso amarelo significa que o agente está a ter problemas de comunicação com o Log Analytics. Uma das razões comuns é que o serviço Microsoft Monitoring Agent parou. Utilize o Gestor de controlo de serviço para reiniciar o serviço.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>P: Como faço para interromper um agente de comunicar com o Log Analytics?

R: em System Center Operations Manager, remova o computador da lista Log Analytics computadores gerenciados. Do Operations Manager atualiza a configuração do agente já não é o relatório para o Log Analytics. Para agentes ligados diretamente ao Log Analytics, pode impedi-los de comunicar através de: painel de controlo, segurança e as definições, **Microsoft Monitoring Agent**.
Sob **do Azure Log Analytics (OMS)** , remover todas as áreas de trabalho listadas.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>P: por que eu ganho um erro ao tentar mover a minha área de trabalho de uma subscrição do Azure para outra?

R: para mover uma área de trabalho para uma subscrição diferente ou um grupo de recursos, tem primeiro de desassociar a conta de automatização na área de trabalho. A desassociar uma conta de automatização requer a remoção destas soluções se estiverem instalados na área de trabalho: gestão de atualizações, controlo de alterações ou iniciar/parar VMs fora do horário comercial são removidas. Depois destas soluções são removidas, desassociar a conta de automatização, selecionando **ligado áreas de trabalho** no painel da esquerda a automação de conta de recurso e clique em **desassociar área de trabalho** da faixa de opções.
 > Removido de soluções precisam de ser reinstalados na área de trabalho e a ligação de automatização à área de trabalho tem de ser expressas novamente após a mudança.

Certifique-se de que tem permissão em ambas as subscrições do Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>P: por que eu ganho um erro ao tentar atualizar um SavedSearch?

R: precisa adicionar "etag" no corpo da API ou de propriedades do modelo do Azure Resource Manager:
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
R. Não há nenhum limite para a quantidade de dados que é carregada, ela se baseia na opção de preço que você seleciona-reserva de capacidade ou pré-pago. Um espaço de trabalho Log Analytics foi projetado para escalar verticalmente para lidar com o volume proveniente de um cliente, mesmo que ele seja de terabytes por dia. Para obter mais informações, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/monitor/).

O agente Log Analytics foi concebido para garantir que ele ocupa pouco espaço. O volume de dados varia consoante as soluções que ativa. Pode encontrar informações detalhadas sobre o volume de dados e ver a divisão pela solução no [utilização](../../azure-monitor/platform/data-usage.md) página.

Para obter mais informações, você pode ler um [blog do cliente](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) mostrando seus resultados depois de avaliar a utilização de recursos (superfície) do agente de log Analytics.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>P. Quanta largura de banda de rede é utilizada pelo agente de gestão da Microsoft (MMA) ao enviar dados para o Log Analytics?

R. Largura de banda é uma função na quantidade de dados enviados. Dados são comprimidos à medida que ele é enviado através da rede.

### <a name="q-how-much-data-is-sent-per-agent"></a>P. A quantidade de dados é enviado por agente?

R. A quantidade de dados enviados por agente depende de:

* As soluções que tiver ativado
* O número de registos e contadores de desempenho recolhidos
* O volume de dados nos registos

Geral de utilização é apresentada no [utilização](../../azure-monitor/platform/data-usage.md) página.

Para computadores que são capazes de executar o agente de WireData, utilize a seguinte consulta para ver a quantidade de dados está sendo enviada:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Passos seguintes

Comece a [usar o Azure monitor](../../azure-monitor/overview.md) para saber mais sobre log Analytics e entrar em funcionamento em minutos.
