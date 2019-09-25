---
title: Habilitar blocos de anotações na conta de Azure Cosmos DB
description: Os blocos de anotações internos do Azure Cosmos DB permitem que você analise e visualize seus dados de dentro do Portal. Este artigo descreve como habilitar esse recurso para contas do cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 34e1d9fe937118a2eafcdd85c153653eb28752d4
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219259"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts"></a>Habilitar blocos de anotações para contas de Azure Cosmos DB

Os blocos de anotações internos do Jupyter no Azure Cosmos DB permitem que você analise e visualize os dados do portal do Azure. Este artigo descreve como habilitar esse recurso para sua conta do Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Habilitar blocos de anotações em uma nova conta do cosmos
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Selecione **Criar um recurso** > **Bases de dados** > **Azure Cosmos DB**.
1. Na página **criar conta de Azure Cosmos DB** , selecione **notebooks**. 
 
    ![Opção selecionar blocos de anotações na folha Azure Cosmos DB criar](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Selecione **Rever + criar**. Você pode ignorar a opção de **rede** e **marcas** . 
1. Examine as configurações da conta e, em seguida, selecione **criar**. Leva alguns minutos para criar a conta. Aguarde até que a página do portal exiba **sua implantação esteja concluída**. 

    ![O painel de Notificações de portal do Azure](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Selecione **ir para o recurso** para acessar a página da conta Azure Cosmos DB. 

    ![A página da conta Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Navegue até o painel de **Data Explorer** . Agora você deve ver seu espaço de trabalho de blocos de anotações.

    ![Espaço de trabalho novo Azure Cosmos DB blocos de anotações](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Habilitar blocos de anotações em uma conta existente do cosmos
Você também pode habilitar blocos de anotações em contas existentes. Esta etapa precisa ser feita apenas uma vez por conta.

1. Navegue até o painel de **Data Explorer** em sua conta do cosmos.
1. Selecione **habilitar blocos de anotações**.

    ![Criar um novo espaço de trabalho de blocos de anotações no Data Explorer](media/enable-notebooks/enable-notebooks-workspace.png)
1. Isso solicitará que você crie um novo espaço de trabalho de blocos de anotações. Selecione **concluir configuração.**
1. Sua conta agora está habilitada para usar blocos de anotações!

## <a name="create-and-run-your-first-notebook"></a>Criar e executar seu primeiro bloco de anotações

Para verificar se você pode usar blocos de anotações, selecione um dos blocos de anotações em blocos de anotações de exemplo. Isso salvará uma cópia do bloco de anotações no espaço de trabalho e o abrirá.

Neste exemplo, usaremos **gettingstarted. ipynb**. 

![Exibir o GettingStarted. ipynb Notebook](media/enable-notebooks/select-getting-started-notebook.png)

Para executar o bloco de anotações:
1. Selecione a primeira célula de código que contém o código Python. 
1. Selecione **executar** para executar a célula. Você também pode usar **Shift + Enter** para executar a célula.
1. Atualize o painel de recursos para ver o banco de dados e o contêiner que foram criados.

    ![Executar o bloco de anotações de introdução](media/enable-notebooks/run-first-notebook-cell.png)

Você também pode selecionar **novo bloco de anotações** para criar um novo bloco de anotações ou carregar um arquivo de bloco de anotações (. ipynb) existente selecionando **carregar arquivo** no menu **meus blocos de anotações** . 

![Criar ou carregar um novo bloco de anotações](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os benefícios do [Azure Cosmos DB notebooks Jupyter](cosmosdb-jupyter-notebooks.md)
