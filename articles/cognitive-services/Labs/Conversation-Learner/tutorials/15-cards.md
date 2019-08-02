---
title: Como usar cartões com um modelo de Conversation Learner, parte 1-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar cartões com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 426d7c8de29abeb88833e94962a7291a641702ac
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703753"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Como usar cartões (parte 1 de 2)

Este tutorial mostra como adicionar e usar um cartão simples em seu bot.

> [!NOTE]
> Conversation Learner atualmente espera que os arquivos de definição do cartão estejam localizados em um diretório chamado "cards", que está presente no diretório onde o bot é iniciado.

## <a name="video"></a>Vídeo

[![Visualização do tutorial de cartões](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Os cartões são elementos da interface de usuário que permitem ao usuário selecionar uma opção na conversa. 

### <a name="open-the-demo"></a>Abrir a demonstração

Na interface do usuário da Web, clique em "importar tutoriais" e selecione o modelo chamado "tutorial-15-cards".

### <a name="the-card"></a>O cartão

A definição do cartão está no seguinte local: C:\<InstalledPath\>\src\cards\prompt.JSON.

O sistema espera encontrar suas definições de cartão neste diretório de "cartões".

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Observe o tipo de corpo "TextBlock" e o espaço reservado "{{Question}}" no campo de texto.
> Há dois botões de envio e o texto que é enviado para cada um.

### <a name="actions"></a>Ações

Criamos três ações. Como você vê abaixo, a primeira ação é um cartão.

![](../media/tutorial13_actions.PNG)

Vejamos como o tipo de ação do cartão foi criado:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> O cartão contém três parâmetros diferentes – entrada de pergunta, botão 1 e botão 2. Esses elementos são referências de modelo no cartão em que você insere a pergunta e as respectivas respostas. Você também pode referenciar e usar entidades ou uma mistura de texto e entidades.

O ícone de olho mostra a aparência do cartão.

### <a name="practicing-creating-card-actions"></a>Praticando a criação de ações de cartão

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Selecione "cartão" para o "tipo de ação".
3. Selecione "prompt" na lista "modelo".
4. No campo "pergunta", digite "ir para a esquerda ou para a direita"
5. No campo "Button1", digite "Left"
6. No campo "Button2", digite "Right"
7. Clique no botão "Cancelar".

### <a name="train-dialog-using-an-adaptive-card"></a>Treinar caixa de diálogo usando um cartão adaptável

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel de chat, onde diz "digite sua mensagem...", digite "Hi"
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta, "prompt: pergunta: Ir para a esquerda ou para a direita? "
    - O ícone de olho pode ser usado para visualizar o cartão
5. No painel de bate-papo, clique no botão "esquerda" no prompt renderizado.
6. Clique no botão "ações de Pontuação".
7. Selecione a resposta "esquerda"
8. Clique no botão "salvar".
9. Selecione a resposta, "prompt: pergunta: Ir para a esquerda ou para a direita? "
10. No painel de bate-papo, clique no botão "direito" no prompt renderizado.
11. Clique no botão "ações de Pontuação".
12. Selecione a resposta "direita"

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Bots híbridos](./16-hybrid-bots.md)
