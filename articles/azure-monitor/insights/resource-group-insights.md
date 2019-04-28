---
title: Informações do grupo de recursos do Azure Monitor | Documentos da Microsoft
description: Compreender o estado de funcionamento e o desempenho dos aplicativos distribuídos e serviços ao nível do grupo de recursos com o Azure Monitor
services: azure-monitor
author: NumberByColors
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: d5c07e0d4aca8bda42ea9f78a1475ea7bb5861f0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119426"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorizar grupos de recursos com o Azure Monitor (pré-visualização)

Aplicativos modernos costumam ser complexos e altamente distribuídos com muitas partes discretas trabalhar em conjunto para prestar um serviço. RECONHECENDO essa complexidade, o Azure Monitor fornece informações de monitorização para grupos de recursos. Isto torna mais fácil fazer a triagem e diagnosticar quaisquer problemas que encontram os recursos individuais, oferecendo contexto relativamente ao estado de funcionamento e o desempenho do grupo de recursos&mdash;e a sua aplicação&mdash;como um todo.

## <a name="access-insights-for-resource-groups"></a>Aceder a informações para grupos de recursos

1. Selecione **grupos de recursos** na barra de navegação esquerda.
2. Escolha um dos seus grupos de recursos que queira explorar. (Se tiver um grande número de grupos de recursos, a filtragem por subscrição pode por vezes, ser útil.)
3. Para aceder a informações para um grupo de recursos, clique em **Insights** no menu da esquerda de qualquer grupo de recursos.

