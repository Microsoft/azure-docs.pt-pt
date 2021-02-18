---
title: Implementar o Azure Monitor
description: Descreve os diferentes passos necessários para uma implementação completa do Azure Monitor para monitorizar todos os recursos na sua subscrição Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: c37693bc6c9ce1cc5fed6c06ecb7fe628c315176
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573587"
---
# <a name="deploy-azure-monitor"></a>Implementar o Azure Monitor
Permitir que o Azure Monitor monitorize todos os seus recursos Azure é uma combinação de configurar componentes do Monitor Azure e configurar recursos Azure para gerar dados de monitorização para o Azure Monitor recolher. Este artigo descreve os diferentes passos necessários para uma implementação completa do Azure Monitor utilizando uma configuração comum para monitorizar todos os recursos na sua subscrição Azure. Descrições básicas para cada passo são fornecidas com ligações a outra documentação para requisitos de configuração detalhada.

> [!IMPORTANT]
> As funcionalidades do Azure Monitor e a sua configuração variarão consoante os requisitos do seu negócio, equilibrado com o custo das funcionalidades ativadas. Cada passo abaixo identificará se existe um custo potencial, e deve avaliar estes custos antes de prosseguir com esse passo. Consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter detalhes completos sobre os preços.

## <a name="configuration-goals"></a>Objetivos de configuração
O objetivo de uma implementação completa do Azure Monitor é recolher todos os dados disponíveis de todos os seus recursos e aplicações na nuvem e ativar o maior número possível de funcionalidades no Azure Monitor com base nesses dados.

Os dados recolhidos pelo Azure Monitor são enviados para [métricas do Monitor Azure](essentials/data-platform-metrics.md) ou [para registos do Monitor Azure](logs/data-platform-logs.md). Cada um armazena diferentes tipos de dados e permite diferentes tipos de análise e alerta. Consulte [Compare Azure Monitor Metrics and Logs](/data-platform.md) para uma comparação entre os dois e a [visão geral dos alertas no Microsoft Azure](alerts/alerts-overview.md) para uma descrição de diferentes tipos de alerta. 

Alguns dados podem ser enviados tanto para Métricas como para Registos, de forma a alavancar o mesmo utilizando diferentes funcionalidades. Nestes casos, poderá ser necessário configurar cada um separadamente. Por exemplo, os dados métricos são automaticamente enviados pelos recursos da Azure para as Métricas, que suporta métricas exploradores e alertas métricos. Tem de criar uma definição de diagnóstico para cada recurso para enviar esses mesmos dados métricos para Registos, o que lhe permite analisar tendências de desempenho com outros dados de registo utilizando o Log Analytics. As secções abaixo identificam onde os dados são enviados e incluem cada passo necessário para enviar dados para todos os locais possíveis.

Pode ter requisitos adicionais, tais como monitorizar recursos fora do Azure e enviar dados para fora do Azure Monitor. Requisitos como estes podem ser alcançados com configuração adicional das funcionalidades descritas neste artigo. Siga os links para a documentação em cada passo para obter opções adicionais de configuração.

## <a name="collect-data-from-azure-resources"></a>Recolher dados dos recursos da Azure

> [!NOTE]
> Consulte [os recursos de Monitor Azure com o Azure Monitor](essentials/monitor-azure-resource.md) para obter um guia completo sobre a monitorização de máquinas virtuais com o Azure Monitor.

Alguma monitorização dos recursos Azure está disponível automaticamente sem necessidade de configuração, enquanto deve executar etapas de configuração para recolher dados de monitorização adicionais. A tabela a seguir ilustra os passos de configuração necessários para recolher todos os dados disponíveis dos seus recursos Azure, incluindo em que os dados de etapa são enviados para Azure Monitor Metrics e Azure Monitor Logs. As secções abaixo descrevem cada passo em mais detalhes.

