---
title: Ativar os cadernos na conta Azure Cosmos DB (pré-visualização)
description: Os cadernos integrados da Azure Cosmos DB permitem analisar e visualizar os seus dados a partir do Portal. Este artigo descreve como ativar esta funcionalidade para contas cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76768026"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Ativar cadernos para contas Azure Cosmos DB (pré-visualização)

> [!IMPORTANT]
> Os cadernos incorporados para o Azure Cosmos DB estão atualmente disponíveis nas seguintes regiões de Azure: Austrália Leste, Leste dos EUA, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Reino Unido Sul, Europa Ocidental e Oeste DOS 2. Para utilizar cadernos, [crie uma nova conta com cadernos](#enable-notebooks-in-a-new-cosmos-account) ou [permita os cadernos numa conta existente](#enable-notebooks-in-an-existing-cosmos-account) numa dessas regiões.

Os cadernos Jupyter incorporados em Azure Cosmos DB permitem-lhe analisar e visualizar os seus dados a partir do portal Azure. Este artigo descreve como ativar esta funcionalidade para a conta do Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Ativar cadernos numa nova conta Cosmos
1. Assine no [portal Azure.](https://portal.azure.com/)
1. Selecione **Criar um recurso** > **Bases de** > dados**Azure Cosmos DB**.
1. Na página **Create Azure Cosmos DB Account,** selecione **Notebooks**. 
 
    ![Selecione a opção de cadernos em Azure Cosmos DB Criar lâmina](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Selecione **Rever + criar**. Pode saltar a opção **Rede** e **Etiquetas.** 
1. Reveja as definições da conta e, em seguida, selecione **Criar**. A criação da conta demora alguns minutos. Aguarde até que a página do portal apresente **A implementação está concluída**. 

    ![O painel de Notificações de portal do Azure](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Selecione **Ir para recurso** para aceder à página da conta do Azure Cosmos DB. 

    ![A página da conta do Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Navegue para o painel do Explorador de **Dados.** Agora deviaver o espaço de trabalho dos seus cadernos.

    ![Novo espaço de trabalho dos novos cadernos Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Ativar cadernos numa conta cosmos existente
Também pode ativar os cadernos nas contas existentes. Este passo tem de ser feito apenas uma vez por conta.

1. Navegue para o painel **do Data Explorer** na sua conta Cosmos.
1. Selecione **Ativar cadernos**.

    ![Criar um novo espaço de trabalho de cadernos no Data Explorer](media/enable-notebooks/enable-notebooks-workspace.png)
1. Isto irá instá-lo a criar um novo espaço de trabalho de cadernos. Selecione **Configuração completa completa.**
1. A sua conta está agora habilitada a utilizar cadernos!

## <a name="create-and-run-your-first-notebook"></a>Crie e execute o seu primeiro caderno

Para verificar se pode utilizar cadernos, selecione um dos cadernos em Cadernos de Amostra. Isto irá guardar uma cópia do caderno para o seu espaço de trabalho e abri-lo.

Neste exemplo, usaremos **GettingStarted.ipynb**. 

![Ver Caderno GettingStarted.ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Para executar o caderno:
1. Selecione a primeira célula de código que contém código Python. 
1. Selecione **Executar** para executar a célula. Também pode utilizar **o Shift + Enter** para executar a célula.
1. Refresque o painel de recursos para ver a base de dados e o recipiente que foi criado.

    ![Correr começando o caderno](media/enable-notebooks/run-first-notebook-cell.png)

Também pode selecionar **o Novo Caderno** para criar um novo caderno ou carregar um ficheiro de portátil (.ipynb) existente, selecionando o Upload **File** do menu **My Notebooks.** 

![Criar ou carregar um novo caderno](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Passos seguintes

- Conheça os benefícios dos [cadernos Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
