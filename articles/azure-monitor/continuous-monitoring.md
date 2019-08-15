---
title: Monitoramento contínuo com Azure Monitor | Microsoft Docs
description: Descreve as etapas específicas para usar Azure Monitor para habilitar o monitoramento contínuo em todos os fluxos de trabalho.
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
ms.openlocfilehash: ab633ca47f684688019b1313de61571252760a20
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967752"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Monitoramento contínuo com Azure Monitor

O monitoramento contínuo refere-se ao processo e à tecnologia necessária para incorporar o monitoramento em cada fase de seus ciclos de vida de operações de ti e DevOps. Ele ajuda a garantir continuamente a integridade, o desempenho e a confiabilidade do seu aplicativo e da infraestrutura à medida que ele se move do desenvolvimento para a produção. O monitoramento contínuo baseia-se nos conceitos de integração contínua e implantação contínua (CI/CD) que ajudam você a desenvolver e entregar software de forma mais rápida e confiável para fornecer valor contínuo aos seus usuários.

[Azure monitor](overview.md) é a solução de monitoramento unificada no Azure que fornece a observação de uma pilha completa entre aplicativos e infraestrutura na nuvem e no local. Ele funciona perfeitamente com o [Visual Studio e Visual Studio Code](https://visualstudio.microsoft.com/) durante o desenvolvimento e o teste e integra-se com o [Azure DevOps](/azure/devops/user-guide/index) para gerenciamento de versão e gerenciamento de itens de trabalho durante a implantação e as operações. Ele se integra a todas as ferramentas de ITSM e SIEM de sua escolha para ajudar a acompanhar problemas e incidentes em seus processos de ti existentes.

Este artigo descreve as etapas específicas para usar Azure Monitor para habilitar o monitoramento contínuo em todos os fluxos de trabalho. Ele inclui links para outras documentações que fornecem detalhes sobre a implementação de recursos diferentes.


## <a name="enable-monitoring-for-all-your-applications"></a>Habilitar o monitoramento de todos os seus aplicativos
Para obter uma observação de desempenho em todo o ambiente, você precisa habilitar o monitoramento em todos os seus aplicativos e serviços Web. Isso permitirá que você visualize facilmente transações e conexões de ponta a ponta em todos os componentes.

- [Azure DevOps Projects](../devops-project/overview.md) oferece uma experiência simplificada com seu código existente e o repositório git, ou escolha um dos aplicativos de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.
- O [monitoramento contínuo em seu pipeline de versão do DevOps](../azure-monitor/app/continuous-monitoring.md) permite que você portão ou reverta sua implantação com base nos dados de monitoramento.
- [Status monitor](../azure-monitor/app/monitor-performance-live-website-now.md) permite instrumentar um aplicativo .net ativo no Windows com o aplicativo Azure insights, sem a necessidade de modificar ou reimplantar seu código.
- Se você tiver acesso ao código para seu aplicativo, habilite o monitoramento completo com [Application insights](../azure-monitor/app/app-insights-overview.md) instalando o SDK do Application Insights do Azure monitor para [.net](../azure-monitor/learn/quick-monitor-portal.md), [Java](../azure-monitor/app/java-get-started.md), [node. js](../azure-monitor/learn/nodejs-quick-start.md)ou [qualquer outra programação idiomas](../azure-monitor/app/platforms.md). Isso permite que você especifique eventos personalizados, métricas ou exibições de página que são relevantes para seu aplicativo e seu negócio.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Habilitar o monitoramento para toda a sua infraestrutura
Os aplicativos são tão confiáveis quanto sua infraestrutura subjacente. Ter o monitoramento habilitado em toda a sua infraestrutura ajudará você a obter uma observação completa e facilitar a descoberta de uma possível causa raiz quando algo falhar. Azure Monitor ajuda a acompanhar a integridade e o desempenho de toda a sua infraestrutura híbrida, incluindo recursos como VMs, contêineres, armazenamento e rede.

- Você obtém automaticamente [métricas de plataforma, logs de atividades e logs de diagnóstico](platform/data-sources.md) da maioria dos recursos do Azure sem configuração.
- Habilite o monitoramento mais profundo para VMs com [Azure monitor para VMs](insights/vminsights-overview.md).
-  Habilite o monitoramento mais profundo para clusters AKS com [Azure monitor para contêineres](insights/container-insights-overview.md).
- Adicione [soluções de monitoramento](insights/solutions-inventory.md) para diferentes aplicativos e serviços em seu ambiente.


A [Infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code) é o gerenciamento da infraestrutura em um modelo descritivo, usando o mesmo controle de versão que as equipes DevOps usam para o código-fonte. Ele adiciona confiabilidade e escalabilidade ao seu ambiente e permite que você aproveite processos semelhantes usados para gerenciar seus aplicativos.

-  Use [modelos do Resource Manager](platform/template-workspace-configuration.md) para habilitar o monitoramento e configurar alertas em um grande conjunto de recursos.
- Use [Azure Policy](../governance/policy/overview.md) para impor regras diferentes sobre seus recursos. Isso garante que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Combinar recursos em grupos de recursos do Azure
Um aplicativo típico no Azure atualmente inclui vários recursos, como VMs e serviços de aplicativo ou microservices hospedados em serviços de nuvem, clusters AKS ou Service Fabric. Esses aplicativos frequentemente utilizam dependências como hubs de eventos, armazenamento, SQL e barramento de serviço.

- Combine recursos inazure grupos de recursos para obter visibilidade total de todos os seus recursos que compõem seus aplicativos diferentes. [Azure monitor para grupos de recursos](../azure-monitor/insights/resource-group-insights.md) fornece uma maneira simples de controlar a integridade e o desempenho de todo o seu aplicativo completo de pilha e permite a busca detalhada dos respectivos componentes para qualquer investigação ou depuração.

## <a name="ensure-quality-through-continuous-deployment"></a>Garantir a qualidade por meio da implantação contínua
A integração contínua/implantação contínua permite que você integre e implante automaticamente as alterações de código em seu aplicativo com base nos resultados do teste automatizado. Ele simplifica o processo de implantação e garante a qualidade das alterações antes que elas se movam para a produção.


- Use [Azure pipelines](/azure/devops/pipelines) para implementar a implantação contínua e automatizar todo o processo desde a confirmação do código até a produção com base nos testes de CI/CD.
- Use [Gates de qualidade](/azure/devops/pipelines/release/approvals/gates) para integrar o monitoramento à pré-implantação ou pós-implantação. Isso garante que você esteja atendendo às principais métricas de integridade/desempenho (KPIs), uma vez que seus aplicativos se movem de desenvolvimento para produção e quaisquer diferenças no ambiente de infraestrutura ou escala não afetem negativamente os KPIs.
- [Mantenha instâncias de monitoramento separadas](../azure-monitor/app/separate-resources.md) entre seus diferentes ambientes de implantação, como dev, Test, canário e prod. Isso garante que os dados coletados sejam relevantes entre os aplicativos e a infraestrutura associados. Se você precisar correlacionar dados entre ambientes, poderá usar [gráficos de vários recursos em Metrics Explorer](../azure-monitor/platform/metrics-charts.md) ou criar [consultas entre recursos no Azure monitor](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Criar alertas acionáveis com ações
Um aspecto crítico do monitoramento é notificar de forma proativa os administradores de quaisquer problemas atuais e previstos. 

- Crie [alertas no Azure monitor](../azure-monitor/platform/alerts-overview.md) com base em logs e métricas para identificar Estados de falha previsíveis. Você deve ter uma meta de tornar todos os alertas acionáveis, o que significa que eles representam as condições críticas reais e buscam reduzir os falsos positivos. Use [limites dinâmicos](platform/alerts-dynamic-thresholds.md) para calcular automaticamente linhas de base em dados de métrica em vez de definir seus próprios limites estáticos. 
- Defina ações para que os alertas usem o meio mais eficaz de notificar seus administradores. As [ações disponíveis para notificação](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) são SMS, emails, notificações por Push ou chamadas de voz.
- Use ações mais avançadas para [se conectar à sua ferramenta de ITSM](platform/itsmc-overview.md) ou a outros sistemas [](platform/activity-log-alerts-webhook.md)de gerenciamento de alertas por meio de WebHooks.
- Corrija as situações identificadas em alertas também com os [runbooks de automação do Azure](../automation/automation-webhooks.md) ou os [aplicativos lógicos](/connectors/custom-connectors/create-webhook-trigger) que podem ser iniciados por meio de um alerta usando WebHooks. 
- Use [](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) o dimensionamento automático para aumentar e diminuir dinamicamente seus recursos de computação com base nas métricas coletadas.

## <a name="prepare-dashboards-and-workbooks"></a>Preparar painéis e pastas de trabalho
Garantir que seu desenvolvimento e suas operações tenham acesso à mesma telemetria e ferramentas permite que eles exibam padrões em todo o ambiente e minimizem o tempo médio de detecção (MTTD) e o tempo médio de restauração (MTTR).

- Prepare [painéis personalizados](../azure-monitor/learn/tutorial-app-dashboards.md) com base em métricas e logs comuns para as diferentes funções em sua organização. Os painéis podem combinar dados de todos os recursos do Azure.
- Prepare [pastas de trabalho](../azure-monitor/app/usage-workbooks.md) para garantir o compartilhamento de conhecimento entre desenvolvimento e operações. Eles podem ser preparados como relatórios dinâmicos com gráficos de métricas e consultas de log, ou até mesmo como guias de solução de problemas preparados por desenvolvedores que ajudam o atendimento ao cliente ou operações para lidar com problemas básicos.

## <a name="continuously-optimize"></a>Otimizar continuamente
 O monitoramento é um dos aspectos fundamentais da filosofia popular de avaliação de compilação-aprendizado, que recomenda controlar continuamente os KPIs e as métricas de comportamento do usuário e, em seguida, se empenhar em otimizá-los por meio de iterações de planejamento. Azure Monitor ajuda a coletar métricas e logs relevantes para seus negócios e adicionar novos pontos de dados na próxima implantação, conforme necessário.

- Use as ferramentas no Application Insights para [controlar o comportamento e o envolvimento do usuário final](../azure-monitor/learn/tutorial-users.md).
- Use a [análise de impacto](../azure-monitor/app/usage-impact.md) para ajudá-lo a priorizar em quais áreas concentrar-se em direcionar para KPIs importantes.


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os componentes de diferença do [Azure monitor](overview.md).
- [Adicione monitoramento contínuo](../azure-monitor/app/continuous-monitoring.md) ao seu pipeline de lançamento.