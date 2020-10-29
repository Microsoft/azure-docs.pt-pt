---
title: Conecte-se ao SQL synapse com power BI Professional
description: Neste tutorial, vamos passar por etapas como ligar o desktop Power BI ao SQL on demand (pré-visualização).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: d88406646099a136d196a104f9cf4352a367f6d2
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899120"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Conecte-se ao SQL synapse com power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

Neste tutorial, passaremos os passos para ligar o desktop Power BI ao SQL on demand (pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos

Precisa das seguintes ferramentas para emitir consultas:

- Cliente SQL à sua escolha:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI desktop instalado

Parâmetros:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço de ponto final de serviço a pedido da SQL    | Será usado como nome de servidor                                   |
| SqL região de ponto final de serviço a pedido     | Será usado para determinar que armazenamento vamos usar em amostras |
| Nome de utilizador e senha para acesso ao ponto final | Será usado para aceder ao ponto final                               |
| Base de dados que utilizará para criar vistas     | Esta base de dados será usada como ponto de partida em amostras       |

## <a name="first-time-setup"></a>Configuração pela primeira vez

Existem dois passos antes da utilização de amostras:

1. Crie base de dados para os seus pontos de vista
2. Criar credenciais a serem usadas pela SQL a pedido de acesso a ficheiros no armazenamento

### <a name="create-database"></a>Criar base de dados

Para este artigo de início, deve criar a sua própria base de dados para utilizar como uma demonstração. É necessária uma base de dados para a criação de pontos de vista. Você usará esta base de dados em algumas das consultas de amostra dentro desta documentação.

> [!NOTE]
> As bases de dados são utilizadas apenas para visualização de metadados, não para dados reais.
>
> Escreva o nome da base de dados que está a usar e vai precisar mais tarde.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Criar credenciais

Precisamos de criar credenciais antes que possas fazer consultas. Esta credencial será utilizada pelo serviço a pedido da SQL para aceder a ficheiros armazenados.

> [!NOTE]
> Precisa de criar credenciais para o acesso à conta de armazenamento. Embora o SQL on demand possa aceder ao armazenamento de diferentes regiões, ter armazenamento e espaço de trabalho Azure Synapse na mesma região proporcionará uma melhor experiência de desempenho.

**Código snippet sobre como criar credenciais para recipientes de dados do Censos,** executar:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>Criar um relatório de ambiente de trabalho Power BI

Abra a aplicação de ambiente de trabalho Power BI e selecione a opção **Obter dados.**

![Abrir a aplicação de ambiente de trabalho DO BI e selecionar obter dados.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Passo 1 - Selecione fonte de dados

Selecione **Azure** no menu e, em seguida, **Azure SQL Database** .
![Selecione fonte de dados.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Passo 2 - Selecione base de dados

Escreva o URL para a base de dados e o nome da base de dados onde a vista reside.
![Selecione base de dados no ponto final.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Próximas etapas

Avance para [consultar ficheiros de armazenamento](get-started-azure-data-studio.md) para aprender a ligar-se ao SQL on demand usando o Azure Data Studio.
 
