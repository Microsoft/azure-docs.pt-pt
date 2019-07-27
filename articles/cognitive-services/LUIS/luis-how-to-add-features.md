---
title: Listas de frases-LUIS
titleSuffix: Azure Cognitive Services
description: Utilizar a compreensão de idiomas (LUIS) para adicionar funcionalidades de aplicação que podem melhorar a deteção ou a predição de objetivos e entidades que categorias e padrões
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: 75764fd0a3f862157d9377d7dc886334ef1231db
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563721"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Apresenta uma lista de frase de utilização para o sinal de aumento de lista de palavras

Pode adicionar funcionalidades à sua aplicação LUIS para melhorar a precisão. Recursos ajudam o LUIS, fornecendo as sugestões que determinadas palavras e frases fazem parte de um vocabulário de domínio de aplicação. 

R [lista de frase](luis-concept-feature.md) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e deve ser tratado de maneira semelhante (por exemplo, nomes de cidades ou produtos). O que LUIS aprende sobre uma delas é aplicada automaticamente para os outros também. Esta lista não é uma entidade de lista fechado (corresponde a texto exato) de palavras correspondentes.

Uma lista de frase adiciona ao vocabulário do domínio de aplicativo como um sinal de segundo para LUIS sobre essas palavras.

## <a name="add-phrase-list"></a>Adicionar a lista de frase

LUIS permite até 10 listas de frases por aplicativo. 

1. Abra a sua aplicação ao clicar em seu nome no **as minhas aplicações** página e, em seguida, clique em **crie**, em seguida, clique em **frase listas** no painel esquerdo da sua aplicação. 

2. Sobre o **frase listas** página, clique em **criar nova lista de frase**. 
 
3. Na **lista de frase adicionar** caixa de diálogo, escreva "Cidades" como o nome da lista frase. Na **valor** , escreva os valores da lista frase. Escreva um valor numa hora ou um conjunto de valores separados por vírgulas e, em seguida, prima **Enter**.

    ![Adicionar frase lista cidades](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS pode propor valores relacionados para adicionar à sua lista de frase. Clique em **Recomendamos** para obter um grupo de valores propostos semanticamente relacionados com o added value(s). Pode clicar em qualquer um dos valores propostos ou clique em **adicionar todos os** para adicioná-los todos.

    ![Valores de lista de frases propostas – adicionar tudo](./media/luis-add-features/related-values.png)

5. Clique em **estes valores são intercambiáveis** se os valores de lista de frase adicionados são alternativas que podem ser utilizadas alternadamente.

    ![Valores propostos da lista de frases – selecionar caixa intercambiável](./media/luis-add-features/interchangeable.png)

6. Clique em **Guardar**. A lista de frase "Cidades" é adicionada para o **frase listas** página.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Pode eliminar ou desativar uma lista de frase da barra de ferramentas contextual sobre o **frase listas** página.

## <a name="next-steps"></a>Passos Seguintes

Após adicionar, editar, eliminar ou desativar uma lista de frase [treinar e testar a aplicação](luis-interactive-test.md) novamente para ver se melhora o desempenho.
