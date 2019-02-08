---
title: Teste de batch
titleSuffix: Language Understanding - Azure Cognitive Services
description: Utilize o batch de compreensão de idiomas (LUIS) conjuntos de testes para encontrar expressões com incorretas intenções e entidades.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: fddd54e7c4e88b85f6e6fa8f7319661db96e9524
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877546"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Lote de teste com um conjunto de expressões de exemplo

 Teste de batch é um teste abrangente em seu modelo treinado atual para medir o desempenho no LUIS. Os conjuntos de dados usados para teste de lote não deve incluir expressões com os objetivos ou expressões com receberam do ponto final de tempo de execução de predição de exemplo. 

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importar um ficheiro de conjunto de dados para fins de teste do batch

1. Selecione **teste** na parte superior da barra e, em seguida, selecione **painel de teste do Batch**.

    ![Ligação de teste de batch](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Selecione **conjunto de dados de importação**. O **novo conjunto de dados importação** é apresentada a caixa de diálogo. Selecione **Escolher ficheiro** e localize um ficheiro JSON com o correto [formato JSON](luis-concept-batch-test.md#batch-file-format) que contém *não mais do que 1.000* expressões para testar.

    Erros de importação são apresentados numa barra de notificação vermelho na parte superior do navegador. Quando uma importação tem erros, não é criado nenhum conjunto de dados. Para obter mais informações, consulte [erros comuns](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. Na **nome do conjunto de dados** , insira um nome para o ficheiro de conjunto de dados. O arquivo do conjunto de dados inclui um **matriz de expressões** incluindo o *rotulado intenção* e *entidades*. Reveja a [exemplo de ficheiro de batch](luis-concept-batch-test.md#batch-file-format) sintaxe. 

4. Selecione **Done** (Concluído). O ficheiro de conjunto de dados é adicionado.

## <a name="run-rename-export-or-delete-dataset"></a>Executar, mudar o nome, exportar ou eliminar o conjunto de dados

Para executar, mudar o nome, exportar ou eliminar o conjunto de dados, utilize o botão de reticências (***...*** ) botão no final da linha de conjunto de dados.

![Ações de conjunto de dados](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Executar um teste de batch em seu aplicativo preparado

Para executar o teste, selecione o nome do conjunto de dados. Quando concluir o teste, esta linha apresenta o resultado do teste do conjunto de dados.

![Resultado do teste de batch](./media/luis-how-to-batch-test/run-test.png)

O conjunto de dados que pode ser baixado é o mesmo ficheiro que foi carregado para o teste do batch.

|Estado|Significado|
|--|--|
|![Ícone do círculo verde de teste com êxito](./media/luis-how-to-batch-test/batch-test-result-green.png)|Todas as expressões são com êxito.|
|![Teste falhar Vermelho x ícone](./media/luis-how-to-batch-test/batch-test-result-red.png)|A intenção, pelo menos, uma expressão não correspondeu a predição.|
|![Pronto para testar o ícone](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Teste está pronto para ser executado.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Ver resultados do teste de batch 

Para rever os resultados de teste do batch, selecione **ver resultados**.

![Resultados do teste de batch](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Filtrar os resultados de gráfico

Para filtrar o gráfico por um objetivo específico ou uma entidade, selecione a intenção ou a entidade no painel de filtragem do lado direito. Os pontos de dados e a respetiva distribuição atualizar no gráfico, de acordo com sua seleção. 
 
![Resultado do teste de Batch visualizados](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Dados de expressão de ponto único de exibição

No gráfico, Paire o rato sobre um ponto de dados para ver a classificação de certeza da sua predição. Selecione um ponto de dados para recuperar a sua expressão correspondente na lista de expressões com na parte inferior da página. 

![Expressão selecionada](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Ver dados de secção

No gráfico de seção de quatro, selecione o nome da seção, como **falsos positivos** na parte superior direita do gráfico. Abaixo do gráfico, todas as expressões com nesta secção apresentam abaixo do gráfico numa lista. 

![Expressões com selecionado pela secção](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Nesta imagem anterior, a expressão `switch on` está etiquetado com a intenção de TurnAllOn, mas foram recebidos a predição de None intenção. Esta é uma indicação de que a intenção de TurnAllOn precisa de mais expressões de exemplo para fazer a predição esperada. 

As duas secções do gráfico em vermelho indicam expressões que não correspondeu a predição esperada. Estes erros indicam expressões com os LUIS precisa de mais treinamento. 

As duas secções do gráfico em verde correspondeu a predição esperada.

## <a name="next-steps"></a>Passos Seguintes

Se o teste indica que a sua aplicação LUIS não reconhece o corretas intenções e entidades, pode trabalhar para melhorar o desempenho da sua aplicação LUIS expressões com mais de etiquetagem ou adicionar recursos. 

* [Identifique expressões sugeridas com os LUIS](luis-how-to-review-endoint-utt.md) 
* [Utilizar as funcionalidades para melhorar o desempenho da sua aplicação LUIS](luis-how-to-add-features.md) 
* [Compreender o teste com este tutorial do batch](luis-tutorial-batch-testing.md)
* [Aprenda conceitos de teste de batch](luis-concept-batch-test.md).
