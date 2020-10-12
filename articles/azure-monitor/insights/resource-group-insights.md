---
title: Azure Monitor Resource Group Insights / Microsoft Docs
description: Compreender a saúde e o desempenho das suas aplicações e serviços distribuídos ao nível do Grupo de Recursos com o Azure Monitor
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: bf8ea45f37e421d70a68c4a6e138f5203e92d78f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045777"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Monitorize grupos de recursos com monitor Azure (pré-visualização)

As aplicações modernas são muitas vezes complexas e altamente distribuídas com muitas partes discretas trabalhando em conjunto para prestar um serviço. Reconhecendo esta complexidade, o Azure Monitor fornece informações de monitorização para grupos de recursos. Isto facilita a triagem e o diagnóstico de quaisquer problemas que os seus recursos individuais encontrem, ao mesmo tempo que oferece contexto quanto à saúde e desempenho do grupo de recursos e da &mdash; sua aplicação &mdash; como um todo.

## <a name="access-insights-for-resource-groups"></a>Informações de acesso a grupos de recursos

1. Selecione **grupos**  de recursos da barra de navegação do lado esquerdo.
2. Escolha um dos seus grupos de recursos que pretende explorar. (Se tiver um grande número de grupos de recursos filtrando por subscrição pode por vezes ser útil.)
3. Para aceder a insights para um grupo de recursos, clique em **Insights** no menu do lado esquerdo de qualquer grupo de recursos.

