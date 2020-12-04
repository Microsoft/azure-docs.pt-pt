---
title: Resiliência através da monitorização e análise utilizando a Azure AD B2C Microsoft Docs
description: Resiliência através da monitorização e análise utilizando o Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 766fd80ae5f7450c8e45d10afa4612a788a8d5fc
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602161"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Resiliência através da monitorização e análise

A monitorização maximiza a disponibilidade e desempenho das suas aplicações e serviços. Oferece uma solução abrangente para recolher, analisar e agir em telemetria a partir da sua infraestrutura e aplicações. Os alertas notificam-no proativamente quando os problemas são encontrados com o seu serviço ou aplicações. Permitem identificar e resolver problemas antes que os utilizadores finais do seu serviço os notem. [O Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) ajuda-o a analisar, pesquisar os registos de auditoria e registos de login e a construir vistas personalizadas.

## <a name="monitor-and-get-notified-through-alerts"></a>Monitorize e a notifique-se através de alertas

Monitorizar o seu sistema e infraestrutura é fundamental para garantir a saúde geral dos seus serviços. Começa com a definição de métricas de negócio, tais como, chegada de novo utilizador, taxas de autenticação do utilizador final e conversão. Configure tais indicadores para monitorizar. Se está a planear um próximo aumento devido à promoção ou tráfego de férias, reveja as suas estimativas especificamente para o evento e referência correspondente para as métricas de negócio. Após o evento, recue para o referencial anterior.

Da mesma forma, detetar falhas ou perturbações de desempenho, criar uma boa linha de base e, em seguida, definir alertas é uma prática indispensável para responder rapidamente a questões emergentes.

![A imagem mostra componentes de monitorização e análise](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Como implementar monitorização e alerta

- **Monitorização**: Utilize [o Monitor Azure](https://docs.microsoft.com/azure/active-directory-b2c/azure-monitor) para monitorizar continuamente a saúde contra os principais objetivos do nível de serviço (SLO) e receba a notificação sempre que uma alteração crítica o faça. Comece por identificar a política Azure AD B2C ou uma aplicação como uma componente crítica do seu negócio cuja saúde precisa de ser monitorizada para manter a SLO. Identifique indicadores-chave que se alinham com os seus SLOs.
Por exemplo, acompanhe as seguintes métricas, uma vez que uma queda súbita em ambos os casos levará a uma perda de negócio.

  - **Total de pedidos**: O número total "n" de pedidos enviados para a política Azure AD B2C.

  - **Taxa de sucesso (%)**: Pedidos bem sucedidos/Número total de pedidos.

  Aceda aos [indicadores-chave](https://docs.microsoft.com/azure/active-directory-b2c/view-audit-logs) em [insights de aplicação](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights) onde são armazenados registos baseados em políticas Azure AD B2C, [registos](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights)de auditoria e registos de login.  

   - **Visualizações**: Utilizar o Log analytics constrói dashboards para monitorizar visualmente os indicadores-chave.

   - **Período atual**: Criar gráficos temporais para mostrar alterações na taxa total de pedidos e sucesso (%) no período atual, por exemplo, na semana atual.

   - **Período anterior**: Criar gráficos temporais para mostrar alterações na taxa total de pedidos e sucesso (%) em algum período anterior para fins de referência, por exemplo, na semana passada.

- **Aviso:** A utilização de registos de análise define [alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) que são acionados quando há alterações repentinas nos indicadores-chave. Estas alterações podem ter um impacto negativo nos SLOs. Os alertas utilizam várias formas de métodos de notificação, incluindo e-mail, SMS e webhooks. Comece por definir um critério que atue como um limiar contra o qual o alerta será desencadeado. Por exemplo:
  - Alerta contra queda abrupta nos pedidos totais: Desencadeie um alerta quando o número total de pedidos cair abruptamente. Por exemplo, quando houver uma queda de 25% no número total de pedidos em relação ao período anterior, levante um alerta.  
  - Alerta contra uma queda significativa na taxa de sucesso (%): Desencadear um alerta quando a taxa de sucesso da política selecionada baixar significativamente.
  - Ao receber um alerta, resolva o problema usando [Log Analytics,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views) [Application Insights](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-with-application-insights#:~:text=Setup%20Application%20Insights%201%20Go%20to%20the%20Azure,left-menu%2C%20and%20click%20on%20it.%20More%20items...%20)e [extensão do Código VS](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) para Azure AD B2C. Depois de resolver o problema e implementar uma aplicação ou política atualizada, continua a monitorizar os indicadores-chave até que regressem ao normal.

- **Alertas de serviço**: Utilize os alertas de nível de [serviço Azure AD B2C](https://docs.microsoft.com/azure/service-health/service-health-overview) para ser notificado de problemas de serviço, manutenção planeada, aconselhamento de saúde e aviso de segurança.

- **Reportagem**: [Ao utilizar a análise de registos,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)crie relatórios que o ajudem a compreender os conhecimentos dos utilizadores, os desafios técnicos e as oportunidades de crescimento.
  - **Painel de Saúde**: Crie dashboards personalizados utilizando a função [Azure Dashboard,](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards) que suporta a adição de gráficos usando consultas de Log Analytics. Por exemplo, identificar padrões de inserções bem sucedidas e falhadas, razões de falha e telemetria sobre dispositivos utilizados para fazer os pedidos.
  - **Abandone as viagens Azure AD B2C**: Utilize o [livro](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) para acompanhar a lista de viagens abandonadas do Azure AD B2C onde o utilizador iniciou a viagem de inscrição ou inscrição, mas nunca terminou. Fornece-lhe detalhes sobre o ID da política e a repartição das medidas que são tomadas pelo utilizador antes de abandonar a viagem.
  - **Azure AD B2C monitorando livros**: Utilize os [livros de monitorização](https://github.com/azure-ad-b2c/siem), que inclui o painel Azure AD B2C, operações de autenticação multi-factor (MFA), relatório de acesso condicional e registos de pesquisa por correlaid, para obter melhores informações sobre a saúde do seu ambiente Azure AD B2C.
  
## <a name="next-steps"></a>Próximos passos

- [Recursos de resiliência para desenvolvedores Azure AD B2C](resilience-b2c.md)
  - [Experiência resiliente do utilizador final](resilient-end-user-experience.md)
  - [Interfaces resilientes com processos externos](resilient-external-processes.md)
  - [Resiliência através das melhores práticas do desenvolvedor](resilience-b2c-developer-best-practices.md)
- [Construa resiliência na sua infraestrutura de autenticação](resilience-in-infrastructure.md)
- [Aumentar a resiliência da autenticação e autorização nos seus pedidos](resilience-app-development-overview.md)
