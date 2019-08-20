---
title: Lote de teste-LUIS
titleSuffix: Azure Cognitive Services
description: Este tutorial demonstra como usar o teste de batch para localizar problemas de predição de expressão na sua aplicação e corrigi-los.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: diberry
ms.openlocfilehash: 60cd87b6cecfb30ebc90f445c79e25c241980a86
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623340"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutorial: Conjuntos de dados de teste em lotes

Este tutorial demonstra como usar o teste de batch para localizar problemas de predição de expressão na sua aplicação e corrigi-los.  

Teste de batch permite-lhe validar ativo, formação estado do modelo com um conjunto conhecido de expressões etiquetados com e entidades. No ficheiro batch formatada em JSON, adicionar as expressões e definir as etiquetas de entidade que tem de prever dentro da expressão. 

Requisitos do teste de batch:

* Máximo de 1000 discursos por teste. 
* Não contém duplicados. 
* Tipos de entidade permitidos: somente entidades aprendidas por máquina de simples e compostas. Teste de batch apenas é útil para gigantesca máquina acastanhada aprendidas intenções e entidades.

Ao utilizar uma aplicação que não seja neste tutorial, fazer *não* usar as expressões de exemplo já adicionadas a um objetivo. 

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar aplicativo de exemplo
> * Crie um ficheiro de teste do batch 
> * Executar um teste de batch
> * Rever os resultados de teste
> * Corrigir erros 
> * Testar novamente o batch

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importar aplicativo de exemplo

Continue com a aplicação criada no último tutorial, com o nome **RecursosHumanos**. 

Utilize os passos seguintes:

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Importe o JSON para uma nova aplicação.

3. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `batchtest`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL. 

4. Prepare a aplicação.

## <a name="batch-file"></a>Arquivo em lotes

1. Crie `HumanResources-jobs-batch.json` num editor de texto ou [transferir](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) -lo. 

2. No ficheiro batch formatada em JSON, adicionar expressões com o **intenção** desejar prevista no teste. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>A executar o batch

1. Selecione **teste** na barra de navegação superior. 

