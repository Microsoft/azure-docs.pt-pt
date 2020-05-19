---
title: Características - LUIS
titleSuffix: Azure Cognitive Services
description: Use a Compreensão da Linguagem (LUIS) para adicionar funcionalidades de aplicações que possam melhorar a deteção ou previsão de intenções e entidades que categorias e padrões
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 12445ec5b14f4c274e471bf1b061a3b221664d20
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592309"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Utilize funcionalidades para aumentar o sinal da lista de palavras

Pode adicionar funcionalidades à sua aplicação LUIS para melhorar a sua precisão. As funcionalidades ajudam o LUIS, fornecendo dicas de que certas palavras e frases fazem parte de um vocabulário de domínio de aplicação.

Reveja [os conceitos](luis-concept-feature.md) para entender quando e porquê usar uma funcionalidade.

## <a name="add-phrase-list-as-a-feature"></a>Adicione a lista de frases como recurso

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione **Build**e, em seguida, selecione **Funcionalidades** no painel esquerdo da sua aplicação.

1. Na página **Funcionalidades,** selecione **+ Criar**.

1. Na **nova lista** de frases, introduza um nome como `Cities` . Na caixa **Valor,** insira exemplos das cidades, tais como `Seattle` . Pode escrever um valor de cada vez, ou um conjunto de valores separados por vírgulas e, em seguida, pressionar **Enter**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar recurso (lista de frases) Cidades](./media/luis-add-features/add-phrase-list-cities.png)

    Depois de ter introduzido valores suficientes para o LUIS, surgem sugestões. Pode **+ Adicionar todos os** valores propostos ou selecionar termos individuais.

1. Mantenha **estes valores verificados intercambiáveis** se as frases puderem ser utilizadas de forma interpermutada.

1. A lista de frases pode aplicar-se a toda a aplicação com a definição **Global,** ou a um modelo específico (intenção ou entidade). Se criar a lista de frases, como _recurso_ de uma intenção ou entidade, o toggle não está definido para global. Neste caso, o significado do alternância é que a funcionalidade é local apenas para esse modelo, portanto, _não global_ para a aplicação.

1. Selecione **Done** (Concluído). A nova funcionalidade é adicionada à página **ml Features.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Pode eliminar ou desativar uma lista de frases a partir da barra de ferramentas contextual na página **ml Features.**

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar, editar, apagar ou desativar uma funcionalidade, [treinar e testar novamente a app](luis-interactive-test.md) para ver se o desempenho melhora.
