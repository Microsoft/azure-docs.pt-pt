---
title: Adicionar chit-chat a uma base de conhecimento do QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicionar chit-chat pessoal ao seu bot torna-o mais conversador e envolvente quando cria um KB. O QnA Maker permite-lhe adicionar facilmente um conjunto pré-povoado do chit-chat superior, no seu KB.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 3d5c47d3e3e9485c3a6de8f93782c654fb34a516
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330643"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Adicione chit-chat a uma base de conhecimento

Adicionar chit-chat ao seu bot torna-o mais conversador e envolvente. A funcionalidade de chit-chat no fabricante QnA permite-lhe adicionar facilmente um conjunto pré-povoado do chit-chat superior, na sua base de conhecimento (KB). Este pode ser um ponto de partida para a personalidade do seu bot, e irá poupar-lhe o tempo e o custo de escrevê-los do zero.

Este conjunto de dados tem cerca de 100 cenários de chit-chat na voz de várias personalidades, como Professional,Friendly e Witty. Escolha a persona que mais se assemelha à voz do seu bot. Dada uma consulta do utilizador, a QnA Maker tenta compará-la com a QnA mais próxima conhecida.

Alguns exemplos das diferentes personalidades estão abaixo. Pode ver todos os [conjuntos de dados de](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) personalidade juntamente com detalhes das personalidades.

Para a consulta do `When is your birthday?` utilizador, cada personalidade tem uma resposta estilosa:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personalidade|Exemplo|
|--|--|
|Profissional|A idade não se aplica a mim.|
|Amigável|Não tenho uma idade.|
|Espirituoso|Estou livre de idade.|
|Cuidar|Não tenho idade.|
|Entusiasta|Sou um bot, então não tenho idade.|
||


## <a name="language-support"></a>Suporte de idiomas

Os conjuntos de dados de chit-chat são suportados nos seguintes idiomas:

|Idioma|
|--|
|Chinês|
|Inglês|
|Francês|
|Alemanha|
|Italiano|
|Japonês|
|Coreano|
|Português|
|Espanhol|


## <a name="add-chit-chat-during-kb-creation"></a>Adicione chit-chat durante a criação do KB
Durante a criação da base de conhecimento, depois de adicionar os URLs e ficheiros de origem, existe uma opção para adicionar chit-chat. Escolha a personalidade que deseja como base de chit-chat. Se não quiser adicionar chit-chat, ou se já tiver suporte para chit-chat nas suas fontes de dados, escolha **Nenhum**.

## <a name="add-chit-chat-to-an-existing-kb"></a>Adicione chit-chat a um KB existente
Selecione o seu KB e navegue para a página **Definições.** Existe uma ligação com todos os conjuntos de dados de chit-chat no formato **.tsv** apropriado. Descarregue a personalidade que deseja e, em seguida, carrede-a como fonte de ficheiro. Certifique-se de que não edita o formato ou os metadados quando descarregar e carregar o ficheiro.

![Adicione chit-chat ao KB existente](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Edite as suas perguntas e respostas de conversa fiada
Quando editar o seu KB, verá uma nova fonte para o chit-chat, com base na personalidade que selecionou. Pode agora adicionar perguntas alteradas ou editar as respostas, tal como em qualquer outra fonte.

![Editar chit-chat QnAs](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Para visualizar os metadados, selecione **Ver Opções** na barra de ferramentas e, em seguida, selecione **Mostrar metadados**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adicione perguntas e respostas adicionais de chit-chat
Pode adicionar um novo par QnA de chit-chat que não está no conjunto de dados predefinido. Certifique-se de que não está a duplicar um par QnA que já está coberto no conjunto de chit-chat. Quando adicionas qualquer novo chit-chat QnA, ele é adicionado à tua fonte **Editorial.** Para garantir que o ranker entende que isto é chit-chat, adicione a chave de metadados/par de valor "Editorial: chitchat", como visto na imagem seguinte:

:::image type="content" source="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png" alt-text="Adicione chit-chat QnAs" lightbox="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png":::

## <a name="delete-chit-chat-from-an-existing-kb"></a>Apagar chit-chat de um KB existente
Selecione o seu KB e navegue para a página **Definições.** A sua fonte específica de chit-chat está listada como um ficheiro, com o nome de personalidade selecionado. Pode eliminar isto como um ficheiro de origem.

![Apagar chit-chat do KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Importar uma base de conhecimento](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Consulte também

[Descrição geral do Criador de FAQ](../Overview/overview.md)
