---
title: Azure Monitor Resource Group Insights [ Azure Monitor Resource Group Insights ] Microsoft Docs
description: Compreenda a saúde e desempenho das suas aplicações e serviços distribuídos ao nível do Grupo de Recursos com o Monitor Azure
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6d97e40bf2bf2298fb53609621db8ff2c6f1038f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663543"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorize grupos de recursos com monitor Azure (pré-visualização)

As aplicações modernas são muitas vezes complexas e altamente distribuídas com muitas peças discretas que trabalham em conjunto para prestar um serviço. Reconhecendo esta complexidade, o Azure Monitor fornece informações de monitorização para grupos de recursos. Isto facilita a triagem e diagnóstico de quaisquer problemas que os seus recursos individuais encontrem, ao mesmo tempo que oferece contexto sobre a saúde e desempenho do grupo&mdash;de recursos e a sua aplicação&mdash;como um todo.

## <a name="access-insights-for-resource-groups"></a>Acesso a insights para grupos de recursos

1. Selecione **Grupos de Recursos** da barra de navegação do lado esquerdo.
2. Escolha um dos seus grupos de recursos que queira explorar. (Se tiver um grande número de grupos de recursos filtrando por subscrição pode por vezes ser útil.)
3. Para aceder a insights para um grupo de recursos, clique em **Insights** no menu do lado esquerdo de qualquer grupo de recursos.

