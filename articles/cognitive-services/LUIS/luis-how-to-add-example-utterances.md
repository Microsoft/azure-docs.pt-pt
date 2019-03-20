---
title: Adicionar expressões de exemplo
titleSuffix: Language Understanding - Azure Cognitive Services
description: Expressões com de exemplo são exemplos de texto de perguntas de utilizador ou de comandos. Ensinar a compreensão de idiomas (LUIS), terá de adicionar expressões de exemplo para um objetivo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 1dac87ae07fac6a997cfd8e83c1e47ff39a91a83
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096695"
---
# <a name="add-an-entity-to-example-utterances"></a>Adicionar uma entidade a expressões de exemplo 

Expressões com de exemplo são exemplos de texto de perguntas de utilizador ou de comandos. Ensinar a compreensão de idiomas (LUIS), precisa adicionar [expressões de exemplo](luis-concept-utterance.md) para um [intenção](luis-concept-intent.md).

Normalmente, adicione uma expressão de exemplo para um objetivo primeiro e, em seguida, criar entidades e expressões de etiqueta na página de intenção. Se o primeiro, em vez disso, seria criar entidades, veja [adicionar entidades](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Marcar entidades em expressões de exemplo

Quando seleciona texto da expressão de exemplo para marcar para uma entidade, é apresentado um menu de pop-up no local. Use esse menu para criar ou selecionar uma entidade. 

Determinados tipos de entidade, como entidades previamente concebidas e entidades de expressão regular, não podem ser marcados na expressão de exemplo, uma vez estão etiquetadas automaticamente. 

## <a name="add-a-simple-entity"></a>Adicione uma entidade

No procedimento seguinte, criar e etiquetar uma entidade personalizada na seguinte expressão na página de intenção:

```text
Are there any SQL server jobs?
```

1. Selecione `SQL server` na expressão que identifique-la como uma entidade. Na caixa de lista pendente de entidade que aparece, pode selecionar uma entidade existente ou adicionar uma nova entidade. Para adicionar uma nova entidade, escreva o nome `Job` na caixa de texto e, em seguida, selecione **criar nova entidade**.

    ![Captura de ecrã da introdução do nome da entidade](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Quando selecionar as palavras a marca como entidades:
    > * Para uma única palavra, basta selecioná-lo. 
    > * Para um conjunto de duas ou mais palavras, selecione no início e, em seguida, no final do conjunto.

1. Na **o tipo de entidade que pretende criar?** caixa pop-up, verifique o nome da entidade e selecione o **simples** tipo de entidade e, em seguida, selecione **feito**.

    R [lista de frase](luis-concept-feature.md) é frequentemente utilizada para impulsionar o sinal de uma entidade.

## <a name="add-a-list-entity"></a>Adicionar uma entidade de lista

Lista de entidades representam um conjunto de correspondências de texto exato de palavras relacionadas no seu sistema. 

Para obter lista de departamento da empresa, pode ter normalizados valores: `Accounting` e `Human Resources`. Cada nome normalizado tem sinónimos. Para um departamento, estes sinónimos podem incluir qualquer acrônimos de departamento, números ou gíria. Não precisa saber todos os valores quando criar a entidade. Pode adicionar mais depois de rever as expressões de utilizador real com sinónimos.

1. Numa expressão de exemplo sobre o **intenções** , selecione a palavra ou expressão que pretende na lista de novo. Quando for apresentada a lista pendente de entidade, introduza o nome para a nova entidade de lista na caixa de texto superior, em seguida, selecione **criar nova entidade**.   

1. Na **o tipo de entidade que pretende criar?** caixa pop-up, o nome da entidade e selecione **lista** como o tipo. Adicionar sinónimos deste item de lista, em seguida, selecione **feito**. 

    ![Captura de ecrã da introdução de sinónimos de entidade de lista](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Pode adicionar mais itens de lista ou mais de item de sinónimos ao etiquetagem outras expressões ou ao editar a entidade a partir da **entidades** na navegação à esquerda. [Editar](luis-how-to-add-entities.md#add-list-entities) as entidades dá-lhe as opções da introdução itens adicionais com correspondente sinónimos ou importando uma lista. 

## <a name="add-composite-entity"></a>Adicionar entidade composta

Entidades compostas criadas a partir de existente **entidades** numa entidade principal. 

A expressão, partindo do princípio `Does John Smith work in Seattle?`, uma expressão composta pode devolver informações da entidade do nome do funcionário `John Smith`e a localização `Seattle` numa entidade composta. As entidades subordinadas tem de existir na aplicação e ser marcado como na expressão de exemplo antes de criar a entidade composta.

1. Para concluir as entidades subordinadas para uma entidade composta, selecione o **primeiro** rotulado como entidade (mais à esquerda) na expressão para a entidade composta. É apresentada uma lista de baixo mostrar as opções para esta seleção.

1. Selecione **encapsular na entidade composta** na lista pendente. 

1. Selecione a última palavra da entidade composta (mais à direita). Observe que uma linha verde segue a entidade composta. Este é o indicador visual para uma entidade composto e deve ser em todas as palavras na entidade composta da entidade filho mais à esquerda para a entidade de filho na extrema direita.

1. Introduza o nome da entidade composta na lista pendente.

    Quando encapsula as entidades corretamente, uma linha verde está sob a frase inteira.

1. Validar os detalhes da entidade compostos no **o tipo de entidade que pretende criar?** pop-up caixa em seguida, selecione **feito**.

    ![Detalhes de captura de ecrã da entidade pop-up](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Apresenta a entidade composta com ambos os destaques azuis para entidades individuais e um sublinhado verde para a entidade inteira composto. 

    ![Página de detalhes de captura de ecrã de objetivos, com a entidade composta](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>Adicionar entidade hierárquica

Uma entidade hierárquica é uma categoria de entidades contextualmente aprendidas e conceitualmente relacionadas. No exemplo seguinte, a entidade contém localizações de origem e de destino. 

Na expressão `Move John Smith from Seattle to Cairo`, Seattle é a localização de origem e Cairo é a localização de destino. Cada localização é diferente e aprendidas contextualmente de ordem das palavras e a escolha do word na expressão.

1. Na página de intenção, na expressão, selecione `Seattle`, em seguida, introduza o nome da entidade `Location`e, em seguida, selecione Enter no teclado.

1. Na **o tipo de entidade que pretende criar?** caixa pop-up, selecione _hierárquica_ para **tipo de entidade**, em seguida, adicione `Origin` e `Destination` como filhos, e, em seguida, selecione **feito**.

    ![Página de detalhes de captura de ecrã de objetivos, com a entidade de ToLocation realçada](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. A palavra a expressão foi identificada com a entidade de hierárquica do principal. Terá de atribuir a palavra a entidade subordinada. Voltar para a expressão na página de detalhes de intenção. Selecione o word, em seguida, na lista pendente, escolha o nome da entidade que criou e siga o menu à direita para escolher a entidade de filho correta.

    >[!CAUTION]
    >Os nomes de entidades de subordinado tem de ser exclusivos em todas as entidades numa única aplicação. Duas entidades hierárquicas diferentes não podem conter entidades subordinadas com o mesmo nome. 

## <a name="entity-status-predictions"></a>Previsões de estado de entidade

Ao introduzir uma expressão de novo no portal do LUIS, a expressão pode ter erros de predição de entidade. O erro de predição é uma diferença entre como assinalada como uma entidade em comparação com como LUIS fez uma previsão a entidade. 

Essa diferença visualmente é representada no portal do LUIS com um sublinhado em vermelho na expressão. O sublinhado em vermelho poderão aparecer entre parênteses Retos de entidade ou fora de parênteses Retos. 

![Discrepância de predição de estado de captura de ecrã da entidade](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Selecione as palavras que são sublinhadas em vermelho na expressão. 

Apresenta a caixa de entidade a **estado de entidade** com uma marca de exclamação vermelha se existe uma discrepância de predição. Para ver o estado de entidade com informações sobre a diferença entre entidades com nome e previstas, selecione **estado de entidade** , em seguida, selecione o item para a direita.

![Seleção de estado de captura de ecrã da entidade](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

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

> [!Note]
> Quando é de uma caixa vermelha à volta a intenção etiquetada na linha da expressão de exemplo, um [erro de previsão de intenção](luis-how-to-add-intents.md#intent-prediction-discrepancy-errors) ocorreu. Tem de corrigi-lo. 

## <a name="other-actions"></a>Outras ações

Pode executar ações em expressões de exemplo como um grupo selecionado ou como um item individual. Grupos de expressões de exemplo selecionado alterar o menu contextual acima da lista. Itens únicas podem utilizar o menu contextual acima da lista e o botão de reticências contextual individual no final de cada linha da expressão. 

### <a name="remove-entity-labels-from-utterances"></a>Remover etiquetas de entidade de expressões

Pode remover as etiquetas de entidade aprendidas de máquina de uma expressão na página de intenção. Se a entidade não ficou a saber de máquina, não pode ser removido de uma expressão. Se precisar de remover a expressão a uma entidade não aprendidas máquina, terá de eliminar a entidade de todo o aplicativo. 

Para remover uma etiqueta de entidade aprendidas de máquina de uma expressão, selecione a entidade a expressão. Em seguida, selecione **remover etiqueta** na caixa de lista pendente de entidade é apresentada.

### <a name="add-prebuilt-entity-label"></a>Adicionar etiqueta de entidade predefinidos

Quando adiciona as entidades previamente concebidas para a sua aplicação LUIS, não precisa de expressões de etiqueta com estas entidades. Para saber mais sobre entidades previamente concebidas e como adicioná-las, veja [adicionar entidades](luis-how-to-add-entities.md#add-prebuilt-entity).

### <a name="add-regular-expression-entity-label"></a>Adicionar etiqueta de entidade de expressão regular

Se adicionar as entidades de expressão regular para a sua aplicação LUIS, não precisa de expressões de etiqueta com estas entidades. Para saber mais sobre entidades de expressão regular e como adicioná-las, veja [adicionar entidades](luis-how-to-add-entities.md#add-regular-expression-entities).


### <a name="create-a-pattern-from-an-utterance"></a>Criar um padrão a partir de uma expressão

Ver [adicionar padrão de expressão existente na página de intenção ou a entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Adicionar entidade pattern.any

Se adicionar as entidades de pattern.any à sua aplicação LUIS, não é possível Etiquetar expressões com estas entidades. Apenas são válidas em padrões. Para saber mais sobre entidades pattern.any e como adicioná-las, veja [adicionar entidades](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Preparar a sua aplicação depois de alterar o modelo com expressões com

Depois de adicionar, editar ou remover expressões, [treinar](luis-how-to-train.md) e [publicar](luis-how-to-publish-app.md) as suas alterações afetar a consultas de ponto final na sua aplicação. 

## <a name="next-steps"></a>Passos Seguintes

Depois de etiquetagem discursos em suas intenções, agora, pode criar uma [entidade composta](luis-how-to-add-entities.md).
