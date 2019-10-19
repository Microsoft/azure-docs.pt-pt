---
title: Azure Monitor informações do grupo de recursos | Microsoft Docs
description: Entenda a integridade e o desempenho de seus aplicativos e serviços distribuídos no nível do grupo de recursos com Azure Monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: fba94a5e723bfbc2719b3b4cf5cd130eda382276
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553986"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorar grupos de recursos com Azure Monitor (versão prévia)

Os aplicativos modernos costumam ser complexos e altamente distribuídos com muitas partes discretas trabalhando em conjunto para fornecer um serviço. Reconhecendo essa complexidade, Azure Monitor fornece informações de monitoramento para grupos de recursos. Isso facilita a triagem e o diagnóstico de quaisquer problemas que seus recursos individuais encontrem, ao mesmo tempo em que oferece contexto para a integridade e o desempenho do grupo de recursos &mdash;and seu aplicativo &mdash;as um todo.

## <a name="access-insights-for-resource-groups"></a>Acessar informações para grupos de recursos

1. Selecione **grupos de recursos** na barra de navegação do lado esquerdo.
2. Escolha um dos seus grupos de recursos que você deseja explorar. (Se você tiver um grande número de grupos de recursos, a filtragem por assinatura pode, às vezes, ser útil).
3. Para acessar informações de um grupo de recursos, clique em **percepções** no menu do lado esquerdo de qualquer grupo de recursos.

