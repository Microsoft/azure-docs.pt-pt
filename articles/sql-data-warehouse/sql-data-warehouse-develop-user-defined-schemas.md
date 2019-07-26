---
title: Usando esquemas definidos pelo usuário no SQL Data Warehouse | Microsoft Docs
description: Dicas para usar esquemas definidos pelo usuário do T-SQL no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e0ae00e0fca5ed4c6fba04444e5c50424462d297
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479573"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Usando esquemas definidos pelo usuário no SQL Data Warehouse
Dicas para usar esquemas definidos pelo usuário do T-SQL no Azure SQL Data Warehouse para desenvolver soluções.

## <a name="schemas-for-application-boundaries"></a>Esquemas para limites de aplicativo

Os data warehouses tradicionais geralmente usam bancos de dados separados para criar limites de aplicativos com base em carga de trabalho, domínio ou segurança. Por exemplo, um SQL Server tradicional data warehouse pode incluir um banco de dados de preparo, um data warehouse banco de dados e alguns bancos de data mart. Nessa topologia, cada banco de dados Opera como uma carga de trabalho e um limite de segurança na arquitetura.

Por outro lado, SQL Data Warehouse executa toda a carga de trabalho data warehouse dentro de um banco de dados. Junções entre bancos de dados não são permitidas. Portanto SQL Data Warehouse espera que todas as tabelas usadas pelo warehouse sejam armazenadas dentro de um banco de dados.

> [!NOTE]
> SQL Data Warehouse não dá suporte a consultas entre bancos de dados de qualquer tipo. Consequentemente, data warehouse implementações que aproveitam esse padrão precisarão ser revisadas.
> 
> 

## <a name="recommendations"></a>Recomendações
Essas são recomendações para consolidar cargas de trabalho, segurança, domínio e limites funcionais usando esquemas definidos pelo usuário

1. Use um banco de dados SQL Data Warehouse para executar toda a carga de trabalho data warehouse
2. Consolide seu ambiente de data warehouse existente para usar um banco de dados de SQL Data Warehouse
3. Aproveite os **esquemas definidos pelo usuário** para fornecer o limite anteriormente implementado usando bancos de dados.

Se esquemas definidos pelo usuário não tiverem sido usados anteriormente, você terá um Slate limpo. Basta usar o nome do banco de dados antigo como base para os esquemas definidos pelo usuário no banco de dados SQL Data Warehouse.

Se os esquemas já tiverem sido usados, você terá algumas opções:

1. Remova os nomes de esquema herdados e inicie o novo
2. Manter os nomes de esquema herdados predependentes do nome do esquema herdado para o nome da tabela
3. Mantenha os nomes de esquema herdados implementando exibições na tabela em um esquema extra para recriar a estrutura de esquema antiga.

> [!NOTE]
> A opção 3 da primeira inspeção pode parecer com a opção mais atraente. No entanto, o crueldade está em detalhes. As exibições são somente leitura no SQL Data Warehouse. Qualquer modificação de tabela ou dados precisaria ser executada na tabela base. A opção 3 também apresenta uma camada de exibições em seu sistema. Talvez você queira dar algumas idéias adicionais se estiver usando exibições em sua arquitetura já.
> 
> 

### <a name="examples"></a>Exemplos:
Implementar esquemas definidos pelo usuário com base em nomes de banco de dados

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha os nomes de esquema herdados colocando-os em um nome de tabela. Use esquemas para o limite de carga de trabalho.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Reter nomes de esquema herdados usando exibições

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Qualquer alteração na estratégia de esquema precisa de uma revisão do modelo de segurança para o banco de dados. Em muitos casos, você poderá simplificar o modelo de segurança atribuindo permissões no nível de esquema. Se forem necessárias permissões mais granulares, você poderá usar funções de banco de dados.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Para obter mais dicas de desenvolvimento, consulte [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

