---
title: Azure Monitor para clientes gestores de operações existentes
description: Orientação para os utilizadores existentes do Gestor de Operações para a monitorização de transição de determinadas cargas de trabalho para o Azure Monitor como parte de uma transição para a nuvem.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: b1262533c3398a774b85e4143289a9b7c342aeab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593576"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor para clientes gestores de operações existentes
Este artigo fornece orientações para os clientes que atualmente utilizam [o Gestor de Operações do System Center](/system-center/scom/welcome) e estão a planear uma transição para o [Azure Monitor](overview.md) à medida que migram aplicações de negócios e outros recursos para o Azure. Assume que o seu objetivo final é uma transição completa para a nuvem, substituindo o máximo de funcionalidades do Gestor de Operações possível pelo Azure Monitor, sem comprometer os requisitos operacionais do seu negócio e de TI. 

As recomendações específicas feitas neste artigo serão alteradas à medida que o Monitor e o Gestor de Operações do Azure acrescentam funcionalidades. No entanto, a estratégia fundamental continuará a ser coerente.

> [!IMPORTANT]
> Existe um custo para implementar várias funcionalidades do Azure Monitor descritas aqui, por isso deve avaliar o seu valor antes de se implantar em todo o seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já utilize [o Gestor de Operações](/system-center/scom) e pelo menos tenha um conhecimento básico do [Azure Monitor.](overview.md) Para uma comparação completa entre os dois, consulte o [guia de monitorização da Cloud: Visão geral das plataformas de monitorização](/azure/cloud-adoption-framework/manage/monitor/platform-overview). Este artigo detalha diferenças específicas entre os dois para ajudá-lo a entender algumas das recomendações feitas aqui. 


## <a name="general-strategy"></a>Estratégia geral
Não existem ferramentas de migração para converter ativos de Gestor de Operações para Azure Monitor, uma vez que as plataformas são fundamentalmente diferentes. A sua migração constituirá, em vez disso, uma [implementação padrão do Azure Monitor](deploy.md) enquanto continua a utilizar o Gestor de Operações. À medida que personaliza o Azure Monitor para satisfazer os seus requisitos para diferentes aplicações e componentes e à medida que ganha mais funcionalidades, poderá começar a retirar diferentes pacotes de gestão e agentes em Gestor de Operações.

A estratégia geral recomendada neste artigo é a mesma que no [Guia de Monitorização](/azure/cloud-adoption-framework/manage/monitor/)da Nuvem, que recomenda uma estratégia [híbrida de monitorização da nuvem](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) que lhe permite fazer uma transição gradual para a nuvem. Apesar de algumas funcionalidades se poderem sobrepor, esta estratégia irá permitir-lhe manter os seus processos de negócio existentes à medida que se torna mais familiarizado com a nova plataforma. Afaste-se apenas da funcionalidade do Gestor de Operações, pois pode substituí-la pelo Azure Monitor. A utilização de várias ferramentas de monitorização adiciona complexidade, mas permite-lhe tirar partido da capacidade do Azure Monitor de monitorizar as cargas de trabalho na nuvem da próxima geração, mantendo ao mesmo tempo a capacidade do Gestor de Operações de monitorizar o software do servidor e os componentes de infraestruturas que podem estar no local ou noutras nuvens. 


## <a name="components-to-monitor"></a>Componentes para monitorizar
Ajuda a categorizar os diferentes tipos de cargas de trabalho que precisa de monitorizar para determinar uma estratégia de monitorização distinta para cada um. [Guia de monitorização em nuvem: Formular uma estratégia](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) de monitorização fornece uma desagregação detalhada das diferentes camadas do seu ambiente que precisam de ser monitorizadas à medida que progride desde aplicações empresariais antigas a aplicações modernas na nuvem.

Antes da nuvem, usou o Gestor de Operações para monitorizar todas as camadas. Ao iniciar a sua transição com a Infraestrutura como Serviço (IaaS), continua a utilizar o Gestor de Operações para as suas máquinas virtuais, mas começa a utilizar o Azure Monitor para os seus recursos na nuvem. À medida que avança para aplicações modernas usando a Plataforma como Um Serviço (PaaS), pode focar-se mais no Azure Monitor e começar a retirar a funcionalidade de Gestor de Operações.


