---
title: Perguntas frequentes sobre Log Analytics | Microsoft Docs
description: Respostas para perguntas frequentes sobre o serviço de Log Analytics do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/13/2018
ms.openlocfilehash: e3ebb87a7a5f6200d860c1c79591719c32313e11
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932222"
---
# <a name="log-analytics-faq"></a>FAQ do Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre Log Analytics em Microsoft Azure. Se você tiver dúvidas adicionais sobre Log Analytics, vá para o [Fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e poste suas perguntas. Quando uma pergunta é frequente, a adicionamos a este artigo para que ela possa ser encontrada de forma rápida e fácil.


## <a name="new-logs-experience"></a>Nova experiência de logs

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>P: Qual é a diferença entre a nova experiência de logs e a Log Analytics?

R: eles são a mesma coisa. O [log Analytics está sendo integrado como um recurso no Azure monitor](../../azure-monitor/azure-monitor-rebrand.md) para fornecer uma experiência de monitoramento mais unificada. A nova experiência de logs no Azure Monitor é exatamente a mesma das consultas de Log Analytics que muitos clientes já usam.

### <a name="q-can-i-still-use-log-search"></a>P: ainda posso usar a pesquisa de logs? 

R: a pesquisa de logs ainda está disponível no portal do OMS e no portal do Azure nos logs de nome **(clássico)** . O portal do OMS será desativado oficialmente em 15 de janeiro de 2019. A experiência de logs clássicos no portal do Azure será gradualmente desativada e substituída a nova experiência de logs. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>P. Ainda posso usar o portal de análise avançada? 
A nova experiência de logs no portal do Azure é baseada no portal de análise avançada, mas ainda pode ser acessada fora do portal do Azure. O roteiro para desativar esse portal externo será anunciado em breve.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>P. Por que não consigo ver o Gerenciador de consultas e salvar os botões na nova experiência de logs?

O **Gerenciador de consultas**, os botões **salvar** e **definir alerta** não estão disponíveis ao explorar logs no contexto de um recurso específico. Para criar alertas, salvar ou carregar uma consulta, os logs devem ter o escopo de um espaço de trabalho. Para abrir logs no contexto do espaço de trabalho, selecione **todos os serviços** > **monitorar** > **logs**. O último espaço de trabalho usado é selecionado, mas você pode selecionar qualquer outro espaço de trabalho. Consulte [exibindo e analisando dados em log Analytics](../log-query/portals.md) para obter mais informações.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>P. Como fazer extrair campos personalizados na nova experiência de logs? 

R: atualmente, há suporte para extração de campos personalizados na experiência de logs clássicos. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>P. Onde encontro a exibição de lista nos novos logs? 

R: a exibição de lista não está disponível nos novos logs. Há uma seta à esquerda de cada registro na tabela de resultados. Clique nessa seta para abrir os detalhes de um registro específico. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>P. Depois de executar uma consulta, uma lista de filtros sugeridos está disponível. Como posso ver os filtros? 

R: clique em ' filtros ' no painel esquerdo para ver uma visualização da implementação de novos filtros. Isso agora se baseia em seu conjunto de resultados completo, em vez de ser limitado pelo limite de registro 10.000 da interface do usuário. Atualmente, essa é uma lista dos filtros mais populares e os 10 valores mais comuns para cada filtro. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>P. Por que estou recebendo o erro: "registrar provedor de recursos ' Microsoft. insights ' para esta assinatura para habilitar esta consulta" nos logs, após Drill-in da VM? 

R: por padrão, muitos provedores de recursos são automaticamente registrados, no entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Isso configura sua assinatura para trabalhar com o provedor de recursos. O escopo do registro é sempre a assinatura. Veja [Fornecedores e tipos de recursos](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal) para obter mais informações.

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>P. Por que estou recebendo uma mensagem de erro de acesso ao acessar logs de uma página de VM? 

R: para exibir os logs da VM, você precisa ser concedido com permissão de leitura para os espaços de trabalho que armazenam os logs da VM. Nesses casos, o administrador deve conceder a você permissões no Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>P. Por que posso acessar meu espaço de trabalho no portal do OMS, mas obtenho o erro "você não tem acesso" no portal do Azure?  

