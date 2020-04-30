---
title: Use o Estúdio Synapse (pré-visualização)
description: Neste arranque rápido, você verá e aprenderá como é fácil consultar vários tipos de ficheiros usando o Estúdio Synapse.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a29fd66cf264b09cc5e0db7ac1a329be3f297bb8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82096338"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Quickstart: Usando o Estúdio Synapse (pré-visualização)

Neste arranque rápido, aprenderás a consultar ficheiros usando o Estúdio Synapse.

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

[Crie um espaço de trabalho Azure Synapse e uma conta de armazenamento associada.](quickstart-create-workspace.md)

## <a name="launch-synapse-studio"></a>Lançar Estúdio Synapse

No seu espaço de trabalho Azure Synapse no portal Azure, clique em **Launch Synapse Studio**.

![Lançar Estúdio Synapse](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Em alternativa, pode lançar o Estúdio Synapse clicando no [Azure Synapse Analytics](https://web.azuresynapse.net) e fornecendo os valores adequados de inquilino, subscrição e espaço de trabalho.

## <a name="browse-storage-accounts"></a>Navegue nas contas de armazenamento

Assim que abrir o Estúdio Synapse, navegue para **Data** e, em seguida, expanda **as contas** de Armazenamento para ver a conta de armazenamento no espaço de trabalho.

![Navegue ficheiros no armazenamento](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Pode criar novas pastas e carregar ficheiros utilizando os links na barra de ferramentas para organizar os seus ficheiros.

## <a name="query-files-on-storage-account"></a>Arquivos de consulta na conta de armazenamento

> [!IMPORTANT]
> Você precisa ser um `Storage Blob Reader` membro do papel no armazenamento subjacente para ser capaz de consultar os ficheiros. Saiba como atribuir permissões rBAC do Leitor de [Dados **blob de armazenamento** ou do contribuinte de armazenamento **Blob** no Armazenamento Azure](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Faça `PARQUET` upload de alguns ficheiros.
2. Selecione um ou mais ficheiros e, em seguida, crie um novo script SQL ou um notebook Spark para ver o conteúdo dos ficheiros. Se quiser criar um caderno, terá de criar uma [piscina Apache Spark no espaço de trabalho.](quickstart-create-apache-spark-pool.md)

   ![Arquivos de consulta no armazenamento](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Executar a consulta ou caderno gerado para ver o conteúdo do ficheiro:

   ![Ver o conteúdo do ficheiro](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Pode alterar a consulta para filtrar e classificar os resultados. Encontre funcionalidades linguísticas disponíveis no SQL on-demand em [recursos SQL visão geral](sql/overview-features.md).

## <a name="next-steps"></a>Passos seguintes

- Ativar os utilizadores de Anúncios Azure para consultar ficheiros [atribuindo permissões rBAC do Leitor de **Dados Blob de Armazenamento** ou do Contribuinte de Armazenamento **Blob** no Armazenamento Azure](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Arquivos de consulta sobre armazenamento Azure usando SQL On-Demand](sql/on-demand-workspace-overview.md)
- [Criar piscina apache spark](quickstart-create-apache-spark-pool.md)
- [Criar relatório Power BI sobre ficheiros armazenados no Armazenamento Azure](sql/tutorial-connect-power-bi-desktop.md)
