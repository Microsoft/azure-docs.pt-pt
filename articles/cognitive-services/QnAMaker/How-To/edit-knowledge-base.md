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
ms.openlocfilehash: b5ee7f60eab0349378767473c9c80f035a65c9a5
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669322"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Editar uma base de dados de conhecimento do QnA Maker

A ferramenta QnA Maker permite-lhe gerir o conteúdo da sua base de dados de conhecimento, fornecendo uma experiência de edição fácil de usar.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Editar o conteúdo de base de dados de conhecimento

1.  **Selecione as minhas bases de conhecimento** na barra de navegação superior. 

    Pode ver todos os serviços que criou ou partilhou consigo classificados na ordem descendente da última data **modificada.**

    ![Meu Bases de dados de conhecimento](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecione uma base de dados de conhecimento específica para fazer edições ao mesmo.
 
1. Selecione **Definições**. Aqui pode editar o nome do serviço de campo obrigatório.
  
    |Objetivo|Ação|
    |--|--|
    |Adicionar URL|Pode adicionar novos URLs para adicionar novos conteúdos de FAQ à base de conhecimento clicando em gerir a base de **conhecimento -> link '+ Adicionar URL'.**|
    |Eliminar o URL|Pode eliminar URLs existentes ao selecionar o ícone Eliminar, o caixote do lixo.|
    |Atualizar conteúdo|Se quiser que a sua base de conhecimentos rasteje os mais recentes conteúdos dos URLexistentes, selecione a caixa de verificação **Refresh.** Isto irá atualizar a base de conhecimento com o conteúdo de URL mais recente uma vez. Isto não está a definir um calendário regular de atualizações.|
    |Adicionar ficheiro|Pode adicionar um documento de ficheiro suportado para fazer parte de uma base de conhecimentos, selecionando a base de **conhecimento,** selecionando **depois + Adicionar Ficheiro**|
    |Importar|Também pode importar qualquer base de conhecimento existente selecionando o botão **base de import knowledge.** |
    |Atualizar|A atualização da base de conhecimentos depende do nível de preços de **gestão** utilizado enquanto cria o serviço QnA Maker associado à sua base de conhecimentos. Também pode atualizar o escalão de gestão a partir do portal do Azure, se necessário.

1. Uma vez feito alterações na base de conhecimento, selecione **Guardar e treinar** no canto superior direito da página para persistir as alterações.    

    ![Guardar e preparar](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Se deixar a página antes de selecionar **Guardar e treinar,** todas as alterações serão perdidas.

## <a name="add-a-qna-pair"></a>Adicione um par de FAQ

Na página **EDIT,** selecione **Adicionar par QnA** para adicionar uma nova linha à tabela base de conhecimentos.

![Adicionar par QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Eliminar um par de QnA

Para eliminar um QnA, clique no ícone **de exclusão** na extrema direita da linha QnA. Esta é uma operação permanente. Não pode ser anulada. Considere exportar o seu KB da página **Publicar** antes de apagar pares. 

![Eliminar QnA par](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Adicionar perguntas alternativas

Adicione perguntas alternativas para um par de QnA existente para aumentar a probabilidade de uma correspondência com uma consulta de utilizador.

![Adicionar perguntas alternativas](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>adicionar metadados

Adicione os pares de metadados selecionando primeiro **as opções de Visualização**e, em seguida, selecione **metadados do Show**. Isto exibe a coluna de metadados. Em seguida, selecione o sinal **+** para adicionar um par de metadados. Este par consiste numa chave e num valor.

![Adicionar metadados](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Não se esqueça de salvar periodicamente e preparar a base de dados de conhecimento depois de fazer edições para evitar perder as alterações.

## <a name="manage-large-knowledge-bases"></a>Gerenciar grandes bases de dados de conhecimento

* **Grupos**de fonte de dados : Os QnAs são agrupados pela fonte de dados a partir da qual foram extraídos. Pode expandir ou fechar a origem de dados.

    ![Use a barra de fonte de dados do QnA Maker para colapsar e expandir questões e respostas de fonte de dados](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Pesquisar base**de conhecimento : Pode pesquisar a base de conhecimento digitando na caixa de texto no topo da tabela Base de Conhecimento. Clique em Inserir para pesquisar o conteúdo de pergunta, resposta ou metadados. Clique no ícone de X para remover o filtro de pesquisa.

    ![Utilize a caixa de pesquisa qnA Maker acima das perguntas e respostas para reduzir a vista apenas para itens que combinam filtros](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginação**: Mova-se rapidamente através de fontes de dados para gerir grandes bases de conhecimento

    ![Use as funcionalidades de paginação do Fabricante qnA acima das perguntas e respostas para mover-se através de páginas de perguntas e respostas](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Eliminar bases de dados de conhecimento

A eliminar uma base de dados de conhecimento (KB) é uma operação permanente. Não pode ser anulada. Antes de apagar uma base de conhecimento, deve exportar a base de conhecimentos a partir da página **Definições** do portal QnA Maker. 

Se partilhar o seu KB com [colaboradores](collaborate-knowledge-base.md) então apague-o, todos perdem acesso ao KB. 

## <a name="delete-azure-resources"></a>Eliminar recursos do Azure 

Se eliminar qualquer um dos recursos do Azure utilizados para as bases de dados de conhecimento do QnA Maker, as bases de dados de conhecimento deixarão de funcionar. Antes de apagar quaisquer recursos, certifique-se de exportar as suas bases de conhecimento a partir da página **Definições.** 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Colabore numa base de conhecimento](./collaborate-knowledge-base.md)
