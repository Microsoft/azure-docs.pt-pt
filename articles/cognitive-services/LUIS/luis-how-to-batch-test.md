---
title: Como realizar um teste de lote - LUIS
titleSuffix: Azure Cognitive Services
description: Use conjuntos de testes de lote de Compreensão linguística (LUIS) para encontrar expressões com intenções e entidades incorretas.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 8b34005f2796403e32b41a93e4163c7da16d40bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540953"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Teste de lote com um conjunto de expressões de exemplo

 O teste de lote é um teste abrangente no seu modelo treinado atual para medir o seu desempenho no LUIS. Os conjuntos de dados utilizados para o teste do lote não devem incluir palavras de exemplo nas intenções ou expressões recebidas do ponto final de tempo de previsão.

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importar um ficheiro conjunto de dados para testes de lote

1. Selecione **Teste** na barra superior e, em seguida, selecione **painel de testes de lote**.

    ![Ligação de teste de lote](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Selecione **Conjunto de dados de importação**. Aparece a nova caixa de diálogo **do conjunto de dados Import.** Selecione **Escolha O Ficheiro** e localize um ficheiro JSON com o formato [JSON](luis-concept-batch-test.md#batch-file-format) correto que não contenha *mais de 1.000* expressões para testar.

    Erros de importação são relatados numa barra de notificação vermelha no topo do navegador. Quando uma importação tem erros, nenhum conjunto de dados é criado. Para obter mais informações, consulte [erros comuns.](luis-concept-batch-test.md#common-errors-importing-a-batch)

3. No campo Nome do **Conjunto de Dados,** insira um nome para o seu ficheiro conjunto de dados. O ficheiro dataset inclui uma **série de expressões,** incluindo a *intenção rotulada* e *as entidades.* Reveja o [ficheiro de lote de exemplo](luis-concept-batch-test.md#batch-file-format) para sintaxe.

4. Selecione **Concluído**. O ficheiro do conjunto de dados é adicionado.

## <a name="run-rename-export-or-delete-dataset"></a>Executar, renomear, exportar ou apagar conjunto de dados

Para executar, renomear, exportar ou eliminar o conjunto de dados, utilize o botão elipse ***(...***) no final da linha de conjunto de dados.

> [!div class="mx-imgBorder"]
> ![Screenshot da lista de testes de lote com opções](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Executar um teste de lote na sua app treinada

Para executar o teste, selecione o nome do conjunto de dados e, em seguida, selecione **Executar** a partir da barra de ferramentas contextual. Quando o teste termina, esta linha apresenta o resultado do teste do conjunto de dados.

O conjunto de dados transferível é o mesmo ficheiro que foi carregado para testes de lote.

|Estado|Significado|
|--|--|
|![Ícone de círculo verde de teste bem-sucedido](./media/luis-how-to-batch-test/batch-test-result-green.png)|Todas as declarações são bem sucedidas.|
|![Falha no teste vermelho x ícone](./media/luis-how-to-batch-test/batch-test-result-red.png)|Pelo menos uma intenção de expressão não correspondeu à previsão.|
|![Pronto para testar ícone](./media/luis-how-to-batch-test/batch-test-result-blue.png)|O teste está pronto para ser feito.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Ver resultados dos testes de lote

Para rever os resultados dos testes de lote, **selecione Ver os resultados**.

<a name="filter-chart-results-by-intent-or-entity"></a>

## <a name="filter-chart-results"></a>Resultados do gráfico do filtro

Para filtrar o gráfico por uma intenção ou entidade específica, selecione a intenção ou entidade no painel de filtragem do lado direito. Os pontos de dados e a sua atualização de distribuição no gráfico de acordo com a sua seleção.

![Resultado do teste do lote visualizado](./media/luis-how-to-batch-test/filter-by-entity.png)

## <a name="view-single-point-utterance-data"></a>Ver dados de expressão de um ponto único

No gráfico, paire sobre um ponto de dados para ver a pontuação de certeza da sua previsão. Selecione um ponto de dados para recuperar a sua expressão correspondente na lista de expressões na parte inferior da página.

![Expressão selecionada](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Ver dados da secção

Na tabela de quatro secções, selecione o nome da secção, tal como **Falso Positivo** no topo direito da tabela. Abaixo da tabela, todas as expressões nessa secção mostram abaixo da tabela numa lista.

![Expressões selecionadas por secção](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Nesta imagem anterior, a expressão `switch on` é rotulada com a intenção TurnAllOn, mas recebeu a previsão de nenhuma intenção. Esta é uma indicação de que a intenção turnAllOn precisa de mais declarações de exemplo para fazer a previsão esperada.

As duas secções do gráfico em vermelho indicam expressões que não correspondem à previsão esperada. Estas indicam declarações que o LUIS precisa de mais formação.

As duas secções do gráfico em verde correspondem à previsão esperada.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Passos seguintes

Se os testes indicarem que a sua aplicação LUIS não reconhece as intenções e entidades corretas, pode trabalhar para melhorar o desempenho da sua app LUIS rotulando mais expressões ou adicionando funcionalidades.

* [Rótulo sugeriu declarações com LUIS](luis-how-to-review-endpoint-utterances.md)
* [Utilize funcionalidades para melhorar o desempenho da sua app LUIS](luis-how-to-add-features.md)
* [Compreenda os testes de lote com este tutorial](luis-tutorial-batch-testing.md)
* [Aprenda conceitos de teste de lote.](luis-concept-batch-test.md)
