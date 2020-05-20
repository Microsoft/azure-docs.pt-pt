---
title: Importar e executar cadernos de um gitHub repo para Azure Cosmos DB
description: Aprenda a ligar-se ao GitHub e importe os cadernos de um repo GitHub para a sua conta Azure Cosmos. Depois de importar, pode correr, editá-los e guardar as alterações para o GitHub.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 9ce65d316b8236b83435388d2c1883cb9b89b03a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691384"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Importar cadernos de um gitHub repo para Azure Cosmos DB

Depois de ativar o [suporte portátil](enable-notebooks.md) para as suas contas Azure Cosmos, pode criar novos cadernos, fazer upload de novos cadernos a partir do seu computador local ou importar os cadernos existentes a partir das suas contas GitHub. Este artigo mostra como ligar o espaço de trabalho dos seus cadernos ao GitHub e importar os cadernos de um repo GitHub para a sua conta Azure Cosmos. Depois de importar, pode executá-las, fazer alterações e guardar as alterações para o GitHub.

## <a name="get-notebooks-from-github"></a>Obtenha cadernos do GitHub

Você pode conectar-se com seus próprios repositórios GitHub ou outros repositórios públicos gitHub para ler, autor e partilhar cadernos em Azure Cosmos DB. Utilize os seguintes passos para se ligar a uma conta GitHub:

1. Assine no [portal Azure](https://portal.azure.com/) e navegue para a sua conta Azure Cosmos.

1. Abra o separador **Data Explorer.** Este separador mostrará todas as suas bases de dados, contentores e cadernos existentes.

1. Selecione o item do menu **Connect to GitHub.**

1. Abre-se um separador onde pode optar por ligar-se apenas a **repos públicos** ou **públicos e privados.**  Depois de escolher a opção necessária, **selecione Autorizar o acesso**. É necessária autorização para que a Azure Cosmos DB aceda aos repositórios na sua conta GitHub.

   ![Autorize o Azure Cosmos DB a aceder aos seus repositórios GitHub](./media/import-github-notebooks/authorize-access-github.png)

1. Você é redirecionado para a página web "github.com", onde pode confirmar a autorização. Selecione o botão Autorizar o **Botão AzureCosmosDBNotebooks** e introduza a sua palavra-passe da conta GitHub no pedido.

1. Depois da autorização ser bem sucedida, leva-o de volta à sua conta Azure Cosmos. Em seguida, pode ver todos os repos públicos/privados da sua conta GitHub. Pode selecionar um repo da lista disponível ou adicionar um repo diretamente utilizando o seu URL.

1. Uma vez selecionada a repo necessária, a entrada de repo passa da secção **de repos unpinned** para a secção **de repos pinned.** Se necessário, também pode escolher um ramo específico desse repo para importar os cadernos.

   ![Escolha um repositório e um ramo](./media/import-github-notebooks/choose-repo-branch.png)

1. Selecione **OK** para completar a operação de importação. Todos os cadernos disponíveis no ramo selecionado do seu repo são importados para a sua conta Azure Cosmos.

Depois de integrar com uma conta GitHub, só você pode ver a lista de repositórios e cadernos na sua conta Azure Cosmos. Esta afirmação é verdadeira mesmo que vários utilizadores acedam à conta Do MB E acrescentem as suas próprias contas. Por outras palavras, vários utilizadores podem usar a mesma conta Azure Cosmos para ligar o espaço de trabalho do bloco de notas ao GitHub. No entanto, cada utilizador vê apenas a lista de repositórios e cadernos que importaram. Os cadernos importados por outros não são visíveis para si.

Para desligar a sua conta GitHub do espaço de trabalho dos cadernos, abra o separador **Data Explorer,** selecione `…` ao lado dos **repos GitHub** e selecione **Disconnect from GitHub**.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Editar um caderno e impulsionar alterações ao GitHub

Pode editar um caderno existente ou adicionar um novo caderno ao repo e guardar as alterações de volta ao GitHub.

Depois de editar um caderno existente, selecione **Guardar**. Abre-se uma caixa de diálogo onde pode introduzir a mensagem de compromisso para as alterações que fez. Selecione **Commit** e o caderno no GitHub é atualizado. Pode validar as atualizações assinando na sua conta GitHub e verificando o histórico de compromissos.

No fluxo regular do GitHub depois de cometer as alterações, normalmente irá empurrar alterações para um controlo remoto. No entanto, neste caso, a opção de compromisso serve o propósito de "encenar, comprometer e empurrar" as suas atualizações para o GitHub.

![Editar cadernos e comprometer alterações ao GitHub](./media/import-github-notebooks/commit-changes-github.png)

## <a name="next-steps"></a>Passos seguintes

* Conheça os benefícios dos [cadernos Azure Cosmos DB Jupyter.](cosmosdb-jupyter-notebooks.md)