R: para acessar um espaço de trabalho no Azure, você deve ter permissões do Azure atribuídas. Há alguns casos em que você pode não ter as permissões de acesso apropriadas. Nesses casos, o administrador deve conceder permissões no Azure. consulte [portal do OMS migrando para o Azure](oms-portal-transition.md) para obter mais informações.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>P. Por que não consigo ver a entrada do designer de exibição nos logs? 
R: o designer de exibição só está disponível em logs para usuários atribuídos com permissões de colaborador ou superior.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>P. Ainda posso usar o portal de análise fora do Azure?
R. Sim, a página de logs no Azure e o portal de análise avançada são baseados no mesmo código. O Log Analytics está sendo integrado como um recurso no Azure Monitor para fornecer uma experiência de monitoramento mais unificada. Você ainda pode acessar o portal de análise usando a URL: https:\/\/Portal. loganalytics. Io/inscrições/{SubscriptionId}/resourcegroups/{resourceGroupName}/espaços de trabalho/{WorkspaceName}.



## <a name="general"></a>Geral

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>P. Como posso ver minhas exibições e soluções no portal do Azure? 

R: a lista de exibições e soluções instaladas estão disponíveis no portal do Azure. Clique em **Ver todos os serviços**. Na lista de recursos, selecione **Monitor**e, em seguida, clique em **... Mais**. O último espaço de trabalho usado é selecionado, mas você pode selecionar qualquer outro espaço de trabalho. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>P. Por que não consigo criar espaços de trabalho na região Oeste EUA Central? 

R: essa região tem um limite de capacidade temporário. Esse limite está planejado para ser resolvido até o final de setembro de 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>P. Log Analytics usar o mesmo agente que a central de segurança do Azure?

