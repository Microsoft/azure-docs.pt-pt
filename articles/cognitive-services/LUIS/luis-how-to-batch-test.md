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
ms.date: 04/13/2021
ms.openlocfilehash: 9fe4f21a5c9e9e26a2f94b8a60cba47916842fe3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501795"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Teste de lote com um conjunto de expressões de exemplo

O teste de lote valida a sua versão treinada ativa para medir a sua precisão de previsão. Um teste de lote ajuda-o a ver a precisão de cada intenção e entidade na sua versão ativa. Reveja os resultados dos testes de lote para tomar as medidas adequadas para melhorar a precisão, como adicionar mais palavras de exemplo a uma intenção se a sua app frequentemente não identificar as entidades corretas de intenção ou rotulagem dentro da expressão.

## <a name="group-data-for-batch-test"></a>Dados do grupo para teste de lote

É importante que as expressões utilizadas para o teste do lote sejam novas para o LUIS. Se tiver um conjunto de dados de expressões, divida as expressões em três conjuntos: palavras de exemplo adicionadas a uma intenção, declarações recebidas do ponto final publicado, e expressões usadas para testar o LUIS após a sua formação.

O ficheiro JSON do lote que utiliza deve incluir expressões com entidades de aprendizagem automática de alto nível rotuladas, incluindo posição inicial e final. As declarações não devem fazer parte dos exemplos já na app. Devem ser expressões que se pretende prever positivamente para as intenções e entidades.

Pode separar os testes por intenção e/ou entidade ou ter todos os testes (até 1000 expressões) no mesmo ficheiro. 

### <a name="common-errors-importing-a-batch"></a>Erros comuns que importam um lote

Se encontrar erros ao enviar o seu ficheiro de lote para o LUIS, verifique as seguintes questões comuns:

* Mais de 1.000 expressões num ficheiro de lote
* Um objeto JSON que não tem propriedade de entidades. A propriedade pode ser uma matriz vazia.
* Palavras(s) rotuladas em várias entidades
* Etiquetas de entidades que começam ou terminam num espaço.

## <a name="fixing-batch-errors"></a>Corrigir erros de lote

Se houver erros no teste do lote, pode adicionar mais expressões a uma intenção, e/ou rotular mais expressões com a entidade para ajudar a LUIS a fazer a discriminação entre intenções. Se adicionou expressões, e as rotulou, e ainda obtém erros de previsão nos testes de lote, considere adicionar uma funcionalidade de lista de [frases](luis-concept-feature.md) com vocabulário específico de domínio para ajudar o LUIS a aprender mais rapidamente.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Teste de lote usando o portal LUIS 

### <a name="import-and-train-an-example-app"></a>Importar e treinar uma aplicação de exemplo

Importe uma aplicação que leve uma encomenda de pizza como `1 pepperoni pizza on thin crust` .

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Selecione a seta ao lado **da nova aplicação** e clique em **Import como JSON** para importar o JSON em uma nova aplicação. Nomeie a `Pizza app` aplicação.


1. Selecione **Train** no canto superior direito da navegação para treinar a aplicação.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Ficheiro de teste de lote

O exemplo JSON inclui uma expressão com uma entidade rotulada para ilustrar como é um ficheiro de teste. Nos seus próprios testes, deve ter muitas expressões com a intenção correta e a entidade de aprendizagem automática rotulada.

1. Crie `pizza-with-machine-learned-entity-test.json` num editor de texto ou [descarregue-o.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)

2. No ficheiro de lote em formatado JSON, adicione uma expressão com a **Intenção** que pretende prever no teste.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Executar o lote

1. Selecione **Teste** na barra de navegação superior.

