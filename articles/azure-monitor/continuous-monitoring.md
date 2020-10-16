---
title: Monitorização contínua com o Azure Monitor Microsoft Docs
description: Descreve passos específicos para a utilização do Azure Monitor para ativar a monitorização contínua ao longo dos seus fluxos de trabalho.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: e41fa9f9918f4f17bd89e80610ae608e83eb3402
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996399"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Monitorização contínua com monitor azure

A monitorização contínua refere-se ao processo e à tecnologia necessárias para incorporar a monitorização em cada fase dos seus ciclos de vida de operações de DevOps e TI. Ajuda a garantir continuamente a saúde, o desempenho e a fiabilidade da sua aplicação e infraestrutura à medida que passa do desenvolvimento para a produção. A monitorização contínua baseia-se nos conceitos de Integração Contínua e Implementação Contínua (CI/CD) que o ajudam a desenvolver e fornecer software de forma mais rápida e fiável para fornecer valor contínuo aos seus utilizadores.

[O Azure Monitor](overview.md) é a solução de monitorização unificada em Azure que proporciona uma visibilidade completa em todas as aplicações e infraestruturas na nuvem e no local. Funciona perfeitamente com [o Visual Studio e Visual Studio Code](https://visualstudio.microsoft.com/) durante o desenvolvimento e teste e integra-se com a [Azure DevOps](/azure/devops/user-guide/index) para gestão de lançamentos e gestão de item de trabalho durante a implementação e operações. Até se integra nas ferramentas ITSM e SIEM à sua escolha para ajudar a rastrear problemas e incidentes dentro dos seus processos de TI existentes.

Este artigo descreve passos específicos para a utilização do Azure Monitor para permitir uma monitorização contínua ao longo dos seus fluxos de trabalho. Inclui links para outra documentação que fornece detalhes sobre a implementação de diferentes funcionalidades.


## <a name="enable-monitoring-for-all-your-applications"></a>Ativar a monitorização de todas as suas aplicações
Para obter a observabilidade em todo o seu ambiente, precisa de ativar a monitorização em todas as suas aplicações e serviços web. Isto permitir-lhe-á visualizar facilmente transações e ligações de ponta a ponta em todos os componentes.

- [Os Projetos Azure DevOps](../devops-project/overview.md) dão-lhe uma experiência simplificada com o seu código existente e repositório git, ou escolha entre uma das aplicações da amostra para criar um pipeline de Integração Contínua (CI) e Entrega Contínua (CD) para Azure.
- [A monitorização contínua no seu oleoduto de libertação de DevOps](./app/continuous-monitoring.md) permite-lhe adiar ou reverter a sua implementação com base em dados de monitorização.
- [O Status Monitor](./app/monitor-performance-live-website-now.md)  permite-lhe instrumentar uma aplicação live .NET no Windows com Azure Application Insights, sem ter de modificar ou redistribuir o seu código.
- Se tiver acesso ao código da sua aplicação, em seguida, ative a monitorização completa com [o Application Insights,](./app/app-insights-overview.md) instalando o Azure Monitor Application Insights SDK para [.NET](./learn/quick-monitor-portal.md), [Java,](./app/java-get-started.md) [Node.js, ](./learn/nodejs-quick-start.md)ou [quaisquer outras linguagens de programação.](./app/platforms.md) Isto permite-lhe especificar eventos personalizados, métricas ou vistas de página que sejam relevantes para a sua aplicação e para o seu negócio.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Permitir a monitorização de toda a sua infraestrutura
As aplicações são tão fiáveis quanto as suas infraestruturas subjacentes. Ter a monitorização ativada em toda a sua infraestrutura irá ajudá-lo a alcançar a plena observabilidade e facilitar a descoberta de uma potencial causa de raiz quando algo falha. O Azure Monitor ajuda-o a acompanhar a saúde e o desempenho de toda a sua infraestrutura híbrida, incluindo recursos como VMs, contentores, armazenamento e rede.

- Obtém automaticamente [métricas de plataforma, registos de atividades e registos](platform/data-sources.md) de diagnóstico da maioria dos seus recursos Azure sem configuração.
- Ativar uma monitorização mais profunda dos VMs com [monitores Azure para VMs](insights/vminsights-overview.md).
-  Ativar uma monitorização mais profunda dos aglomerados AKS com [o Azure Monitor para contentores](insights/container-insights-overview.md).
- Adicione [soluções de monitorização](./monitor-reference.md) para diferentes aplicações e serviços no seu ambiente.


[A infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code) é a gestão da infraestrutura num modelo descritivo, utilizando a mesma versão que as equipas de DevOps usam para código fonte. Adiciona fiabilidade e escalabilidade ao seu ambiente e permite-lhe alavancar processos semelhantes que costumavam gerir as suas aplicações.

-  Utilize [modelos de Gestor de Recursos](./samples/resource-manager-workspace.md) para permitir a monitorização e configurar alertas sobre um grande conjunto de recursos.
- Use [a Política Azure](../governance/policy/overview.md) para impor regras diferentes sobre os seus recursos. Isto garante que esses recursos se mantenham conformes com os seus padrões corporativos e acordos de nível de serviço. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Combine recursos em grupos de recursos Azure
Uma aplicação típica no Azure hoje inclui múltiplos recursos, tais como VMs e App Services ou microserviços hospedados em Cloud Services, clusters AKS ou Service Fabric. Estas aplicações utilizam frequentemente dependências como Event Hubs, Storage, SQL e Service Bus.

- Combine recursos em Grupos de Recursos Azure para obter visibilidade total em todos os seus recursos que compõem as suas diferentes aplicações. [O Azure Monitor for Resource Groups](./insights/resource-group-insights.md) fornece uma forma simples de acompanhar a saúde e o desempenho de toda a sua aplicação full-stack e permite perfurar os respetivos componentes para quaisquer investigações ou depuração.

## <a name="ensure-quality-through-continuous-deployment"></a>Garantir a qualidade através da implementação contínua
Integração Contínua / Implementação Contínua permite-lhe integrar e implementar automaticamente alterações de código na sua aplicação com base nos resultados de testes automatizados. Agiliza o processo de implantação e garante a qualidade de quaisquer alterações antes de entrarem em produção.


- Utilize [pipelines Azure](/azure/devops/pipelines) para implementar a Implementação Contínua e automatizar todo o seu processo desde código comprometendo-se com a produção com base nos seus testes de CI/CD.
- Utilize [portões de qualidade](/azure/devops/pipelines/release/approvals/gates) para integrar a monitorização na sua pré-implantação ou pós-implantação. Isto garante que está a cumprir as principais métricas de saúde/desempenho (KPI's) à medida que as suas aplicações passam de dev para produção e quaisquer diferenças no ambiente ou escala da infraestrutura não estão a afetar negativamente os seus KPI's.
- [Mantenha instâncias de monitorização separadas](./app/separate-resources.md) entre os seus diferentes ambientes de implantação, tais como Dev, Test, Canary e Prod. Isto garante que os dados recolhidos são relevantes em todas as aplicações e infraestruturas associadas. Se precisar de correlacionar dados em ambientes, pode utilizar [gráficos de vários recursos no Metrics Explorer](./platform/metrics-charts.md) ou criar [consultas de recursos cruzados no Azure Monitor](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Criar alertas accuáveis com ações
Um aspeto crítico da monitorização é notificar proactivamente os administradores de quaisquer problemas atuais e previstos. 

- Crie [alertas no Azure Monitor](./platform/alerts-overview.md) com base em registos e métricas para identificar estados de falha previsíveis. Deve ter o objetivo de tornar todos os alertas accuíveis, significando que eles representam condições críticas reais e procuram reduzir falsos positivos. Utilize [limiares dinâmicos](platform/alerts-dynamic-thresholds.md) para calcular automaticamente as linhas de base em dados métricos em vez de definir os seus próprios limiares estáticos. 
- Defina ações para alertas para utilizar os meios mais eficazes de notificar os seus administradores. As [ações](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) disponíveis para notificação são SMS, e-mails, notificações push ou chamadas de voz.
- Utilize ações mais avançadas [para ligar à sua ferramenta ITSM](platform/itsmc-overview.md) ou outros sistemas de gestão de alerta através de [webhooks](platform/activity-log-alerts-webhook.md).
- Remediar situações identificadas em alertas também com [runbooks da Azure Automation](../automation/automation-webhooks.md) ou [Aplicações Lógicas](/connectors/custom-connectors/create-webhook-trigger) que podem ser lançadas a partir de um alerta usando webhooks. 
- Utilize [autoscaling](./learn/tutorial-autoscale-performance-schedule.md) para aumentar dinamicamente e diminuir os seus recursos de computação com base em métricas recolhidas.

## <a name="prepare-dashboards-and-workbooks"></a>Preparar painéis e livros
Garantir que o seu desenvolvimento e operações têm acesso à mesma telemetria e ferramentas permite-lhes visualizar padrões em todo o seu ambiente e minimizar o seu Tempo Médio para Detetar (MTTD) e Tempo Médio para Restaurar (MTTR).

- Prepare [dashboards personalizados](./learn/tutorial-app-dashboards.md) com base em métricas e registos comuns para os diferentes papéis na sua organização. Os dashboards podem combinar dados de todos os recursos da Azure.
- Preparar [livros de trabalho](./platform/workbooks-overview.md) para garantir a partilha de conhecimentos entre desenvolvimento e operações. Estes poderiam ser preparados como relatórios dinâmicos com gráficos métricos e consultas de registo, ou mesmo como guias de resolução de problemas preparados por desenvolvedores que ajudam o apoio ao cliente ou operações para lidar com problemas básicos.

## <a name="continuously-optimize"></a>Otimizar continuamente
 A monitorização é um dos aspetos fundamentais da popular filosofia Build-Measure-Learn, que recomenda monitorizar continuamente os seus KPI's e as métricas de comportamento do utilizador e, em seguida, tentar otimizá-los através do planeamento de iterações. O Azure Monitor ajuda-o a recolher métricas e registos relevantes para o seu negócio e a adicionar novos pontos de dados na próxima implementação, conforme necessário.

- Utilize ferramentas em Insights de Aplicação para acompanhar o [comportamento e o envolvimento do utilizador final](./learn/tutorial-users.md).
- Use [a Análise de Impacto](./app/usage-impact.md) para ajudá-lo a priorizar quais áreas se concentrar para dirigir para KPI's importantes.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os componentes da diferença do [Azure Monitor](overview.md).
- [Adicione uma monitorização contínua](./app/continuous-monitoring.md) ao seu gasoduto de libertação.