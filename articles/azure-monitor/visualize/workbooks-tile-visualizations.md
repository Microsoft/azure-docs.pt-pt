---
title: Visualizações de azulejos do Azure Monitor
description: Saiba mais sobre todas as visualizações de azulejos do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100615941"
---
# <a name="tile-visualizations"></a>Visualizações de azulejos

Os azulejos são uma forma útil de apresentar dados sumários nos livros. A imagem abaixo mostra um caso de uso comum de azulejos com resumo do nível da aplicação em cima de uma grelha detalhada.

[![Screenshot da vista sumária do azulejo](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

Suporte de azulejos de livros de trabalho mostrando um título, legenda, texto grande, ícones, gradientes de base métrica, linha de faísca/barras, rodapé, etc.

## <a name="adding-a-tile"></a>Adicionando um azulejo

1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. **Selecione Adicione** então *Adicione consulta* para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Ajuste tamanho para **completo**.
6. Desa estaleia a visualização de **Azulejos.**
7. Selecione o botão **Definições de Azulejos** para abrir o painel de definições.
    1. No *Título*, conjunto:
        * Coluna de utilização: `name` .
    2. Em *Esquerda,* conjunto:
        * Coluna de utilização: `Requests` .
        * Renderizador de colunas: `Big Number` .
        * Paleta de cores: `Green to Red`
        * Valor mínimo: `0` .
    3. Em *Bottom*, set:
        * Coluna de utilização: `appName` .
8. Selecione o botão **Guardar e Fechar** na parte inferior do painel.

[![Screenshot da vista de resumo do azulejo com regulações acima da consulta e azulejos.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

Os azulejos em modo de leitura:

[![Screenshot da vista de resumo do azulejo no modo de leitura.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Linhas de faísca em azulejos

1. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
2. Adicione um parâmetro de intervalo de tempo chamado `TimeRange` .
    1. **Selecione Adicionar** e, em seguida, *Adicionar parâmetros*.
    2. No controlo de parâmetros, selecione **Adicionar Parâmetro**.
    3. Introduza `TimeRange` no campo nome do *parâmetro* e escolha o tipo `Time range picker` de *parâmetro*.
    4. **Selecione Guardar** na parte superior do painel e, em seguida, selecione **'Edição' de feito** no controlo dos parâmetros.
3. **Selecione Adicione** então *Adicionar consulta* para adicionar um controlo de consulta de log abaixo do controlo de parâmetros.
4. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
5. Utilize o editor de consulta para introduzir o KQL para a sua análise.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Selecione **Executar Consulta**. (Certifique-se de definir `TimeRange` um valor à sua escolha antes de executar a consulta.)
7. Desa estade a *Visualização* a "Azulejos".
8. Desa ajuste o *tamanho* para "Full".
9. Selecione **Definições de azulejos**.
    1. Em *Azulejo,* conjunto:
        * Coluna de utilização: `name` .
    2. Em *Subtile,* conjunto:
        *  Coluna de utilização: `appNAme` .
    3. Em *Esquerda,* conjunto:
        *  Coluna de utilização: `Requests` .
        * Renderizador de colunas: `Big Number` .
        * Paleta de cores: `Green to Red` .
        * Valor mínimo: `0` .
    4. Em *Bottom*, set:
        * Coluna de utilização: `Trend` .
        * Renderizador de colunas: `Spark line` .
        * Paleta de cores: `Green to Red` .
        * Valor mínimo: `0` .
10. **Selecione Guardar e Fechar** na parte inferior do painel.

![Screenshot da visualização de azulejos com uma linha de faísca](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Tamanhos de azulejos

O autor tem a opção de definir a largura do azulejo nas definições de azulejos.

* `fixed` (predefinição)

    O comportamento padrão dos azulejos deve ser a mesma largura fixa, aproximadamente 160 pixels de largura, mais o espaço em torno dos azulejos.

    ![Screenshot exibindo azulejos de largura fixa](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Cada título encolherá ou crescerá para se adaptar ao seu conteúdo, no entanto, os azulejos estão limitados à largura da vista dos azulejos (sem deslocamento horizontal).

    ![Screenshot exibindo azulejos de largura automática](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Cada título será sempre a largura total da vista dos azulejos, um título por linha.

     ![Screenshot exibindo azulejos de largura de tamanho completo](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Passos seguintes

* Os azulejos também suportam o renderizador de barras compósitos. Para saber mais visite [a documentação do Bar Composto.](workbooks-composite-bar.md)
* Para saber mais sobre parâmetros de tempo como `TimeRange` visitar [a documentação dos parâmetros do tempo](workbooks-time.md)do livro.