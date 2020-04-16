---
title: Ligue-se ao Power BI Professional
description: Neste tutorial, vamos passar por etapas de como ligar o ambiente de trabalho power BI à SQL on-demand (pré-visualização).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0ce8f3a447f1896ae6d96d343782f8cdb44d4c6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422565"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Ligue-se ao Synapse SQL com power bi professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

Neste tutorial, vamos passar por etapas de como ligar o ambiente de trabalho power BI à SQL on-demand (pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos

Ferramenta para emitir consultas:

- Cliente SQL à sua escolha:

  - Azure Data Studio
  - SQL Server Management Studio

- Ambiente de trabalho power BI instalado

Parâmetros:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço final do serviço sql on-demand    | Será usado como nome do servidor                                   |
| Região de ponto final do serviço sql a pedido     | Será usado para determinar que armazenamento usaremos em amostras |
| Nome de utilizador e senha para acesso ao ponto final | Será usado para aceder ao ponto final                               |
| Base de Dados que utilizará para criar pontos de vista     | Esta base de dados será usada como ponto de partida em amostras       |

## <a name="first-time-setup"></a>Configuração pela primeira vez

Há dois passos antes da utilização de amostras:

1. Crie base de dados para os seus pontos de vista
2. Criar credenciais a utilizar pela SQL a pedido para aceder a ficheiros no armazenamento

### <a name="create-database"></a>Criar base de dados

Uma vez que utilizará o ambiente de demonstração, deverá criar a sua própria base de dados para fins de demonstração. A base de dados é necessária para criar pontos de vista. Utilizará esta base de dados em algumas das consultas de amostra nesta documentação.

> [!NOTE]
> Note que as bases de dados são utilizadas apenas para visualizar metadados, não para dados reais.
>
> Escreva o nome da base de dados que usa, e vai precisar dele mais tarde.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Criar credenciais

Temos de criar credenciais antes que possas fazer consultas. Esta credencial será utilizada pelo serviço on-demand SQL para aceder a ficheiros armazenados.

> [!NOTE]
> Note que precisa de criar credenciais para acesso à conta de armazenamento. Embora a SQL on-demand possa aceder a armazenamentos de diferentes regiões, ter armazenamento e espaço de trabalho Azure Synapse na mesma região proporcionará uma melhor experiência de desempenho.

**Código de snippet sobre como criar credenciais para os recipientes**de dados do Census, executar:

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

## <a name="creating-power-bi-desktop-report"></a>Criação de relatório de desktop Power BI

Abra a aplicação de ambiente de trabalho do Open Power BI e selecione a opção "Obter dados".
![Abra a aplicação de ambiente de trabalho Do Power BI e selecione obter dados.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Passo 1 - Selecione fonte de dados

Selecione "Azure" no menu e, em seguida, "Base de Dados Azure SQL".
![Selecione fonte de dados.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Passo 2 - Selecionar base de dados

Escreva URL para a base de dados e nome da base de dados onde a vista está a residir.
![Selecione base de dados no ponto final.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Passos seguintes

Avançar para [ficheiros](get-started-azure-data-studio.md) de armazenamento de Consulta para aprender a ligar-se à SQL on-demand usando o Azure Data Studio.
 