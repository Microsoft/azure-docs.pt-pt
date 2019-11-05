---
title: Adicionar tentativas-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione tentativas ao seu aplicativo LUIS para identificar grupos de perguntas ou comandos que têm as mesmas intenções.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 1f2f001489552203f0157dd24356341eb3184c81
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467557"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Adicionar tentativas para determinar a intenção do usuário de declarações

Adicione [intenções](luis-concept-intent.md) ao seu aplicativo Luis para identificar grupos de perguntas ou comandos que têm a mesma intenção. 

As intenções são gerenciadas na seção de **Build** da barra de navegação superior e, em seguida, das **tentativas**do painel esquerdo. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-intent"></a>Adicionar intenções

1. Na página **Intents** (Intenções), selecione **Create new intent** (Criar nova intenção).

1. Na caixa de diálogo **criar nova tentativa** , insira o nome da intenção, `GetEmployeeInformation`e clique em **concluído**.

    ![Adicionar intenção](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Adicionar um exemplo de expressão

Exemplo declarações são exemplos de texto de perguntas ou comandos do usuário. Para ensinar Reconhecimento vocal (LUIS), você precisa adicionar o declarações de exemplo a uma intenção.

1. Na página detalhes da tentativa de **GetEmployeeInformation** , insira um expressão relevante que você espera de seus usuários, como `Does John Smith work in Seattle?` na caixa de texto abaixo do nome da intenção e pressione Enter.
 
    ![Captura de tela da página de detalhes de tentativas, com expressão realçado](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converte todas as declarações para minúsculas e adiciona espaços em volta de tokens como hifens.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção 

Um exemplo de expressão em uma intenção pode ter um erro de previsão de intenção entre a intenção em que o exemplo expressão está atualmente em e a intenção de previsão determinada durante o treinamento. 

Para localizar erros de previsão do expressão e corrigi-los, use as opções de **avaliação** da opção de **filtro** de incorreto e não claro combinados com a opção **Exibir** da **exibição detalhada**. 

![Para localizar erros de previsão de expressão e corrigi-los, use a opção de filtro.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Quando os filtros e a exibição são aplicados e há um exemplo de declarações com erros, a lista de expressão de exemplo mostra o declarações e os problemas.

![! [Quando os filtros e a exibição são aplicados e há um exemplo de declarações com erros, a lista de expressão de exemplo mostra o declarações e os problemas.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Cada linha mostra a pontuação de previsão do treinamento atual para o exemplo expressão, a Pontuação do rival mais próximo, que é a diferença nessas duas pontuações. 

### <a name="fixing-intents"></a>Corrigindo tentativas

Para saber como corrigir erros de previsão de intenção, use o [painel de resumo](luis-how-to-use-dashboard.md). O painel de resumo fornece análise para o último treinamento da versão ativa e oferece as principais sugestões para corrigir seu modelo.  

## <a name="add-a-custom-entity"></a>Adicionar uma entidade personalizada

Depois que um expressão é adicionado a uma intenção, você pode selecionar o texto de dentro do expressão para criar uma entidade personalizada. Uma entidade personalizada é uma maneira de marcar o texto para extração, juntamente com a intenção correta. 

Consulte [Adicionar entidade ao expressão](luis-how-to-add-example-utterances.md) para saber mais.

## <a name="entity-prediction-discrepancy-errors"></a>Erros de discrepância de previsão de entidade 

A entidade é sublinhada em vermelho para indicar uma [discrepância de previsão de entidade](luis-how-to-add-example-utterances.md#entity-status-predictions). Como esta é a primeira ocorrência de uma entidade, não há exemplos suficientes para LUIS ter uma alta confiança de que esse texto é marcado com a entidade correta. Essa discrepância é removida quando o aplicativo é treinado. 

![Captura de tela da página de detalhes de tentativas, nome de entidade personalizado realçado em azul](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

O texto é realçado em azul, indicando uma entidade.  

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida

Para obter informações, consulte [entidade predefinida](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Usando a barra de ferramentas contextual

Quando um ou mais exemplos de declarações são selecionados na lista, marcando a caixa à esquerda de um expressão, a barra de ferramentas acima da lista expressão permite que você execute as seguintes ações:

* Reatribuir intenção: mover expressão (s) para outra intenção
* Excluir expressão (s)
* Filtros de entidade: mostrar somente declarações contendo entidades filtradas
* Mostrar somente todos/erros: mostrar declarações com erros de previsão ou mostrar todos os declarações
* Exibição de entidades/tokens: mostrar exibição de entidades com nomes de entidade ou mostrar texto bruto de expressão
* Lupa: pesquisar declarações contendo texto específico

## <a name="working-with-an-individual-utterance"></a>Trabalhando com um expressão individual

As ações a seguir podem ser executadas em um expressão individual no menu de reticências à direita do expressão:

* Editar: alterar o texto do expressão
* Excluir: Remova o expressão da intenção. Se você ainda quiser o expressão, um método melhor é movê-lo para a intenção **None** . 
* Adicionar um padrão: um padrão permite que você faça um expressão comum e marque texto substituível e texto ignorável, reduzindo assim a necessidade de mais declarações na intenção. 

A coluna de **intenção rotulada** permite que você altere a intenção do expressão.

## <a name="train-your-app-after-changing-model-with-intents"></a>Treinar seu aplicativo depois de alterar o modelo com tentativas

Depois de adicionar, editar ou remover tentativas, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que suas alterações sejam aplicadas às consultas de ponto de extremidade. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como adicionar [declarações de exemplo](luis-how-to-add-example-utterances.md) com entidades. 
