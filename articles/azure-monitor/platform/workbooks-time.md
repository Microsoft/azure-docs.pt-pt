---
title: Parâmetros de tempo dos livros do Azure Monitor
description: Aprenda a definir parâmetros de tempo para permitir que os utilizadores desatem o contexto de tempo da análise. Os parâmetros de tempo são usados por quase todos os relatórios.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 471b775abfebec646ab3950803622271c0bd559b
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932299"
---
# <a name="workbook-time-parameters"></a>Parâmetros de tempo do livro

Os parâmetros de tempo permitem que os utilizadores desatem o contexto de tempo da análise e são utilizados por quase todos os relatórios. É relativamente simples de configurar e usar - permitindo que os autores especifiquem os intervalos de tempo para mostrar no drop-down, incluindo a opção para intervalos de tempo personalizados. 

## <a name="creating-a-time-parameter"></a>Criar um parâmetro de tempo
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _adicionar parâmetro._
4. No novo painel de parâmetros que aparece, insira:
    1. Nome do parâmetro: `TimeRange`
    2. Tipo de parâmetro: `Time range picker`
    3. Necessário: `checked`
    4. Intervalos de tempo disponíveis: Última hora, Últimas 12 horas, Últimas 24 horas, Últimas 48 horas, Últimos 3 dias, Últimos 7 dias e Permitir a seleção de intervalos de tempo personalizados
5. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando a criação de um parâmetro de intervalo de tempo](./media/workbooks-time/time-settings.png)

É assim que o livro será em modo de leitura.

![Imagem mostrando um parâmetro de intervalo de tempo no modo de leitura](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Referenciando um parâmetro de tempo
### <a name="via-bindings"></a>Via Encadernações
1. Adicione um controlo de consulta ao livro de trabalho e selecione um recurso Application Insights.
2. A maioria dos controlos de livros suportam um selecionador _de alcance de tempo._ Abra o _intervalo do intervalo_ do intervalo do tempo e selecione o grupo de `{TimeRange}` parâmetros de toque de tempo na parte inferior.
3. Isto liga o parâmetro do intervalo de tempo ao intervalo de tempo do gráfico. O âmbito de tempo da consulta da amostra é agora dura 24 horas.
4. Fazer consulta para ver os resultados

    ![Imagem mostrando um parâmetro de intervalo de tempo referenciado através de encadernações](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>Em KQL
1. Adicione um controlo de consulta ao livro de trabalho e selecione um recurso Application Insights.
2. No KQL, introduza um filtro de âmbito temporal utilizando o parâmetro: `| where timestamp {TimeRange}`
3. Isto expande-se no tempo de avaliação de consulta para `| where timestamp > ago(1d)` , que é o valor da gama de tempo do parâmetro.
4. Fazer consulta para ver os resultados

    ![Imagem mostrando um intervalo de tempo referenciado em KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>Em Texto 
1. Adicione um controlo de texto ao livro.
2. Na marcação, insira `The chosen time range is {TimeRange:label}`
3. Escolha _A Edição Feita_
4. O controlo de texto mostrará texto: _O intervalo de tempo escolhido é de últimas 24 horas_

## <a name="time-parameter-options"></a>Opções de parâmetros de tempo
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Etiqueta de intervalo de tempo | Últimas 24 horas |
| `{TimeRange:label}` | Etiqueta de intervalo de tempo | Últimas 24 horas |
| `{TimeRange:value}` | Valor da gama de tempo | > há >(1d) |
| `{TimeRange:query}` | Consulta do intervalo de tempo | > há >(1d) |
| `{TimeRange:start}` | Tempo de início do intervalo | 3/20/2019 16:18 |
| `{TimeRange:end}` | Tempo de fim do intervalo | 3/21/2019 16:18 |
| `{TimeRange:grain}` | Grão de intervalo de tempo | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Usando opções de parâmetros numa consulta
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.
