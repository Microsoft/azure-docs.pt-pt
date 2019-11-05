---
title: 'Tutorial: teste em lote-LUIS'
titleSuffix: Azure Cognitive Services
description: Este tutorial demonstra como usar o teste em lotes para encontrar problemas de previsão de expressão em seu aplicativo e corrigi-los.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: ac88931d79df6c2527a2a5fd72b440baeb463115
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499049"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutorial: conjuntos de dados de teste em lotes

Este tutorial demonstra como usar o teste em lotes para encontrar problemas de previsão de expressão em seu aplicativo e corrigi-los.  

Os testes em lotes permitem que você valide o estado ativo e treinado do modelo com um conjunto conhecido de declarações rotuladas e entidades. No arquivo em lotes formatado em JSON, adicione o declarações e defina os rótulos de entidade que você precisa prever dentro do expressão. 

Requisitos para teste em lotes:

* Máximo de 1000 declarações por teste. 
* Sem duplicatas. 
* Tipos de entidade permitidos: somente entidades aprendidas por máquina de simples e compostas. O teste em lotes só é útil para entidades e intenções aprendidas com o computador.

Ao usar um aplicativo diferente deste tutorial, *não use o* exemplo declarações já adicionado a uma intenção. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar aplicativo de exemplo
> * Criar um arquivo de teste em lotes 
> * Executar um teste em lotes
> * Examinar os resultados do teste
> * Corrigir erros 
> * Testar o lote novamente

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importar aplicativo de exemplo

Continue com a aplicação criada no último tutorial, com o nome **RecursosHumanos**. 

Utilize os passos seguintes:

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Importe o JSON para uma nova aplicação.

3. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `batchtest`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL. 

4. Prepare a aplicação.

## <a name="batch-file"></a>Arquivo em lotes

1. Crie `HumanResources-jobs-batch.json` em um editor de texto ou [Baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) -o. 

2. No arquivo em lotes formatado em JSON, adicione declarações com a **intenção** que você deseja prever no teste. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Executar o lote

1. Selecione **teste** na barra de navegação superior. 

