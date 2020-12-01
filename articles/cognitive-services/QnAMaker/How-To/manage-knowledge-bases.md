---
title: Gerir bases de conhecimento - QnA Maker
description: O QnA Maker permite-lhe gerir as suas bases de conhecimento, proporcionando acesso às definições e conteúdos da base de conhecimento.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 8c13dc0854fb29467b0fe8a1ce5f2dfc1c19bd78
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352342"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Criar base de conhecimento e gerir definições

O QnA Maker permite-lhe gerir as suas bases de conhecimento, fornecendo acesso às definições da base de conhecimento e fontes de dados.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.
> * Um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) criado no portal Azure. Lembre-se do seu ID, Subscrição, nome de recurso QnA do Azure Ative que selecionou quando criou o recurso.

## <a name="create-a-knowledge-base"></a>Criar uma base de dados de conhecimento

1. Inscreva-se no portal [QnAMaker.ai](https://QnAMaker.ai) com as suas credenciais Azure.

1. No portal QnA Maker, selecione **Criar uma base de conhecimento.**

1. Na página **Criar,** salte **o Passo 1** se já tiver o seu recurso QnA Maker.

    Se ainda não criou o recurso, selecione **Criar um serviço QnA**. Será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar um serviço do Criador de FAQ na sua subscrição. Lembre-se do seu ID, Subscrição, nome de recurso QnA do Azure Ative que selecionou quando criou o recurso.

    Quando terminar de criar o recurso no portal Azure, volte ao portal QnA Maker, refresque a página do navegador e continue até ao **Passo 2**.

1. No **Passo 3,** selecione o seu diretório Ative, subscrição, serviço (recurso) e o idioma para todas as bases de conhecimento criadas no serviço.

   ![Screenshot de selecionar uma base de conhecimento de serviço QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. No **Passo 3,** nomeie a sua base de `My Sample QnA KB` conhecimentos.

1. No **passo 4**, configurar as definições com a seguinte tabela:

    |Definição|Valor|
    |--|--|
    |**Ativar a extração em várias voltas a partir de ficheiros URLs, .pdf ou .docx.**|Marcado|
    |**Texto de resposta predefinido**| `Quickstart - default answer not found.`|
    |**+ Adicionar URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Conversa**|Selecione **Profissional**|

1. No **passo 5**, selecione **Criar o seu KB**.

    O processo de extração leva alguns momentos para ler o documento e identificar perguntas e respostas.

    Depois de o QnA Maker criar com sucesso a base de conhecimento, abre-se a página base do **Conhecimento.** Pode editar o conteúdo da base de conhecimento nesta página.

## <a name="edit-knowledge-base"></a>Editar base de dados de conhecimento

1.  Selecione **as minhas bases de conhecimento** na barra de navegação superior.

       Pode ver todos os serviços que criou ou partilhou consigo na ordem descendente da última data **modificada.**

       ![As minhas bases de conhecimento](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecione uma base de conhecimento específica para fazer edições.

1.  Selecione **Definições**. A lista a seguir contém campos que pode alterar.

       |Objetivo|Ação|
       |--|--|
       |Adicionar URL|Pode adicionar novos URLs para adicionar novos conteúdos FAQ à base de conhecimento clicando em Gerir a **base de conhecimentos -> link '+ Add URL'.**|
       |Excluir URL|Pode eliminar os URLs existentes selecionando o ícone de eliminação, o caixote do lixo.|
       |Atualizar conteúdo|Se pretender que a sua base de conhecimentos rastreie o conteúdo mais recente dos URLs existentes, selecione a caixa de verificação **Refresh.** Isto atualizará a base de conhecimento com o conteúdo de URL mais recente uma vez. Isto não está a definir um horário regular de atualizações.|
       |Adicionar ficheiro|Pode adicionar um documento de ficheiro suportado para fazer parte de uma base de conhecimento, selecionando Gerir a base de **conhecimentos,** selecionando **então + Adicionar Ficheiro**|
    |Importar|Também pode importar qualquer base de conhecimento existente selecionando o botão **base de importância importância.** |
    |Atualizar|A atualização da base de conhecimento depende do **nível de preços de gestão** utilizado ao mesmo tempo que cria o serviço QnA Maker associado à sua base de conhecimentos. Pode também atualizar o nível de gestão a partir do portal Azure, se necessário.

  1. Uma vez terminada a realização de alterações na base de conhecimento, **selecione Save and train** in the top-right corner of the page para persistir nas alterações.

       ![Salvar e Treinar](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Se deixar a página antes de selecionar **Salvar e treinar,** todas as alterações serão perdidas.



## <a name="manage-large-knowledge-bases"></a>Gerir grandes bases de conhecimento

* **Grupos de origem de dados**: Os QNAs são agrupados pela fonte de dados a partir da qual foram extraídos. Pode expandir ou destruir a fonte de dados.

    ![Utilize a barra de origem de dados do QnA Maker para colapsar e expandir perguntas e respostas de fontes de dados](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Pesquisa rumo à base de conhecimentos**: Pode pesquisar a base de conhecimentos digitando na caixa de texto no topo da tabela Base de Conhecimento. Clique para inserir para pesquisar o conteúdo de perguntas, respostas ou metadados. Clique no ícone X para remover o filtro de pesquisa.

    ![Utilize a caixa de pesquisa QnA Maker acima das perguntas e respostas para reduzir a vista apenas para itens que combinam filtros](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Pagination**: Mover-se rapidamente através de fontes de dados para gerir grandes bases de conhecimento

    ![Utilize as funcionalidades de paginação do Criador QnA acima das perguntas e respostas para mover-se através de páginas de perguntas e respostas](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Eliminar bases de conhecimento

A eliminação de uma base de conhecimento (KB) é uma operação permanente. Não pode ser desfeito. Antes de eliminar uma base de conhecimento, deve exportar a base de conhecimento a partir da página **Definições** do portal QnA Maker.

Se partilhar a sua base de conhecimento com os colaboradores,](colaborar-conhecimento-base.md) então elimine-a, todos perdem acesso ao KB.

## <a name="next-steps"></a>Passos seguintes

Saiba como [gerir a linguagem](../index.yml) de todas as bases de conhecimento num recurso.

* Editar pares QnA
* Gerir os recursos do Azure utilizados pela QnA Maker