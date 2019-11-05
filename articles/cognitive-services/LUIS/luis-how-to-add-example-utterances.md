---
title: Adicionar exemplo declarações-LUIS
titleSuffix: Azure Cognitive Services
description: Exemplo declarações são exemplos de texto de perguntas ou comandos do usuário. Para ensinar Reconhecimento vocal (LUIS), você precisa adicionar o declarações de exemplo a uma intenção.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: ed6321963422cf17c858f43593092f8ffa4a1119
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467742"
---
# <a name="add-an-entity-to-example-utterances"></a>Adicionar uma entidade ao exemplo declarações 

Exemplo declarações são exemplos de texto de perguntas ou comandos do usuário. Para ensinar Reconhecimento vocal (LUIS), você precisa adicionar o [declarações de exemplo](luis-concept-utterance.md) a uma [intenção](luis-concept-intent.md).


Normalmente, você adiciona um exemplo expressão a uma intenção primeiro e, em seguida, cria entidades e o rótulo declarações na página de **tentativas** . Se você preferir criar entidades primeiro, consulte [adicionar entidades](luis-how-to-add-entities.md).

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="marking-entities-in-example-utterances"></a>Marcando entidades no exemplo declarações

Quando você seleciona o texto no exemplo expressão para marcar uma entidade, um menu pop-up no local é exibido. Use esse menu para criar ou selecionar uma entidade. 

Determinados tipos de entidade, como entidades predefinidas e entidades de expressão regular, não podem ser marcados no exemplo expressão porque são marcados automaticamente. 

## <a name="add-a-simple-entity"></a>Adicionar uma entidade simples

No procedimento a seguir, você cria e marca uma entidade personalizada dentro do seguinte expressão na página de **tentativas** :

```text
Are there any SQL server jobs?
```