2. Selecione **painel de teste do Batch** no painel do lado direito. 

    [![Aplicação de captura de ecrã do LUIS com painel de teste de Batch realçado](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Selecione **conjunto de dados de importação**.

    [![Aplicação de captura de ecrã do LUIS com o conjunto de dados de importação realçado](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Escolha a localização do ficheiro do `HumanResources-jobs-batch.json` ficheiro.

5. Nome do conjunto de dados `intents only` e selecione **feito**.

    ![Selecionar ficheiro](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Selecionar o botão **Executar**. 

7. Selecione **ver resultados**.

8. Reveja os resultados no gráfico e da legenda.

    [![Aplicação de captura de ecrã do LUIS com resultados de teste do batch](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Rever os resultados de batch

O gráfico de batch apresenta quatro quadrantes de resultados. À direita do gráfico é um filtro. O filtro contém intenções e entidades. Quando seleciona uma [secção do gráfico](luis-concept-batch-test.md#batch-test-results) ou um ponto no gráfico, a utterance(s) associado apresenta abaixo do gráfico. 

Ao passar o rato sobre o gráfico, a roda do rato pode aumentar ou reduzir a apresentar no gráfico. Isto é útil quando há muitos pontos no gráfico rigidamente agrupado. 

O gráfico está em quatro quadrantes, com duas das seções apresentadas a vermelho. **Estas são as secções concentrar-se no**. 

### <a name="getjobinformation-test-results"></a>Resultados do teste GetJobInformation

O **GetJobInformation** resultados de teste apresentados no filtro de mostrarem que 2 das predições a quatro foram bem-sucedidas. Selecione o nome **falso negativo** no quadrante inferior esquerdo para ver o declarações abaixo do gráfico. 

Use o teclado, CTRL + E, para alternar para o modo de exibição de rótulo para ver o texto exato do usuário expressão. 

O expressão `Is there a database position open in Los Colinas?` é rotulado como _GetJobInformation_ , mas o modelo atual prevê o expressão como _ApplyForJob_. 

Há quase três vezes o número de exemplos para **ApplyForJob** do que **GetJobInformation**. Essa irregularidade do declarações pesa no favor da intenção de **ApplyForJob** , causando a previsão incorreta. 

Tenha em atenção que ambos os objetivos tem a mesma contagem de erros. Uma previsão incorreta na um intenção afeta a outra intenção também. Ambos têm erros, porque as expressões foram previstas incorretamente para um objetivo e também não previstas incorretamente para outro objetivo. 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>Como corrigir o aplicativo

O objetivo desta seção é fazer com que todas as expressões previstas corretamente para **GetJobInformation** corrigindo a aplicação. 

Uma correção rápida aparentemente seria adicionar estas expressões de ficheiro de batch para a intenção correta. Isso não é o que você deseja fazer. Pretende que o LUIS para prever corretamente estas expressões sem adicionar-os como exemplos. 

Também deve estar imaginando sobre a remoção de expressões a partir **ApplyForJob** até que a quantidade de expressão é igual a **GetJobInformation**. Isso pode corrigir os resultados do teste, mas seria atrapalham LUIS dessa intenção prever com precisão próxima vez. 

A correção é adicionar mais declarações ao **GetJobInformation**. Lembre-se de variar o comprimento do expressão, a opção de palavra e a organização e, ao mesmo tempo, direcionar a intenção de localizar informações sobre o trabalho, _não_ a aplicação do trabalho.

### <a name="add-more-utterances"></a>Adicionar mais expressões

1. Fechar o painel de teste do batch, selecionando o **testar** botão no painel de navegação superior. 

2. Selecione **GetJobInformation** na lista de objetivos. 

3. Adicionar mais expressões que são diversificados de comprimento, a escolha do word e a disposição do word, certificar-se de que incluem os termos `resume`, `c.v.`, e `apply`:

    |Expressões com de exemplo para **GetJobInformation** intenção|
    |--|
    |A nova tarefa de armazém de para um stocker exige que eu aplicam-se com um currículo?|
    |Onde estão as tarefas de roofing hoje em dia?|
    |Ouvi foi uma tarefa de codificação médica que necessita de um currículo.|
    |Eu gostaria de uma tarefa, ajudando a crianças de faculdade escrever seus c.v.s. |
    |Aqui está meu currículo, à procura de uma nova mensagem na faculdade de Comunidade através de computadores.|
    |As posições estão disponíveis no cuidado filho e em casa?|
    |Existe uma mesa de colaborador no Jornal?|
    |Meu C.v. mostra que sou bom em análise de aprovisionamento, orçamentos e perder dinheiro. Existe algo para este tipo de trabalho?|
    |Onde está a terra desagregação tarefas neste momento?|
    |Trabalhei oito anos como um driver de EMS. As tarefas novas?|
    |Novas tarefas de processamento de comida requerem aplicação?|
    |Quantas novas tarefas de trabalho de jardim estão disponíveis?|
    |Existe uma nova mensagem de RH para relações de trabalho e negociações?|
    |Tenho um principais na gestão de biblioteca e de arquivo. Quaisquer novas funções?|
    |Existem todas as tarefas babysitting para 13 ano olds na cidade hoje em dia?|

    Não etiquetar os **tarefa** entidade nas expressões. Esta secção do tutorial concentra-se na intenção predição apenas.

4. Preparar a aplicação, selecionando **Train** na barra de navegação direita superior.

## <a name="verify-the-new-model"></a>Verifique se o novo modelo

Para verificar que as expressões no teste de batch estão previstas corretamente, execute novamente o teste de batch.

1. Selecione **teste** na barra de navegação superior. Se os resultados do batch são ainda abertos, selecione **voltar à lista**.  

1. Selecione o botão de reticências (***...***) à direita do nome do lote e selecione **executar**. Aguarde até que o teste de batch é concluído. Tenha em atenção que o **ver resultados** botão agora está verde. Isso significa que o lote inteiro foi executado com êxito.

1. Selecione **ver resultados**. Os objetivos devem todos ter ícones verde à esquerda dos nomes de intenção. 

## <a name="create-batch-file-with-entities"></a>Criar o arquivo em lotes com entidades 

Para verificar as entidades num teste de lote, as entidades tem de ser o nome do arquivo em lotes JSON. 

A variação de entidades para o total word ([token](luis-glossary.md#token)) contagem pode afetar a qualidade de previsão. Certifique-se de que os dados de treinamento fornecidos para a intenção com expressões etiquetados com incluem uma variedade de tamanhos de entidade. 

Quando o primeiro escrever e testar arquivos em lote, é melhor começar com algumas expressões e entidades que sabe que funcionar, bem como alguns que imagine pode ser previsto incorretamente. Esta ajuda que nos concentrar em áreas problemáticas rapidamente. Após testar os **GetJobInformation** e **ApplyForJob** intenções usando vários tarefa nomes diferentes, que não foram previstos, este arquivo de lote de teste foi desenvolvido para ver se existe um problema de predição com determinados valores para **tarefa** entidade. 

O valor de um **tarefa** entidade, fornecida nas expressões de teste, é normalmente uma ou duas palavras, com alguns exemplos que está a ser mais palavras. Se _suas próprias_ aplicação de recursos humanos normalmente tem os nomes das tarefas de várias palavras, as expressões de exemplo com o nome com **tarefa** entidade nesta aplicação não funcionaria bem.

1. Crie `HumanResources-entities-batch.json` num editor de texto, como [VSCode](https://code.visualstudio.com/) ou [transferir](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) -lo.

2. No ficheiro batch formatada em JSON, adicione uma matriz de objetos que incluem expressões com o **intenção** desejar prevista no teste, bem como localizações de qualquer entidades na expressão. Uma vez que uma entidade é baseada em tokens, certifique-se iniciar e parar a cada entidade num caractere. Não começar ou terminar a expressão num espaço. Isso faz com que um erro durante a importação de ficheiros do batch.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Executar o batch com entidades

1. Selecione **teste** na barra de navegação superior. 

2. Selecione **painel de teste do Batch** no painel do lado direito. 

3. Selecione **conjunto de dados de importação**.

4. Escolha a localização de sistema de ficheiros do `HumanResources-entities-batch.json` ficheiro.

5. Nome do conjunto de dados `entities` e selecione **feito**.

6. Selecionar o botão **Executar**. Aguarde até que o teste é concluído.

7. Selecione **ver resultados**.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="review-entity-batch-results"></a>Reveja os resultados de entidade de batch

O gráfico é aberto com todos os objetivos previstos corretamente. Role para baixo no filtro do lado direito para localizar as previsões de entidade com erros. 

1. Selecione o **tarefa** entidade no filtro.

    ![Previsões de entidade de erro no filtro](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    As alterações de gráfico para apresentar as previsões de entidade. 

2. Selecione **falsos negativos** na parte inferior, esquerda quadrante do gráfico. Em seguida, utilize o controlo de combinação de teclado + E alternar para a vista de token. 

    [![Vista de token de previsões de entidade](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Rever as expressões abaixo do gráfico revela um erro de consistente quando o nome da tarefa inclui `SQL`. Rever as expressões de exemplo e a lista de frase de tarefa, SQL é apenas utilizado uma vez e apenas como parte de um nome de trabalho maior, `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Corrigir a aplicação com base nos resultados de lote da entidade

Corrigir a aplicação requer o LUIS determinar corretamente as variações de trabalhos do SQL. Existem várias opções para essa correção. 

* Mais expressões de exemplo, o que utilizam o SQL e etiquetar essas palavras como uma entidade de tarefa de adicionar explicitamente. 
* Adicionar explicitamente mais tarefas SQL para a lista de frase

Estas tarefas deixarei para fazer.

Adicionar uma [padrão](luis-concept-patterns.md) antes da entidade está prevista corretamente, não vai para corrigir o problema. Isto acontece porque o padrão não corresponder ao até que todas as entidades no padrão são detetadas. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

O tutorial usado um teste de batch para encontrar problemas com o modelo atual. O modelo foi corrigido e retested com o arquivo em lotes para verificar que a alteração foi correta.

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões de](luis-tutorial-pattern.md)

