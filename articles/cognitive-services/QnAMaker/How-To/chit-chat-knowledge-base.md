---
title: Adicionar chit-bate-papo para uma base de dados de conhecimento do QnA Maker
titleSuffix: Azure Cognitive Services
description: A adição chit-bate-papo pessoal para o seu bot torna mais conversacionais e apelativas quando cria um KB. A ferramenta QnA Maker permite-lhe adicionar facilmente um conjunto preenchido previamente do chat-chit superior, em sua BDC.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389282"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Adicionar Chit-bate-papo para uma base de dados de conhecimento

Adicionar chit-bate-papo ao seu bot torna mais conversacionais e apelativas. A funcionalidade de chit chat do QnA maker permite-lhe adicionar facilmente um conjunto preenchido previamente do chat-chit superior, em sua base de dados de conhecimento (KB). Isso pode ser um ponto de partida para a personalidade de seu bot, e isso vai economizar o tempo e o custo de escrevê-los a partir do zero.  

Este conjunto de dados tem cerca de 100 cenários de chit-chat na voz de várias personalidades, como Professional, Friendly e Witty. Escolha a pessoa que se assemelhe mais às voz seu bot. Devido uma consulta de utilizador, a QnA Maker tenta fazer sua correspondência com o mais próximo QnA chit-bate-papo conhecidos.  

Alguns exemplos das diferentes personalidades estão abaixo. Pode ver todos os [conjuntos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) de dados de personalidade juntamente com detalhes das personalidades.

Para a consulta do utilizador de `When is your birthday?`, cada personalidade tem uma resposta estilo:

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
|Português|
|Francês|
|Alemanha|
|Italiano|
|Japonês|
|Coreano|
|Português|
|Espanhol|


## <a name="add-chit-chat-during-kb-creation"></a>Adicionar chit-bate-papo durante a criação da KB
Durante a criação de base de dados de conhecimento, depois de adicionar os URLs de origem e arquivos, há uma opção para adicionar chit-bate-papo. Escolha a personalidade que pretende como sua base de chit-bate-papo. Se não quiser adicionar chit-chat, ou se já tiver suporte para chit-chat nas suas fontes de dados, escolha **Nenhum**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Adicionar Chit-bate-papo para um KB existente
Selecione o seu KB e navegue para a página **Definições.** Existe uma ligação com todos os conjuntos de dados chit-chat no formato **.tsv** apropriado. Transferir a personalidade que desejar, em seguida, carregue-o como uma origem de ficheiro. Lembre-se de que não edite o formato ou os metadados quando transferir e carregar o ficheiro. 
  
![Adicionar chit-bate-papo para KB existente](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Editar chit-bate-papo perguntas e respostas
Ao editar o KB, verá uma nova origem para chit-bate-papo, com base em personalidade que selecionou. Agora pode adicionar alterado perguntas ou editar as respostas, tal como com qualquer outra origem. 

![Editar QnAs chit-bate-papo](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Para visualizar os metadados, selecione **Ver Opções** na barra de ferramentas e, em seguida, selecione **Metadados de Mostrar**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adicione a conversa chit adicionais perguntas e respostas
Pode adicionar novos QnA chit-bate-papo não no predefinidos definida. Certifique-se de que não estará duplicando um par de QnA que já é abrangido no conjunto de chit-bate-papo. Quando adiciona sacana nova QnA, é adicionado à sua fonte **editorial.** Para garantir que o ranker entende que este é chit-chat, adicione a chave/par de metadados "Editorial: chitchat", como se pode ver na seguinte imagem:
   
![! [Adicionar chit-chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Eliminar chit-bate-papo de um KB existente
Selecione o seu KB e navegue para a página **Definições.** A origem do chat chit específico é listada como um arquivo, com o nome de personalidade selecionado. Pode eliminar este como um ficheiro de origem.

![Eliminar chit-bate-papo da KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Importar uma base de conhecimento](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Consulte também 

[Descrição geral do Criador de FAQ](../Overview/overview.md)
