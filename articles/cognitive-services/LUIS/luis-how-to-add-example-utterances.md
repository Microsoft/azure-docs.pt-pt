---
title: Adicionar exemplo declarações-LUIS
titleSuffix: Azure Cognitive Services
description: Expressões com de exemplo são exemplos de texto de perguntas de utilizador ou de comandos. Ensinar a compreensão de idiomas (LUIS), terá de adicionar expressões de exemplo para um objetivo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 04f0944173df59989745ee9167984b493f202b14
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638232"
---
# <a name="add-an-entity-to-example-utterances"></a>Adicionar uma entidade a expressões de exemplo 

Expressões com de exemplo são exemplos de texto de perguntas de utilizador ou de comandos. Ensinar a compreensão de idiomas (LUIS), precisa adicionar [expressões de exemplo](luis-concept-utterance.md) para um [intenção](luis-concept-intent.md).

Normalmente, você adiciona um exemplo expressão a uma intenção primeiro e, em seguida, cria entidades e o rótulo declarações na página de **tentativas** . Se o primeiro, em vez disso, seria criar entidades, veja [adicionar entidades](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Marcar entidades em expressões de exemplo

Quando seleciona texto da expressão de exemplo para marcar para uma entidade, é apresentado um menu de pop-up no local. Use esse menu para criar ou selecionar uma entidade. 

Determinados tipos de entidade, como entidades previamente concebidas e entidades de expressão regular, não podem ser marcados na expressão de exemplo, uma vez estão etiquetadas automaticamente. 

## <a name="add-a-simple-entity"></a>Adicione uma entidade

No procedimento a seguir, você cria e marca uma entidade personalizada dentro do seguinte expressão na página de **tentativas** :

```text
Are there any SQL server jobs?
```

1. Selecione `SQL server` na expressão que identifique-la como uma entidade. Na caixa de lista pendente de entidade que aparece, pode selecionar uma entidade existente ou adicionar uma nova entidade. Para adicionar uma nova entidade, digite seu nome `Job` na caixa de texto e, em seguida, selecione **criar nova entidade**.

    ![Captura de ecrã da introdução do nome da entidade](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Quando selecionar as palavras a marca como entidades:
    > * Para uma única palavra, basta selecioná-lo. 
    > * Para um conjunto de duas ou mais palavras, selecione a primeira palavra e, em seguida, a palavra final.

1. Na **o tipo de entidade que pretende criar?** caixa pop-up, verifique o nome da entidade e selecione o **simples** tipo de entidade e, em seguida, selecione **feito**.

    R [lista de frase](luis-concept-feature.md) é frequentemente utilizada para impulsionar o sinal de uma entidade.

## <a name="add-a-list-entity"></a>Adicionar uma entidade de lista

As entidades de lista representam um conjunto de correspondências exatas de texto de palavras relacionadas em seu sistema. 

Para obter lista de departamento da empresa, pode ter normalizados valores: `Accounting` e `Human Resources`. Cada nome normalizado tem sinónimos. Para um departamento, estes sinónimos podem incluir qualquer acrônimos de departamento, números ou gíria. Não precisa saber todos os valores quando criar a entidade. Pode adicionar mais depois de rever as expressões de utilizador real com sinónimos.

1. Em um exemplo de expressão na página de **tentativas** , selecione a palavra ou frase desejada na nova lista. Quando a lista suspensa entidade for exibida, insira o nome para a nova entidade na caixa de texto superior e, em seguida, selecione **criar nova entidade**.   

1. Na **o tipo de entidade que pretende criar?** caixa pop-up, o nome da entidade e selecione **lista** como o tipo. Adicionar sinónimos deste item de lista, em seguida, selecione **feito**. 

    ![Captura de ecrã da introdução de sinónimos de entidade de lista](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Pode adicionar mais itens de lista ou mais de item de sinónimos ao etiquetagem outras expressões ou ao editar a entidade a partir da **entidades** na navegação à esquerda. [Editar](luis-how-to-add-entities.md#add-list-entities) as entidades dá-lhe as opções da introdução itens adicionais com correspondente sinónimos ou importando uma lista. 

## <a name="add-a-composite-entity"></a>Adicionar uma entidade composta

As entidades compostas são criadas a partir de **entidades** existentes para formar uma entidade pai. 

Supondo que expressão `Does John Smith work in Seattle?`,, um expressão composto possa retornar informações de entidade do nome `John Smith`do funcionário e o `Seattle` local em uma entidade composta. As entidades filho já devem existir no aplicativo e ser marcadas no exemplo expressão antes de criar a entidade composta.

1. Para encapsular as entidades filho em uma entidade composta, selecione a **primeira** entidade rotulada (mais à esquerda) no expressão para a entidade composta. Uma lista suspensa é exibida para mostrar as opções para essa seleção.

1. Selecione **encapsular em entidade composta** na lista suspensa. 

1. Selecione a última palavra da entidade composta (mais à direita). Observe que uma linha verde segue a entidade composta. Este é o indicador visual de uma entidade composta e deve estar sob todas as palavras na entidade composta da entidade filho mais à esquerda para a entidade filho mais à direita.

1. Introduza o nome da entidade composta na lista pendente.

    Quando encapsula as entidades corretamente, uma linha verde está sob a frase inteira.

1. Validar os detalhes da entidade compostos no **o tipo de entidade que pretende criar?** pop-up caixa em seguida, selecione **feito**.

    ![Detalhes de captura de ecrã da entidade pop-up](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Apresenta a entidade composta com ambos os destaques azuis para entidades individuais e um sublinhado verde para a entidade inteira composto. 

    ![Página de detalhes de captura de ecrã de objetivos, com a entidade composta](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Adicionar função da entidade ao expressão

Uma função é um subtipo nomeado de uma entidade, determinado pelo contexto do expressão. Você pode marcar uma entidade em um expressão como a entidade ou selecionar uma função dentro dessa entidade. Qualquer entidade pode ter funções, incluindo entidades personalizadas que são aprendidas por máquina (entidades simples e entidades compostas), não são aprendidas por máquina (entidades predefinidas, entidades de expressão regular, entidades de lista). 

Saiba [como marcar um expressão com funções de entidade](tutorial-entity-roles.md) de um tutorial prático. 

## <a name="entity-status-predictions"></a>Previsões de estado de entidade

Ao introduzir uma expressão de novo no portal do LUIS, a expressão pode ter erros de predição de entidade. O erro de predição é uma diferença entre como assinalada como uma entidade em comparação com como LUIS fez uma previsão a entidade. 

Essa diferença visualmente é representada no portal do LUIS com um sublinhado em vermelho na expressão. O sublinhado em vermelho poderão aparecer entre parênteses Retos de entidade ou fora de parênteses Retos. 

![Discrepância de predição de estado de captura de ecrã da entidade](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Selecione as palavras que são sublinhadas em vermelho na expressão. 

Apresenta a caixa de entidade a **estado de entidade** com uma marca de exclamação vermelha se existe uma discrepância de predição. Para ver o estado de entidade com informações sobre a diferença entre entidades com nome e previstas, selecione **estado de entidade** , em seguida, selecione o item para a direita.

![Captura de tela da seleção de status da entidade](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

A linha de vermelho pode aparecer em qualquer um dos seguintes horas:

   * Quando uma expressão é introduzido, mas antes da entidade tem o nome
   * Quando é aplicada a etiqueta de entidade
   * Quando a etiqueta de entidade é removida
   * Quando mais do que uma etiqueta de entidade é prevista para esse texto 

As seguintes soluções de ajudar a resolver as discrepâncias de predição de entidade:

|Entidade|Indicador visual|predição|Solução|
|--|--|--|--|
|Expressão introduzido, entidade não é rotulado como ainda.|sublinhado em vermelho|Predição está correta.|A entidade com o valor previsto da etiqueta.|
|Texto sem etiqueta|sublinhado em vermelho|Previsão incorreta|As expressões atuais através desta entidade incorreta tem de ser revisto em todas as intenções. As expressões atuais tem mistaught LUIS que este texto é a entidade prevista.
|Corretamente etiquetado texto|entidade azul, realce o sublinhado em vermelho|Previsão incorreta|Forneça mais expressões com a entidade etiquetada corretamente numa variedade de locais e utilizações. As expressões atuais são não é suficiente para ensinar LUIS que se trata a entidade é ou aparecem as entidades semelhantes no mesmo contexto. Entidade semelhante deve ser combinada numa única entidade então LUIS não é confuso. Outra solução é adicionar uma lista de frase para aumentar a significância das palavras. |
|Incorretamente etiquetado texto|entidade azul, realce o sublinhado em vermelho|Previsão correta| Forneça mais expressões com a entidade etiquetada corretamente numa variedade de locais e utilizações. 

## <a name="other-actions"></a>Outras ações

Pode executar ações em expressões de exemplo como um grupo selecionado ou como um item individual. Grupos de expressões de exemplo selecionado alterar o menu contextual acima da lista. Itens únicas podem utilizar o menu contextual acima da lista e o botão de reticências contextual individual no final de cada linha da expressão. 

### <a name="remove-entity-labels-from-utterances"></a>Remover etiquetas de entidade de expressões

Você pode remover rótulos de entidade aprendidas por computador de um expressão na página de tentativas. Se a entidade não ficou a saber de máquina, não pode ser removido de uma expressão. Se precisar de remover a expressão a uma entidade não aprendidas máquina, terá de eliminar a entidade de todo o aplicativo. 

Para remover uma etiqueta de entidade aprendidas de máquina de uma expressão, selecione a entidade a expressão. Em seguida, selecione **remover etiqueta** na caixa de lista pendente de entidade é apresentada.

### <a name="add-a-prebuilt-entity-label"></a>Adicionar um rótulo de entidade predefinida

Quando adiciona as entidades previamente concebidas para a sua aplicação LUIS, não precisa de expressões de etiqueta com estas entidades. Para saber mais sobre entidades previamente concebidas e como adicioná-las, veja [adicionar entidades](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Adicionar um rótulo de entidade de expressão regular

Se adicionar as entidades de expressão regular para a sua aplicação LUIS, não precisa de expressões de etiqueta com estas entidades. Para saber mais sobre entidades de expressão regular e como adicioná-las, veja [adicionar entidades](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Criar um padrão a partir de uma expressão

Ver [adicionar padrão de expressão existente na página de intenção ou a entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Adicionar um padrão. qualquer entidade

Se adicionar as entidades de pattern.any à sua aplicação LUIS, não é possível Etiquetar expressões com estas entidades. Apenas são válidas em padrões. Para saber mais sobre entidades pattern.any e como adicioná-las, veja [adicionar entidades](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Preparar a sua aplicação depois de alterar o modelo com expressões com

Depois de adicionar, editar ou remover expressões, [treinar](luis-how-to-train.md) e [publicar](luis-how-to-publish-app.md) as suas alterações afetar a consultas de ponto final na sua aplicação. 

## <a name="next-steps"></a>Passos seguintes

Depois de rotular declarações em suas **intenções**, agora você pode criar uma [entidade composta](luis-how-to-add-entities.md).