![Screenshot da página geral de insights de grupo de recursos](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Recursos com alertas ativos e problemas de saúde

A página geral mostra quantos alertas foram disparados e ainda estão ativos, juntamente com a atual Azure Resource Health de cada recurso. Juntos, esta informação pode ajudá-lo a detetar rapidamente quaisquer recursos que estejam a passar por problemas. Os alertas ajudam-no a detetar problemas no seu código e como configuraram a sua infraestrutura. As superfícies de Saúde do Recurso Azure problemam com a própria plataforma Azure, que não são específicas das suas aplicações individuais.

![Screenshot do painel de saúde do recurso Azure](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Para exibir a Azure Resource Health, consulte a caixa **de Saúde do Recurso Show Azure** acima da tabela. Esta coluna é escondida por defeito para ajudar a carregar a página rapidamente.

![Screenshot com gráfico de saúde de recurso adicionado](./media/resource-group-insights/0003-overview.png)

Por padrão, os recursos são agrupados por camada de aplicativo e tipo de recurso. **A camada de aplicativo** é uma simples categorização de tipos de recursos, que só existe no contexto da página de visão geral do grupo de recursos. Existem tipos de recursos relacionados com código de aplicação, infraestrutura de computação, networking, armazenamento + bases de dados. As ferramentas de gestão obtêm as suas próprias camadas de aplicativos, e todos os outros recursos são categorizados como pertencentes à camada de **aplicações Other.** Este agrupamento pode ajudá-lo a ver de relance quais subsistemas da sua aplicação são saudáveis e insalubres.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnosticar problemas no seu grupo de recursos

A página de insights do grupo de recursos fornece várias outras ferramentas telescópios para ajudá-lo a diagnosticar problemas

   |         |          |
   | ---------------- |:-----|
   | [**Alertas**](../platform/alerts-overview.md)      |  Veja, crie e gere os seus alertas. |
   | [**Métricas**](../platform/data-platform.md) | Visualize e explore os seus dados métricos.    |
   | [**Troncos de atividade**](../platform/platform-logs-overview.md) | Eventos de nível de subscrição que ocorreram em Azure.  |
   | [**Mapa de aplicação**](../app/app-map.md) | Navegue na topologia da sua aplicação distribuída para identificar estrangulamentos de desempenho ou hotspots de falha. |

## <a name="failures-and-performance"></a>Falhas e desempenho

E se tiver reparado que a sua aplicação está a correr lentamente ou se os utilizadores reportarem erros? É demorado procurar todos os seus recursos para isolar problemas.

Os **separadores** **performance** e falhas simplificam este processo, reunindo visões de diagnóstico de desempenho e falha para muitos tipos de recursos comuns.

A maioria dos tipos de recursos abrirá uma galeria de modelos de livro do Monitor Azure. Cada livro que criar pode ser personalizado, guardado, partilhado com a sua equipa e reutilizado no futuro para diagnosticar problemas semelhantes.

### <a name="investigate-failures"></a>Investigar falhas

Para testar o separador Falhas selecione **Falhas** no menu **da** mão esquerda.

A barra de menu do lado esquerdo muda após a sua seleção, oferecendo-lhe novas opções.

![Screenshot do painel de visão geral do fracasso](./media/resource-group-insights/00004-failures.png)

Quando o Serviço de Aplicações é escolhido, é-lhe apresentada uma galeria de modelos de livro do Monitor Azure.

![Screenshot da galeria de livros de aplicação](./media/resource-group-insights/0005-failure-insights-workbook.png)

A escolha do modelo para Insights de Falhas abrirá o livro.

![Screenshot do relatório de falha](./media/resource-group-insights/0006-failure-visual.png)

Pode selecionar qualquer uma das linhas. A seleção é então exibida numa vista de detalhes gráficos.

![Screenshot dos detalhes do falha](./media/resource-group-insights/0007-failure-details.png)

Os livros de trabalho abstraem o difícil trabalho de criar relatórios e visualizações personalizadas num formato facilmente consumível. Embora alguns utilizadores apenas queiram ajustar os parâmetros pré-construídos, os livros são completamente personalizáveis.

Para ter uma ideia de como este livro funciona internamente, **selecione Editar** na barra superior.

![Screenshot da opção de edição adicional](./media/resource-group-insights/0008-failure-edit.png)

Várias caixas de **edição** aparecem perto dos vários elementos do livro. Selecione a caixa **editar** abaixo da tabela de operações.

![Screenshot das caixas de edição](./media/resource-group-insights/0009-failure-edit-graph.png)

Isto revela a consulta de log subjacente que está a impulsionar a visualização da tabela.

 ![Screenshot da janela de consulta de log](./media/resource-group-insights/0010-failure-edit-query.png)

Pode modificar a consulta diretamente. Ou pode usá-lo como referência e pedir emprestado ao desenhar o seu próprio livro de trabalho parametrizado personalizado.

### <a name="investigate-performance"></a>Investigar desempenho

A performance oferece a sua própria galeria de livros. Para o Serviço de Aplicações, o livro de desempenho de aplicação pré-construído oferece a seguinte vista:

 ![Screenshot da vista de desempenho](./media/resource-group-insights/0011-performance.png)

Neste caso, se selecionar a edição, verá que este conjunto de visualizações é alimentado por Métricas do Monitor Azure.

 ![Screenshot da vista de desempenho com Azure Metrics](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="enabling-access-to-alerts"></a>Permitir o acesso a alertas

Para ver alertas no Azure Monitor para Grupos de Recursos, alguém com uma função de Proprietário ou Contribuinte para esta subscrição precisa de abrir o Azure Monitor para grupos de recursos para qualquer grupo de recursos na subscrição. Isto permitirá que qualquer pessoa com acesso lido veja alertas no Azure Monitor para grupos de recursos para todos os grupos de recursos na subscrição. Se tiver uma função de Proprietário ou Contribuinte, refresque esta página em poucos minutos.

O Azure Monitor for Resource Groups conta com o sistema de gestão de alertas de monitores Azure para recuperar o estado de alerta. A Gestão de Alertas não está configurada para cada grupo de recursos e subscrição por padrão, e só pode ser ativada por alguém com um papel de Proprietário ou Contribuinte. Pode ser ativado quer:
* Abertura do Monitor Azure para grupos de recursos para qualquer grupo de recursos na subscrição.
* Ou indo para a subscrição, clicando em **Fornecedores de Recursos,** clicando em **Registar para Alertas.Management**.

## <a name="next-steps"></a>Passos seguintes

- [Livros de trabalho do Monitor Azure](../platform/workbooks-overview.md)
- [Azure Resource Health](../../service-health/resource-health-overview.md)
- [Alertas do Azure Monitor](../platform/alerts-overview.md)
