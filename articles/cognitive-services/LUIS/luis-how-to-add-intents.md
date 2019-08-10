---
title: Adicionar tentativas-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione intenções à sua aplicação LUIS para identificar os grupos de perguntas ou comandos que têm as mesmo intenções.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: eb90a902b8f7fe8b37b81c2825cbdfc25ef5dc0d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932898"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Adicionar tentativas para determinar a intenção do usuário de declarações

Adicione [intenções](luis-concept-intent.md) à sua aplicação LUIS para identificar os grupos de perguntas ou comandos que têm a mesma intenção. 

Objetivos são geridos a partir da barra de navegação superior **crie** secção, em seguida, a partir do painel esquerdo **intenções**. 

## <a name="add-intent"></a>Adicionar intenções

1. Na página **Intents** (Intenções), selecione **Create new intent** (Criar nova intenção).

1. Na **criar novo intenção** caixa de diálogo, introduza o nome da intenção, `GetEmployeeInformation`e clique em **feito**.

    ![Adicionar intenções](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Adicionar uma expressão de exemplo

Expressões com de exemplo são exemplos de texto de perguntas de utilizador ou de comandos. Ensinar a compreensão de idiomas (LUIS), terá de adicionar expressões de exemplo para um objetivo.

1. Sobre o **GetEmployeeInformation** detalhes da intenção página, introduza uma expressão relevante que se espera de seus usuários, como `Does John Smith work in Seattle?` na caixa de texto abaixo do nome intenção e, em seguida, prima Enter.
 
    ![Página de detalhes de captura de ecrã de objetivos, com a expressão realçado](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converte todas as expressões de com em minúsculas e adiciona os espaços em torno de tokens, como hífens.

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

Depois de uma expressão é adicionado a uma intenção, pode selecionar o texto da dentro da expressão para criar uma entidade personalizada. Uma entidade personalizada é uma forma de texto da etiqueta de extração, juntamente com a intenção correta. 

Consulte [Adicionar entidade ao expressão](luis-how-to-add-example-utterances.md) para saber mais.

## <a name="entity-prediction-discrepancy-errors"></a>Erros de discrepância de predição de entidade 

A entidade está sublinhada em vermelho para indicar uma [discrepância de predição de entidade](luis-how-to-add-example-utterances.md#entity-status-predictions). Uma vez que esta é a primeira ocorrência de uma entidade, não há suficiente exemplos para LUIS ter uma confiança elevada que este texto é marcado com a entidade correta. Essa discrepância é removida quando a aplicação é preparada. 

![Página de detalhes de captura de ecrã de objetivos, nome de entidade personalizada realçado em azul](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

O texto é realçado em azul, indicando uma entidade.  

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade pré-criados

Para obter informações, consulte [criados previamente entidade](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

## <a name="using-the-contextual-toolbar"></a>Usando a barra de ferramentas contextual

Quando um ou mais exemplos de declarações são selecionados na lista, marcando a caixa à esquerda de um expressão, a barra de ferramentas acima da lista expressão permite que você execute as seguintes ações:

* Reatribuir intenção: mover utterance(s) em intenção diferente
* Eliminar utterance(s)
* Filtros de entidade: Mostrar apenas as expressões que contém entidades filtradas
* Mostrar tudo / apenas erros: Mostrar expressões com erros de predição ou mostrar todas as expressões de com
* Vista de entidades/Tokens: Mostrar a exibição de entidades com nomes de entidade ou mostrar o texto não processado de expressão
* Lupa: procure expressões com contendo texto específico

## <a name="working-with-an-individual-utterance"></a>Trabalhar com uma expressão individual

As seguintes ações podem ser executadas numa expressão individual no menu de reticências à direita da expressão:

* Editar: alterar o texto da expressão
* Eliminação: remova a expressão a intenção. Se pretender continuar a expressão, um método melhor é para movê-lo para o **None** intenção. 
* Adicionar um padrão: Um padrão permite que você faça um expressão comum e marque o texto substituível e o texto ignorável, reduzindo assim a necessidade de mais declarações na intenção. 

O **rotulado intenção** coluna permite-lhe alterar o objetivo da expressão.

## <a name="train-your-app-after-changing-model-with-intents"></a>Preparar a sua aplicação depois de alterar o modelo com objetivos

Depois de adicionar, editar ou remover intenções, [treinar](luis-how-to-train.md) e [publicar](luis-how-to-publish-app.md) seu aplicativo para que as suas alterações são aplicadas às consultas de ponto final. 

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como adicionar [expressões de exemplo](luis-how-to-add-example-utterances.md) com entidades. 