2. Selecione painel de **testes** de lote no painel do lado direito.

    ![Ligação de teste de lote](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Selecione **Import** (Importar). Na caixa de diálogo que aparece, selecione **Choose File** e localize um ficheiro JSON com o formato JSON correto que não contenha mais *de 1.000* expressões para testar.

    Erros de importação são relatados numa barra de notificação vermelha no topo do navegador. Quando uma importação tem erros, nenhum conjunto de dados é criado. Para obter mais informações, consulte [erros comuns.](#common-errors-importing-a-batch)

4. Escolha a localização do ficheiro `pizza-with-machine-learned-entity-test.json` do ficheiro.

5. Nomeie o conjunto de dados `pizza test` e selecione **Feito**.

6. Selecione o botão **Run** (Executar). Depois do teste do lote, selecione **Ver os resultados**. 

    > [!TIP]
    > * Selecionar **Download** irá descarregar o mesmo ficheiro que fez o upload.
    > * Se vir o teste do lote falhado, pelo menos uma intenção de expressão não corresponde à previsão.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Rever os resultados do lote para intenções

Para rever os resultados dos testes de lote, **selecione Ver os resultados**. Os resultados dos testes mostram graficamente como as declarações de teste foram previstas contra a versão ativa.

O gráfico do lote apresenta quatro quadrantes de resultados. À direita da tabela está um filtro. O filtro contém intenções e entidades. Quando seleciona uma [secção do gráfico](#review-batch-results-for-intents) ou um ponto dentro da tabela, o(s) visualização de expressão associada abaixo da tabela.

Enquanto paira sobre a tabela, uma roda de rato pode ampliar ou reduzir o visor na tabela. Isto é útil quando há muitos pontos na tabela agrupados firmemente juntos.

O gráfico está em quatro quadrantes, com duas das secções exibidas a vermelho.

1. Selecione a intenção **modificar-se** na lista de filtros. A expressão é prevista como um **Verdadeiro Positivo,** o que significa que a expressão correspondeu com sucesso à sua previsão positiva listada no ficheiro do lote.

    > [!div class="mx-imgBorder"]
    > ![A expressão correspondeu com sucesso à sua previsão positiva](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    As marcas verdes na lista de filtros também indicam o sucesso do teste para cada intenção. Todas as outras intenções são listadas com uma pontuação positiva de 1/1 porque a expressão foi testada contra cada intenção, como um teste negativo para quaisquer intenções não listadas no teste do lote.

1. Selecione a intenção **de confirmação.** Esta intenção não está listada no teste do lote, pelo que este é um teste negativo da expressão que está listada no teste do lote.

    > [!div class="mx-imgBorder"]
    > ![Pronunciação com sucesso previu negativo para intenção não cotada no ficheiro de lote](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    O teste negativo foi bem sucedido, como se nota com o texto verde no filtro e na grelha.

### <a name="review-batch-test-results-for-entities"></a>Rever os resultados dos testes de lote para entidades

A entidade ModificarOrder, enquanto entidade de máquina com subentidades, apresenta se a entidade de nível superior corresponde e como as subentígios são previstas.

1. Selecione a entidade **ModificarOrder** na lista de filtros e, em seguida, selecione o círculo na grelha.

1. A previsão da entidade apresenta abaixo do gráfico. O ecrã inclui linhas sólidas para previsões que correspondem às expectativas e linhas pontilhadas para previsões que não correspondem às expectativas.

    > [!div class="mx-imgBorder"]
    > ![Progenitor da entidade com sucesso previsto em ficheiro de lote](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Resultados do gráfico do filtro

Para filtrar o gráfico por uma intenção ou entidade específica, selecione a intenção ou entidade no painel de filtragem do lado direito. Os pontos de dados e a sua atualização de distribuição no gráfico de acordo com a sua seleção.

![Resultado do teste do lote visualizado](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Exemplos de resultados do gráfico

O gráfico no portal LUIS, pode realizar as seguintes ações:
 
#### <a name="view-single-point-utterance-data"></a>Ver dados de expressão de um ponto único

No gráfico, paire sobre um ponto de dados para ver a pontuação de certeza da sua previsão. Selecione um ponto de dados para recuperar a sua expressão correspondente na lista de expressões na parte inferior da página.

![Expressão selecionada](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Ver dados da secção

Na tabela de quatro secções, selecione o nome da secção, tal como **Falso Positivo** no topo direito da tabela. Abaixo da tabela, todas as expressões nessa secção mostram abaixo da tabela numa lista.

![Expressões selecionadas por secção](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Nesta imagem anterior, a expressão `switch on` é rotulada com a intenção TurnAllOn, mas recebeu a previsão de nenhuma intenção. Esta é uma indicação de que a intenção turnAllOn precisa de mais declarações de exemplo para fazer a previsão esperada.

As duas secções do gráfico em vermelho indicam expressões que não correspondem à previsão esperada. Estas indicam declarações que o LUIS precisa de mais formação.

As duas secções do gráfico em verde correspondem à previsão esperada.

## <a name="batch-testing-using-the-rest-api"></a>Testes de lote utilizando a API REST 

O LUIS permite-lhe testar o lote utilizando o portal LUIS e a API REST. Os pontos finais da API REST estão listados abaixo. Para obter informações sobre os testes de lote utilizando o portal LUIS, consulte [Tutorial: conjuntos de dados de teste de lote](). Utilize os URLs completos abaixo, substituindo os valores do espaço reservado pela sua própria chave de previsão LUIS e ponto final. 

Lembre-se de adicionar a sua chave LUIS `Ocp-Apim-Subscription-Key` no cabeçalho, e definir `Content-Type` para `application/json` .

### <a name="start-a-batch-test"></a>Iniciar um teste de lote

Inicie um teste de lote utilizando um ID de versão de aplicação ou uma ranhura de publicação. Envie um pedido **DEM** para um dos seguintes formatos de ponto final. Inclua o seu ficheiro de lote no corpo do pedido.

Slot de publicação
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

ID versão da aplicação
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Estes pontos finais irão devolver um ID de operação que utilizará para verificar o estado e obter resultados. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Obtenha o estado de um teste de lote em curso

Utilize o ID de operação a partir do teste de lote que começou a obter o seu estado a partir dos seguintes formatos de ponto final: 

Slot de publicação
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

ID versão da aplicação
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Obtenha os resultados de um teste de lote

Utilize o ID de operação a partir do teste de lote que começou a obter os seus resultados a partir dos seguintes formatos de ponto final: 

Slot de publicação
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

ID versão da aplicação
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Arquivo de lote de expressões

Envie um ficheiro de pronâncias de lote, conhecido como conjunto de *dados,* para testes de lote. O conjunto de dados é um ficheiro com formato JSON que contém um máximo de 1.000 expressões etiquetadas. Pode testar até 10 conjuntos de dados numa aplicação. Se precisar de testar mais, elimine um conjunto de dados e adicione um novo. Todas as entidades personalizadas do modelo aparecem no filtro das entidades de teste de lote, mesmo que não existam entidades correspondentes nos dados do ficheiro do lote.

O ficheiro do lote consiste em expressões. Cada expressão deve ter uma previsão de intenção esperada, juntamente com quaisquer [entidades de aprendizagem automática](luis-concept-entity-types.md#machine-learned-ml-entity) que espera ser detetada.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Modelo de sintaxe de lote para intenções com entidades

Utilize o seguinte modelo para iniciar o seu ficheiro de lote:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

O ficheiro de lote utiliza as propriedades **startPos** e **endPos** para notar o início e o fim de uma entidade. Os valores são baseados em zero e não devem começar ou terminar num espaço. Isto é diferente dos registos de consulta, que usam propriedades startIndex e endIndex.

Se não quiser testar entidades, inclua o `entities` imóvel e desafine o valor como uma matriz vazia, `[]` .

### <a name="rest-api-batch-test-results"></a>Rest API batch resultados

Existem vários objetos devolvidos pela API:

* Informação sobre os modelos de intenções e entidades, tais como precisão, recordação e pontuação F.
* Informação sobre os modelos das entidades, tais como precisão, recolha e pontuação F) para cada entidade 
  * Utilizando a `verbose` bandeira, pode obter mais informações sobre a entidade, tais como `entityTextFScore` `entityTypeFScore` .
* Proferiu declarações com os nomes de intenções previstos e rotulados
* Uma lista de entidades falsas positivas, e uma lista de falsas entidades negativas.

## <a name="next-steps"></a>Passos seguintes

Se os testes indicarem que a sua aplicação LUIS não reconhece as intenções e entidades corretas, pode trabalhar para melhorar o desempenho da sua app LUIS rotulando mais expressões ou adicionando funcionalidades.

* [Rótulo sugeriu declarações com LUIS](luis-how-to-review-endpoint-utterances.md)
* [Utilize funcionalidades para melhorar o desempenho da sua app LUIS](luis-how-to-add-features.md)
