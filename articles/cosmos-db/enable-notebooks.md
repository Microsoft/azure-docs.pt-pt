---
title: Ativar os cadernos na conta DB do Azure Cosmos (pré-visualização)
description: Os cadernos incorporados da Azure Cosmos DB permitem analisar e visualizar os seus dados a partir do Portal. Este artigo descreve como ativar esta funcionalidade para contas Cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 02e8ad5f2b5326f947ba0bca6456ce9d9d3c27d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692781"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Ativar cadernos para contas DB do Azure Cosmos (pré-visualização)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Os cadernos incorporados para a Azure Cosmos DB estão atualmente disponíveis em [29 regiões.](#supported-regions) Para utilizar cadernos, [crie uma nova conta Cosmos](#create-a-new-cosmos-account) ou permita [cadernos numa conta existente](#enable-notebooks-in-an-existing-cosmos-account) numa dessas regiões. 

Os cadernos Jupyter incorporados em Azure Cosmos DB permitem-lhe analisar e visualizar os seus dados a partir do portal Azure. Este artigo descreve como ativar esta funcionalidade para a conta do Azure Cosmos DB.

## <a name="create-a-new-cosmos-account"></a>Criar uma nova conta cosmos
A partir de 10 de fevereiro de 2021, as novas contas Azure Cosmos criadas numa das [regiões suportadas](#supported-regions) terão automaticamente os cadernos ativados. Não é necessária nenhuma configuração adicional para permitir os cadernos. Utilize as seguintes instruções para criar uma nova conta:
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Selecione **Criar uma** base de  >  **dados de** recursos  >  **Azure Cosmos DB**.
1. Introduza as definições básicas para a conta.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="A página de nova conta do Azure Cosmos DB":::

1. Selecione **Rever + criar**. Pode saltar a opção **Rede** e **Tags.** 
1. Reveja as definições da conta e, em seguida, selecione **Criar**. A criação da conta demora alguns minutos. Aguarde até que a página do portal apresente **A implementação está concluída**.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="O painel Notificações do portal do Azure":::

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

## <a name="supported-regions"></a>Regiões suportadas
Os blocos de notas incorporados do Azure Cosmos DB estão disponíveis atualmente em 29 regiões do Azure. As novas contas Azure Cosmos criadas nestas regiões terão cadernos automaticamente ativados. Os cadernos são gratuitos com a sua conta. 

- Austrália Central
- Austrália Central 2
- Leste da Austrália
- Austrália Sudeste
- Sul do Brasil
- Canadá Central
- Leste do Canadá
- Índia Central
- E.U.A. Central
- E.U.A. Leste
- E.U.A. Leste 2
- França Central
- Sul de França
- Alemanha Norte
- Alemanha Centro-Oeste
- Oeste do Japão
- Sul da Coreia do Sul
- E.U.A. Centro-Norte
- Europa do Norte
- E.U.A. Centro-Sul
- Sudeste Asiático
- Suíça Norte
- Centro dos Emirados Árabes Unidos
- Sul do Reino Unido
- Oeste do Reino Unido
- E.U.A. Centro-Oeste
- Europa Ocidental
- Oeste da Índia
- E.U.A. Oeste 2

## <a name="next-steps"></a>Passos seguintes

* Conheça os benefícios dos [cadernos Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
* [Explore a galeria de amostras de cadernos](https://cosmos.azure.com/gallery.html)
* [Publique cadernos na galeria de cadernos Azure Cosmos DB](publish-notebook-gallery.md)
* [Use funcionalidades e comandos de caderno Python](use-python-notebook-features-and-commands.md)
* [Use funcionalidades e comandos de caderno C#](use-csharp-notebook-features-and-commands.md)
* [Importar cadernos de um repo GitHub](import-github-notebooks.md)
