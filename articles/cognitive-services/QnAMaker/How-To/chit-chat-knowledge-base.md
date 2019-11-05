---
title: Adicionando Chit-Chat a uma base de dados de conhecimento QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicionar o Chit pessoal-chat ao bot torna mais conversação e atraente quando você cria um KB. QnA Maker permite que você adicione facilmente um conjunto previamente preenchido da parte superior do Chit, à sua base de conhecimento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 15253e67283d1ad53bb1d4c018987dceaafac6c8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486756"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Adicionar Chit-Chat a uma base de dados de conhecimento

Adicionar Chit-Chat ao bot torna mais conversação e atraente. O recurso Chit-Chat no QnA Maker permite que você adicione facilmente um conjunto previamente preenchido do Top Chit-Chat, em sua base de dados de conhecimento (KB). Isso pode ser um ponto de partida para a personalidade do bot e poupará o tempo e o custo de escrevê-los do zero.  

Esse conjunto de informações tem cerca de 100 cenários de Chit-Chat na voz de várias pessoas, como profissional, amigável e voluntária. Escolha a pessoa que mais se assemelha à voz do bot. Dada uma consulta de usuário, QnA Maker tenta fazer a correspondência com a QnA conhecida mais próxima do Chit-Chat.  

Veja abaixo alguns exemplos dos diferentes personalidades. Você pode ver todos os [conjuntos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) de informações de personalidade junto com os detalhes do personalidades.

Para a consulta do usuário de `When is your birthday?`, cada personalidade tem uma resposta com estilo:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personalidade|Exemplo|
|--|--|
|Profissional|A idade não se aplica de fato a mim.|
|AmigáveI|Eu realmente não tenho uma idade.|
|Voluntariay|Sou sem idade.|
|Preocupar|Não tenho uma idade.|
|Entusiasmado|Sou um bot, então eu não tenho uma idade.|
||


## <a name="language-support"></a>Suporte de idiomas

Chit-os conjuntos de dados de chat têm suporte nos seguintes idiomas:

|Idioma|
|--|
|Chinês, `zh-cn`|
|Inglês, `en-us`|
|Francês, `fr-fr`|
|Alemanha, `de-de`|
|Italiano, `it-it`|
|Japonês, `ja-jp`|
|Coreano, `ko-kr`|
|Português, `pr-br`|
|Espanhol, `es-es`|


## <a name="add-chit-chat-during-kb-creation"></a>Adicionar Chit-Chat durante a criação do KB
Durante a criação da base de dados de conhecimento, depois de adicionar os arquivos e URLs de origem, há uma opção para adicionar o Chit-Chat. Escolha a personalidade que você deseja como sua base de Chit de bate-papo. Se você não quiser adicionar o Chit-Chat ou se já tiver o suporte do Chit-Chat em suas fontes de dados, escolha **nenhum**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Adicionar Chit-Chat a um KB existente
Selecione a KB e navegue até a página **configurações** . Há um link para todos os conjuntos de Chit de chat no formato **. tsv** apropriado. Baixe a personalidade desejada e, em seguida, carregue-a como uma fonte de arquivo. Certifique-se de não editar o formato ou os metadados ao baixar e carregar o arquivo. 
  
![Adicionar Chit-Chat a KB existentes](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Edite suas perguntas e respostas do Chit
Ao editar sua base de conhecimento, você verá uma nova fonte para Chit, com base na personalidade selecionada. Agora você pode adicionar perguntas alteradas ou editar as respostas, assim como com qualquer outra fonte. 

![Editar Chit-Chat QnAs](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Para exibir os metadados, selecione **Exibir opções** na barra de ferramentas e selecione **Mostrar metadados**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adicione perguntas e respostas adicionais sobre o Chit-Chat
Você pode adicionar novo Chit-Chat QnA que não está no conjunto predefinido. Certifique-se de que você não está duplicando um par QnA que já está coberto no conjunto Chit-Chat. Quando você adiciona qualquer novo QnA do Chit-Chat, ele é adicionado à sua fonte **editorial** . Para garantir que o classificador entenda que se trata de Chit, adicione o par chave/valor de metadados "editorial: Chit-Chat", como mostrado na imagem a seguir:
   
![! [Adicionar Chit-Chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Excluir Chit-Chat de um KB existente
Selecione a KB e navegue até a página **configurações** . Sua origem específica do Chit-Chat é listada como um arquivo, com o nome de personalidade selecionado. Você pode excluir isso como um arquivo de origem.

![Excluir Chit-Chat da KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Importar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Consultar também 

[Descrição geral do Criador de FAQ](../Overview/overview.md)
