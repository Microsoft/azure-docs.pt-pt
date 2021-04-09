---
title: Ligue ao Synapse SQL com o Azure Data Studio
description: Utilize o Azure Data Studio para ligar e consultar o Synapse SQL em Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6b039d934993d2acee630205c5b5e5d8e0f6145e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667598"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Ligue ao Synapse SQL com o Azure Data Studio

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Pode utilizar [o Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio?view=azure-sqldw-latest&preserve-view=true) para ligar e consultar o Synapse SQL em Azure Synapse Analytics. 

## <a name="connect"></a>Ligar

Para ligar ao Synapse SQL, abra o Azure Data Studio e selecione **New Connection**.

![Aberto Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Escolha **o Microsoft SQL Server** como o tipo de **ligação.**

A ligação requer os seguintes parâmetros:

* **Servidor:** Servidor no formulário `<Azure Synapse workspace name>` -ondemand.sql.azuresynapse.net
* **Base de dados:** Nome da base de dados

> [!NOTE]
> Se você gostaria de usar piscina **SQL sem servidor,** o URL deve parecer:
>
> - `<Azure Synapse workspace name>`ondemand.sql.azuresynapse.net.
>
> Se você gostaria de usar **piscina SQL dedicada,** o URL deve parecer:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Escolha a **autenticação do Windows**, **Diretório Ativo Azure,** ou **SQL Login** como tipo de **autenticação**.

Para utilizar o **SQL Login** como tipo de autenticação, adicione os parâmetros username/password:

* **Utilizador:** Utilizador do servidor no formulário `<User>`
* **Senha:** Palavra-passe associada ao utilizador

Para utilizar o Azure Ative Directory, tem de escolher o tipo de autenticação necessário.

![Autenticação do AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

A imagem que se segue mostra os **dados de ligação** para **autenticação do Windows**:

![Autenticação do Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

A imagem que se segue mostra os **Detalhes de Conexão** utilizando **o SQL Login**:

![Início de sessão do SQL](./media/get-started-azure-data-studio/2-database-details.png)

Após o login bem sucedido, você deve ver um dashboard como este: ![ Dashboard](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Consulta

Uma vez ligado, pode consultar o Synapse SQL utilizando declarações [de Transact-SQL (T-SQL)](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) suportadas contra a instância. Selecione **Nova consulta** a partir da vista do painel de instrumentos para começar.

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

Visite [o Azure Data Studio para conectar e consultar dados utilizando um pool DE SQL dedicado em Azure Synapse Analytics,](/sql/azure-data-studio/quickstart-sql-dw)para obter mais informações.
