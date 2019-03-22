---
title: Como iniciar sessão caixas de diálogo num modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como iniciar sessão caixas de diálogo num modelo de aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e3a84bfa643ebe74983bcef0d0ea72c701ffa589
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170501"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Como iniciar sessão caixas de diálogo num modelo de aprendiz de conversação

Este tutorial demonstra como caixas de diálogo de registo são empregadas a melhor criar modelos de aprendiz de conversação de gravado interações com os usuários do mundo real.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial de caixas de diálogo do registo](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot tutorial geral está em execução

    npm run tutorial-general

e o modelo de Meteorologia criados nos tutoriais anteriores.

## <a name="details"></a>Detalhes
Caixas de diálogo de log são gravados registos de interação com o seu bot com os utilizadores finais. Ao tirar partido destas caixas de diálogo de registo, pode corrigir as etiquetas de entidade e seleções de ação para melhorar o desempenho e o desempenho geral do sistema do modelo.

## <a name="steps"></a>Passos

Na IU da web, clique em "Importar tutorial" e selecione o modelo com o nome "Tutorial-11-LogDialogs".

Este modelo contém uma entidade com o nome "city" e ações desenvolvidas para responder às consultas sobre o clima nessa cidade. Dois treinar caixas de diálogo foram utilizadas para preparar o modelo, pelo que as expectativas de desempenho são um pouco baixas. O modelo seria melhorar com treinamento adicional e a exposição a interações de utilizador do mundo real.

### <a name="create-a-new-conversation"></a>Criar uma nova conversa

1. No painel esquerdo, clique em "Caixas de diálogo Log", em seguida, no botão "Registo caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Previsão meteorológica de Austin"
3. Clique no botão "Testar feito".
4. Clique a caixa de diálogo de registo de "Previsão meteorológica de Austin" da lista.
5. Clique a expressão de "Previsão meteorológica de Austin" no painel de bate-papo.
6. Clique em "Austin", em seguida, clique em "Cidade" na lista de entidades.
7. Clique no botão "Enviar alterações".
    - Esta alteração no valor de entidade causam alterações jusante com a conversa, uma vez que temos novos valores de entidade na memória. Ações posteriores provavelmente têm se tornado inválidas especialmente aqueles envolvendo a entidade "Cidade".
8. Clique no "Qual cidade?" expressão no painel de bate-papo.
9. Selecione a resposta, "o clima em Austin é provavelmente ensolarado".
10. Clique no botão "Guardar como Train caixa de diálogo".
    - Treinamento é inicializado imediatamente

![](../media/T11_logdialog.png)

Uma última observação. Consoante as necessidades de negócios, a funcionalidade de registo de conversação pode ser desativada ao aceder a definições e desmarcar a opção "conversas registo."

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Retorno de chamada de detecção de entidade](./12-entity-detection-callback.md)
