---
title: Usando os esquemas definidos pelo utilizador no armazém de dados SQL | Documentos da Microsoft
description: Dicas para usar esquemas definidos pelo utilizador do T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 7e22dc69a9da1d9b5a8c0ff13f73769b1ed4514a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861707"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Usando os esquemas definidos pelo utilizador no armazém de dados SQL
Dicas para usar esquemas definidos pelo utilizador do T-SQL no Azure SQL Data Warehouse para o desenvolvimento de soluções.

## <a name="schemas-for-application-boundaries"></a>Esquemas de limites de aplicação

Armazéns de dados tradicionais utilizam frequentemente bases de dados diferentes para criar limites de aplicação com base na carga de trabalho, o domínio ou segurança. Por exemplo, um armazém de dados do SQL Server tradicional pode incluir uma base de dados de teste, uma base de dados do armazém de dados e algumas bases de dados do data mart. Nesta topologia, cada base de dados funciona como uma carga de trabalho e o limite de segurança na arquitetura.

Por outro lado, o SQL Data Warehouse é executada a carga de trabalho de armazém de dados inteiro dentro de uma base de dados. Cruzada da base de dados não são permitidas associações. Por conseguinte, o SQL Data Warehouse espera todas as tabelas utilizadas pelo armazém de sejam armazenados dentro de uma base de dados.

> [!NOTE]
> O SQL Data Warehouse não suporta consultas de entre várias bases de dados de qualquer tipo. Conseqüentemente, implementações de armazém de dados que utilizam este padrão tem de ser revisto.
> 
> 

## <a name="recommendations"></a>Recomendações
Estas são recomendações para a consolidação de cargas de trabalho, segurança, domínio e limites funcionais por usando esquemas definidos pelo utilizador

1. Utilizar uma base de dados do armazém de dados SQL para executar a sua carga de trabalho do armazém de dados inteiro
2. Consolidar o seu ambiente de armazém de dados existente para utilizar uma base de dados do SQL Data Warehouse
3. Tire partido **esquemas definidos pelo utilizador** para fornecer o limite de anteriormente implementado com bases de dados.

Se esquemas definidos pelo utilizador não foram utilizadas anteriormente, em seguida, terá uma ficha limpa. Basta use o nome de base de dados antigo como base para seus esquemas definidos pelo utilizador na base de dados SQL Data Warehouse.

Se já tem sido utilizados a esquemas, em seguida, tem algumas opções:

1. Remova os nomes de esquema herdados e comece do zero
2. Manter os nomes de esquema herdados através da pré-pendente o nome do esquema de legado para o nome da tabela
3. Manter os nomes de esquema herdados através da implementação de modos de exibição sobre a tabela num esquema extra para voltar a criar a estrutura de esquema antigo.

> [!NOTE]
> Na inspeção primeiro a opção 3 pode parecer que a opção mais atraente. No entanto, o detalhe é o mais detalhadamente. Modos de exibição são de leitura apenas no SQL Data Warehouse. Qualquer modificação de dados ou tabela têm de ser executadas com base na tabela base. Opção 3 também apresenta uma camada de vistas no seu sistema. Pode querer dar isso um pouco de atenção adicional se estiver a utilizar as vistas em sua arquitetura já.
> 
> 

### <a name="examples"></a>Exemplos:
Implementar os esquemas definidos pelo utilizador com base nos nomes de base de dados

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

Manter nomes de esquema herdados através da pré-pendente-los para o nome da tabela. Use esquemas para o limite de carga de trabalho.

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

Manter os nomes de esquema herdados através de vistas

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
> Qualquer alteração na estratégia de esquema precisa de uma revisão do modelo de segurança para a base de dados. Em muitos casos poderá conseguir simplificar o modelo de segurança através da atribuição de permissões no nível de esquema. Se são necessárias permissões mais granulares, em seguida, pode utilizar funções de base de dados.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Para obter mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento](sql-data-warehouse-overview-develop.md).

