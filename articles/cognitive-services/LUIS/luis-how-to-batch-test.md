---
title: Lote de teste-LUIS
titleSuffix: Azure Cognitive Services
description: Use os conjuntos de testes do lote Reconhecimento vocal (LUIS) para encontrar declarações com tentativas incorretas e entidades.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: a99b2e7097f2abf36f184368b3b4fdaba8f2730b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467420"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Teste em lotes com um conjunto de exemplos de declarações

 O teste em lote é um teste abrangente de seu modelo treinado atual para medir seu desempenho no LUIS. Os conjuntos de dados usados para testes em lotes não devem incluir o exemplo declarações em tentativas ou declarações recebidos do ponto de extremidade de tempo de execução de previsão. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importar um arquivo de conjunto de arquivos para testes em lotes

1. Selecione **teste** na barra superior e, em seguida, selecione **painel de teste do lote**.

    ![Link de teste do lote](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Selecione **Importar conjunto**de um. A caixa de diálogo **importar novo conjunto de novos DataSet** é exibida. Selecione **escolher arquivo** e localize um arquivo JSON com o [formato JSON](luis-concept-batch-test.md#batch-file-format) correto que não contenha *mais de 1.000* declarações para testar.

    Erros de importação são relatados em uma barra de notificação vermelha na parte superior do navegador. Quando uma importação tem erros, nenhum conjunto de nenhum é criado. Para obter mais informações, consulte [Common Errors](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. No campo **nome do conjunto** de campos, insira um nome para o arquivo do conjunto de arquivos. O arquivo do conjunto de arquivos inclui uma **matriz de declarações** , incluindo a *intenção rotulada* e as *entidades*. Examine o [arquivo em lotes de exemplo](luis-concept-batch-test.md#batch-file-format) para obter a sintaxe. 

4. Selecione **Done** (Concluído). O arquivo do conjunto de arquivos é adicionado.

## <a name="run-rename-export-or-delete-dataset"></a>Executar, renomear, exportar ou excluir DataSet

Para executar, renomear, exportar ou excluir o conjunto de um, use o botão de reticências (***...***) no final da linha do conjunto de linhas.

![Ações de conjunto de](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Executar um teste em lote em seu aplicativo treinado

Para executar o teste, selecione o nome do conjunto de um. Quando o teste for concluído, essa linha exibirá o resultado do teste do conjunto de resultados.

![Resultado do teste em lotes](./media/luis-how-to-batch-test/run-test.png)

O conjunto de testes para download é o mesmo arquivo que foi carregado para teste em lotes.

|Estado|Significado|
|--|--|
|![Ícone de círculo verde de teste com êxito](./media/luis-how-to-batch-test/batch-test-result-green.png)|Todos os declarações foram bem-sucedidos.|
|![Ícone de x vermelho de teste com falha](./media/luis-how-to-batch-test/batch-test-result-red.png)|Pelo menos uma tentativa de expressão não correspondeu à previsão.|
|![Ícone pronto para testar](./media/luis-how-to-batch-test/batch-test-result-blue.png)|O teste está pronto para ser executado.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Exibir resultados de teste do lote 

Para examinar os resultados do teste em lotes, selecione **Ver resultados**.

![Resultados de teste em lotes](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Filtrar resultados do gráfico

Para filtrar o gráfico por uma intenção ou entidade específica, selecione a intenção ou entidade no painel de filtragem do lado direito. Os pontos de dados e sua atualização de distribuição no grafo de acordo com sua seleção. 
 
![Resultado do teste de lote visualizado](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Exibir dados de expressão de ponto único

No gráfico, passe o mouse sobre um ponto de dados para ver a pontuação de certeza de sua previsão. Selecione um ponto de dados para recuperar seu expressão correspondente na lista declarações na parte inferior da página. 

![Expressão selecionado](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Exibir dados da seção

No gráfico de quatro seções, selecione o nome da seção, como **falso positivo** na parte superior direita do gráfico. Abaixo do gráfico, todos os declaraçõess nessa seção são exibidos abaixo do gráfico em uma lista. 

![Declarações selecionado por seção](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Nesta imagem anterior, a `switch on` expressão é rotulada com a intenção TurnAllOn, mas recebeu a previsão de nenhuma intenção de nenhum. Essa é uma indicação de que a intenção TurnAllOn precisa de mais exemplos declarações para fazer a previsão esperada. 

As duas seções do gráfico em vermelho indicam declarações que não corresponderam à previsão esperada. Eles indicam declarações que o LUIS precisa de mais treinamento. 

As duas seções do gráfico em verde corresponderam à previsão esperada.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Passos seguintes

Se o teste indicar que seu aplicativo LUIS não reconhece as intenções e as entidades corretas, você poderá trabalhar para melhorar o desempenho do aplicativo LUIS rotulando mais declarações ou adicionando recursos. 

* [Rótulo sugerido declarações com LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Use os recursos para melhorar o desempenho do aplicativo LUIS](luis-how-to-add-features.md) 
* [Entenda os testes em lotes com este tutorial](luis-tutorial-batch-testing.md)
* [Aprenda conceitos de teste em lote](luis-concept-batch-test.md).
