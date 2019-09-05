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
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1450d6b41d9a193f8069509aacd3343ae646c6ac
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307026"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Editar uma base de dados de conhecimento do QnA Maker

A ferramenta QnA Maker permite-lhe gerir o conteúdo da sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Editar o conteúdo de base de dados de conhecimento

1.  Selecione **meu bases de dados de conhecimento** na barra de navegação superior. 

    Pode ver todos os serviços criados ou partilhados consigo classificadas na ordem decrescente do **modificado pela última vez** data.

    ![Meu Bases de dados de conhecimento](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecione uma base de dados de conhecimento específica para fazer edições ao mesmo.
 
1. Selecione **definições**. Aqui pode editar o nome do serviço de campo obrigatório.
  
    |Objetivo|Ação|
    |--|--|
    |Adicionar URL|Pode adicionar novos URLs para adicionar novo conteúdo de perguntas frequentes sobre a base de dados de conhecimento clicando **base de dados de conhecimento de gerir -> "+ Adicionar URL'** ligação.|
    |Eliminar o URL|Pode eliminar URLs existentes ao selecionar o ícone Eliminar, o caixote do lixo.|
    |Atualizar o conteúdo do URL|Se pretender que a sua base de dados de conhecimento para pesquisar o conteúdo mais recente de URLs existentes, selecione o **atualizar** caixa de verificação. Isto irá atualizar a base de dados de conhecimento com conteúdo mais recente do URL.|
    |Adicionar ficheiro|Pode adicionar um documento de ficheiro suportados para fazer parte de uma base de dados de conhecimento, selecionando **gerir a base de dados de conhecimento**, em seguida, selecionar **+ adicionar ficheiro**|
    |Importar|Também pode importar qualquer base de dados de conhecimento existente selecionando **base de dados de conhecimento Ímport** botão. |
    |Atualizar|Atualizações da base de dados de conhecimento de depende **escalão de preço de gestão** utilizado ao criar o serviço QnA Maker associado à sua base de dados de conhecimento. Também pode atualizar o escalão de gestão a partir do portal do Azure, se necessário.

1. Depois de concluída a efetuar alterações para a base de dados de conhecimento, selecione **guardar e treinar** no canto superior direito da página para manter as alterações.    

    ![Guardar e preparar](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Se deixar a página antes de selecionar **guardar e treinar**, todas as alterações serão perdidas.

## <a name="add-a-qna-pair"></a>Adicione um par de FAQ

Na página **Editar** , selecione **Adicionar par de QnA** para adicionar uma nova linha à tabela base de dados de conhecimento.

![Adicionar par QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Eliminar um par de QnA

Para eliminar um QnA, clique nas **eliminar** ícone mais à direita da linha QnA. Esta é uma operação permanente. Não pode ser anulada. Considerar a exportação de sua BDC do **publicar** página antes de eliminar pares. 

![Eliminar QnA par](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Adicione perguntas alternativas para um par de QnA existente para aumentar a probabilidade de uma correspondência com uma consulta de utilizador.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>adicionar metadados

Adicione pares de metadados selecionando primeiro **as opções de exibição**e, em seguida, selecionando **Mostrar metadados**. Isso exibe a coluna de metadados. Em seguida, selecione **+** o sinal para adicionar um par de metadados. Esse par consiste em uma chave e um valor.

![Adicionar metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Não se esqueça de salvar periodicamente e preparar a base de dados de conhecimento depois de fazer edições para evitar perder as alterações.

## <a name="manage-large-knowledge-bases"></a>Gerenciar grandes bases de dados de conhecimento

* **Grupos de fontes de dados**: Os QnAs são agrupados pela fonte de dados da qual foram extraídos. Pode expandir ou fechar a origem de dados.

    ![Use a barra de fonte de dados QnA Maker para recolher e expandir perguntas e respostas da fonte de dados](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Pesquisar base de dados de conhecimento**: Você pode pesquisar a base de dados de conhecimento digitando na caixa de texto na parte superior da tabela da base de dados de conhecimento. Clique em Inserir para pesquisar o conteúdo de pergunta, resposta ou metadados. Clique no ícone de X para remover o filtro de pesquisa.

    ![Use a caixa de pesquisa QnA Maker acima das perguntas e respostas para reduzir a exibição apenas para filtrar itens correspondentes](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginação**: Movimente-se rapidamente pelas fontes de dados para gerenciar grandes bases de conhecimento

    ![Use os recursos de paginação QnA Maker acima das perguntas e respostas para percorrer as páginas de perguntas e respostas](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Eliminar bases de dados de conhecimento

A eliminar uma base de dados de conhecimento (KB) é uma operação permanente. Não pode ser anulada. Antes de eliminar uma base de dados de conhecimento, deve exportar a base de dados de conhecimento a partir da **definições** página do portal do QnA Maker. 

Se partilhar a sua BDC com [colaboradores](collaborate-knowledge-base.md) , em seguida, eliminá-lo, todos os utilizadores perde o acesso para o KB. 

## <a name="delete-azure-resources"></a>Eliminar recursos do Azure 

Se eliminar qualquer um dos recursos do Azure utilizados para as bases de dados de conhecimento do QnA Maker, as bases de dados de conhecimento deixarão de funcionar. Antes de eliminar todos os recursos, certifique-se de exportar o seu bases de dados de conhecimento do **definições** página. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Colaborar numa base de dados de conhecimento](./collaborate-knowledge-base.md)
