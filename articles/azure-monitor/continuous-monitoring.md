---
title: Monitorização contínua com o Azure Monitor | Documentos da Microsoft
description: Descreve os passos específicos para utilizar o Azure Monitor para ativar a monitorização contínua ao longo de seus fluxos de trabalho.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: bwren
ms.openlocfilehash: 1b86bc015b187fe75e79ba04df60a6bc5257a9b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60497425"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Monitorização contínua com o Azure Monitor

Monitorização contínua refere-se para o processo e tecnologia necessários para incorporar monitoramento em cada fase da sua DevOps e ciclos de vida de operações de IT. Ajuda para garantir continuamente o estado de funcionamento, desempenho e fiabilidade da sua aplicação e infraestrutura como move de desenvolvimento para produção. Compilações de monitorização contínuas nos conceitos de integração contínua e implementação contínuas (CI/CD) que ajudarão a desenvolveram e entregar software rápida e mais fiável para fornecer valor contínuo aos seus utilizadores.

[O Azure Monitor](overview.md) é a solução de monitorização unificada no Azure que fornece observability de pilha completa em todas as aplicações e infraestrutura na cloud e no local. Funciona perfeitamente com o [Visual Studio e Visual Studio Code](https://visualstudio.microsoft.com/) durante o desenvolvimento e teste e integra-se [do Azure DevOps](/azure/devops/user-guide/index) para gerenciamento de liberação e gestão de item de trabalho durante a implementação e operações. Integra-se até mesmo entre as ferramentas de ITSM e SIEM à sua escolha para ajudar a controlar problemas e incidentes dentro de seus processos de TI existentes.

Este artigo descreve os passos específicos para utilizar o Azure Monitor para ativar a monitorização contínua ao longo de seus fluxos de trabalho. Ele inclui links para outras documentações que fornece detalhes sobre a implementação de recursos diferentes.


## <a name="enable-monitoring-for-all-your-applications"></a>Ative a monitorização para todas as suas aplicações
Para obter observability em todo o seu ambiente, terá de ativar a monitorização em todos os seus aplicativos web e serviços. Isso permitirá que visualizar facilmente as transações de ponta a ponta e ligações em todos os componentes.

- [Projetos de DevOps do Azure](../devops-project/overview.md) dão-lhe uma experiência simplificada com o seu código existente e o repositório de Git ou escolher uma das aplicações de exemplo para criar um pipeline de integração contínua (CI) e a entrega contínua (CD) para o Azure.
- [Monitorização contínua no seu pipeline de lançamento de DevOps](../azure-monitor/app/continuous-monitoring.md) permite-lhe para a porta ou reverter a implementação com base em dados de monitorização.
- [Monitor de estado](../azure-monitor/app/monitor-performance-live-website-now.md) , pode instrumentar uma aplicação .NET em direto no Windows com o Azure Application Insights, sem ter de modificar ou voltar a implementar seu código.
- Se tiver acesso ao código para a sua aplicação, em seguida, ative a monitorização completa com [Application Insights](../azure-monitor/app/app-insights-overview.md) ao instalar o Azure Monitor Application Insights SDK para [.NET](../azure-monitor/learn/quick-monitor-portal.md), [Java ](../azure-monitor/learn/java-quick-start.md), [Node. js](../azure-monitor/learn/nodejs-quick-start.md), ou [outras linguagens de programação](../azure-monitor/app/platforms.md). Isto permite-lhe especificar os eventos personalizados, métricas ou vistas de página que são relevantes para a sua aplicação e o seu negócio.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Ative a monitorização para toda a sua infraestrutura
Os aplicativos só são tão fiáveis como a respetiva infraestrutura subjacente. Ter monitorização ativada em toda a infra-estrutura irá ajudá-lo a alcançar observability completa e tornar mais fácil detetar uma potencial causa quando algo falhar. O Azure Monitor ajuda a monitorizar o estado de funcionamento e o desempenho da sua infraestrutura híbrida todo, incluindo recursos, tais como VMs, contentores, armazenamento e rede.

- Obtém automaticamente [métricas de plataforma, registos de atividades e os registos de diagnóstico](platform/data-sources.md) da maioria dos recursos do Azure sem qualquer configuração.
- Ativar a monitorização mais aprofundada para VMs com [do Azure Monitor para VMs](insights/vminsights-overview.md).
-  Ativar a monitorização mais aprofundada para clusters do AKS com [Monitor do Azure para contentores](insights/container-insights-overview.md).
- Adicione [soluções de monitorização](insights/solutions-inventory.md) para diferentes aplicações e serviços no seu ambiente.


[Infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code) é o gerenciamento da infra-estrutura num modelo descritivo, usando o mesmo controle de versão, como o uso do código-fonte as equipas de DevOps. Adiciona a confiabilidade e escalabilidade para o seu ambiente e permite tirar partido dos processos semelhantes que utilizado para gerir as suas aplicações.

-  Uso [modelos do Resource Manager](platform/template-workspace-configuration.md) para ativar a monitorização e configurar alertas ao longo de um grande conjunto de recursos.
- Uso [do Azure Policy](../governance/policy/overview.md) para impõem diferentes regras aos recursos. Isto garante que esses recursos mantêm-se em conformidade com os seus padrões empresariais e contratos de nível de serviço. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Combinar recursos em grupos de recursos do Azure
Um aplicativo típico no Azure hoje mesmo inclui vários recursos, como VMs e serviços de aplicações ou microsserviços alojados em serviços Cloud, a clusters do AKS ou o Service Fabric. Esses aplicativos utilizam com frequência dependências como os Hubs de eventos, armazenamento, SQL e do Service Bus.

- Combine recursos inAzure grupos de recursos para obter visibilidade completa por todos os recursos que compõem a seus aplicativos diferentes. [O Azure Monitor para grupos de recursos](../azure-monitor/insights/resource-group-insights.md) fornece uma forma simples para controlar o estado de funcionamento e o desempenho de seu aplicativo inteiro de pilha completa e permite desagregar os respectivos componentes para qualquer investigações ou depuração.

## <a name="ensure-quality-through-continuous-deployment"></a>Garantir a qualidade através de implementação contínua
Integração contínua / implementação contínua permite-lhe automaticamente integre e implemente as alterações de código para seu aplicativo com base nos resultados do teste automatizado. Ele simplifica o processo de implantação e assegura a qualidade de todas as alterações antes de eles mover para produção.


- Uso [Pipelines do Azure](/azure/devops/pipelines) para a implementação contínua de implementar e automatizar o processo inteiro da consolidação de código para produção com base em seus testes de CI/CD.
- Uso [limites de qualidade](/azure/devops/pipelines/release/approvals/gates) para integrar a monitorização em seu pré-implantação ou pós-implementação. Isto garante que está a cumprir as métricas chave de estado de funcionamento/desempenho (KPIs) à medida que seus aplicativos movem de desenvolvimento para a produção e as diferenças no ambiente de infraestrutura ou de escala é não afetar negativamente os KPIs.
- [Manter a monitorização de instâncias separadas](../azure-monitor/app/separate-resources.md) entre seus ambientes de implementação diferentes, como o desenvolvimento, teste, Canárias e Prod. Isto garante que os dados recolhidos são relevantes em toda a infra-estrutura e os aplicativos associados. Se precisar de correlacionar dados em ambientes, pode utilizar [vários recursos gráficos no Explorador de métricas](../azure-monitor/platform/metrics-charts.md) ou crie [consultas entre recursos no Azure Monitor](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Criar alertas acionáveis com as ações
Um aspeto fundamental do monitoramento proativo é notificando os administradores de quaisquer problemas atuais e previstos. 

- Crie [alertas no Azure Monitor](../azure-monitor/platform/alerts-overview.md) com base em registos e métricas para identificar os Estados de falha previsível. Deve ter um objetivo de tornar todos os alertas acionáveis que significa que eles representam condições críticas reais e procurar o ressarcimento de reduzir os falsos positivos. Uso [limiares dinâmicos](platform/alerts-dynamic-thresholds.md) para calcular automaticamente as linhas de base de dados de métrica, em vez de definir seu próprio limiares estáticos. 
- Defina as ações de alertas utilizar a forma mais eficaz de notificar os administradores. Disponível [ações de notificação](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) são SMS, emails, notificações push ou chamadas de voz.
- Utilização avançada mais ações para [ligar a sua ferramenta ITSM](platform/itsmc-overview.md) ou outros sistemas de gestão de alertas por meio [webhooks](platform/activity-log-alerts-webhook.md).
- Remediar situações identificadas nos alertas, igualmente com [runbooks de automatização do Azure](../automation/automation-webhooks.md) ou [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) que pode ser iniciado a partir de um alerta através de webhooks. 
- Uso [dimensionamento automático](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) para aumentar e diminuir os recursos de computação com base nas métricas recolhidas dinamicamente.

## <a name="prepare-dashboards-and-workbooks"></a>Preparar a dashboards e pastas de trabalho
Garantir que seu desenvolvimento e operações tenham acesso à mesma telemetria e ferramentas permite-lhes ver padrões em todo o seu ambiente e minimizar o tempo médio para detetar (MTTD) e o tempo médio para restaurar (MTTR).

- Preparar [dashboards personalizados](../azure-monitor/learn/tutorial-app-dashboards.md) com base nas métricas e registos para as diferentes funções na sua organização comuns. Dashboards podem combinar dados de todos os recursos do Azure.
- Preparar [livros](../azure-monitor/app/usage-workbooks.md) para garantir que a partilha entre o desenvolvimento e operações de dados de conhecimento. Isso foi possível preparar como relatórios dinâmicos com gráficos de métricas e consultas de registo ou até mesmo como resolução de problemas guias preparados por desenvolvedores ajudar o suporte ao cliente ou operações para lidar com problemas básicos.

## <a name="continuously-optimize"></a>Otimize continuamente
 A monitorização é um dos aspectos fundamentais da popular compilação-avaliação-Aprendizado filosofia, que é recomendado pela controlo continuamente os seus KPIs e métricas de comportamento do utilizador e, em seguida, nos esforçando para otimizá-los por meio de planejamento de iterações. O Azure Monitor ajuda a coletar métricas e registos relevantes para o seu negócio e para adicionar novos pontos de dados na próxima implantação conforme necessário.

- Utilizar ferramentas do Application Insights para [controlar o comportamento do utilizador final e envolvimento](../azure-monitor/learn/tutorial-users.md).
- Uso [análise de impacto](../azure-monitor/app/usage-impact.md) para o ajudar a priorizar quais áreas concentrar-se para a unidade para KPIs importantes.


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os componentes de diferença de [do Azure Monitor](overview.md).
- [Adicionar a monitorização contínua](../azure-monitor/app/continuous-monitoring.md) para o seu pipeline de lançamento.