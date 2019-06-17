---
title: Como utilizar cartões com um modelo de aprendiz de conversação, parte 1 - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar cartões com um modelo de aprendiz de conversação.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: d0b87597c506aca5c3b4f6f3815f58656203ac3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389521"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Como utilizar cartões (parte 1 de 2)

Este tutorial mostra como adicionar e utilizar um cartão de simple no seu bot.

> [!NOTE]
> Aprendiz de conversação espera, atualmente, os arquivos de definição de cartão estejam localizadas num diretório chamado "cartões", que está presente no diretório onde o Bot é iniciado.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do cartões](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot tutorial geral está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Os cartões são elementos de IU que permitem ao usuário selecionar uma opção na conversação. 

### <a name="open-the-demo"></a>Abra a demonstração

Na IU da web, clique em "Importar tutorial" e selecione o modelo com o nome "Cartões de 15 de Tutorial".

### <a name="the-card"></a>O cartão

A definição de cartão está na seguinte localização: C:\<installedpath\>\src\cards\prompt.json.

O sistema espera encontrar as definições de cartão neste diretório de "cartões".

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Tenha em atenção o corpo do tipo "TextBlock" e o marcador de posição de "{{pergunta}}" no campo de texto.
> Existem dois submeter botões e o texto que é enviado para cada um.

### <a name="actions"></a>Ações

Criámos três ações. Como pode ver abaixo, a primeira ação é um cartão.

![](../media/tutorial13_actions.PNG)

Vamos ver como o tipo de ação do cartão foi criado:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> O cartão contém três parâmetros diferentes - pergunta de entrada, botões 1 e 2. Estes elementos são referências de modelo no cartão onde introduzir a pergunta e as respetivas respostas. Também pode referenciar e utilizar entidades ou uma combinação de texto e entidades.

O ícone do olho mostra-lhe o aspeto de cartão.

### <a name="practicing-creating-card-actions"></a>Que trabalham com a criação de cartão de ações

1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
2. Selecione "Cartão" para o "tipo de ação".
3. Selecione "Pedir" na lista de "Modelo".
4. No campo de "pergunta", escreva "Aceda à esquerda ou direita"
5. No campo de "button1", escreva "esquerda"
6. No campo de "button2", escreva "certo"
7. Clique no botão "Cancelar".

### <a name="train-dialog-using-an-adaptive-card"></a>Preparar a caixa de diálogo com um cartão adaptável

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Olá"
3. Clique no botão "Pontuação ações".
4. Selecione a resposta, "linha de comandos: pergunta: Aceda à esquerda ou direita?"
    - O ícone do olho pode ser utilizado para pré-visualizar o cartão
5. No painel de bate-papo clique no botão de "Left" na linha de comandos renderizada.
6. Clique no botão "Pontuação ações".
7. Selecione "Esquerda" a resposta
8. Clique no botão "Guardar".
9. Selecione a resposta, "linha de comandos: pergunta: Aceda à esquerda ou direita?"
10. No painel de bate-papo clique no botão de "Direita" na linha de comandos renderizada.
11. Clique no botão "Pontuação ações".
12. Selecione a resposta, o "Direito à"

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Bots híbrida](./16-hybrid-bots.md)