R: no início de junho de 2017, A central de segurança do Azure começou a usar o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, confira [perguntas frequentes sobre migração da plataforma da central de segurança do Azure](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>P. Quais verificações são executadas pelo AD e Avaliação do SQL soluções?

R: a consulta a seguir mostra uma descrição de todas as verificações realizadas no momento:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Os resultados podem ser exportados para o Excel para análise adicional.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>P. Por que vejo algo diferente do OMS no console do System Center Operations Manager?

R: dependendo do pacote cumulativo de atualizações do Operations Manager em que você está, você poderá ver um nó para o *System Center Advisor*, *insights operacionais*ou *log Analytics*.

A atualização de cadeia de caracteres de texto para *OMS* está incluída em um pacote de gerenciamento, que precisa ser importado manualmente. Para ver o texto e a funcionalidade atuais, siga as instruções no artigo mais recente do KB de pacote cumulativo de Atualizações System Center Operations Manager e atualize o console.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>P: existe uma versão local do Log Analytics?

R: não. Log Analytics é um serviço de nuvem escalonável que processa e armazena grandes quantidades de dados. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>P. Como fazer solucionar problemas se Log Analytics não estiver mais coletando dados?

R: para uma assinatura e um espaço de trabalho criados antes de 2 de abril de 2018, que está no tipo de preço *gratuito* , se mais de 500 MB de dados forem enviados em um dia, a coleta de dados será interrompida para o restante do dia. Atingir o limite diário é um motivo comum que Log Analytics para de coletar dados ou que os dados pareçam estar ausentes.  

Log Analytics cria um evento do tipo *pulsação* e pode ser usado para determinar se a coleta de dados é interrompida. 

Execute a seguinte consulta na pesquisa para verificar se você está atingindo o limite diário e os dados ausentes: `Heartbeat | summarize max(TimeGenerated)`

Para verificar um computador específico, execute a seguinte consulta: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

Quando a coleta de dados for interrompida, dependendo do intervalo de tempo selecionado, você não verá nenhum registro retornado.   

A tabela a seguir descreve os motivos para a coleta de dados parar e uma ação sugerida para retomar a coleta de dados:

| Motivo para a coleta de dados parar                       | Para retomar a coleta de dados |
| -------------------------------------------------- | ----------------  |
| Limite de dados livres atingido<sup>1</sup>       | Aguarde até que o mês seguinte para a coleção seja reiniciado automaticamente ou<br> Alterar para um tipo de preço pago |
| A assinatura do Azure está em um estado suspenso devido a: <br> A avaliação gratuita terminou <br> O Azure Pass expirou <br> Limite de gastos mensal atingido (por exemplo, em uma assinatura do MSDN ou do Visual Studio)                          | Converter em uma assinatura paga <br> Converter em uma assinatura paga <br> Remover limite ou aguardar até que o limite seja redefinido |

<sup>1</sup> se o seu espaço de trabalho estiver no tipo de preço *gratuito* , você estará limitado a enviar 500 MB de dados por dia para o serviço. Quando você atingir o limite diário, a coleta de dados será interrompida até o dia seguinte. Os dados enviados enquanto a coleta de dados é interrompida não são indexados e não estão disponíveis para pesquisa. Quando a coleta de dados é retomada, o processamento ocorre somente para novos dados enviados. 

Log Analytics usa a hora UTC e cada dia começa à meia-noite UTC. Se o espaço de trabalho atingir o limite diário, o processamento será retomado durante a primeira hora do dia UTC seguinte.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>P. Como posso ser notificado quando a coleta de dados for interrompida?

R: Use as etapas descritas em [criar um novo alerta de log](../../azure-monitor/platform/alerts-metric.md) para ser notificado quando a coleta de dados for interrompida.

Ao criar o alerta para quando a coleta de dados for interrompida, defina:

- **Definir condição de alerta** especifique a sua área de trabalho do Log Analytics como o destino de recursos.
- **Critérios de alerta** especifique o seguinte:
   - **Nome do sinal** selecione **pesquisa de logs personalizada**.
   - A **consulta de pesquisa** como `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - A **Lógica de alerta** é **Baseada no** *número de resultados* e a **Condição** é *Maior do que* um **Limiar** de *0*
   - **Período de tempo** de *30* minutos e **frequência de alerta** a cada *10* minutos
- **Definir detalhes do alerta** especifique o seguinte:
   - **Nome** para *coleta de dados interrompido*
   - A **gravidade** como *Aviso*

Especifique um [grupo de ação](../../azure-monitor/platform/action-groups.md) existente ou criar um novo para que, quando o alerta de log corresponder aos critérios, você será notificado se tiver uma pulsação ausente por mais de 15 minutos.

## <a name="configuration"></a>Configuração
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>P. Posso alterar o nome do contêiner de tabela/BLOB usado para ler de Diagnóstico do Azure (WAD)?

R. Não, atualmente não é possível ler de tabelas ou contêineres arbitrários no armazenamento do Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>P. Quais endereços IP o serviço Log Analytics usa? Como fazer garantir que meu firewall permita apenas o tráfego para o serviço Log Analytics?

R. O serviço de Log Analytics é criado sobre o Azure. Log Analytics endereços IP estão nos [intervalos de IP do datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

À medida que são feitas implantações de serviço, os endereços IP reais do serviço de Log Analytics mudam. Os nomes DNS para permitir por meio do firewall são documentados em [requisitos de rede](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>P. Uso o ExpressRoute para conexão com o Azure. Meu tráfego de Log Analytics usa minha conexão de ExpressRoute?

R. Os diferentes tipos de tráfego de ExpressRoute são descritos na [documentação do expressroute](../../expressroute/expressroute-faqs.md#supported-services).

O tráfego para Log Analytics usa o circuito de ExpressRoute de emparelhamento público.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>P. Há uma maneira simples e fácil de mover um espaço de trabalho de Log Analytics existente para outra assinatura de Log Analytics espaço de trabalho/Azure?

R. O cmdlet `Move-AzResource` permite mover um espaço de trabalho do Log Analytics e também uma conta de automação de uma assinatura do Azure para outra. Para obter mais informações, consulte [move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Essa alteração também pode ser feita no portal do Azure.

Não é possível mover dados de um espaço de trabalho Log Analytics para outro ou alterar a região em que Log Analytics dados são armazenados.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>P: Como fazer adicionar Log Analytics a System Center Operations Manager?

R: a atualização para o pacote cumulativo de atualizações mais recente e a importação de pacotes de gerenciamento permite que você se conecte Operations Manager ao Log Analytics.

>[!NOTE]
>A conexão Operations Manager com Log Analytics só está disponível para o System Center Operations Manager 2012 SP1 e posterior.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>P: como posso confirmar se um agente pode se comunicar com Log Analytics?

R: para garantir que o agente possa se comunicar com o OMS, acesse: painel de controle, configurações de & de segurança **Microsoft Monitoring Agent**.

Na guia **log Analytics do Azure (OMS)** , procure uma marca de seleção verde. Um ícone de marca de seleção verde confirma que o agente é capaz de se comunicar com o serviço do Azure.

Um ícone de aviso amarelo significa que o agente está tendo problemas de comunicação com o Log Analytics. Um motivo comum é que o serviço de Microsoft Monitoring Agent foi interrompido. Use o Gerenciador de controle de serviço para reiniciar o serviço.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>P: Como fazer impedir que um agente se comunique com Log Analytics?

R: em System Center Operations Manager, remova o computador da lista de computadores gerenciados do OMS. Operations Manager atualiza a configuração do agente para que ele não seja mais relatado para Log Analytics. Para agentes conectados a Log Analytics diretamente, você pode interrompê-los de se comunicar por meio do: painel de controle, configurações de & de segurança **Microsoft Monitoring Agent**.
Em **log Analytics do Azure (OMS)** , remova todos os espaços de trabalho listados.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>P: por que estou recebendo um erro quando tento mover meu espaço de trabalho de uma assinatura do Azure para outra?

R: para mover um espaço de trabalho para uma assinatura ou grupo de recursos diferente, você deve primeiro desvincular a conta de automação no espaço de trabalho. Desvincular uma conta de automação requer a remoção dessas soluções se elas estiverem instaladas no espaço de trabalho: Gerenciamento de Atualizações, Controle de Alterações ou Iniciar/Parar VMs fora do horário comercial forem removidas. Depois que essas soluções forem removidas, desvincule a conta de automação selecionando **espaços de trabalho vinculados** no painel esquerdo no recurso de conta de automação e clique em **desvincular espaço de trabalho** na faixa de faixas.
 > As soluções removidas precisam ser reinstaladas no espaço de trabalho e o link de automação para o espaço de trabalho precisa ser reestado após a movimentação.

Verifique se você tem permissão em ambas as assinaturas do Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>P: por que estou recebendo um erro quando tento atualizar um SavedSearch?

R: você precisa adicionar ' ETag ' no corpo da API ou as propriedades do modelo de Azure Resource Manager:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Dados do agente
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>P. Quantos dados posso enviar por meio do agente para Log Analytics? Há uma quantidade máxima de dados por cliente?
R. O plano gratuito define um limite diário de 500 MB por espaço de trabalho. Os planos standard e Premium não têm limite para a quantidade de dados que são carregados. Como um serviço de nuvem, o Log Analytics foi projetado para escalar verticalmente para lidar com o volume proveniente de um cliente, mesmo que ele seja de terabytes por dia.

O agente de Log Analytics foi projetado para garantir que ele tenha uma pequena superfície. O volume de dados varia de acordo com as soluções que você habilita. Você pode encontrar informações detalhadas sobre o volume de dados e ver a divisão por solução na página [uso](../../azure-monitor/platform/data-usage.md) .

Para obter mais informações, você pode ler um [blog do cliente](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) mostrando seus resultados depois de avaliar a utilização de recursos (superfície) do agente do OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>P. Quanta largura de banda de rede é usada pelo MMA (agente de gerenciamento da Microsoft) ao enviar dados para Log Analytics?

R. A largura de banda é uma função na quantidade de dados enviados. Os dados são compactados à medida que são enviados pela rede.

### <a name="q-how-much-data-is-sent-per-agent"></a>P. Qual a quantidade de dados enviada por agente?

R. A quantidade de dados enviados por agente depende de:

* As soluções que você habilitou
* O número de logs e contadores de desempenho que estão sendo coletados
* O volume de dados nos logs

O tipo de preço gratuito é uma boa maneira de integrar vários servidores e medir o volume de dados típico. O uso geral é mostrado na página [uso](../../azure-monitor/platform/data-usage.md) .

Para computadores que são capazes de executar o agente do WireData, use a seguinte consulta para ver a quantidade de dados que está sendo enviada:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Passos seguintes
* Comece a [usar o log Analytics](../../azure-monitor/overview.md) para saber mais sobre log Analytics e entrar em funcionamento em minutos.
