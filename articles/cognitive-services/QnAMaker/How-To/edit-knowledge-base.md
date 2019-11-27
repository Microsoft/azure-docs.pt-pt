---
title: Editar uma base de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: A ferramenta QnA Maker permite-lhe gerir o conteúdo da sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: cc4ead968a0ee2c9890c1cd24a6b70516b2b2e74
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326790"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Editar uma base de dados de conhecimento do QnA Maker

A ferramenta QnA Maker permite-lhe gerir o conteúdo da sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Editar o conteúdo de base de dados de conhecimento

1.  Selecione **minhas bases de dados de conhecimento** na barra de navegação superior. 

    Você pode ver todos os serviços criados ou compartilhados com você classificados na ordem decrescente da data da **última modificação** .

    ![Meu Bases de dados de conhecimento](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecione uma base de dados de conhecimento específica para fazer edições ao mesmo.
 
1. Selecione **configurações**. Aqui pode editar o nome do serviço de campo obrigatório.
  
    |Objetivo|Ação|
    |--|--|
    |Adicionar URL|Você pode adicionar novas URLs para adicionar o novo conteúdo de perguntas frequentes à base de dados de conhecimento clicando em **gerenciar base de dados de conhecimento-> o link ' + Adicionar URL '** .|
    |Eliminar o URL|Pode eliminar URLs existentes ao selecionar o ícone Eliminar, o caixote do lixo.|
    |Atualizar conteúdo|Se você quiser que sua base de dados de conhecimento rastreie o conteúdo mais recente das URLs existentes, marque a caixa de seleção **Atualizar** . Isso atualizará a base de dados de conhecimento com o conteúdo da URL mais recente uma vez. Isso não define uma agenda regular de atualizações.|
    |Adicionar ficheiro|Você pode adicionar um documento de arquivo com suporte para fazer parte de uma base de dados de conhecimento, selecionando **gerenciar base de dados de conhecimento**e, em seguida, selecionando **+ Adicionar arquivo**|
    |Importar|Você também pode importar qualquer base de dados de conhecimento existente selecionando o botão **da base de dados de conhecimento Ímport** . |
    |Atualizar|A atualização da base de dados de conhecimento depende do **tipo de preço de gerenciamento** usado ao criar QnA Maker serviço associado à sua base de dados de conhecimento. Também pode atualizar o escalão de gestão a partir do portal do Azure, se necessário.

1. Quando terminar de fazer alterações na base de dados de conhecimento, selecione **salvar e treinar** no canto superior direito da página para manter as alterações.    

    ![Guardar e preparar](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Se você sair da página antes de selecionar **salvar e treinar**, todas as alterações serão perdidas.

## <a name="add-a-qna-pair"></a>Adicione um par de FAQ

Na página **Editar** , selecione **Adicionar par de QnA** para adicionar uma nova linha à tabela base de dados de conhecimento.

![Adicionar par QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Eliminar um par de QnA

Para excluir um QnA, clique no ícone **excluir** na extrema direita da linha QnA. Esta é uma operação permanente. Não pode ser anulada. Considere exportar sua KB da página de **publicação** antes de excluir pares. 

![Eliminar QnA par](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Adicione perguntas alternativas para um par de QnA existente para aumentar a probabilidade de uma correspondência com uma consulta de utilizador.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>adicionar metadados

Adicione pares de metadados selecionando primeiro **as opções de exibição**e, em seguida, selecionando **Mostrar metadados**. Isso exibe a coluna de metadados. Em seguida, selecione o sinal de **+** para adicionar um par de metadados. Esse par consiste em uma chave e um valor.

![Adicionar metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Não se esqueça de salvar periodicamente e preparar a base de dados de conhecimento depois de fazer edições para evitar perder as alterações.

## <a name="manage-large-knowledge-bases"></a>Gerenciar grandes bases de dados de conhecimento

* **Grupos de fontes de dados**: os QnAs são agrupados pela fonte de dados da qual foram extraídos. Pode expandir ou fechar a origem de dados.

    ![Use a barra de fonte de dados QnA Maker para recolher e expandir perguntas e respostas da fonte de dados](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Pesquisar base de dados**de conhecimento: você pode pesquisar a base de dados de conhecimento digitando na caixa de texto na parte superior da tabela da base de dados de conhecimento. Clique em Inserir para pesquisar o conteúdo de pergunta, resposta ou metadados. Clique no ícone de X para remover o filtro de pesquisa.

    ![Use a caixa de pesquisa QnA Maker acima das perguntas e respostas para reduzir a exibição apenas para filtrar itens correspondentes](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginação**: movimentar-se rapidamente pelas fontes de dados para gerenciar grandes bases de conhecimento

    ![Use os recursos de paginação QnA Maker acima das perguntas e respostas para percorrer as páginas de perguntas e respostas](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Eliminar bases de dados de conhecimento

A eliminar uma base de dados de conhecimento (KB) é uma operação permanente. Não pode ser anulada. Antes de excluir uma base de dados de conhecimento, você deve exportar a base de dados de conhecimento na página **configurações** do portal de QnA Maker. 

Se você compartilhar sua base de conhecimento com [colaboradores](collaborate-knowledge-base.md) e, em seguida, excluí-lo, todos perderão o acesso ao KB. 

## <a name="delete-azure-resources"></a>Eliminar recursos do Azure 

Se eliminar qualquer um dos recursos do Azure utilizados para as bases de dados de conhecimento do QnA Maker, as bases de dados de conhecimento deixarão de funcionar. Antes de excluir todos os recursos, certifique-se de exportar suas bases de dados de conhecimento na página **configurações** . 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Colaborar em uma base de dados de conhecimento](./collaborate-knowledge-base.md)
