---
title: Tempo de inatividade, SLA e livro de paragem - Insights de Aplicação
description: Calcular e reportar SLA para teste web através de um único painel de vidro através dos recursos de Insights de Aplicação e subscrições Azure.
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714056"
---
# <a name="downtime-sla-and-outages-workbook"></a>Tempo de inatividade, SLA e paragens

Introduzindo uma forma simples de calcular e reportar SLA (acordo de nível de serviço) para testes web através de um único painel de vidro através dos seus recursos de Insights de Aplicação e subscrições Azure. O relatório Downtime and Outage fornece consultas e visualizações de dados pré-construídas poderosas para melhorar a sua compreensão da conectividade do seu cliente, o tempo típico de resposta da aplicação e tempo de inatividade experimentado.

O modelo de livro SLA está acessível através da galeria de livros no seu recurso Application Insights ou através do separador de disponibilidade selecionando **relatórios SLA** no topo.
:::image type="content" source="./media/sla-report/availability.png" alt-text="Screenshot do separador de disponibilidade com relatórios SLA destacados." lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="Screenshot da galeria de livros com tempo de inatividade e livro de paragens em destaque." lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>Flexibilidade dos parâmetros

Os parâmetros definidos no livro influenciam o resto do seu relatório.

:::image type="content" source="./media/sla-report/outages.png" alt-text=" Screenshot do separador de parâmetros de paragem/manutenção no tempo de inatividade e no livro de trabalho de paragens." lightbox ="./media/sla-report/outages.png":::

`Subscriptions`, `App Insights Resources` e `Web Test` os parâmetros determinam as suas opções de recursos de alto nível. Estes parâmetros baseiam-se em consultas de análise de registo e são utilizados em todas as consultas de relatório.

`Failure Threshold` e `Outage Window` permitir-lhe determinar os seus próprios critérios para uma interrupção de serviço, por exemplo, os critérios para o alerta de Disponibilidade de Insights de App com base no contador de localização falhado durante um período escolhido. O limiar típico é de três localizações sobre uma janela de cinco minutos.

`Maintenance Period` permite-lhe selecionar a sua frequência de manutenção típica e `Maintenance Window` é um seletor de datas para um período de manutenção por exemplo. Todos os dados que ocorrerem durante o período identificado serão ignorados nos seus resultados.

`Availability Target 9s` especifica o seu objetivo Target 9s de dois 9s a 5 9s.

## <a name="overview-page"></a>Página de descrição geral

A página geral contém informações de alto nível sobre o seu SLA total (excluindo períodos de manutenção se definidos), instâncias de fim a fim e tempo de inatividade da aplicação. As situações de paragem são definidas quando um teste começa a falhar até que seja bem sucedido com base nos parâmetros de paragem. Se um teste começar a falhar às 8:00 da manhã e voltar a ter sucesso às 10:00, então todo o período de dados é considerado a mesma falha.

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" GIF da página geral mostrando a tabela geral por teste." lightbox="./media/sla-report/overview.gif":::

Também pode investigar a sua paragem mais longa que ocorreu durante o seu período de reporte.

Alguns testes são ligados de volta ao seu recurso Application Insights para uma investigação mais aprofundada, mas isso só é possível no [recurso Workspace-based Application Insights](create-workspace-resource.md).

## <a name="downtime-outages-and-failures"></a>Tempo de paragem, interrupções e falhas

O **separador Outages and Downtime** tem informações sobre as instâncias de paragem total e o tempo total de paragem discriminado por teste. O **separador Falhas por Localização** tem um geo-mapa de locais de testes falhados para ajudar a identificar potenciais áreas de ligação de problemas.

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" GIF de Outages e Downtime tab e Falha por Localização no tempo de inatividade e paragens." lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>Editar o relatório

Pode editar o relatório como qualquer outro [Livro de Trabalho do Monitor Azure](../visualize/workbooks-overview.md). Pode personalizar as consultas ou visualizações com base nas necessidades da sua equipa.

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" GIF de selecionar o botão de edição para alterar a visualização para um gráfico de tartes." lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

As consultas podem ser executadas no [Log Analytics](../logs/log-analytics-overview.md) e usadas em outros relatórios ou dashboards. Retire a restrição do parâmetro e reutilizá-lo na consulta do núcleo.

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" GIF de consulta de registo." lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>Acesso e partilha

O relatório pode ser partilhado com as suas equipas, liderança ou preso a um dashboard para posterior utilização. O utilizador precisa de ter a permissão/acesso ao recurso Applications Insights onde o livro real está armazenado.

:::image type="content" source="./media/sla-report/share.png" alt-text=" Screenshot de partilhar este modelo." lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>Passos seguintes

- [Dicas de otimização de consultas de log analytics](../logs/query-optimization.md).
- Saiba como [criar um gráfico em livros de trabalho.](../visualize/workbooks-chart-visualizations.md)
- Saiba como monitorizar o seu website com [testes de disponibilidade.](monitor-web-app-availability.md)