---
title: 'Quickstart: Use o Estúdio Synapse'
description: Neste arranque rápido, você verá e aprenderá como é fácil consultar vários tipos de ficheiros usando o Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a45c98192eccdb320814f88f7f4823603357f528
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031760"
---
# <a name="quickstart-use-synapse-studio-preview"></a>Quickstart: Use Synapse Studio (pré-visualização)

Neste arranque rápido, aprenderás a consultar ficheiros usando o Synapse Studio.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

[Crie um espaço de trabalho Azure Synapse e conta de armazenamento associada.](quickstart-create-workspace.md)

## <a name="launch-synapse-studio"></a>Iniciar o Synapse Studio

No seu espaço de trabalho Azure Synapse no portal Azure, clique em **Launch Synapse Studio**.

![Iniciar o Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Em alternativa, pode lançar o Synapse Studio clicando no [Azure Synapse Analytics](https://web.azuresynapse.net) e fornecendo os valores adequados de inquilino, subscrição e espaço de trabalho.

## <a name="browse-storage-accounts"></a>Procurar contas de armazenamento

Assim que abrir o Synapse Studio, navegue pelos **Dados** e expanda as **contas de Armazenamento** para ver a conta de armazenamento no espaço de trabalho.

![Navegue ficheiros no armazenamento](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Pode criar novas pastas e carregar ficheiros utilizando os links na barra de ferramentas para organizar os seus ficheiros.

## <a name="query-files-on-storage-account"></a>Ficheiros de consulta na conta de armazenamento

> [!IMPORTANT]
> É necessário ser membro do `Storage Blob Reader` papel no armazenamento subjacente para poder consultar os ficheiros. Saiba como atribuir permissões de [Armazenamento **Blob Data Reader** ou Storage **Blob Data Contributor** RBAC no Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-an-azure-built-in-role).

1. Faça upload de `PARQUET` alguns ficheiros.
2. Selecione um ou mais ficheiros e, em seguida, crie um novo script SQL ou um portátil Spark para ver o conteúdo dos ficheiros. Se quiser criar um caderno, terá de criar uma [piscina Apache Spark em espaços de trabalho da Synapse.](quickstart-create-apache-spark-pool-studio.md)

   ![Ficheiros de consulta no armazenamento](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Executar a consulta gerada ou o caderno para ver o conteúdo do ficheiro.

   ![Ver o conteúdo do ficheiro](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Pode alterar a consulta para filtrar e classificar resultados. Encontre funcionalidades linguísticas disponíveis em SQL on demand em [SQL features overview](sql/overview-features.md).

## <a name="next-steps"></a>Próximos passos

- Permitir que os utilizadores de Azure AD questionem ficheiros [atribuindo permissões de **Armazenamento Blob Data Reader** ou De Armazenamento **Blob Data Contributor** RBAC no Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-an-azure-built-in-role)
- [Ficheiros de consulta sobre o armazenamento do Azure utilizando o SQL On-Demand](sql/on-demand-workspace-overview.md)
- [Criar um conjunto do Apache Spark com o portal do Azure](quickstart-create-apache-spark-pool-portal.md)
- [Criar relatório power BI sobre ficheiros armazenados no Azure Storage](sql/tutorial-connect-power-bi-desktop.md)