[![Implementar monitorização de recursos Azure ](media/deploy/deploy-azure-resources.png)](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>Sem configuração
As seguintes funcionalidades do Azure Monitor estão ativadas sem necessidade de configuração quando cria uma subscrição Azure. Não há qualquer custo associado a esta monitorização.

[Registos de Diretório Ativo Azure](../active-directory/reports-monitoring/overview-reports.md) - Fornece histórico ao nível do inquilino de atividade de login e pista de auditoria de alterações escoradas no Diretório Ativo Azure. Consulte os relatórios de [atividades de Auditoria no portal Azure Ative Directory](../active-directory/reports-monitoring/concept-audit-logs.md) e [relatórios de atividade de Inscrição no portal Azure Ative Directory](../active-directory/reports-monitoring/concept-sign-ins.md) para obter detalhes dos registos do Diretório Ativo Azure e como vê-los no portal Azure.

[Log de atividade](essentials/platform-logs-overview.md) - Fornece informações sobre grupo de gestão e eventos de nível de subscrição que ocorreram em Azure. Os eventos são automaticamente escritos no registo de Atividade quando cria um novo recurso Azure, modifica um recurso ou executa uma atividade significativa. Pode ver eventos no portal Azure e criar alertas de registo de atividade quando determinados eventos são criados. Consulte [o registo de atividades Azure](essentials/activity-log.md) para obter mais detalhes sobre o registo de atividade e como vê-lo no portal Azure.

[Métricas da plataforma](essentials/metrics-supported.md) - Recolhidos automaticamente dos serviços Azure para [as Métricas do Monitor Azure](essentials/data-platform-metrics.md). Estes dados são frequentemente apresentados na página **Geral** do portal Azure para diferentes serviços. Veja [como começar com o Azure Metrics Explorer](essentials/metrics-getting-started.md) para obter detalhes sobre a análise das métricas da plataforma no portal Azure. 


### <a name="create-log-analytics-workspace"></a>Criar uma área de trabalho do Log Analytics
Necessita de pelo menos um espaço de trabalho log Analytics para ativar [registos do Monitor Azure](logs/data-platform-logs.md), que é necessário para recolher dados como registos a partir de recursos Azure, recolher dados do sistema operativo convidado de máquinas virtuais Azure, e para a maioria dos insights do Azure Monitor. Outros serviços como O Azure Sentinel e o Azure Security Center também usam um espaço de trabalho Log Analytics e podem partilhar o mesmo que você usa para O Azure Monitor. Pode começar com um único espaço de trabalho para suportar esta monitorização, mas consulte  [a implementação de Registos do Monitor Azure](logs/design-logs-deployment.md) para obter orientação sobre quando utilizar vários espaços de trabalho.

Não há custo para a criação de um espaço de trabalho Log Analytics, mas existe uma carga potencial uma vez que configura os dados a serem recolhidos no mesmo. Consulte [Gerir a utilização e os custos com os Registos do Monitor Azure](logs/manage-cost-storage.md) para obter mais detalhes.  

Consulte [criar um espaço de trabalho log Analytics no portal Azure](logs/quick-create-workspace.md) para criar um espaço de trabalho inicial do Log Analytics. Consulte [Gerir o acesso aos dados de registo e espaços de trabalho no Azure Monitor](logs/manage-access.md) para configurar o acesso. 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>Criar definição de diagnóstico para recolher registos de inquilinos e assinaturas
Enquanto os registos do [Azure Ative Directory](../active-directory/reports-monitoring/overview-reports.md) para o seu inquilino e o [registo de Atividade](essentials/platform-logs-overview.md) para a sua subscrição são recolhidos automaticamente, o envio para um espaço de trabalho Log Analytics permite-lhe analisar estes eventos com outros dados de registo utilizando consultas de registo no Log Analytics. Isto também permite criar alertas de consulta de log, que é a única forma de alertar nos registos do Azure Ative Directory e fornecer uma lógica mais complexa do que os alertas de registo de atividade.

Não há custo para o envio do registo de atividade para um espaço de trabalho, mas há uma taxa de ingestão de dados e retenção para registos do Azure Ative Directory. 

Consulte [registos integrados de Azure AD com registos do Monitor Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) e [Crie definições de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos](essentials/diagnostic-settings.md) para criar uma definição de diagnóstico para o seu inquilino e subscrição para enviar entradas de registo para o seu espaço de trabalho Log Analytics. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>Criar definição de diagnóstico para recolher registos de recursos e métricas de plataforma
Os recursos em Azure geram automaticamente registos de recursos que fornecem detalhes das operações [realizadas](essentials/platform-logs-overview.md) dentro do recurso. No entanto, ao contrário das métricas da plataforma, é necessário configurar registos de recursos para serem recolhidos. Crie uma definição de diagnóstico para enviá-los para um espaço de trabalho Log Analytics para combiná-los com os outros dados utilizados com registos do Monitor Azure. A mesma definição de diagnóstico pode ser usada para também enviar as métricas da plataforma para a maioria dos recursos para o mesmo espaço de trabalho, o que permite analisar dados métricos usando consultas de registo com outros dados recolhidos.

Há um custo para esta coleção, por isso consulte os [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) antes de implementar um número significativo de recursos. Consulte também [gerir o uso e os custos com os Registos do Monitor Azure](logs/manage-cost-storage.md) para obter detalhes sobre a otimização do custo da sua recolha de registos.

Consulte [Criar definição de diagnóstico para recolher registos e métricas de recursos em Azure](essentials/diagnostic-settings.md#create-in-azure-portal) para criar uma definição de diagnóstico para um recurso Azure. Uma vez que é necessário criar uma definição de diagnóstico para cada recurso Azure, consulte [o Monitor Deploy Azure em escala](deploy-scale.md) para obter detalhes sobre a utilização da política [Azure](../governance/policy/overview.md) para ter configurações criadas automaticamente cada vez que um recurso Azure é criado.

### <a name="enable-insights-and-solutions"></a>Permitir insights e soluções
Os insights e as soluções fornecem uma monitorização especializada para um determinado serviço ou solução. Os insights utilizam funcionalidades mais recentes do Azure Monitor, como livros de trabalho, pelo que deve utilizar uma visão se estiver disponível para o seu serviço. Estão automaticamente disponíveis em todas as subscrições do Azure, mas podem necessitar de alguma configuração para a funcionalidade completa. Eles normalmente usarão métricas de plataforma e registos de recursos que você previamente configurado e poderia recolher dados adicionais.

As soluções devem ser adicionadas a cada subscrição e trabalhar exclusivamente com dados dos Registos do Monitor Azure e podem recolher dados adicionais de registo.

Não há custos para soluções ou insights, mas pode ser cobrado por quaisquer dados que recolham.

Ver [O que é monitorizado pelo Azure Monitor?](monitor-reference.md) Consulte a documentação para cada um para obter qualquer informação única de configuração ou preços. 

## <a name="collect-data-from-virtual-machines"></a>Recolher dados de máquinas virtuais

> [!NOTE]
> Consulte [as máquinas virtuais monitor de Monitor Azure com o Azure Monitor](vm/monitor-vm-azure.md) para obter um guia completo sobre a monitorização de máquinas virtuais com o Azure Monitor. 

As máquinas virtuais geram dados semelhantes aos de outros recursos Azure, mas precisa de um agente para recolher dados do sistema operativo dos hóspedes. Consulte [a visão geral dos agentes do Monitor Azure](agents/agents-overview.md) para uma comparação dos agentes utilizados pelo Azure Monitor. 

[O Azure Monitor para VMs](vm/vminsights-overview.md) utiliza o agente Log Analytics e o agente Dependency para recolher dados do sistema operativo convidado de máquinas virtuais, para que possa implementar estes agentes como parte da implementação desta introspeção. Isto permite ao agente Log Analytics para outros serviços que o utilizem como o Azure Security Center.


[![Implementar Azure VM ](media/deploy/deploy-azure-vm.png)](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>Configure espaço de trabalho para monitor azure para VMs
O Azure Monitor para VMs requer um espaço de trabalho Log Analytics que será normalmente o mesmo que o criado para recolher dados de outros recursos Azure. Antes de ativar quaisquer máquinas virtuais, deve adicionar a solução necessária para o Azure Monitor para VMs no espaço de trabalho.

Consulte [o espaço de trabalho Configure Log Analytics para O Monitor Azure para VMs](vm/vminsights-configure-workspace.md) para obter detalhes sobre a configuração do seu espaço de trabalho Log Analytics para O Monitor Azure para VMs.

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>Ativar o Monitor Azure para VMs em cada máquina virtual
Uma vez configurado um espaço de trabalho, pode ativar cada máquina virtual instalando o agente Log Analytics e o agente De dependência. Existem vários métodos para instalar estes agentes, incluindo a Azure Policy, que lhe permite configurar automaticamente cada máquina virtual à medida que é criada. Os dados de desempenho e os detalhes do processo recolhidos pelo Azure Monitor para VMs são armazenados em Registos monitores Azure.

Consulte [o Enable Azure Monitor para versões gerais dos VMs](vm/vminsights-enable-overview.md) para obter opções para implantar os agentes nas suas máquinas virtuais e capacitá-los para monitorização.

### <a name="configure-workspace-to-collect-events"></a>Configure espaço de trabalho para recolher eventos
O Azure Monitor para VMs irá recolher dados de desempenho e os detalhes e dependências dos processos do sistema operativo convidado de cada máquina virtual. O agente Log Analytics também pode recolher registos do hóspede, incluindo o registo de eventos do Windows e syslog da Linux. Recupera a configuração para estes registos do espaço de trabalho Log Analytics a que está ligado. Só é necessário configurar o espaço de trabalho uma vez, e cada vez que um agente se ligar, irá descarregar quaisquer alterações de configuração. 

Consulte [fontes de dados do Agente no Azure Monitor](agents/agent-data-sources.md) para obter detalhes sobre a configuração do seu espaço de trabalho Log Analytics para recolher dados adicionais das máquinas virtuais do seu agente.

> [!NOTE]
> Também pode configurar o espaço de trabalho para recolher contadores de desempenho, mas isso provavelmente será redundante com os dados de desempenho recolhidos pelo Azure Monitor para VMs. Os dados de desempenho recolhidos pelo espaço de trabalho serão armazenados na tabela *Perf,* enquanto os dados de desempenho recolhidos pelo Azure Monitor para VMs são armazenados na tabela *InsightsMetrics.* Configure a coleção de desempenho no espaço de trabalho apenas se necessitar de contadores que ainda não foram recolhidos pelo Azure Monitor para VMs.

### <a name="diagnostic-extension-and-telegraf-agent"></a>Extensão de diagnóstico e agente telegraf
O Azure Monitor para VMs utiliza o agente Log Analytics que envia dados de desempenho para um espaço de trabalho log analytics, mas não para métricas do Monitor Azure. O envio destes dados para Métricas permite que seja analisado com o Metrics Explorer e utilizado com alertas métricos. Isto requer a extensão de diagnóstico no Windows e no agente Telegraf no Linux.

Consulte [instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)](agents/diagnostics-extension-windows-install.md) e [recolher métricas personalizadas para um Linux VM com o agente InfluxData Telegraf](essentials/collect-custom-metrics-linux-telegraf.md) para obter detalhes sobre a instalação e configuração destes agentes.


## <a name="monitor-applications"></a>Monitorizar aplicações
O Azure Monitor monitoriza as suas aplicações personalizadas utilizando [o Application Insights](app/app-insights-overview.md), que tem de configurar para cada aplicação que pretende monitorizar. O processo de configuração variará consoante o tipo de aplicação que está a ser monitorizado e o tipo de monitorização que pretende realizar. Os dados recolhidos pela Application Insights são armazenados em Métricas do Monitor Azure, Registos monitores Azure e armazenamento de blob Azure, dependendo da funcionalidade. Os dados de desempenho são armazenados tanto em Métricas do Monitor Azure como em Registos monitores Azure sem necessidade de configuração adicional.

### <a name="create-an-application-resource"></a>Criar um recurso de aplicação
Tem de criar um recurso no Application Insights para cada aplicação que vai monitorizar. Os dados de registo recolhidos pela Application Insights são armazenados em Registos monitores do Azure para uma aplicação baseada no espaço de trabalho. Os dados de registo de aplicações clássicas são armazenados separados do seu espaço de trabalho Log Analytics, conforme descrito na [estrutura de Dados.](logs/data-platform-logs.md#data-structure)

 Ao criar a aplicação, tem de selecionar se utiliza um espaço de trabalho clássico ou baseado no espaço de trabalho. Consulte [criar um recurso Application Insights](app/create-new-resource.md) para criar uma aplicação clássica. Consulte [os recursos de Insights de Aplicação baseados no Espaço de Trabalho (pré-visualização)](app/create-workspace-resource.md) para criar uma aplicação baseada no espaço de trabalho.

### <a name="configure-codeless-or-code-based-monitoring"></a>Configurar uma monitorização codificada ou baseada em códigos
Para permitir a monitorização de uma aplicação, tem de decidir se utilizará uma monitorização codificada ou baseada em códigos. O processo de configuração variará dependendo desta decisão e do tipo de aplicação que irá monitorizar.

**A monitorização sem código** é mais fácil de implementar e pode ser configurada após o desenvolvimento do seu código. Não requer nenhuma atualização do seu código. Consulte os seguintes recursos para obter detalhes sobre a monitorização ativa em função da sua aplicação.

- [Aplicações hospedadas em Azure Web Apps](app/azure-web-apps.md)
- [Aplicações Java](app/java-in-process-agent.md)
- [ASP.NET aplicações hospedadas no IIS em Azure VM ou conjunto de escala de máquina virtual Azure](app/azure-vm-vmss-apps.md)
- [ASP.NET candidaturas hospedadas no IIS no local VM](app/monitor-performance-live-website-now.md)


**A monitorização baseada em códigos** é mais personalizável e recolhe telemetria adicional, mas requer a adição de uma dependência ao seu código nos pacotes SDK NuGet do Application Insights. Consulte os seguintes recursos para obter detalhes sobre a monitorização ativa, dependendo da sua aplicação.

- [Aplicações ASP.NET](app/asp-net.md)
- [Aplicações ASP.NET Core](app/asp-net-core.md)
- [Aplicações de consola .NET](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Outras plataformas](app/platforms.md)

### <a name="configure-availability-testing"></a>Configurar testes de disponibilidade
Os testes de disponibilidade em Application Insights são testes recorrentes que monitorizam a disponibilidade e capacidade de resposta da sua aplicação a intervalos regulares de pontos em todo o mundo. Pode criar gratuitamente um teste de ping simples ou criar uma sequência de pedidos web para simular transações de utilizadores que têm custos associados. 

Consulte [o Monitor a disponibilidade de qualquer website](app/monitor-web-app-availability.md) para resumo dos diferentes tipos de testes e detalhes sobre a sua criação.

### <a name="configure-profiler"></a>Perfil de configuração
Profiler in Application Insights fornece traços de desempenho para aplicações .NET. Ajuda-o a identificar o caminho do código "quente" que demora mais tempo quando está a lidar com um determinado pedido web. O processo de configuração do perfil varia consoante o tipo de aplicação. 

Consulte [as aplicações de produção de perfis em Azure com Insights de Aplicação](app/profiler-overview.md) para obter detalhes sobre a configuração do Profiler.

### <a name="configure-snapshot-debugger"></a>Configurar Snapshot Debugger
O Snapshot Debugger in Application Insights monitoriza a telemetria de exceção da sua aplicação .NET e recolhe instantâneos nas suas exceções de lançamento superior para que tenha a informação de que necessita para diagnosticar problemas na produção. O processo de configuração do Snapshot Debugger varia consoante o tipo de aplicação. 

Consulte [as fotos de Debug sobre exceções em aplicações .NET](app/snapshot-debugger.md) para obter detalhes sobre a configuração do Snapshot Debugger.


## <a name="visualize-data"></a>Visualizar os dados
Insights e soluções incluirão os seus próprios livros e pontos de vista para analisar os seus dados. Além destes, pode criar as suas [próprias visualizações,](visualizations.md) incluindo livros de dados para dados e dashboards do Azure Monitor para combinar dados do Azure Monitor com dados de outros serviços em Azure.


### <a name="create-workbooks"></a>Criar livros
[Os livros de trabalho](visualize/workbooks-overview.md) no Azure Monitor permitem-lhe criar relatórios visuais ricos no portal Azure. Pode combinar diferentes conjuntos de dados a partir de Métricas do Monitor Azure e registos do Monitor Azure para criar experiências interativas unificadas. Pode aceder a uma galeria de livros no **separador Livros** do menu Azure Monitor. 

Consulte [os livros de trabalho do Monitor Azure](visualize/workbooks-overview.md) para obter mais detalhes sobre a criação de livros de trabalho personalizados.

### <a name="create-dashboards"></a>Criar dashboards
[Os dashboards Azure](../azure-portal/azure-portal-dashboards.md) são a principal tecnologia de dashboarding para a Azure e permitem combinar dados do Azure Monitor com dados de outros serviços para fornecer uma única vidraça de vidro sobre a sua infraestrutura Azure. Consulte [criar e partilhar os dashboards de dados do Log Analytics](visualize/tutorial-logs-dashboards.md) para obter detalhes sobre a criação de um dashboard que inclua dados de Registos do Monitor Azure. 

Consulte [criar dashboards KPI personalizados utilizando insights de aplicação Azure](app/tutorial-app-dashboards.md) para obter detalhes sobre a criação de um dashboard que inclua dados de Insights de Aplicação. 

## <a name="alerts"></a>Alertas
Os alertas no Azure Monitor notificam-no proativamente de dados ou padrões importantes identificados nos seus dados de monitorização. Algumas introspeções gerarão alertas sem configuração. Para outros cenários, é necessário criar [regras](alerts/alerts-overview.md) de alerta que incluam os dados a analisar e os critérios para quando gerar um alerta, e grupos de ação que definem a ação a tomar quando um alerta é gerado. 


### <a name="create-action-groups"></a>Criar grupos de ações
[Os grupos](alerts/action-groups.md) de ação são uma coleção de preferências de notificação utilizadas pelas regras de alerta para determinar a ação a executar quando um alerta é desencadeado. Exemplos de ações incluem o envio de um correio ou texto, ligar para um webhook ou enviar dados para uma ferramenta ITSM. Cada regra de alerta requer pelo menos um grupo de ação, e um único grupo de ação pode ser usado por várias regras de alerta.

Consulte [criar e gerir grupos de ação no portal Azure](alerts/action-groups.md) para obter detalhes sobre a criação de um grupo de ação e uma descrição das diferentes ações que pode incluir.


### <a name="create-alert-rules"></a>Criar regras de alertas
Existem vários tipos de regras de alerta definidas pelo tipo de dados que utilizam. Cada um tem capacidades diferentes, e um custo diferente. A estratégia básica que deve seguir é usar o tipo de regra de alerta com o custo mais baixo que fornece a lógica que você precisa.

- [Regras de registo de atividades](alerts/activity-log-alerts.md). Cria um alerta em resposta a um novo evento de registo de atividade que corresponde às condições especificadas. Não há custos para estes alertas, por isso devem ser a sua primeira escolha. Consulte [Criar, visualizar e gerir alertas de registo de atividade utilizando o Azure Monitor](alerts/alerts-activity-log.md) para obter detalhes sobre a criação de um alerta de registo de atividade.
- [Regras de alerta métricos](alerts/alerts-metric-overview.md). Cria um alerta em resposta a um ou mais valores métricos que excedam um limiar. Os alertas métricos são indicativo que o alerta fechará automaticamente quando o valor descer abaixo do limiar, e só enviará notificações quando o estado mudar. Há um custo para alertas métricos, mas este é significativamente menos do que alertas de log. Consulte [Criar, visualizar e gerir alertas métricos utilizando o Azure Monitor](alerts/alerts-metric.md) para obter detalhes sobre a criação de um alerta métrico.
- [Regras de alerta de registo](alerts/alerts-unified-log.md). Cria um alerta quando os resultados de uma consulta de agenda correspondem a critérios especificados. São as regras de alerta mais caras, mas permitem os critérios mais complexos. Consulte [Criar, visualizar e gerir alertas de registo utilizando o Azure Monitor](alerts/alerts-log.md) para obter detalhes sobre a criação de um alerta de consulta de registo.
- [Os alertas](app/monitor-web-app-availability.md) de aplicação permitem-lhe realizar testes proactivos de desempenho e disponibilidade da sua aplicação web. Você pode realizar um teste de ping simples sem custos, mas há um custo para testes mais complexos. Consulte [o Monitor a disponibilidade de qualquer website](app/monitor-web-app-availability.md) para uma descrição dos diferentes testes e detalhes sobre a sua criação.


## <a name="next-steps"></a>Passos seguintes

- Consulte [o Monitor Azure em escala utilizando a política Azure](deploy-scale.md).