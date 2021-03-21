---
title: Parâmetros de texto dos livros do Azure Monitor
description: Simplificar relatórios complexos com livros de trabalho parametrizados pré-construídos e personalizados. Saiba mais sobre os parâmetros de texto do livro.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 22078f242ddeb882b39b85769537b9a282741250
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725531"
---
# <a name="workbook-text-parameters"></a>Parâmetros de texto do livro

Os parâmetros da caixa de texto fornecem uma forma simples de recolher a entrada de texto dos utilizadores do livro. São utilizados quando não é prático utilizar uma gota para recolher a entrada (por exemplo, um limiar arbitrário ou filtros genéricos). Os livros permitem que os autores obtenham o valor padrão da caixa de texto a partir de uma consulta. Isto permite cenários interessantes como a definição do limiar padrão com base no p95 da métrica.

Um uso comum de caixas de texto é como variáveis internas usadas por outros controlos de livros. Isto é feito aproveitando uma consulta para valores predefinidos, e tornando o controlo de entrada invisível em modo de leitura. Por exemplo, um utilizador pode querer que um limiar venha de uma fórmula (não um utilizador) e, em seguida, utilizar o limiar em consultas posteriores.

## <a name="creating-a-text-parameter"></a>Criação de um parâmetro de texto
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _adicionar parâmetro._
4. No novo painel de parâmetros que aparece, insira:
    1. Nome do parâmetro: `SlowRequestThreshold`
    2. Tipo de parâmetro: `Text`
    3. Necessário: `checked`
    4. Obtenha o valor predefinido da consulta: `unchecked`
5. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando a criação de um parâmetro de texto](./media/workbooks-text/text-create.png)

É assim que o livro será em modo de leitura.

![Imagem mostrando um parâmetro de texto no modo de leitura](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Referenciando um parâmetro de texto
1. Adicione um controlo de consulta ao livro de trabalho selecionando o link azul `Add query` e selecione um recurso Application Insights.
2. Na caixa KQL, adicione este corte:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Ao utilizar o parâmetro de texto com um valor de 500 juntamente com o controlo de consulta, executa efetivamente a consulta abaixo:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Fazer consulta para ver os resultados

    ![Imagem mostrando um parâmetro de texto referenciado em KQL](./media/workbooks-text/text-reference.png)

> [!NOTE]
> No exemplo acima, `{SlowRequestThreshold}` representa um valor inteiro. Se estivesse a consultar uma cadeia como `{ComputerName}` precisaria de modificar a sua consulta Kusto para adicionar cotações `"{ComputerName}"` para que o campo de parâmetros aceitasse uma entrada sem cotações.

## <a name="setting-default-values"></a>Definição de valores predefinidos
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _adicionar parâmetro._
4. No novo painel de parâmetros que aparece, insira:
    1. Nome do parâmetro: `SlowRequestThreshold`
    2. Tipo de parâmetro: `Text`
    3. Necessário: `checked`
    4. Obtenha o valor predefinido da consulta: `checked`
5. Na caixa KQL, adicione este corte:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Esta consulta define o valor padrão da caixa de texto para a duração do percentil 95 para todos os pedidos na app.
6. Executar consulta para ver o resultado
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando um parâmetro de texto com valor padrão de KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Embora este exemplo questione os dados do Application Insights, a abordagem pode ser usada para qualquer fonte de dados baseada em registos - Log Analytics, Azure Resource Graph, etc.

## <a name="next-steps"></a>Passos seguintes

* [Começar a](./workbooks-overview.md#visualizations) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](./workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.