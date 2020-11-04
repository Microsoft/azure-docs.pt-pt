---
title: Conecte-se ao SYNapse SQL com o Azure Data Studio (pré-visualização)
description: Utilize o Azure Data Studio (pré-visualização) para ligar e consultar o Synapse SQL em Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: fb4c7f8ba49cf701ef13cb57ab2b323a94c928cc
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323825"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Conecte-se ao SYNapse SQL com o Azure Data Studio (pré-visualização)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Pode utilizar [o Azure Data Studio (pré-visualização)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para ligar e consultar o Synapse SQL em Azure Synapse Analytics. 

## <a name="connect"></a>Ligar

Para ligar ao Synapse SQL, abra o Azure Data Studio e selecione **New Connection**.

![Aberto Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Escolha **o Microsoft SQL Server** como o tipo de **ligação.**

A ligação requer os seguintes parâmetros:

* **Servidor:** Servidor no formulário `<Azure Synapse workspace name>` -ondemand.sql.azuresynapse.net
* **Base de dados:** Nome da base de dados

> [!NOTE]
> Se você gostaria de usar piscina **SQL sem servidor (pré-visualização)** o URL deve parecer:
>
> - `<Azure Synapse workspace name>`ondemand.sql.azuresynapse.net.
>
> Se você gostaria de usar **piscina SQL dedicada,** o URL deve parecer:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Escolha a **autenticação do Windows** , **Diretório Ativo Azure,** ou **SQL Login** como tipo de **autenticação**.

Para utilizar o **SQL Login** como tipo de autenticação, adicione os parâmetros username/password:

* **Utilizador:** Utilizador do servidor no formulário `<User>`
* **Senha:** Palavra-passe associada ao utilizador

Para utilizar o Azure Ative Directory, tem de escolher o tipo de autenticação necessário.

![Autenticação do AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

A imagem que se segue mostra os **dados de ligação** para **autenticação do Windows** :

![Autenticação do Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

A imagem que se segue mostra os **Detalhes de Conexão** utilizando **o SQL Login** :

![Início de sessão do SQL](./media/get-started-azure-data-studio/2-database-details.png)

Após o login bem sucedido, você deve ver um dashboard como este: ![ Dashboard](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Consulta

Uma vez ligado, pode consultar o Synapse SQL utilizando declarações [de Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) suportadas contra a instância. Selecione **Nova consulta** a partir da vista do painel de instrumentos para começar.

![New Query](./media/get-started-azure-data-studio/5-new-query.png)

Por exemplo, pode utilizar a seguinte declaração Transact-SQL para [consultar ficheiros Parquet](query-parquet-files.md) utilizando a piscina SQL sem servidor:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Passos seguintes 
Explore outras formas de se conectar ao SQL da Sinapse: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)

Visite [o Azure Data Studio para conectar e consultar dados utilizando um pool DE SQL dedicado em Azure Synapse Analytics,](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-dw)para obter mais informações. 