1. Selecione `SQL server` no expressão para rotulá-lo como uma entidade simples. Na caixa suspensa entidade que aparece, você pode selecionar uma entidade existente ou adicionar uma nova entidade. Para adicionar uma nova entidade, digite seu nome `Job` na caixa de texto e, em seguida, selecione **criar nova entidade**.

    ![Captura de tela da inserção do nome da entidade](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Ao selecionar palavras para marcar como entidades:
    > * Para uma única palavra, basta selecioná-la. 
    > * Para um conjunto de duas ou mais palavras, selecione a primeira palavra e, em seguida, a palavra final.

1. Na caixa pop-up **que tipo de entidade você deseja criar?** , verifique o nome da entidade e selecione o tipo de entidade **simples** e, em seguida, selecione **concluído**.

    Uma [lista de frases](luis-concept-feature.md) é normalmente usada para impulsionar o sinal de uma entidade simples.

## <a name="add-a-list-entity"></a>Adicionar uma entidade de lista

As entidades de lista representam um conjunto de correspondências exatas de texto de palavras relacionadas em seu sistema. 

Para a lista de departamentos de uma empresa, você pode ter valores normalizados: `Accounting` e `Human Resources`. Cada nome normalizado tem sinônimos. Para um departamento, esses sinônimos podem incluir quaisquer acrônimos de departamento, números ou gírias. Você não precisa saber todos os valores ao criar a entidade. Você pode adicionar mais depois de revisar declarações de usuário reais com sinônimos.

1. Em um exemplo de expressão na página de **tentativas** , selecione a palavra ou frase desejada na nova lista. Quando a lista suspensa entidade for exibida, insira o nome para a nova entidade na caixa de texto superior e, em seguida, selecione **criar nova entidade**.   

1. Na caixa pop-up **que tipo de entidade você deseja criar?** , nomeie a entidade e selecione **lista** como o tipo. Adicione sinônimos deste item de lista e, em seguida, selecione **concluído**. 

    ![Captura de tela da inserção de sinônimos de entidade de lista](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Você pode adicionar mais itens de lista ou sinônimos de item rotulando outros declarações ou editando a entidade das **entidades** no painel de navegação esquerdo. [Editar](luis-how-to-add-entities.md#add-list-entities) as entidades oferece as opções de inserção de itens adicionais com sinônimos correspondentes ou importação de uma lista. 

## <a name="add-a-composite-entity"></a>Adicionar uma entidade composta

As entidades compostas são criadas a partir de **entidades** existentes para formar uma entidade pai. 

Supondo que expressão, `Does John Smith work in Seattle?`, um expressão composto possa retornar informações de entidade do nome do funcionário `John Smith`e o local `Seattle` em uma entidade composta. As entidades filho já devem existir no aplicativo e ser marcadas no exemplo expressão antes de criar a entidade composta.

1. Para encapsular as entidades filho em uma entidade composta, selecione a **primeira** entidade rotulada (mais à esquerda) no expressão para a entidade composta. Uma lista suspensa é exibida para mostrar as opções para essa seleção.

1. Selecione **encapsular em entidade composta** na lista suspensa. 

1. Selecione a última palavra da entidade composta (a mais à direita). Observe que uma linha verde segue a entidade composta. Este é o indicador visual de uma entidade composta e deve estar sob todas as palavras na entidade composta da entidade filho mais à esquerda para a entidade filho mais à direita.

1. Insira o nome da entidade composta na lista suspensa.

    Quando você encapsula as entidades corretamente, uma linha verde está abaixo da frase inteira.

1. Valide os detalhes da entidade composta na caixa pop-up **que tipo de entidade você deseja criar?** e, em seguida, selecione **concluído**.

    ![Captura de tela do pop-up detalhes da entidade](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. A entidade composta é exibida com os dois destaques azuis para entidades individuais e um sublinhado verde para toda a entidade composta. 

    ![Captura de tela da página de detalhes de tentativas, com a entidade composta](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Adicionar função da entidade ao expressão

Uma função é um subtipo nomeado de uma entidade, determinado pelo contexto do expressão. Você pode marcar uma entidade em um expressão como a entidade ou selecionar uma função dentro dessa entidade. Qualquer entidade pode ter funções, incluindo entidades personalizadas que são aprendidas por máquina (entidades simples e entidades compostas), não são aprendidas por máquina (entidades predefinidas, entidades de expressão regular, entidades de lista). 

Saiba [como marcar um expressão com funções de entidade](tutorial-entity-roles.md) de um tutorial prático. 

## <a name="entity-status-predictions"></a>Previsões de status de entidade

Quando você insere um novo expressão no portal do LUIS, o expressão pode ter erros de previsão de entidade. O erro de previsão é uma diferença entre como uma entidade é rotulada em comparação com o modo como LUIS prevê a entidade. 

Essa diferença é representada visualmente no portal do LUIS com um sublinhado vermelho no expressão. O sublinhado vermelho pode aparecer em colchetes de entidade ou fora dos colchetes. 

![Captura de tela de discrepância de previsão de status de entidade](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Selecione as palavras que são sublinhadas em vermelho no expressão. 

A caixa entidade exibe o **status da entidade** com um ponto de exclamação vermelho se houver uma discrepância de previsão. Para ver o status da entidade com informações sobre a diferença entre entidades rotuladas e previstas, selecione **status da entidade** e, em seguida, selecione o item à direita.

![Captura de tela da seleção de status da entidade](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

A linha vermelha pode aparecer em qualquer um dos seguintes horários:

   * Quando um expressão é inserido, mas antes de a entidade ser rotulada
   * Quando o rótulo da entidade é aplicado
   * Quando o rótulo da entidade é removido
   * Quando mais de um rótulo de entidade é previsto para esse texto 

As soluções a seguir ajudam a resolver a discrepância da previsão de entidade:

|Entidade|Indicador visual|Previsão|Solução|
|--|--|--|--|
|Expressão inserida, a entidade não está rotulada ainda.|sublinhado vermelho|A previsão está correta.|Rotule a entidade com o valor previsto.|
|Texto sem rótulo|sublinhado vermelho|Previsão incorreta|O declarações atual que usa essa entidade incorreta precisa ser examinado em todas as intenções. O declarações atual já foi ensinado LUIS que esse texto é a entidade prevista.
|Texto rotulado corretamente|realce de entidade azul, sublinhado vermelho|Previsão incorreta|Forneça mais declarações com a entidade rotulada corretamente em uma variedade de locais e usos. O declarações atual não é suficiente para ensinar LUIS de que essa é a entidade ou entidades semelhantes aparecem no mesmo contexto. Uma entidade semelhante deve ser combinada em uma única entidade, de modo que LUIS não seja confuso. Outra solução é adicionar uma lista de frases para impulsionar o significado das palavras. |
|Texto rotulado incorretamente|realce de entidade azul, sublinhado vermelho|Corrigir previsão| Forneça mais declarações com a entidade rotulada corretamente em uma variedade de locais e usos. 

## <a name="other-actions"></a>Outras ações

Você pode executar ações no exemplo declarações como um grupo selecionado ou como um item individual. Grupos de exemplos de exemplo selecionados declarações alteram o menu contextual acima da lista. Itens únicos podem usar o menu contextual acima da lista e as reticências contextuais individuais no final de cada linha expressão. 

### <a name="remove-entity-labels-from-utterances"></a>Remover rótulos de entidade do declarações

Você pode remover rótulos de entidade aprendidas por computador de um expressão na página de tentativas. Se a entidade não for aprendida por máquina, ela não poderá ser removida de um expressão. Se precisar remover uma entidade não aprendida por computador do expressão, você precisará excluir a entidade de todo o aplicativo. 

Para remover um rótulo de entidade aprendida por computador de um expressão, selecione a entidade no expressão. Em seguida, selecione **remover rótulo** na caixa suspensa entidade que aparece.

### <a name="add-a-prebuilt-entity-label"></a>Adicionar um rótulo de entidade predefinida

Ao adicionar as entidades predefinidas ao seu aplicativo LUIS, você não precisa marcar declarações com essas entidades. Para saber mais sobre entidades predefinidas e como adicioná-las, consulte [adicionar entidades](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Adicionar um rótulo de entidade de expressão regular

Se você adicionar as entidades de expressão regular ao seu aplicativo LUIS, não precisará marcar declarações com essas entidades. Para saber mais sobre entidades de expressão regular e como adicioná-las, confira [adicionar entidades](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Criar um padrão de um expressão

Consulte [Adicionar padrão de expressão existentes na página de intenção ou entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Adicionar um padrão. qualquer entidade

Se você adicionar o padrão. quaisquer entidades ao seu aplicativo LUIS, não será possível rotular declarações com essas entidades. Eles são válidos apenas em padrões. Para saber mais sobre o padrão. quaisquer entidades e como adicioná-las, consulte [adicionar entidades](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Treinar seu aplicativo depois de alterar o modelo com declarações

Depois de adicionar, editar ou remover o declarações, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que suas alterações afetem as consultas de ponto de extremidade. 

## <a name="next-steps"></a>Passos seguintes

Depois de rotular declarações em suas **intenções**, agora você pode criar uma [entidade composta](luis-how-to-add-entities.md).
