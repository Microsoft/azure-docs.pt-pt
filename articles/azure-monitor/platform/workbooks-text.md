---
title: Parâmetros de texto dos livros Azure Monitor
description: Simplificar relatórios complexos com livros pré-construídos e parametrizados personalizados. Saiba mais sobre os parâmetros de texto do livro.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c5fb585d0eb6aeb7866c2ab04b324ee31fe903ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658054"
---
# <a name="workbook-text-parameters"></a>Parâmetros de texto do livro

Os parâmetros da caixa de texto fornecem uma forma simples de recolher a entrada de texto dos utilizadores de livros. São utilizados quando não é prático utilizar uma gota para recolher a entrada (por exemplo, um limiar arbitrário ou filtros genéricos). Os livros permitem que os autores obtenha o valor padrão da caixa de texto a partir de uma consulta. Isto permite cenários interessantes como definir o limiar padrão com base no p95 da métrica.

Uma utilização comum das caixas de texto é como variáveis internas utilizadas por outros controlos de livro. Isto é feito aproveitando uma consulta para valores padrão, e tornando o controlo de entrada invisível no modo de leitura. Por exemplo, um utilizador pode querer que um limiar venha de uma fórmula (não um utilizador) e, em seguida, utilizar o limiar em consultas subsequentes.

## <a name="creating-a-text-parameter"></a>Criação de um parâmetro de texto
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _Adicionar Parâmetro._
4. Na nova vidraça de parâmetroque aparece:
    1. Nome do parâmetro:`SlowRequestThreshold`
    2. Tipo de parâmetro:`Text`
    3. Necessário:`checked`
    4. Obtenha valor predefinido a partir de consulta:`unchecked`
5. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando a criação de um parâmetro de texto](./media/workbooks-text/text-create.png)

É assim que o livro vai ficar em modo de leitura.

![Imagem mostrando um parâmetro de texto no modo de leitura](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Referenciando um parâmetro de texto
1. Adicione um controlo de consulta ao livro, `Add query` selecionando o link azul e selecione um recurso Desinformação de Aplicação.
2. Na caixa KQL, adicione este corte:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Ao utilizar o parâmetro de texto com um valor de 500 associado ao controlo de consulta, executa efetivamente a consulta abaixo:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Corra para ver os resultados

    ![Imagem mostrando um parâmetro de texto referenciado em KQL](./media/workbooks-text/text-reference.png)


## <a name="setting-default-values"></a>Definição de valores predefinidos
1. Comece com um livro vazio no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro do livro.
3. Clique no botão azul _Adicionar Parâmetro._
4. Na nova vidraça de parâmetroque aparece:
    1. Nome do parâmetro:`SlowRequestThreshold`
    2. Tipo de parâmetro:`Text`
    3. Necessário:`checked`
    4. Obtenha valor predefinido a partir de consulta:`checked`
5. Na caixa KQL, adicione este corte:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Esta consulta define o valor padrão da caixa de texto para a duração do percentil 95 para todos os pedidos na app.
6. Executar consulta para ver o resultado
7. Escolha 'Guardar' da barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando um parâmetro de texto com valor padrão da KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Embora este exemplo questione os dados da Aplicação Insights, a abordagem pode ser usada para qualquer fonte de dados baseada em registo - Log Analytics, Azure Resource Graph, etc.

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
* [Controle](workbooks-access-control.md) e partilhe o acesso aos recursos do seu livro.
