---
title: Como utilizar entidades Negatable com um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar entidades Negatable com um modelo de aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 78dc759632c4fc3116a59ea1e5bc0b93200bca45
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168192"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Como utilizar entidades Negatable com um modelo de aprendiz de conversação

Este tutorial demonstra a propriedade de "Negatable" de entidades.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do negatable entidades](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o tutorial geral Bot está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
A propriedade de "Negatable" de uma entidade permite-lhe rotular tanto normal (positivo) e negativos instâncias da entidade, ensinar com base em modelos positivos e negativos e limpar o valor de uma entidade existente. Entidades com seu conjunto de propriedade de "Negatable" são denominadas entidades Negatable na conversação Leaner.

## <a name="steps"></a>Passos

Comece na home page na IU da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Introduza **NegatableEntity** para **nome**.
3. Selecione **Criar**.

### <a name="entity-creation"></a>Criação de entidade

1. Selecione **entidades** no painel esquerdo, em seguida, **nova entidade**.
2. Selecione **personalizado treinado** para **tipo de entidade**.
3. Introduza **name** para **nome da entidade**.
4. Verifique **Negatable** permitir que os utilizadores fornecer um valor de entidade ou, digamos que algo está *não* valor de uma entidade, deste modo, a eliminar o valor de entidade correspondente.
5. Selecione **Criar**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Introduza **não sei que o seu nome.** para **de resposta do Bot...** .
3. Introduza **name** para **Disqualifying dá direito**.
4. Selecione **Criar**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Introduza **sei seu nome. É $name.** para **de resposta do Bot...** .
3. Selecione **Criar**.

> [!NOTE]
> O **name** entidade foi adicionada automaticamente como um **necessárias entidades** por referência na expressão de resposta.

Agora tem duas ações.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Preparar o modelo

1. Selecione **caixas de diálogo Train** no painel esquerdo, em seguida, **caixa de diálogo do novo Train**.
2. Introduza **hello** para expressão do usuário no painel à esquerda de bate-papo.
3. Selecione **pontuação ações**.
4. Selecione **não sei que o seu nome.** na lista de ações. O percentil é 100%, como a ação só é válida com base nas restrições.
5. Introduza **meu nome é o Frank** para expressão do usuário no painel à esquerda de bate-papo.
6. Realçar **Frank** , em seguida, selecione **+ nome**. Entidades negatable tem duas instâncias: (+) e ainda adiciona ou substitui o valor; (-) subtração remove o valor.
7. Selecione **pontuação ações**. O **name** entidade agora é definida como **Frank** na memória do modelo, pelo que a **sei seu nome. É $name** ação está disponível.
8. Selecione **sei seu nome. É $name.** na lista de ações.
9. Introduza **não, o meu nome é o Frank.** para a expressão do utilizador no painel à esquerda de bate-papo.
10. Realçar **Frank** , em seguida, selecione **-nome** para limpar o valor a partir do **nome** entidade.
11. Selecione **pontuação ações**.
12. Selecione **não sei que o seu nome.** na lista de ações.
13. Introduza **meu nome é Susan.** para a expressão terceiro do usuário no painel à esquerda de bate-papo.
14. Realçar **Susan** , em seguida, **+ nome** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entidades de valores múltiplos](./07-multi-value-entities.md)
