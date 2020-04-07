---
title: Gerir bases de conhecimento - QnA Maker
description: O QnA Maker permite-lhe gerir as suas bases de conhecimento, proporcionando acesso às definições e conteúdos da base de conhecimento.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4eb07b30b10826c361f4f9b805b517e372fc5ea1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756812"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Criar base de conhecimento e gerir configurações

O QnA Maker permite-lhe gerir as suas bases de conhecimento, fornecendo acesso às definições da base de conhecimento e fontes de dados.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
> * Um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) criado no portal Azure. Lembre-se do id do Diretório Ativo Azure, Subscrição, nome de recurso QnA que selecionou quando criou o recurso.

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

1. Inscreva-se no portal [QnAMaker.ai](https://QnAMaker.ai) com as suas credenciais Azure.

1. No portal QnA Maker, selecione **Criar uma base de conhecimento**.

1. Na página **Criar,** salte o **Passo 1** se já tiver o seu recurso QnA Maker.

    Se ainda não criou o recurso, selecione **Criar um serviço QnA**. Será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar um serviço do Criador de FAQ na sua subscrição. Lembre-se do id do Diretório Ativo Azure, Subscrição, nome de recurso QnA que selecionou quando criou o recurso.

    Quando terminar de criar o recurso no portal Azure, volte ao portal QnA Maker, refresque a página do navegador e continue a **passo 2**.

1. No **Passo 3,** selecione o seu diretório Ativo, subscrição, serviço (recurso) e o idioma para todas as bases de conhecimento criadas no serviço.

   ![Screenshot de selecionar uma base de conhecimento de serviço QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. No **passo 3,** nomeie a sua base `My Sample QnA KB`de conhecimentos.

1. No **passo 4,** configure as definições com a tabela seguinte:

    |Definição|Valor|
    |--|--|
    |**Ativar a extração de várias voltas a partir de URLs, ficheiros .pdf ou .docx.**|Assinalado|
    |**Texto de resposta padrão**| `Quickstart - default answer not found.`|
    |**+ Adicionar URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Selecionar **Profissional**|

1. No **passo 5,** selecione **Criar o seu KB**.

    O processo de extração demora alguns momentos a ler o documento e a identificar perguntas e respostas.

    Depois da QnA Maker criar com sucesso a base de conhecimentos, a página base do **Conhecimento** abre. Pode editar o conteúdo da base de conhecimentos nesta página.

## <a name="edit-knowledge-base"></a>Editar base de dados de conhecimento

1.  **Selecione as minhas bases de conhecimento** na barra de navegação superior.

       Pode ver todos os serviços que criou ou partilhou consigo classificados na ordem descendente da última data **modificada.**

       ![Minhas Bases de Conhecimento](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecione uma determinada base de conhecimento para fazer edificações.

1.  Selecione **Definições**. A lista que se segue contém campos que pode alterar.

       |Objetivo|Ação|
       |--|--|
       |Adicionar URL|Pode adicionar novos URLs para adicionar novos conteúdos de FAQ à base de conhecimento clicando em gerir a base de conhecimento - > link **'+ Adicionar URL'.**|
       |Eliminar URL|Pode eliminar urLs existentes selecionando o ícone de exclusão, o caixote do lixo.|
       |Atualizar conteúdo|Se quiser que a sua base de conhecimentos rasteje os mais recentes conteúdos dos URLexistentes, selecione a caixa de verificação **Refresh.** Isto irá atualizar a base de conhecimento com o conteúdo de URL mais recente uma vez. Isto não está a definir um calendário regular de atualizações.|
       |Adicionar ficheiro|Pode adicionar um documento de ficheiro suportado para fazer parte de uma base de conhecimentos, selecionando a base de **conhecimento,** selecionando **depois + Adicionar Ficheiro**|
    |Importar|Também pode importar qualquer base de conhecimento existente selecionando o botão **base de import knowledge.** |
    |Atualizar|A atualização da base de conhecimentos depende do nível de preços de **gestão** utilizado enquanto cria o serviço QnA Maker associado à sua base de conhecimentos. Pode também atualizar o nível de gestão do portal Azure, se necessário.

  1. Uma vez feito alterações na base de conhecimento, selecione **Guardar e treinar** no canto superior direito da página para persistir as alterações.

       ![Salvar e Treinar](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Se deixar a página antes de selecionar **Guardar e treinar,** todas as alterações serão perdidas.



## <a name="manage-large-knowledge-bases"></a>Gerir grandes bases de conhecimento

* **Grupos**de fonte de dados : Os QnAs são agrupados pela fonte de dados a partir da qual foram extraídos. Pode expandir ou colapsar a fonte de dados.

    ![Use a barra de fonte de dados do QnA Maker para colapsar e expandir questões e respostas de fonte de dados](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Pesquisar base**de conhecimento : Pode pesquisar a base de conhecimento digitando na caixa de texto no topo da tabela Base de Conhecimento. Clique em introduzir para pesquisar sobre o conteúdo da pergunta, resposta ou metadados. Clique no ícone X para remover o filtro de pesquisa.

    ![Utilize a caixa de pesquisa qnA Maker acima das perguntas e respostas para reduzir a vista apenas para itens que combinam filtros](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginação**: Mova-se rapidamente através de fontes de dados para gerir grandes bases de conhecimento

    ![Use as funcionalidades de paginação do Fabricante qnA acima das perguntas e respostas para mover-se através de páginas de perguntas e respostas](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Eliminar bases de conhecimento

A exclusão de uma base de conhecimento (KB) é uma operação permanente. Não pode ser desfeito. Antes de apagar uma base de conhecimento, deve exportar a base de conhecimentos a partir da página **Definições** do portal QnA Maker.

Se partilhar a sua base de conhecimento com colaboradores,](colaborar-conhecimento-base.md) então apague-a, todos perdem acesso ao KB.

## <a name="next-steps"></a>Passos seguintes

Aprenda [a gerir a linguagem](language-knowledge-base.md) de todas as bases de conhecimento num recurso.

* Editar pares QnA
* Gerir os recursos azure utilizados pela QnA Maker