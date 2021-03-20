---
title: Use funcionalidades para melhorar a lista de palavras LUIS
titleSuffix: Azure Cognitive Services
description: Use o Com understanding idioma (LUIS) para adicionar funcionalidades de aplicações que possam melhorar a deteção ou previsão de intenções e entidades que categorias e padrões
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 33435d2367b92f312d41f0e32fdbb3a93d59a9fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999931"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Use funcionalidades para aumentar o sinal da lista de palavras

Pode adicionar funcionalidades à sua app LUIS para melhorar a sua precisão. As funcionalidades ajudam o LUIS, fornecendo dicas de que certas palavras e frases fazem parte de um vocabulário de domínio de aplicações.

Reveja [conceitos](luis-concept-feature.md) para entender quando e porquê usar uma funcionalidade.

## <a name="add-phrase-list-as-a-feature"></a>Adicione a lista de frases como uma característica

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione **Build** e, em seguida, selecione **Funcionalidades** no painel esquerdo da sua aplicação.

1. Na página **'Funcionalidades',** selecione **+ Criar**.

1. Na caixa de diálogo **de lista de novas frases,** introduza um nome como `Cities` . Na caixa **de valor,** insira exemplos das cidades, tais `Seattle` como. Pode escrever um valor de cada vez, ou um conjunto de valores separados por vírgulas e, em seguida, **premir Enter**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar recurso (lista de frases) Cidades](./media/luis-add-features/add-phrase-list-cities.png)

    Depois de ter introduzido valores suficientes para o LUIS, surgem sugestões. Pode **+ Adicionar todos os** valores propostos ou selecionar termos individuais.

1. Manter **estes valores são permutáveis** verificados se as frases podem ser utilizadas intercambiavelmente.

1. A lista de frases pode aplicar-se a toda a aplicação com a configuração **Global,** ou a um modelo específico (intenção ou entidade). Se criar a lista de frases, como _recurso_ de uma intenção ou entidade, o toggle não está definido para global. Neste caso, o significado do toggle é que a funcionalidade é local apenas para este modelo, portanto, _não global_ para a aplicação.

1. Selecione **Concluído**. A nova funcionalidade é adicionada à página **ML Features.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> Pode eliminar ou desativar uma lista de frases da barra de ferramentas contextual na página **ML Features.**

## <a name="global-phrase-list-applies-to-entire-app"></a>Lista global de frases aplica-se a toda a app

Uma lista de frases deve ser aplicada à intenção ou entidade que se destina a ajudar, mas pode haver momentos em que uma lista de frases deve ser aplicada a toda a app como uma funcionalidade **Global.**

Na página ML Features, selecione a lista de frases e, em seguida, **selecione Tornar global** na barra de ferramentas contextual superior.

## <a name="model-as-a-feature"></a>Modelo como recurso

Uma entidade pode ser uma [característica para uma intenção ou entidade.](luis-concept-feature.md)

Para adicionar uma entidade como recurso a uma intenção, selecione a intenção na página Intenções e, em seguida, **selecione + Adicione a funcionalidade** acima da barra de ferramentas contextual. A lista incluirá todas as listas de frases e entidades que podem ser aplicadas como funcionalidades.

Para adicionar uma entidade como recurso a outra entidade, pode adicionar a funcionalidade na página de detalhes de Intenção utilizando a [Paleta de Entidade](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) ou pode adicionar a [funcionalidade](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) na página de detalhes da Entidade.

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar, editar, eliminar ou desativar uma funcionalidade, [treine e teste novamente a aplicação](luis-interactive-test.md) para ver se o desempenho melhora.