![Captura de tela da página Visão geral do grupo de recursos](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Recursos com alertas ativos e problemas de integridade

A página Visão geral mostra quantos alertas foram acionados e ainda estão ativos, juntamente com o Azure Resource Health atual de cada recurso. Juntas, essas informações podem ajudá-lo a identificar rapidamente todos os recursos que estão enfrentando problemas. Os alertas ajudam a detectar problemas em seu código e como você configurou sua infraestrutura. Azure Resource Health o problema de superfícies com a própria plataforma do Azure, que não é específica para seus aplicativos individuais.

![Captura de tela do painel de Azure Resource Health](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Para exibir Azure Resource Health, marque a caixa **mostrar Azure Resource Health** acima da tabela. Essa coluna fica oculta por padrão para ajudar a carregar a página rapidamente.

![Captura de tela com grafo Resource Health adicionado](./media/resource-group-insights/0003-overview.png)

Por padrão, os recursos são agrupados por camada de aplicativo e tipo de recurso. A **camada de aplicativo** é uma categorização simples de tipos de recursos, que existe apenas dentro do contexto da página Visão geral do grupo de recursos. Há tipos de recursos relacionados ao código do aplicativo, infraestrutura de computação, rede, armazenamento + bancos de dados. As ferramentas de gerenciamento obtêm suas próprias camadas de aplicativo e todos os outros recursos são categorizados como pertencentes à **outra** camada de aplicativo. Esse agrupamento pode ajudá-lo a ver rapidamente quais subsistemas de seu aplicativo estão íntegros e não íntegros.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnosticar problemas em seu grupo de recursos

A página de informações do grupo de recursos fornece várias outras ferramentas com escopo para ajudá-lo a diagnosticar problemas

   |         |          |
   | ---------------- |:-----|
   | [**Alertas**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Exiba, crie e gerencie seus alertas. |
   | [**Métricas**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Visualize e explore seus dados baseados em métrica.    |
   | [**Logs de atividade**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Eventos de nível de assinatura que ocorreram no Azure.  |
   | [**Application map**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) (Mapeamento de aplicações) | Navegue pela topologia do aplicativo distribuído para identificar gargalos de desempenho ou pontos de interativações de falha. |

## <a name="failures-and-performance"></a>Falhas e desempenho

E se você observou que o aplicativo está em execução lentamente ou se os usuários relataram erros? É muito demorado Pesquisar em todos os seus recursos para isolar problemas.

As guias **desempenho** e **falhas** simplificam esse processo reunindo exibições de diagnóstico de desempenho e falha para muitos tipos de recursos comuns.

A maioria dos tipos de recursos abrirá uma galeria de modelos de pasta de trabalho Azure Monitor. Cada pasta de trabalho criada pode ser personalizada, salva, compartilhada com sua equipe e reutilizada no futuro para diagnosticar problemas semelhantes.

### <a name="investigate-failures"></a>Investigar falhas

Para testar a guia falhas, selecione **falhas** em **investigar** no menu à esquerda.

A barra de menus do lado esquerdo é alterada depois que a seleção é feita, oferecendo novas opções.

![Captura de tela do painel Visão geral da falha](./media/resource-group-insights/00004-failures.png)

Quando o serviço de aplicativo for escolhido, você verá uma galeria de Azure Monitor modelos de pasta de trabalho.

![Captura de tela da Galeria de pastas de trabalho do aplicativo](./media/resource-group-insights/0005-failure-insights-workbook.png)

A escolha do modelo para informações de falha abrirá a pasta de trabalho.

![Captura de tela do relatório de falha](./media/resource-group-insights/0006-failure-visual.png)

Você pode selecionar qualquer uma das linhas. Em seguida, a seleção é exibida em uma exibição de detalhes gráficos.

![Captura de tela de detalhes da falha](./media/resource-group-insights/0007-failure-details.png)

As pastas de trabalho abstraim o trabalho difícil de criar relatórios personalizados e visualizações em um formato facilmente consumível. Embora alguns usuários possam querer apenas ajustar os parâmetros predefinidos, as pastas de trabalho são completamente personalizáveis.

Para ter uma noção de como essa pasta de trabalho funciona internamente, selecione **Editar** na barra superior.

![Captura de tela da opção de edição adicional](./media/resource-group-insights/0008-failure-edit.png)

Várias caixas de **edição** aparecem perto dos vários elementos da pasta de trabalho. Selecione a caixa de **edição** abaixo da tabela de operações.

![Captura de tela das caixas de edição](./media/resource-group-insights/0009-failure-edit-graph.png)

Isso revela a consulta de log subjacente que está orientando a visualização de tabela.

 ![Captura de tela da janela de consulta de log](./media/resource-group-insights/0010-failure-edit-query.png)

Você pode modificar a consulta diretamente. Ou você pode usá-lo como uma referência e um empréstimo dele ao criar sua própria pasta de trabalho com parâmetros personalizados.

### <a name="investigate-performance"></a>Investigar o desempenho

O desempenho oferece sua própria galeria de pastas de trabalho. Para o serviço de aplicativo, a pasta de trabalho de desempenho de aplicativo predefinida oferece o seguinte modo de exibição:

 ![Captura de tela da exibição de desempenho](./media/resource-group-insights/0011-performance.png)

Nesse caso, se você selecionar Editar, verá que esse conjunto de visualizações é alimentado por Azure Monitor métricas.

 ![Captura de tela do modo de exibição de desempenho com métricas do Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="enabling-access-to-alerts"></a>Habilitando o acesso a alertas

Para ver alertas em Azure Monitor para grupos de recursos, alguém com uma função de proprietário ou colaborador para essa assinatura precisa abrir Azure Monitor para grupos de recursos para qualquer grupo de recursos na assinatura. Isso permitirá que qualquer pessoa com acesso de leitura Veja alertas no Azure Monitor para grupos de recursos para todos os grupos de recursos na assinatura. Se você tiver uma função de proprietário ou colaborador, atualize esta página em alguns minutos.

Azure Monitor para grupos de recursos depende do sistema de gerenciamento de alertas de Azure Monitor para recuperar o status do alerta. O gerenciamento de alertas não está configurado para cada grupo de recursos e assinatura por padrão e só pode ser habilitado por alguém com uma função de proprietário ou colaborador. Ele pode ser habilitado por:
* Abrir Azure Monitor para grupos de recursos para qualquer grupo de recursos na assinatura.
* Ou acessando a assinatura, clicando em **provedores de recursos**e, em seguida, clicando em **registrar para alertas. gerenciamento**.

## <a name="next-steps"></a>Passos seguintes

- [Azure Monitor pastas de trabalho](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Alertas de Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
