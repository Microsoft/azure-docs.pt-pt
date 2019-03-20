---
title: Como adicionar entidades de Pre-Trained para um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como adicionar entidades de Pre-trained para um modelo de aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f5b3234c45a9ee80bc5a2c2afe67046896270802
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58163791"
---
# <a name="how-to-add-pre-trained-entities"></a>Como adicionar entidades de Pre-trained
Este tutorial mostra como adicionar entidades de Pre-Trained ao seu modelo de aprendiz de conversação.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do entidades com formação prévia](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o tutorial geral Bot está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Entidades com formação prévia reconhecem tipos comuns de entidades, como números, datas, quantias monetárias e outras pessoas.  Eles funcionam "out-of-the-box," não requerem qualquer treinamento e seu comportamento não pode ser alterado ao contrário das entidades personalizadas.  Por predefinição, as entidades Pre-Trained são-com múltiplos valores, acumular identificada cada uma das instâncias da entidade.

## <a name="steps"></a>Passos

Comece na home page na IU da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Introduza **PretrainedEntities** para **nome**.
3. Selecione **Criar**.

### <a name="entity-creation"></a>Criação de entidade

1. Selecione **entidades** no painel esquerdo, em seguida, **nova entidade**.
2. Selecione **Pre Trained/datetimeV2** para **tipo de entidade**.
3. Verifique **com múltiplos valores** para permitir que a entidade acumular-se um ou mais valores. Tenha em atenção que a entidades de Pre-Trained não pode ser negatable.
4. Selecione **Criar**.

![](../media/T08_entity_create.png)

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Introduza **a data é $builtin-datetimev2** para **de resposta do Bot...** .
3. Selecione **Criar**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. Selecione **ações** no painel esquerdo, em seguida, **nova ação**.
2. Introduza **o que é a data?** para **de resposta do Bot...** . Não podem ser entidades com formação prévia **necessárias entidades** conforme eles são reconhecidos por predefinição para todas as expressões.
3. Introduza **builtin datetimev2** para **Disqualifying dá direito**.
4. Selecione **Criar**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Preparar o modelo

1. Selecione **caixas de diálogo Train** no painel esquerdo, em seguida, **caixa de diálogo do novo Train**.
2. Introduza **hello** para expressão do usuário no painel à esquerda de bate-papo.
3. Selecione **pontuação ações**.
4. Selecione **o que é a data?** na lista de ações
5. Introduza **hoje mesmo** para expressão do usuário no painel à esquerda de bate-papo.
    - O **hoje mesmo** expressão é reconhecida automaticamente por modelos com formação prévia no LUIS.
    - Pairar o rato sobre os valores de entidades de Pre-Trained mostra dados adicionais fornecidos pelo LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Resoluções de entidade](./09-entity-resolvers.md)
