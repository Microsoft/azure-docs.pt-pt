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
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a8ebd0b36c6ee8bf6762a70af9f4a7c09f6b118d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955159"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Adicionar Chit-bate-papo para uma base de dados de conhecimento

Adicionar chit-bate-papo ao seu bot torna mais conversacionais e apelativas. A funcionalidade de chit chat do QnA maker permite-lhe adicionar facilmente um conjunto preenchido previamente do chat-chit superior, em sua base de dados de conhecimento (KB). Isso pode ser um ponto de partida para a personalidade de seu bot, e isso vai economizar o tempo e o custo de escrevê-los a partir do zero.  

Esse conjunto de informações tem cerca de 100 cenários de Chit-Chat na voz de várias pessoas, como profissional, amigável e voluntária. Escolha a pessoa que se assemelhe mais às voz seu bot. Devido uma consulta de utilizador, a QnA Maker tenta fazer sua correspondência com o mais próximo QnA chit-bate-papo conhecidos.  

Veja abaixo alguns exemplos dos diferentes personalidades. Você pode ver todos os [conjuntos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) de informações de personalidade junto com os detalhes do personalidades.

Para a consulta de usuário `When is your birthday?`do, cada personalidade tem uma resposta com estilo:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personalidade|Exemplo|
|--|--|
|Profissional|A idade não se aplica de fato a mim.|
|AmigáveI|Eu realmente não tenho uma idade.|
|Voluntariay|Sou sem idade.|
|Preocupar|Não tenho uma idade.|
|Entusiasmado|Sou um bot, então eu não tenho uma idade.|
||

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

Para exibir os metadados, selecione **Exibir opções** na barra de ferramentas e selecione **Mostrar metadados**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adicione a conversa chit adicionais perguntas e respostas
Pode adicionar novos QnA chit-bate-papo não no predefinidos definida. Certifique-se de que não estará duplicando um par de QnA que já é abrangido no conjunto de chit-bate-papo. Quando adiciona qualquer QnA chit-bate-papo novo, é adicionado à sua **Editorial** origem. Para garantir que compreende o classificador que se trata de chit-bate-papo, adicione o par de chaves/valores de metadados "Editorial: chit-bate-papo", como mostrado na imagem seguinte:
   
![! [Adicionar Chit-Chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Eliminar chit-bate-papo de um KB existente
Selecione a sua BDC e navegue para o **definições** página. A origem do chat chit específico é listada como um arquivo, com o nome de personalidade selecionado. Pode eliminar este como um ficheiro de origem.

![Eliminar chit-bate-papo da KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Importar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Consulte também 

[Descrição geral do Criador de FAQ](../Overview/overview.md)
