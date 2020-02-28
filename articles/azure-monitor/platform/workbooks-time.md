---
title: Parâmetros de tempo dos livros Azure Monitor
description: Simplificar relatórios complexos com livros pré-construídos e parametrizados personalizados
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658018"
---
# <a name="workbook-time-parameters"></a>Parâmetros de tempo do livro

Os parâmetros temporais permitem aos utilizadores definir o contexto de tempo da análise e é utilizado por quase todos os relatórios. É relativamente simples configurar e usar - permitindo que os autores especifiquem os intervalos de tempo para mostrar no drop-down, incluindo a opção para intervalos de tempo personalizados. 

## <a name="creating-a-time-parameter"></a>Criar um parâmetro temporal
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _Adicionar Parâmetro._
4. Na nova vidraça de parâmetroque aparece:
    1. Nome do parâmetro: `TimeRange`
    2. Tipo de parâmetro: `Time range picker`
    3. Obrigatório: `checked`
    4. Intervalos de tempo disponíveis: Última hora, Últimas 12 horas, Últimas 24 horas, Últimas 48 horas, Últimos 3 dias, Últimos 7 dias e Permitir a seleção de horários personalizados
5. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando a criação de um parâmetro de intervalo de tempo](./media/workbooks-time/time-settings.png)

É assim que o livro vai ficar em modo de leitura.

![Imagem mostrando um parâmetro de intervalo de tempo no modo de leitura](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Referenciando um parâmetro temporal
### <a name="via-bindings"></a>Via Encadernações
1. Adicione um controlo de consulta ao livro e selecione um recurso Application Insights.
2. A maioria dos controlos do livro suportam um apanhador de alcance de _tempo._ Abra a descida do Intervalo de _Tempo_ e selecione o `{TimeRange}` no grupo de parâmetros tocados na parte inferior.
3. Isto liga o parâmetro de intervalo de tempo à faixa de tempo do gráfico. O intervalo de tempo da consulta da amostra é agora de 24 horas.
4. Corra para ver os resultados

    ![Imagem mostrando um parâmetro de intervalo de tempo referenciado através de encadernações](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>Em KQL
1. Adicione um controlo de consulta ao livro e selecione um recurso Application Insights.
2. No KQL, introduza um filtro de alcance de tempo utilizando o parâmetro: `| where timestamp {TimeRange}`
3. Isto expande-se no tempo de avaliação de consulta para `| where timestamp > ago(1d)`, que é o valor de tempo do parâmetro.
4. Corra para ver os resultados

    ![Imagem mostrando um intervalo de tempo referenciado em KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>Em Texto 
1. Adicione um controlo de texto ao livro.
2. Na marcação, entre `The chosen time range is {TimeRange:label}`
3. Escolha _edição feita_
4. O controlo de texto mostrará texto: O intervalo de _tempo escolhido é de 24 horas_

## <a name="time-parameter-options"></a>Opções de parâmetros de tempo
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Etiqueta de intervalo de tempo | Últimas 24 horas |
| `{TimeRange:label}` | Etiqueta de intervalo de tempo | Últimas 24 horas |
| `{TimeRange:value}` | Valor do intervalo de tempo | > há 1d) |
| `{TimeRange:query}` | Consulta de intervalo de tempo | > há 1d) |
| `{TimeRange:start}` | Hora do tempo de início | 15/09/2019 16:18 |
| `{TimeRange:end}` | Tempo de fim do intervalo | 15/21/2019 16:18 |
| `{TimeRange:grain}` | Grão de intervalo de tempo | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Usando opções de parâmetros em uma consulta
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos recursos do seu livro.