![Captura de ecrã da página de descrição geral de informações de grupo de recursos](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Recursos com problemas de estado de funcionamento e de alertas ativos

A página de visão geral mostra quantos alertas foi acionadas e ainda estão ativas, juntamente com o Azure Resource Health atual de cada recurso. Em conjunto, estas informações podem ajudá-lo a detetar rapidamente quaisquer recursos que estão a ter problemas. Alertas ajudam a detectar problemas em seu código e como configurou sua infraestrutura. Problema de superfícies de estado de funcionamento de recursos do Azure com a própria plataforma do Azure, que não são específicos para seus aplicativos individuais.

![Painel de captura de ecrã do Azure Resource Health](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Para apresentar o estado de funcionamento do Azure recursos, consulte a **Mostrar o Azure Resource Health** caixa superior da tabela. Esta coluna está oculta por predefinição, para ajudar a página carregada rapidamente.

![Captura de ecrã com o gráfico de estado de funcionamento de recursos adicionado](./media/resource-group-insights/0003-overview.png)

Por predefinição, os recursos são agrupados por tipo de recurso e de camada de aplicação. **Camada de aplicação** a categorização simples de tipos de recursos, que só existe dentro do contexto da página de descrição geral de informações do grupo de recursos. Existem tipos de recursos relacionados ao código do aplicativo, infraestrutura, redes, armazenamento + bases de dados de computação. Ferramentas de gestão obtém suas próprias camadas da aplicação e todos os outros recursos é categorizado como pertencentes à **outros** camada de aplicação. Este agrupamento pode ajudá-lo a ver em breve os subsistemas da sua aplicação estão em bom estado e mau estado de funcionamento.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnosticar problemas no seu grupo de recursos

A página de informações do grupo de recursos fornece várias outras ferramentas para ajudar a diagnosticar problemas de âmbito

   |         |          |
   | ---------------- |:-----|
   | [**Alertas**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Ver, criar e gerir os alertas. |
   | [**Métricas**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Visualize e explore os seus dados com base métrica.    |
   | [**Registos de atividades**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Subscrição nível eventos que ocorreram no Azure.  |
   | [**Application map**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) (Mapeamento de aplicações) | Navegue até a topologia da sua aplicação distribuída para identificar afunilamentos de desempenho ou de hotspots de falha. |

## <a name="failures-and-performance"></a>Desempenho e falhas

E se reparou que seu aplicativo está sendo lentamente executada ou que os usuários relataram erros? É demorado Procurar em todos os seus recursos para isolar problemas.

O **desempenho** e **falhas** separadores simplificam este processo ao reunir o desempenho e falha vistas de diagnóstico para muitos tipos de recursos comuns.

A maioria dos tipos de recursos serão aberto um modelos da galeria do Azure Monitor livro. Cada pasta de trabalho que cria pode ser personalizada, guardado, partilhado com a sua equipa e reutilizados no futuro para diagnosticar problemas semelhantes.

### <a name="investigate-failures"></a>Investigar falhas

Para testar a selecionar o separador de falhas **falhas** sob **investigar** no menu da esquerda.

Barra de menus do esquerda é alterado após a seleção é feita, oferecendo a novas opções.

![Painel de descrição geral de captura de ecrã de falha](./media/resource-group-insights/00004-failures.png)

Quando o serviço de aplicações for escolhido, é apresentada com um modelo de galeria do Azure Monitor livro.

![Captura de ecrã da Galeria de pasta de trabalho de aplicações](./media/resource-group-insights/0005-failure-insights-workbook.png)

Escolher o modelo para as informações de falha, abrirá o livro.

![Captura de ecrã do relatório de falha](./media/resource-group-insights/0006-failure-visual.png)

Pode selecionar qualquer uma das linhas. A seleção, em seguida, é apresentada numa vista de detalhes do gráfico.

![Captura de ecrã de detalhes da falha](./media/resource-group-insights/0007-failure-details.png)

Pastas de trabalho abstraem o difícil trabalho de criação de relatórios personalizados e visualizações para um formato de facilmente consumível. Embora alguns usuários poderão apenas pretender ajustar os parâmetros criados previamente, pastas de trabalho são totalmente personalizáveis.

Para obter uma noção de como este livro funciona internamente, selecione **editar** na barra superior.

![Captura de ecrã da opção de editar adicionais](./media/resource-group-insights/0008-failure-edit.png)

Diversas **editar** aparecem de caixas de perto os vários elementos da pasta de trabalho. Selecione o **editar** caixa abaixo da tabela de operações.

![Captura de ecrã das caixas de edição](./media/resource-group-insights/0009-failure-edit-graph.png)

Isso revela a consulta do log subjacente que está a orientar a visualização de tabela.

 ![Captura de ecrã da janela de consulta de registo](./media/resource-group-insights/0010-failure-edit-query.png)

É possível modificar a consulta diretamente. Ou pode usá-la como uma referência e emprestam ao conceber a sua própria pasta de trabalho parametrizada personalizada.

### <a name="investigate-performance"></a>Investigar o desempenho

Desempenho oferece sua própria Galeria de pastas de trabalho. Serviço de aplicações, a pasta de trabalho de desempenho de aplicações pré-criadas oferece a vista seguinte:

 ![Captura de ecrã da vista de desempenho](./media/resource-group-insights/0011-performance.png)

Neste caso, se selecionar edição verá que este conjunto de visualizações utiliza a tecnologia de monitorizar as métricas do Azure.

 ![Captura de ecrã da vista de desempenho com a métrica do Azure](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="enabling-access-to-alerts"></a>Permitir o acesso aos alertas

Para ver os alertas no Azure Monitor para grupos de recursos, alguém com uma função de proprietário ou contribuinte para esta subscrição tem de abrir o Azure Monitor para grupos de recursos para qualquer grupo de recursos na subscrição. Isso permitirá que qualquer pessoa com acesso de leitura para ver alertas no Azure Monitor para grupos de recursos para todos os grupos de recursos na subscrição. Se tiver uma função de proprietário ou contribuinte, atualize esta página dentro de alguns minutos.

O Azure Monitor para grupos de recursos se baseia no sistema de gestão de alertas do Azure Monitor para obter o estado do alerta. Gestão de alertas não está configurado para cada grupo de recursos e subscrição por predefinição e só pode ser ativada por alguém com uma função de proprietário ou contribuinte. Pode ser ativado por:
* Abrir o Monitor do Azure para grupos de recursos para qualquer grupo de recursos na subscrição.
* Ou ao aceder à subscrição, ao clicar **fornecedores de recursos**, em seguida, clicar em **se registrar para Alerts.Management**.

## <a name="next-steps"></a>Passos Seguintes

- [Pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Alertas de Monitor do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