2. Selecione **painel de teste do lote** no painel do lado direito. 

    [![captura de tela do aplicativo LUIS com o painel de teste do lote realçado](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Selecione **Importar conjunto**de um.

    [![captura de tela do aplicativo LUIS com Import DataSet realçado](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Escolha o local do arquivo de `HumanResources-jobs-batch.json`.

5. Nomeie o conjunto de `intents only` e selecione **concluído**.

    ![Selecionar arquivo](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Selecionar o botão **Executar**. 

7. Selecione **Ver resultados**.

8. Examine os resultados no gráfico e na legenda.

    [![captura de tela do aplicativo LUIS com resultados de teste em lote](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Examinar resultados do lote

O gráfico do lote exibe quatro quadrantes de resultados. À direita do gráfico está um filtro. O filtro contém intenções e entidades. Quando você seleciona uma [seção do gráfico](luis-concept-batch-test.md#batch-test-results) ou um ponto dentro do gráfico, as expressão associadas são exibidas abaixo do gráfico. 

Ao focalizar o gráfico, uma roda do mouse pode aumentar ou reduzir a exibição no gráfico. Isso é útil quando há muitos pontos no gráfico clusterizados juntos. 

O gráfico está em quatro quadrantes, com duas das seções exibidas em vermelho. **Estas são as seções para se concentrar**. 

### <a name="getjobinformation-test-results"></a>Resultados de teste do GetJobInformation

Os resultados do teste **GetJobInformation** exibidos no filtro mostram que 2 das quatro previsões foram bem-sucedidas. Selecione o nome **falso negativo** no quadrante inferior esquerdo para ver o declarações abaixo do gráfico. 

Use o teclado, CTRL + E, para alternar para o modo de exibição de rótulo para ver o texto exato do usuário expressão. 

O `Is there a database position open in Los Colinas?` expressão é rotulado como _GetJobInformation_ , mas o modelo atual prevê o expressão como _ApplyForJob_. 

Há quase três vezes o número de exemplos para **ApplyForJob** do que **GetJobInformation**. Essa irregularidade do declarações pesa no favor da intenção de **ApplyForJob** , causando a previsão incorreta. 

Observe que ambas as intenções têm a mesma contagem de erros. Uma previsão incorreta em uma intenção também afeta a outra intenção. Ambos têm erros porque o declarações foi incorretamente previsto para uma intenção e também não foi previsto incorretamente para outra intenção. 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>Como corrigir o aplicativo

O objetivo desta seção é ter todos os declarações corretamente previstos para **GetJobInformation** corrigindo o aplicativo. 

Uma correção aparentemente rápida seria adicionar esses declarações de arquivo em lotes à intenção correta. Isso não é o que você deseja fazer. Você quer que o LUIS Predict corretamente esses declarações sem adicioná-los como exemplos. 

Você também pode imaginar a remoção de declarações de **ApplyForJob** até que a quantidade expressão seja a mesma que **GetJobInformation**. Isso pode corrigir os resultados do teste, mas impediria o LUIS de prever essa intenção na próxima vez. 

A correção é adicionar mais declarações ao **GetJobInformation**. Lembre-se de variar o comprimento do expressão, a opção de palavra e a organização e, ao mesmo tempo, direcionar a intenção de localizar informações sobre o trabalho, _não_ a aplicação do trabalho.

### <a name="add-more-utterances"></a>Adicionar mais declarações

1. Feche o painel de teste do lote selecionando o botão **testar** no painel de navegação superior. 

2. Selecione **GetJobInformation** na lista de tentativas. 

3. Adicione mais declarações que são variados para comprimento, opção de palavra e organização de palavras, conferindo-se de incluir os termos `resume`, `c.v.`e `apply`:

    |Exemplo declarações para a intenção de **GetJobInformation**|
    |--|
    |O novo trabalho no warehouse para um Stocker exige que eu se aplique com um currículo?|
    |Onde estão os trabalhos do telhado hoje?|
    |Ouvi um trabalho de codificação médica que exige um currículo.|
    |Eu gostaria que um trabalho ajudando as crianças de faculdades a escreverem seus c.v.s. |
    |Aqui está meu currículo, procurando uma nova postagem na faculdade da Comunidade usando computadores.|
    |Quais cargos estão disponíveis no local e nos cuidados de casa?|
    |Há uma mesa interna no jornal?|
    |Meu C.v. mostra que estou bom na análise de compras, orçamentos e perda de dinheiro. Há algo para esse tipo de trabalho?|
    |Onde estão os trabalhos de análise da terra no momento?|
    |Trabalhei oito anos como um driver do EMS. Há novos trabalhos?|
    |Novos trabalhos de tratamento de alimentos exigem o aplicativo?|
    |Quantas novas tarefas de trabalho de jardim estão disponíveis?|
    |Há uma nova postagem de RH para relações e negociações de mão-de-obra?|
    |Tenho um mestre na biblioteca e no gerenciamento de arquivos. Novas posições?|
    |Há algum trabalho de babá para olds de 13 anos na cidade hoje?|

    Não rotule a entidade de **trabalho** no declarações. Esta seção do tutorial está concentrada apenas na previsão de intenção.

4. Treine o aplicativo selecionando **treinar** na navegação superior direita.

## <a name="verify-the-new-model"></a>Verificar o novo modelo

Para verificar se o declarações no teste do lote está previsto corretamente, execute o teste do lote novamente.

1. Selecione **teste** na barra de navegação superior. Se os resultados do lote ainda estiverem abertos, selecione **voltar à lista**.  

1. Selecione o botão de reticências (***...***) à direita do nome do lote e selecione **executar**. Aguarde até que o teste em lotes seja concluído. Observe que o botão **Ver resultados** agora está verde. Isso significa que todo o lote foi executado com êxito.

1. Selecione **Ver resultados**. As intenções devem todos ter ícones verdes à esquerda dos nomes de intenção. 

## <a name="create-batch-file-with-entities"></a>Criar arquivo em lotes com entidades 

Para verificar as entidades em um teste em lotes, as entidades precisam ser rotuladas no arquivo JSON do lote. 

A variação de entidades para a contagem total de palavras ([token](luis-glossary.md#token)) pode afetar a qualidade da previsão. Verifique se os dados de treinamento fornecidos para a intenção com rotulada declarações incluem uma variedade de comprimentos de entidade. 

Ao escrever e testar arquivos em lotes pela primeira vez, é melhor começar com algumas declarações e entidades que você sabe que funcionam, bem como alguns que você imagina que podem estar incorretamente previstos. Isso ajuda você a se concentrar nas áreas problemáticas rapidamente. Depois de testar as intenções de **GetJobInformation** e **ApplyForJob** usando vários nomes de trabalho diferentes, que não foram previstos, esse arquivo de teste em lotes foi desenvolvido para ver se há um problema de previsão com determinados valores para a entidade de **trabalho** . 

O valor de uma entidade de **trabalho** , fornecido no declarações de teste, geralmente é uma ou duas palavras, com alguns exemplos sendo mais palavras. Se _seu próprio_ aplicativo de recursos humanos normalmente tiver nomes de trabalho de muitas palavras, o exemplo declarações rotulado com a entidade de **trabalho** nesse aplicativo não funcionará bem.

1. Crie `HumanResources-entities-batch.json` em um editor de texto como [VSCode](https://code.visualstudio.com/) ou [Baixe](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) -o.

2. No arquivo em lotes formatado em JSON, adicione uma matriz de objetos que inclua declarações com a **intenção** que você deseja prever no teste, bem como os locais de todas as entidades no expressão. Como uma entidade é baseada em token, certifique-se de iniciar e parar cada entidade em um caractere. Não comece nem termine o expressão em um espaço. Isso causa um erro durante a importação do arquivo em lotes.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Executar o lote com entidades

1. Selecione **teste** na barra de navegação superior. 

2. Selecione **painel de teste do lote** no painel do lado direito. 

3. Selecione **Importar conjunto**de um.

4. Escolha o local do sistema de arquivos do arquivo de `HumanResources-entities-batch.json`.

5. Nomeie o conjunto de `entities` e selecione **concluído**.

6. Selecionar o botão **Executar**. Aguarde até que o teste seja concluído.

7. Selecione **Ver resultados**.

## <a name="review-entity-batch-results"></a>Examinar resultados do lote de entidade

O gráfico é aberto com todas as tentativas previstas corretamente. Role para baixo no filtro do lado direito para localizar as previsões de entidade com erros. 

1. Selecione a entidade de **trabalho** no filtro.

    ![Previsões de entidade de erro no filtro](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    O gráfico é alterado para exibir as previsões de entidade. 

2. Selecione **falso negativo** no quadrante inferior esquerdo do gráfico. Em seguida, use o controle de combinação de teclado + E para alternar para a exibição de token. 

    [![exibição de token de previsões de entidade](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Revisar o declarações abaixo do gráfico revela um erro consistente quando o nome do trabalho inclui `SQL`. Revisar o exemplo declarações e a lista de frases de trabalho, o SQL é usado apenas uma vez e apenas como parte de um nome de trabalho maior, `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Corrigir o aplicativo com base nos resultados do lote de entidade

Corrigir o aplicativo exige que o LUIS determine corretamente as variações de trabalhos SQL. Há várias opções para essa correção. 

* Adicione explicitamente mais exemplos de declarações, que usam SQL e rotulam essas palavras como uma entidade de trabalho. 
* Adicionar explicitamente mais trabalhos SQL à lista de frases

Essas tarefas são deixadas para você.

A adição de um [padrão](luis-concept-patterns.md) antes da entidade ser prevista corretamente, não vai corrigir o problema. Isso ocorre porque o padrão não corresponde até que todas as entidades no padrão sejam detectadas. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos seguintes

O tutorial usou um teste em lotes para encontrar problemas com o modelo atual. O modelo foi corrigido e testado novamente com o arquivo em lotes para verificar se a alteração estava correta.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões](luis-tutorial-pattern.md)

