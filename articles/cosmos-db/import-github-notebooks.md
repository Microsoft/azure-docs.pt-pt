---
title: Importar e executar cadernos de um GitHub repo para Azure Cosmos DB
description: Saiba como ligar-se ao GitHub e importe os cadernos de um repo GitHub para a sua conta Azure Cosmos. Depois de importar, pode correr, editá-los e guardar as alterações de volta para o GitHub.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: d85f020152fa3cadb1d437c125d327f5e895e14e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85262893"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Importar cadernos de um GitHub repo para Azure Cosmos DB

Depois de [ativar o suporte](enable-notebooks.md) de portátil para as suas contas Azure Cosmos, pode criar novos cadernos, carregar novos cadernos a partir do seu computador local ou importar os cadernos existentes a partir das suas contas GitHub. Este artigo mostra como ligar os seus cadernos espaço de trabalho ao GitHub e importar os cadernos de um repo GitHub para a sua conta Azure Cosmos. Depois de importar, pode executá-las, fazer alterações e guardar as alterações de volta para o GitHub.

## <a name="get-notebooks-from-github"></a>Obtenha cadernos do GitHub

Você pode ligar-se aos seus próprios repositórios GitHub ou outros repositórios públicos do GitHub para ler, autor e partilhar cadernos em Azure Cosmos DB. Utilize os seguintes passos para ligar a uma conta GitHub:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue para a sua conta Azure Cosmos.

1. Abra o **separador Data Explorer.** Este separador irá mostrar todas as bases de dados, contentores e cadernos existentes.

1. Selecione o item do menu **'Ligar ao GitHub'.**

1. Abre-se um separador onde pode optar por se ligar apenas aos **repos públicos** ou **aos repos públicos e privados.**  Depois de escolher a opção pretendida, **selecione Acesso autorizado**. É necessária autorização para que a Azure Cosmos DB aceda aos repositórios na sua conta GitHub.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Autorize a Azure Cosmos DB a aceder aos seus repositórios GitHub":::

1. É redirecionado para a página web "github.com", onde pode confirmar a autorização. Selecione o botão **Authorize AzureCosmosDBNotebooks** e introduza a sua palavra-passe da conta GitHub no pedido.

1. Depois de a autorização ter sido bem sucedida, leva-te de volta à tua conta da Azure Cosmos. Você pode então ver todos os repos públicos/privados da sua conta GitHub. Pode selecionar um repo da lista disponível ou adicionar um repo diretamente utilizando o seu URL.

1. Uma vez selecionada a repo requerida, a entrada de repo sai da secção **de repos unpinned** para a secção **de repos pinned.** Se necessário, também pode escolher um ramo específico desse repo para importar os cadernos.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Autorize a Azure Cosmos DB a aceder aos seus repositórios GitHub":::

1. Selecione **OK** para concluir a operação de importação. Todos os cadernos disponíveis no ramo selecionado do seu repo são importados para a sua conta Azure Cosmos.

Depois de se integrar com uma conta GitHub, só pode ver a lista de repositórios e cadernos na sua conta Azure Cosmos. Esta afirmação é verdadeira mesmo que vários utilizadores acedam à conta DB do Azure Cosmos e adicionem as suas próprias contas. Por outras palavras, vários utilizadores podem usar a mesma conta Azure Cosmos para ligar o espaço de trabalho do portátil ao GitHub. No entanto, cada utilizador só vê a lista de repositórios e cadernos que importaram. Os cadernos importados por outros não são visíveis para si.

Para desligar a sua conta GitHub do espaço de trabalho dos cadernos, abra o **separador Data Explorer,** selecione `…` ao lado dos **repos do GitHub** e selecione **Disconnect from GitHub**.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Edite um caderno e empurre as alterações para o GitHub

Pode editar um caderno existente ou adicionar um novo caderno ao repo e guardar as alterações de volta ao GitHub.

Depois de editar um caderno existente, **selecione Save**. Abre-se uma caixa de diálogo onde pode introduzir a mensagem de compromisso para as alterações efetuadas. **Selecione Commit** e o caderno no GitHub é atualizado. Pode validar as atualizações ao iniciar sessão na sua conta GitHub e verificar o histórico de compromissos.

No fluxo gitHub regular depois de cometer as alterações, normalmente irá empurrar alterações para um controlo remoto. No entanto, neste caso, a opção de compromisso serve o propósito de "encenar, comprometer e empurrar" as suas atualizações para o GitHub.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Autorize a Azure Cosmos DB a aceder aos seus repositórios GitHub":::

## <a name="next-steps"></a>Passos seguintes

* Conheça os benefícios dos [cadernos Azure Cosmos DB Jupyter.](cosmosdb-jupyter-notebooks.md)

