---
title: Adicionar chit-chat a uma base de conhecimento do QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicionar chit-chat pessoal ao seu bot torna-o mais conversador e envolvente quando cria um KB. O QnA Maker permite-lhe facilmente adicionar um conjunto pré-povoado do chit-chat superior, no seu KB.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220712"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Adicione chit-chat a uma base de conhecimento

Adicionar chit-chat ao seu bot torna-o mais conversador e envolvente. O recurso chit-chat no fabricante de QnA permite-lhe facilmente adicionar um conjunto pré-povoado do chit-chat superior, na sua base de conhecimentos (KB). Este pode ser um ponto de partida para a personalidade do seu bot, e poupar-lhe-á tempo e custo de escrevê-los do zero.  

Este conjunto de dados tem cerca de 100 cenários de chit-chat na voz de várias personalidades, como Professional, Friendly e Witty. Escolha a persona que mais se assemelha à voz do seu bot. Dada uma consulta de utilizador, o QnA Maker tenta compará-lo com o mais próximo conhecido chit-chat QnA.  

Alguns exemplos das diferentes personalidades estão abaixo. Pode ver todos os [conjuntos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) de dados de personalidade juntamente com detalhes das personalidades.

Para a consulta `When is your birthday?`do utilizador, cada personalidade tem uma resposta estilo:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personalidade|Exemplo|
|--|--|
|Profissional|A idade não se aplica a mim.|
|Amigável|Não tenho idade.|
|Espirituoso|Estou livre de idades.|
|Cuidar|Não tenho idade.|
|Entusiasta|Sou um bot, por isso não tenho idade.|
||


## <a name="language-support"></a>Suporte de idiomas

Os conjuntos de dados do Chit-chat são suportados nos seguintes idiomas:

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
Durante a criação da base de conhecimento, depois de adicionar os seus URLs de origem e ficheiros, existe uma opção para adicionar chit-chat. Escolha a personalidade que deseja como base de conversa fiada. Se não quiser adicionar chit-chat, ou se já tiver suporte para chit-chat nas suas fontes de dados, escolha **Nenhum**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Adicione chit-chat a um KB existente
Selecione o seu KB e navegue para a página **Definições.** Existe uma ligação com todos os conjuntos de dados chit-chat no formato **.tsv** apropriado. Faça o download da personalidade que deseja e, em seguida, carregue-a como fonte de ficheiro. Certifique-se de não editar o formato ou os metadados quando descarregar e carregar o ficheiro. 
  
![Adicione chit-chat ao KB existente](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Editar as suas perguntas e respostas de chit-chat
Quando editar o seu KB, verá uma nova fonte de chit-chat, com base na personalidade que selecionou. Pode agora adicionar perguntas alteradas ou editar as respostas, tal como com qualquer outra fonte. 

![Editar qnAs chit-chat](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Para visualizar os metadados, selecione **Ver Opções** na barra de ferramentas e, em seguida, selecione **Metadados de Mostrar**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adicione perguntas e respostas adicionais de chit-chat
Você pode adicionar novo chit-chat QnA que não está no conjunto pré-definido. Certifique-se de que não está a duplicar um par QnA que já está coberto no conjunto de chit-chat. Quando adiciona sacana nova QnA, é adicionado à sua fonte **editorial.** Para garantir que o ranker entende que este é chit-chat, adicione a chave/par de metadados "Editorial: chitchat", como se pode ver na seguinte imagem:
   
![! [Adicionar chit-chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Eliminar chit-chat de um KB existente
Selecione o seu KB e navegue para a página **Definições.** A sua fonte específica de chit-chat está listada como um ficheiro, com o nome de personalidade selecionado. Pode apagar isto como ficheiro fonte.

![Eliminar chit-chat da KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Importar uma base de conhecimento](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Consulte também 

[Descrição geral do Criador de FAQ](../Overview/overview.md)