![Screenshot de página geral de insights de grupo de recursos](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Recursos com alertas ativos e problemas de saúde

A página geral mostra quantos alertas foram disparados e ainda estão ativos, juntamente com a atual Saúde de Recursos Azure de cada recurso. Juntos, esta informação pode ajudá-lo a detetar rapidamente todos os recursos que estejam a passar por problemas. Os alertas ajudam-no a detetar problemas no seu código e como configuraa a sua infraestrutura. A Azure Resource Health é um problema com a própria plataforma Azure, que não são específicas das suas aplicações individuais.

![Screenshot do painel de saúde de recursos azure](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Para mostrar a Azure Resource Health, verifique a caixa de saúde de **recursos do Show Azure** acima da mesa. Esta coluna é ocultada por padrão para ajudar a página a carregar rapidamente.

![Screenshot com gráfico de saúde de recursos adicionado](./media/resource-group-insights/0003-overview.png)

Por padrão, os recursos são agrupados por camada de aplicativo e tipo de recursos. A camada de **aplicações** é uma simples categorização de tipos de recursos, que só existe no contexto da página geral do grupo de recursos. Existem tipos de recursos relacionados com código de aplicação, infraestrutura de cálculo, networking, armazenamento + bases de dados. As ferramentas de gestão obtêm as suas próprias camadas de aplicações, e todos os outros recursos são categorizados como pertencentes à camada de **aplicações de Outras** aplicações. Este agrupamento pode ajudá-lo a ver de uma forma resumida mente quais os subsistemas da sua aplicação são saudáveis e pouco saudáveis.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnosticar problemas no seu grupo de recursos

A página de insights do grupo de recursos fornece várias outras ferramentas amplas para ajudá-lo a diagnosticar problemas

   |         |          |
   | ---------------- |:-----|
   | [**Alertas**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Ver, criar e gerir os seus alertas. |
   | [**Métricas**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Visualize e explore os seus dados baseados na métrica.    |
   | [**Registos de atividade**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Eventos de nível de subscrição que ocorreram em Azure.  |
   | [**Application map**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) (Mapeamento de aplicações) | Navegue na topologia da sua aplicação distribuída para identificar estrangulamentos de desempenho ou pontos de falha. |

## <a name="failures-and-performance"></a>Falhas e desempenho

E se reparaste que a tua aplicação está a funcionar lentamente, ou se os utilizadores reportaram erros? É demorado procurar todos os seus recursos para isolar problemas.

Os **separadores** **performance** e falhas simplificam este processo, reunindo pontos de vista de diagnóstico de desempenho e falha para muitos tipos comuns de recursos.

A maioria dos tipos de recursos abrirá uma galeria de modelos de livro do Monitor Azure. Cada livro que cria pode ser personalizado, guardado, partilhado com a sua equipa e reutilizado no futuro para diagnosticar problemas semelhantes.

### <a name="investigate-failures"></a>Investigar falhas

Para testar o separador Falhas, selecione **Falhas** sob **investigação** no menu à esquerda.

A barra de menu do lado esquerdo muda após a sua seleção ser feita, oferecendo-lhe novas opções.

![Screenshot do painel de visão geral do fracasso](./media/resource-group-insights/00004-failures.png)

Quando o Serviço de Aplicações é escolhido, é-lhe apresentada uma galeria de modelos de livro do Monitor Azure.

![Screenshot da galeria do livro de aplicações](./media/resource-group-insights/0005-failure-insights-workbook.png)

Escolher o modelo para Insights de Falha abrirá o livro de trabalho.

![Screenshot do relatório de falha](./media/resource-group-insights/0006-failure-visual.png)

Pode selecionar qualquer uma das linhas. A seleção é então exibida numa vista de detalhes gráficos.

![Screenshot dos detalhes do fracasso](./media/resource-group-insights/0007-failure-details.png)

Os livros abstraem o difícil trabalho de criar relatórios e visualizações personalizados num formato facilmente consumível. Embora alguns utilizadores só queiram ajustar os parâmetros pré-construídos, os livros de relojoeiros são completamente personalizáveis.

Para ter uma ideia de como este livro funciona internamente, **selecione Editar** na barra superior.

![Screenshot da opção de edição adicional](./media/resource-group-insights/0008-failure-edit.png)

Várias caixas de **Edição** aparecem perto dos vários elementos do livro. Selecione a caixa **Editar** abaixo da tabela de operações.

![Screenshot das caixas de edição](./media/resource-group-insights/0009-failure-edit-graph.png)

Isto revela a consulta de registo subjacente que está a impulsionar a visualização da tabela.

 ![Screenshot da janela de consulta de log](./media/resource-group-insights/0010-failure-edit-query.png)

Pode modificar a consulta diretamente. Ou pode usá-lo como referência e pedir emprestado ao conceber o seu próprio livro parametrizado personalizado.

### <a name="investigate-performance"></a>Investigar desempenho

A performance oferece a sua própria galeria de livros. Para o Serviço de Aplicações, o livro de trabalho de desempenho pré-construído oferece a seguinte visão:

 ![Screenshot da vista de desempenho](./media/resource-group-insights/0011-performance.png)

Neste caso, se selecionar editar verá que este conjunto de visualizações é alimentado pela Azure Monitor Metrics.

 ![Screenshot da vista de desempenho com métricas Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="enabling-access-to-alerts"></a>Permitir o acesso a alertas

Para ver alertas no Azure Monitor para Grupos de Recursos, alguém com uma função de Proprietário ou Contribuinte para esta subscrição precisa de abrir o Monitor Azure para Grupos de Recursos para qualquer grupo de recursos na subscrição. Isto permitirá que qualquer pessoa com acesso lido veja alertas no Monitor Azure para Grupos de Recursos para todos os grupos de recursos na subscrição. Se tiver uma função de Proprietário ou Colaborador, refresque esta página em poucos minutos.

O Azure Monitor para Grupos de Recursos conta com o sistema de gestão de alertas do Monitor Azure para recuperar o estado de alerta. A Gestão de Alertas não está configurada para cada grupo de recursos e subscrição por padrão, e só pode ser ativada por alguém com uma função de Proprietário ou Colaborador. Pode ser ativado por:
* Abertura do Monitor Azure para Grupos de Recursos para qualquer grupo de recursos na subscrição.
* Ou indo para a subscrição, clicando em **Fornecedores**de Recursos, em seguida, clicando no **Register for Alerts.Management**.

## <a name="next-steps"></a>Passos seguintes

- [Livros de Monitorização Azure](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Alertas de monitor estoque](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
