---
title: 'Azure Data Studio (pré-visualização): Connect and consulta Synapse SQL'
description: Utilize o Azure Data Studio (pré-visualização) para ligar e consultar o Synapse SQL no Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: edf5a6a6a0f17c21abb818a0d41d0d0b1c39949c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423804"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Ligue-se ao Synapse SQL com o Azure Data Studio (pré-visualização)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Pode utilizar o [Azure Data Studio (pré-visualização)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para ligar e consultar o Synapse SQL no Azure Synapse Analytics. 

## <a name="connect"></a>Ligar

Para ligar ao Synapse SQL, abra o Azure Data Studio e selecione **New Connection**.

![Open Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Escolha o **Microsoft SQL Server** como o tipo de **Ligação**.

A ligação requer os seguintes parâmetros:

* **Servidor:** Servidor no `<Azure Synapse workspace name>`formulário -ondemand.sql.azuresynapse.net
* **Base de dados:** Nome da base de dados

> [!NOTE]
> Se quiser utilizar o **SQL on-demand (pré-visualização)** o URL deve parecer:
>
> - `<Azure Synapse workspace name>`- ondemand.sql.azuresynapse.net.
>
> Se você gostaria de usar **piscina SQL** o URL deve parecer:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Escolha a **autenticação do Windows,** **diretório Ativo Azure**ou **SQL** como tipo de **autenticação**.

Para utilizar o **SQL Login** como tipo de autenticação, adicione os parâmetros username/password:

* **Utilizador:** Utilizador do servidor no formulário`<User>`
* **Senha:** Senha associada ao utilizador

Para utilizar o Diretório Ativo Azure, é necessário escolher o tipo de autenticação necessário.

![Autenticação do AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

Esta imagem mostra os detalhes de **ligação** para **autenticação do Windows:**

![Autenticação do Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

Esta imagem mostra os detalhes de **ligação** utilizando **o Login SQL:**

![Início de sessão do SQL](./media/get-started-azure-data-studio/2-database-details.png)

Depois de um login bem sucedido, ![você deve ver um dashboard como este: Dashboard](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Consulta

Uma vez ligado, pode consultar synapse SQL usando declarações de [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) suportadas contra a instância. Selecione **Nova consulta** a partir da vista do painel de instrumentos para começar.

![New Query](./media/get-started-azure-data-studio/5-new-query.png)

Por exemplo, pode utilizar a seguinte declaração Transact-SQL para [consultar ficheiros Parquet](query-parquet-files.md) utilizando sql on-demand:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Passos seguintes 
Explore outras formas de se conectar com synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)
 
