---
title: Ativar os cadernos na conta DB do Azure Cosmos (pré-visualização)
description: Os cadernos incorporados da Azure Cosmos DB permitem analisar e visualizar os seus dados a partir do Portal. Este artigo descreve como ativar esta funcionalidade para contas Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 4c485bf6b9eb34e68e399c24e51286428f47586f
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261907"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Ativar cadernos para contas DB do Azure Cosmos (pré-visualização)

> [!IMPORTANT]
> Os cadernos incorporados para a Azure Cosmos DB estão atualmente disponíveis nas seguintes regiões de Azure: Austrália Oriental, Leste dos EUA, Leste dos EUA 2, Norte da Europa, South Central EUA, Sudeste Asiático, Reino Unido Sul, Europa Ocidental e EUA Ocidental 2. Para utilizar cadernos, [crie uma nova conta com cadernos](#enable-notebooks-in-a-new-cosmos-account) ou permita [cadernos numa conta existente](#enable-notebooks-in-an-existing-cosmos-account) numa dessas regiões.

Os cadernos Jupyter incorporados em Azure Cosmos DB permitem-lhe analisar e visualizar os seus dados a partir do portal Azure. Este artigo descreve como ativar esta funcionalidade para a conta do Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Ativar cadernos numa nova conta cosmos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Criar uma**base de  >  **dados de**recursos  >  **Azure Cosmos DB**.
1. Na página **'Conta DB Create Azure Cosmos',** selecione **Cadernos**. 
 
    :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks.png" alt-text="Selecione a opção de cadernos em Azure Cosmos DB Criar lâmina":::

1. Selecione **Rever + criar**. Pode saltar a opção **Rede** e **Tags.** 
1. Reveja as definições da conta e, em seguida, selecione **Criar**. A criação da conta demora alguns minutos. Aguarde até que a página do portal apresente **A implementação está concluída**. 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="O painel de Notificações de portal do Azure":::

1. Selecione **Ir para recurso** para aceder à página da conta do Azure Cosmos DB.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="A página da conta do Azure Cosmos DB":::

1. Navegue para o painel do Explorador de **Dados.** Agora deve ver o seu espaço de trabalho dos seus cadernos.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Novo Azure Cosmos DB cadernos espaço de trabalho":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Ativar cadernos numa conta cosmos existente

Também pode ativar os cadernos nas contas existentes. Este passo só tem de ser feito uma vez por conta.

1. Navegue para o painel **do Data Explorer** na sua conta Cosmos.
1. Selecione **'Enable Notebooks'.**

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Criar um novo espaço de trabalho de cadernos no Data Explorer":::

1. Isto irá levá-lo a criar um novo espaço de trabalho de cadernos. Selecione **configuração completa.**
1. A sua conta está agora habilitada a utilizar cadernos!

## <a name="create-and-run-your-first-notebook"></a>Crie e execute o seu primeiro caderno

Para verificar se pode utilizar cadernos, selecione um dos cadernos em Cadernos de Amostra. Isto irá guardar uma cópia do caderno para o seu espaço de trabalho e abri-lo.

Neste exemplo, usaremos **GettingStarted.ipynb**. 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Ver caderno GettingStarted.ipynb":::

Para executar o caderno:
1. Selecione a primeira célula de código que contém o código Python. 
1. **Selecione Executar** para executar a célula. Também pode utilizar **o Shift + Enter** para executar a célula.
1. Refresque o painel de recursos para ver a base de dados e o recipiente que foi criado.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Executar começando o caderno":::

Também pode selecionar **Novo Caderno** para criar um novo bloco de notas ou carregar um ficheiro de caderno existente (.ipynb) selecionando o **Upload File** a partir do menu **My Notebooks.** 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Criar ou carregar um novo caderno":::

## <a name="next-steps"></a>Passos seguintes

- Conheça os benefícios dos [cadernos Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
