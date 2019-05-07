---
title: Adicionar chit-bate-papo para uma base de dados de conhecimento do QnA Maker
titleSuffix: Azure Cognitive Services
description: A adição chit-bate-papo pessoal para o seu bot torna mais conversacionais e apelativas quando cria um KB. A ferramenta QnA Maker permite-lhe adicionar facilmente um conjunto preenchido previamente do chat-chit superior, em sua BDC.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/07/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 08d09680ee1797e73666c185f4430c7ef3079477
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153691"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Adicionar Chit-bate-papo para uma base de dados de conhecimento

Adicionar chit-bate-papo ao seu bot torna mais conversacionais e apelativas. A funcionalidade de chit chat do QnA maker permite-lhe adicionar facilmente um conjunto preenchido previamente do chat-chit superior, em sua base de dados de conhecimento (KB). Isso pode ser um ponto de partida para a personalidade de seu bot, e isso vai economizar o tempo e o custo de escrevê-los a partir do zero.  

Este conjunto de dados tem aproximadamente 100 cenários de chit chat na voz das várias pessoas, como profissional, amigável e Witty. Escolha a pessoa que se assemelhe mais às voz seu bot. Devido uma consulta de utilizador, a QnA Maker tenta fazer sua correspondência com o mais próximo QnA chit-bate-papo conhecidos.  

Alguns exemplos dos personalidades diferentes estão abaixo. Pode ver todos os personalidade conjuntos de dados, juntamente com detalhes das personalidades [aqui](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets).

<!-- added quotes so acrolinx doesn't score these sentences -->
|Consulta de utilizador|Profissional|Amigável|Witty|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`Aw, I'm blushing.`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`Those who can, do. Those who can't, don't sing.`|
|`Will you marry me?`|`I think it's best if we stick to a professional relationship.`|`Definitely didn't see that coming!`|`Sure. Take me to city hall. See what happens.`|



> [!NOTE]
> O suporte por chat do chit só está atualmente disponível em inglês. 

## <a name="add-chit-chat-during-kb-creation"></a>Adicionar chit-bate-papo durante a criação da KB
Durante a criação de base de dados de conhecimento, depois de adicionar os URLs de origem e arquivos, há uma opção para adicionar chit-bate-papo. Escolha a personalidade que pretende como sua base de chit-bate-papo. Se não pretender adicionar chit-bate-papo, ou se já tiver chit-bate-papo suporte em suas origens de dados, escolha **None**. 
   
![Adicionar chit-bate-papo durante a criação](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Adicionar Chit-bate-papo para um KB existente
Selecione a sua BDC e navegue para o **definições** página. Existe uma ligação para todos os chit-bate-papo conjuntos de dados apropriado **. tsv** formato. Transferir a personalidade que desejar, em seguida, carregue-o como uma origem de ficheiro. Lembre-se de que não edite o formato ou os metadados quando transferir e carregar o ficheiro. 
  
![Adicionar chit-bate-papo para KB existente](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Editar chit-bate-papo perguntas e respostas
Ao editar o KB, verá uma nova origem para chit-bate-papo, com base em personalidade que selecionou. Agora pode adicionar alterado perguntas ou editar as respostas, tal como com qualquer outra origem. 

![Editar QnAs chit-bate-papo](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Para ver os metadados, selecione **opções de visualização** na barra de ferramentas, em seguida, selecione **Mostrar metadados**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adicione a conversa chit adicionais perguntas e respostas
Pode adicionar novos QnA chit-bate-papo não no predefinidos definida. Certifique-se de que não estará duplicando um par de QnA que já é abrangido no conjunto de chit-bate-papo. Quando adiciona qualquer QnA chit-bate-papo novo, é adicionado à sua **Editorial** origem. Para garantir que compreende o classificador que se trata de chit-bate-papo, adicione o par de chaves/valores de metadados "Editorial: chit-bate-papo", como mostrado na imagem seguinte:
   
![! [Adicionar chit-bate-papo QnAs] (.. / media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Eliminar chit-bate-papo de um KB existente
Selecione a sua BDC e navegue para o **definições** página. A origem do chat chit específico é listada como um arquivo, com o nome de personalidade selecionado. Pode eliminar este como um ficheiro de origem.

![Eliminar chit-bate-papo da KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Importar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Consulte também 

[Descrição geral do Criador de FAQ](../Overview/overview.md)
