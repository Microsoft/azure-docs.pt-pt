---
title: Monitorização contínua com o Monitor Azure [ Monitor Azure] Microsoft Docs
description: Descreve passos específicos para a utilização do Monitor Azure para permitir a monitorização contínua em todos os seus fluxos de trabalho.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 6df27dde997bd34e86b1bb340817648bfe68f2c4
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797605"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Monitorização contínua com o Monitor Azure

A monitorização contínua refere-se ao processo e à tecnologia necessários para incorporar a monitorização em cada fase dos seus Ciclos de vida de operações de DevOps e TI. Ajuda a garantir continuamente a saúde, desempenho e fiabilidade da sua aplicação e infraestrutura à medida que passa do desenvolvimento para a produção. A monitorização contínua baseia-se nos conceitos de Integração Contínua e Implantação Contínua (CI/CD) que o ajudam a desenvolver e fornecer software de forma mais rápida e fiável para fornecer valor contínuo aos seus utilizadores.

[O Azure Monitor](overview.md) é a solução de monitorização unificada em Azure que proporciona observabilidade em pilhas completas através de aplicações e infraestruturas na nuvem e no local. Funciona perfeitamente com [o Visual Studio e visual Studio Code](https://visualstudio.microsoft.com/) durante o desenvolvimento e teste e integra-se com a [Azure DevOps](/azure/devops/user-guide/index) para gestão de lançamentos e gestão de itens de trabalho durante a implantação e operações. Até se integra em todas as ferramentas ITSM e SIEM à sua escolha para ajudar a rastrear problemas e incidentes dentro dos processos de TI existentes.

Este artigo descreve passos específicos para a utilização do Monitor Azure para permitir uma monitorização contínua em todos os seus fluxos de trabalho. Inclui links para outra documentação que fornece detalhes sobre a implementação de diferentes funcionalidades.


## <a name="enable-monitoring-for-all-your-applications"></a>Ativar a monitorização de todas as suas aplicações
Para obter a observabilidade em todo o seu ambiente, precisa de permitir a monitorização de todas as suas aplicações e serviços web. Isto permitirá visualizar facilmente transações e ligações de ponta a ponta em todos os componentes.

- Os [Projetos Azure DevOps](../devops-project/overview.md) oferecem-lhe uma experiência simplificada com o seu código existente e repositório Git, ou escolha entre uma das aplicações de amostra para criar um pipeline de Integração Contínua (CI) e Entrega Contínua (CD) para o Azure.
- [A monitorização contínua no seu pipeline de libertação DevOps](../azure-monitor/app/continuous-monitoring.md) permite-lhe portalar ou reverter a sua implementação com base em dados de monitorização.
- [O Status Monitor](../azure-monitor/app/monitor-performance-live-website-now.md) permite-lhe instrumentar uma aplicação live .NET no Windows com insights de aplicação Azure, sem ter de modificar ou reimplementar o seu código.
- Se tiver acesso ao código da sua aplicação, então ative a monitorização completa com os Insights de [Aplicação,](../azure-monitor/app/app-insights-overview.md) instalando o Azure Monitor Application Insights SDK para [.NET,](../azure-monitor/learn/quick-monitor-portal.md) [Java,](../azure-monitor/app/java-get-started.md) [Node.js,](../azure-monitor/learn/nodejs-quick-start.md)ou [quaisquer outros idiomas de programação.](../azure-monitor/app/platforms.md) Isto permite especificar eventos, métricas ou visualizações de página personalizadas que sejam relevantes para a sua aplicação e para o seu negócio.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Ativar a monitorização de toda a sua infraestrutura
As aplicações são tão fiáveis como as suas infraestruturas subjacentes. Ter monitorização ativada em toda a sua infraestrutura irá ajudá-lo a alcançar a observabilidade total e facilitar a descoberta de uma potencial causa raiz quando algo falha. O Azure Monitor ajuda-o a acompanhar a saúde e o desempenho de toda a sua infraestrutura híbrida, incluindo recursos como VMs, contentores, armazenamento e rede.

- Obtém automaticamente métricas de [plataforma, registos](platform/data-sources.md) de atividade e registos de diagnóstico da maioria dos seus recursos Azure sem configuração.
- Ative uma monitorização mais profunda para VMs com [Monitor Azure para VMs](insights/vminsights-overview.md).
-  Permitir uma monitorização mais profunda dos clusters AKS com [o Monitor Azure para os contentores](insights/container-insights-overview.md).
- Adicione soluções de [monitorização](insights/solutions-inventory.md) para diferentes aplicações e serviços no seu ambiente.


[A infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code) é a gestão da infraestrutura num modelo descritivo, utilizando a mesma versão que as equipas de DevOps usam para código fonte. Adiciona fiabilidade e escalabilidade ao seu ambiente e permite-lhe alavancar processos semelhantes que costumavam gerir as suas aplicações.

-  Utilize [os modelos do Gestor](platform/template-workspace-configuration.md) de Recursos para permitir a monitorização e configuração de alertas sobre um grande conjunto de recursos.
- Use a [Política Azure](../governance/policy/overview.md) para impor regras diferentes sobre os seus recursos. Isto garante que esses recursos se mantenham em conformidade com os seus padrões corporativos e acordos de nível de serviço. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Combine recursos em grupos de recursos azure
Uma aplicação típica no Azure hoje inclui vários recursos, como VMs e App Services ou microserviços hospedados em Serviços cloud, clusters AKS ou Service Fabric. Estas aplicações utilizam frequentemente dependências como Hubs de Eventos, Armazenamento, SQL e Ônibus de Serviço.

- Combine recursos em Grupos de Recursos Azure para obter total visibilidade em todos os seus recursos que compõem as suas diferentes aplicações. [O Monitor Azure para Grupos](../azure-monitor/insights/resource-group-insights.md) de Recursos fornece uma forma simples de acompanhar a saúde e o desempenho de toda a sua aplicação de pilha completa e permite perfurar os respetivos componentes para quaisquer investigações ou depuração.

## <a name="ensure-quality-through-continuous-deployment"></a>Garantir a qualidade através da Implantação Contínua
A Integração Contínua / Implantação Contínua permite-lhe integrar e implementar automaticamente alterações de código na sua aplicação com base nos resultados dos testes automatizados. Simplifica o processo de implantação e assegura a qualidade de quaisquer alterações antes de entrarem em produção.


- Utilize [os Gasodutos Azure](/azure/devops/pipelines) para implementar a Implantação Contínua e automatizar todo o seu processo a partir do código compromete-se com a produção com base nos seus testes CI/CD.
- Utilize [portões](/azure/devops/pipelines/release/approvals/gates) de qualidade para integrar a monitorização na pré-implantação ou pós-implantação. Isto garante que está a cumprir as principais métricas de saúde/desempenho (KPIs) à medida que as suas aplicações passam de dev para produção e quaisquer diferenças no ambiente ou escala da infraestrutura não estão a afetar negativamente os seus KPIs.
- [Mantenha casos de monitorização separados](../azure-monitor/app/separate-resources.md) entre os seus diferentes ambientes de implantação, tais como Dev, Test, Canary e Prod. Isto garante que os dados recolhidos são relevantes em todas as aplicações e infraestruturas associadas. Se precisar de correlacionar dados em ambientes, pode utilizar [gráficos de vários recursos no Metrics Explorer](../azure-monitor/platform/metrics-charts.md) ou criar [consultas de recursos cruzados no Monitor Azure](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Criar alertas atoráveis com ações
Um aspeto crítico da monitorização é notificar proactivamente os administradores de quaisquer problemas atuais e previsíveis. 

- Crie [alertas no Monitor Azure](../azure-monitor/platform/alerts-overview.md) com base em registos e métricas para identificar estados de falha previsíveis. Deve ter o objetivo de tornar todos os alertas atoveis, o que significa que representam condições críticas reais e procurar reduzir falsos positivos. Utilize [limiares dinâmicos](platform/alerts-dynamic-thresholds.md) para calcular automaticamente as linhas de base em dados métricos em vez de definir os seus próprios limiares estáticos. 
- Defina ações para alertas para usar os meios mais eficazes de notificar os seus administradores. As ações disponíveis [para notificação](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) são SMS, e-mails, notificações push ou chamadas de voz.
- Utilize ações mais avançadas para [se conectar à sua ferramenta ITSM](platform/itsmc-overview.md) ou a outros sistemas de gestão de alerta através de [webhooks](platform/activity-log-alerts-webhook.md).
- Remediar situações identificadas em alertas também com os livros de [execução da Azure Automation](../automation/automation-webhooks.md) ou [aplicações lógicas](/connectors/custom-connectors/create-webhook-trigger) que podem ser lançadas a partir de um alerta usando webhooks. 
- Utilize [a autoscalcificação](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) para aumentar e diminuir dinamicamente os seus recursos computacionais com base em métricas recolhidas.

## <a name="prepare-dashboards-and-workbooks"></a>Preparar dashboards e livros
Garantir que o seu desenvolvimento e operações têm acesso à mesma telemetria e ferramentas que lhes permite visualizar padrões em todo o seu ambiente e minimizar o seu tempo médio para detetar (MTTD) e tempo médio para restaurar (MTTR).

- Prepare [dashboards personalizados](../azure-monitor/learn/tutorial-app-dashboards.md) com base em métricas e registos comuns para as diferentes funções da sua organização. Os dashboards podem combinar dados de todos os recursos do Azure.
- Prepare livros de [trabalho](../azure-monitor/platform/workbooks-overview.md) para garantir a partilha de conhecimentos entre desenvolvimento e operações. Estes poderiam ser preparados como relatórios dinâmicos com gráficos métricos e consultas de registo, ou mesmo como guias de resolução de problemas preparados por desenvolvedores ajudando o apoio ao cliente ou operações para lidar com problemas básicos.

## <a name="continuously-optimize"></a>Otimizar continuamente
 A monitorização é um dos aspetos fundamentais da popular filosofia Build-Measure-Learn, que recomenda o acompanhamento contínuo das suas métricas de comportamento do utilizador e, em seguida, esforçando-se por otimizá-los através de iterações de planeamento. O Azure Monitor ajuda-o a recolher métricas e registos relevantes para o seu negócio e a adicionar novos pontos de dados na próxima implementação, conforme necessário.

- Utilize ferramentas em Insights de Aplicação para rastrear o [comportamento e o envolvimento do utilizador final.](../azure-monitor/learn/tutorial-users.md)
- Use [a Análise](../azure-monitor/app/usage-impact.md) de Impacto para ajudá-lo a priorizar quais áreas para se concentrar para conduzir a KPIs importantes.


## <a name="next-steps"></a>Passos seguintes

- Conheça a diferença de componentes do [Monitor Azure.](overview.md)
- [Adicione uma monitorização contínua](../azure-monitor/app/continuous-monitoring.md) ao seu gasoduto de libertação.
