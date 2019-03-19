---
title: Como utilizar entidades com múltiplos valores com um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar entidades com múltiplos valores com um modelo de aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 07550ed9c78aa584e55403d225eef4bcff33b057
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167427"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Como utilizar entidades com múltiplos valores com um modelo de aprendiz de conversação
Este tutorial mostra a propriedade de valor múltiplos de entidades.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do entidades de valores múltiplos](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o tutorial geral Bot está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Entidades de valores múltiplos acumular-se os valores numa lista, em vez de armazenar um valor único.  Estas entidades são úteis quando os utilizadores podem especificar mais de um valor. Sabores numa pizza, por exemplo.

Entidades marcadas como valores múltiplos terão cada instância reconhecida da entidade anexada a uma lista na memória do Bot. Reconhecimento subsequente acrescenta a entidade valor, em vez de substituir.

## <a name="steps"></a>Passos

Comece na home page na IU da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Introduza **MultiValueEntities** para **nome**.
3. Selecione **Criar**.

### <a name="entity-creation"></a>Criação de entidade

1. Selecione **entidades** no painel esquerdo, em seguida, **nova entidade**.
2. Selecione **personalizado treinado** para **tipo de entidade**.
3. Introduza **sabores** para **nome da entidade**.
4. Verifique **com múltiplos valores** para permitir que a entidade acumular-se um ou mais valores.
5. Verifique **Negatable**.
6. Selecione **Criar**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Introduza **seguem-se sua sabores: $toppings** para **de resposta do Bot...** . O símbolo de dólar norte-americano à esquerda indica uma referência de entidade.
3. Selecione **Criar**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Introduza **quais sabores deseja?** para **de resposta do Bot...** .
3. Introduza **sabores** para **Disqualifying dá direito**.
4. Selecione **Criar**.

Agora tem duas ações.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Preparar o modelo

1. Selecione **caixas de diálogo Train** no painel esquerdo, em seguida, **caixa de diálogo do novo Train**.
2. Introduza **Olá** para expressão do usuário no painel à esquerda de bate-papo.
3. Selecione **pontuação ações**.
4. Selecione **quais sabores deseja?** na lista de ações. O percentil é 100%, como a ação só é válida com base nas restrições.
5. Introduza **queijo e cresce** para expressão do usuário no painel à esquerda de bate-papo.
6. Realçar **queijo** , em seguida, selecione **+ sabores**.
7. Realçar **cresce** , em seguida, selecione **+ sabores**.
8. Selecione **pontuação ações**.
9. Selecione **seguem-se sua sabores: $toppings** na lista de ações.
10. Introduza **adicionar pepper** para a expressão seguinte do utilizador no painel à esquerda de bate-papo.
11. Realçar **pepper** , em seguida, selecione **+ sabores**.
12. Selecione **pontuação ações**.
13. Selecione **seguem-se sua sabores: $toppings** na lista de ações.
14. Introduza **remover queijo** para expressão de terceiro do usuário no painel à esquerda de bate-papo.
15. Realçar **queijo** , em seguida, selecione **-sabores**.
16. Selecione **pontuação ações**.
17. Selecione **seguem-se sua sabores: $toppings** na lista de ações.

![](../media/T07_training.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entidades com formação prévia](./08-pre-trained-entities.md)