![Modelos de nuvem](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Estas camadas podem ser simplificadas nas seguintes categorias, que são ainda descritas no resto deste artigo. Embora cada carga de trabalho de monitorização no seu ambiente possa não se encaixar perfeitamente numa destas categorias, cada uma deve estar suficientemente próxima de uma determinada categoria para que as recomendações gerais se apliquem.

**Aplicações de negócios.** Aplicações que fornecem funcionalidades específicas ao seu negócio. Podem ser internos ou externos e são muitas vezes desenvolvidos internamente usando código personalizado. As suas aplicações antigas serão normalmente hospedadas em máquinas virtuais ou físicas que executam o Windows ou o Linux, enquanto as suas aplicações mais recentes serão baseadas em serviços de aplicações em Azure, como aplicações Azure Web e Azure Functions.

**Serviços Azure.** Recursos em Azure que suportam as suas aplicações empresariais que migraram para a nuvem. Isto inclui serviços como Azure Storage, Azure SQL e Azure IoT. Isto também inclui máquinas virtuais Azure uma vez que são monitorizadas como outros serviços Azure, mas as aplicações e software em execução no sistema operativo convidado dessas máquinas virtuais requerem mais monitorização além do hospedeiro.

**Software de servidor.** Software em execução em máquinas virtuais e físicas que suportam aplicações empresariais ou aplicações embaladas que fornecem funcionalidade geral ao seu negócio. Exemplos incluem Internet Information Server (IIS), SQL Server, Exchange e SharePoint. Isto também inclui o sistema operativo Windows ou Linux nas suas máquinas virtuais e físicas.

**Infraestruturas locais.** Componentes específicos do seu ambiente no local que requerem monitorização. Isto inclui recursos como servidores físicos, armazenamento e componentes de rede. Estes são os componentes que são virtualizados quando se move para a nuvem.

## <a name="sample-walkthrough"></a>Instruções de exemplo
Segue-se uma hipotética passagem de uma migração de Gerente de Operações para Monitor Azure. Isto não se destina a representar a complexidade total de uma migração real, mas fornece, pelo menos, os passos básicos e a sequência. As secções abaixo descrevem cada um destes passos com mais detalhes.

O seu ambiente antes de mover quaisquer componentes para o Azure baseia-se em máquinas virtuais e físicas localizadas no local ou com um prestador de serviços gerido. Conta com o Gestor de Operações para monitorizar aplicações de negócios, software de servidores e outros componentes de infraestrutura no seu ambiente, tais como servidores físicos e redes. Utiliza pacotes de gestão padrão para software de servidores como IIS, SQL Server e vários softwares de fornecedores, e afina esses pacotes de gestão para os seus requisitos específicos. Cria pacotes de gestão personalizados para as suas aplicações empresariais e outros componentes que não podem ser monitorizados com pacotes de gestão existentes e configurar o Gestor de Operações para apoiar os seus processos de negócio.

A sua migração para Azure começa com o IaaS, movendo máquinas virtuais que suportam aplicações empresariais para o Azure. Os requisitos de monitorização destas aplicações e do software do servidor de que dependem não mudam, e continua a utilizar o Gestor de Operações nestes servidores com os seus pacotes de gestão existentes. 

O Azure Monitor está ativado para os seus serviços Azure assim que criar uma subscrição do Azure. Recolhe automaticamente as métricas da plataforma e o registo de Atividade, e configura os registos de recursos a serem recolhidos para que possa analisar interativamente toda a telemetria disponível utilizando consultas de registo. Você permite que o Azure Monitor para VMs nas suas máquinas virtuais analise os dados de monitorização em todo o seu ambiente em conjunto e descubra relações entre máquinas e processos. Estenda a sua utilização do Azure Monitor às suas máquinas físicas e virtuais no local, permitindo que os servidores ativados do Azure Arc sejam ativados. 

Você ativa o Application Insights para cada uma das suas aplicações de negócio. Identifica os diferentes componentes de cada aplicação, começa a recolher dados de utilização e desempenho, e identifica quaisquer erros que ocorram no código. Cria testes de disponibilidade para testar proativamente as suas aplicações externas e alertá-lo para quaisquer problemas de desempenho ou disponibilidade. Embora o Application Insights lhe dê funcionalidades poderosas que não tem no Gestor de Operações, continua a contar com pacotes de gestão personalizados que desenvolveu para as suas aplicações empresariais, uma vez que incluem cenários de monitorização ainda não abrangidos pelo Azure Monitor. 

À medida que se familiariza com o Azure Monitor, começa a criar regras de alerta que são capazes de substituir algumas funcionalidades do pacote de gestão e começam a evoluir os seus processos de negócio para utilizar a nova plataforma de monitorização. Isto permite-lhe começar a remover máquinas e pacotes de gestão do grupo de gestão de Gestores de Operações. Continua a utilizar pacotes de gestão para software de servidor crítico e infraestruturas no local, mas continua a observar novas funcionalidades no Azure Monitor que lhe permitirão retirar funcionalidades adicionais.

## <a name="monitor-azure-services"></a>Monitor Azure serviços
Os serviços Azure realmente requerem a Azure Monitor para recolher telemetria, e está ativado no momento em que você cria uma subscrição Azure. O [registo de Atividade](essentials/activity-log.md) é recolhido automaticamente para a subscrição, e as [métricas](essentials/data-platform-metrics.md) da plataforma são automaticamente recolhidas a partir de quaisquer recursos Azure que crie. Pode começar imediatamente a utilizar [o explorador de métricas](essentials/metrics-getting-started.md), que é semelhante às vistas de desempenho na consola Operações, mas fornece análises interativas e [agregações avançadas de dados.](essentials/metrics-charts.md) [Crie um alerta métrico](alerts/alerts-metric.md) para ser notificado quando um valor cruza um limiar ou [adicione um gráfico a um painel Azure](essentials/metrics-charts.md#pinning-to-dashboards) para visibilidade.

[![Explorador de Métricas](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Crie uma definição de diagnóstico](essentials/diagnostic-settings.md) para cada recurso Azure para enviar métricas e [registos](essentials/resource-logs.md)de recursos , que fornecem detalhes sobre o funcionamento interno de cada recurso, para um espaço de trabalho Log Analytics. Isto dá-lhe toda a telemetria disponível para os seus recursos e permite-lhe utilizar [o Log Analytics](logs/log-analytics-overview.md) para analisar interativamente dados de registo e desempenho utilizando uma linguagem de consulta avançada que não tem equivalente no Gestor de Operações. Também pode criar [alertas de consulta de registo,](alerts/alerts-log-query.md)que podem usar lógica complexa para determinar as condições de alerta e correlacionar dados em vários recursos.

[![Registos Analytics](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

[Os insights](monitor-reference.md) no Azure Monitor são semelhantes aos pacotes de gestão, na medida em que fornecem monitorização única para um determinado serviço Azure. Atualmente estão disponíveis insights para vários serviços, incluindo networking, armazenamento e contentores, e outros estão continuamente a ser adicionados.

[![Exemplo de insight](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Os insights são [baseados](visualize/workbooks-overview.md) em livros de trabalho no Azure Monitor, que combinam métricas e consultas de registo em relatórios interativos ricos. Crie os seus próprios livros para combinar dados de vários serviços semelhantes aos de como pode criar visualizações e relatórios personalizados na consola Operações.

### <a name="azure-management-pack"></a>Pacote de gestão Azure
O [pacote de gestão Azure](https://www.microsoft.com/download/details.aspx?id=50013) permite ao Gestor de Operações descobrir os recursos da Azure e monitorizar a sua saúde com base num determinado conjunto de cenários de monitorização. Este pacote de gestão requer que execute configurações adicionais para cada recurso em Azure, mas pode ser útil fornecer alguma visibilidade dos seus recursos Azure na Consola de Operações até que evolua os seus processos de negócio para se concentrar no Azure Monitor.

[![Pacote de gestão Azure](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 Pode optar por utilizar o pacote Azure Management se quiser visibilidade para certos recursos Azure na consola Operações e integrar alguns alertas básicos com os processos existentes. Na verdade, utiliza dados recolhidos pelo Azure Monitor. No entanto, deverá procurar o Azure Monitor para uma monitorização completa a longo prazo dos seus recursos Azure. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Monitorize o software do servidor e a infraestrutura local
Quando se deslocam as máquinas para a nuvem, os requisitos de monitorização do seu software não mudam. Já não é necessário monitorizar os seus componentes físicos uma vez que são virtualizados, mas o sistema operativo dos hóspedes e as suas cargas de trabalho têm os mesmos requisitos, independentemente do seu ambiente.

[O Azure Monitor para VMs](vm/vminsights-overview.md) é a principal característica do Azure Monitor para monitorizar máquinas virtuais e o seu sistema operativo convidado e cargas de trabalho. À semelhança do Gestor de Operações, o Azure Monitor para VMs utiliza um agente para recolher dados do sistema operativo de hóspedes de máquinas virtuais. Estes são os mesmos dados de desempenho e eventos normalmente utilizados pelos pacotes de gestão para análise e alerta. Não existem regras pré-existentes para identificar e alertar sobre problemas para as aplicações empresariais e software de servidores que estão a decorrer nessas máquinas. Deve criar as suas próprias regras de alerta para ser notificado proactivamente de quaisquer problemas detetados.

[![Monitor Azure para desempenho em VMs](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

O Azure Monitor também não mede a saúde de diferentes aplicações e serviços em execução numa máquina virtual. Os alertas métricos podem resolver-se automaticamente quando um valor cai abaixo de um limiar, mas o Azure Monitor não tem atualmente a capacidade de definir critérios de saúde para aplicações e serviços em execução na máquina, nem fornece rollup de saúde para agrupar a saúde dos componentes relacionados.

> [!NOTE]
> Uma nova [funcionalidade de saúde para o Azure Monitor para VMs](vm/vminsights-health-overview.md) está agora em pré-visualização pública e alerta com base no estado de saúde de um conjunto de métricas de desempenho. Isto é inicialmente limitado a um conjunto específico de contadores de desempenho relacionados com o sistema operativo do hóspede e não aplicações ou outras cargas de trabalho em execução na máquina virtual.
> 
> [![Monitor Azure para saúde de hóspedes VMs](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

A monitorização do software nas suas máquinas num ambiente híbrido utilizará normalmente uma combinação de Monitor Azure para VMs e Gestor de Operações, dependendo dos requisitos de cada máquina e da sua maturidade desenvolvendo processos operacionais em torno do Azure Monitor. O Agente de Gestão da Microsoft (designado como agente Log Analytics no Azure Monitor) é utilizado por ambas as plataformas para que uma única máquina possa ser monitorizada simultaneamente por ambas.

> [!NOTE]
> No futuro, o Azure Monitor para VMs irá transitar para o [agente Azure Monitor](agents/azure-monitor-agent-overview.md), que está atualmente em pré-visualização pública. Será compatível com o Microsoft Monitoring Agent para que a mesma máquina virtual continue a ser monitorizada por ambas as plataformas.

Continue a utilizar o Gestor de Operações para funcionalidades que ainda não podem ser fornecidas pelo Azure Monitor. Isto inclui pacotes de gestão para software de servidor crítico como IIS, SQL Server ou Exchange. Você também pode ter pacotes de gestão personalizados desenvolvidos para infraestruturas no local que não podem ser alcançados com o Azure Monitor. Continue também a utilizar o Gestor de Operações se estiver fortemente integrado nos seus processos operacionais até que possa transitar para modernizar as suas operações de serviço onde o Azure Monitor e outros serviços Azure podem aumentar ou substituir. 

Utilize o Azure Monitor fo VMs para melhorar a sua monitorização atual, mesmo que não substitua imediatamente o Gestor de Operações. Exemplos de características únicas do Azure Monitor incluem:

- Descubra e monitorize as relações entre máquinas virtuais e as suas dependências externas.
- Ver dados de desempenho agregados em várias máquinas virtuais em gráficos interativos e livros de trabalho.
- Utilize [consultas de registo](logs/log-query-overview.md) para analisar interativamente a telemetria das suas máquinas virtuais com dados dos seus outros recursos Azure.
- Crie [regras de alerta de registo baseadas](alerts/alerts-log-query.md) em lógicas complexas em várias máquinas virtuais.

[![Monitor Azure para mapa de VMs](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Além das máquinas virtuais Azure, o Azure Monitor para VMs pode monitorizar as máquinas no local e noutras nuvens utilizando [servidores ativados pelo Arco Azure](../azure-arc/servers/overview.md). Os servidores ativados pelo Arco permitem-lhe gerir as suas máquinas Windows e Linux hospedadas fora do Azure, na sua rede corporativa ou noutro fornecedor de nuvem consistente com a forma como gere as máquinas virtuais nativas do Azure.



## <a name="monitor-business-applications"></a>Monitorizar aplicações empresariais
Normalmente, necessita de pacotes de gestão personalizados para monitorizar as suas aplicações de negócio com o Gestor de Operações, aproveitando os agentes instalados em cada máquina virtual. O Application Insights in Azure Monitor monitoriza aplicações baseadas na Web, quer estejam em Azure, outras nuvens ou no local, para que possa ser usada para todas as suas aplicações, quer tenham sido migradas ou não para Azure.

Se a sua monitorização de uma aplicação de negócio se limitar à funcionalidade fornecida pelo modelo de desempenho da [aplicação .NET]() em Gestor de Operações, então é provável que migrar para o Application Insights sem perda de funcionalidade. De facto, os Insights de Aplicação incluirão um número significativo de funcionalidades adicionais, incluindo as seguintes:

- Descubra e monitorize automaticamente os componentes da aplicação.
- Recolher dados detalhados de utilização e desempenho da aplicação, tais como tempo de resposta, taxas de insucesso e taxas de pedido.
- Recolher dados do navegador, tais como visualizações de página e desempenho de carga.
- Detete exceções e berbule vestígios de pilha e pedidos relacionados.
- Realize análises avançadas utilizando funcionalidades como [rastreio distribuído](app/distributed-tracing.md) e [deteção inteligente.](app/proactive-diagnostics.md)
- Use [o explorador de métricas](essentials/metrics-getting-started.md) para analisar interativamente dados de desempenho.
- Utilize [consultas de registo](logs/log-query-overview.md) para analisar interativamente a telemetria recolhida juntamente com os dados recolhidos para os serviços Azure e o Azure Monitor para VMs.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Existem certos cenários, no entanto, onde poderá ter de continuar a utilizar o Gestor de Operações, para além do Application Insights, até conseguir obter a funcionalidade necessária. Exemplos em que poderá ter de continuar com o Gestor de Operações incluem:

-  [Os testes de disponibilidade](app/monitor-web-app-availability.md), que permitem monitorizar e alertar sobre a disponibilidade e capacidade de resposta das suas aplicações requerem pedidos de entrada a partir dos endereços IP dos agentes de teste web. Se a sua política não permitir esse acesso, poderá ter de continuar a utilizar [monitores de disponibilidade de aplicações web](/system-center/scom/web-application-availability-monitoring-template) no Gestor de Operações.
- No Gestor de Operações pode definir qualquer intervalo de votação para testes de disponibilidade, com muitos clientes a verificar a cada 60-120 segundos. A Application Insights tem um intervalo mínimo de votação de 5 minutos, o que pode ser demasiado longo para alguns clientes.
- Uma quantidade significativa de monitorização em Gestor de Operações é realizada através da recolha de eventos gerados por aplicações e executando scripts no agente local. Estas não são opções padrão no Application Insights, por isso pode exigir trabalho personalizado para atingir os requisitos do seu negócio. Isto pode incluir regras de alerta personalizadas usando dados de eventos armazenados num espaço de trabalho do Log Analytics e scripts lançados em um hóspede de máquinas virtuais usando [o trabalhador de runbook híbrido](../automation/automation-hybrid-runbook-worker.md).
- Dependendo do idioma em que a sua aplicação está escrita, pode estar limitado na [instrumentação que pode utilizar com o Application Insights](app/platforms.md).

Seguindo a estratégia básica nas outras secções deste guia, continue a utilizar o Gestor de Operações para as suas aplicações empresariais, mas aproveite as funcionalidades adicionais fornecidas pela Application Insights. Como é capaz de substituir a funcionalidade crítica pelo Azure Monitor, pode começar a retirar os seus pacotes de gestão personalizados.


## <a name="next-steps"></a>Passos seguintes

- Consulte o [Guia de Monitorização](/azure/cloud-adoption-framework/manage/monitor/) da Nuvem para uma comparação detalhada do Azure Monitor e Do System Center Operations Manager e mais detalhes sobre a conceção e implementação de um ambiente de monitorização híbrido.
- Leia mais sobre [a monitorização dos recursos do Azure no Azure Monitor](essentials/monitor-azure-resource.md).
- Leia mais sobre [a monitorização das máquinas virtuais Azure no Azure Monitor](vm/monitor-vm-azure.md).
- Leia mais sobre [O Monitor Azure para VMs](vm/vminsights-overview.md).
- Leia mais sobre [Informações sobre Aplicações.](app/app-insights-overview.md)