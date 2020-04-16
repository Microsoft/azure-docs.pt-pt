---
title: Schemas definidos pelo utilizador dentro do Synapse SQL
description: Nas secções abaixo, encontrará várias dicas para utilizar schemas definidos pelo utilizador T-SQL para desenvolver soluções com a capacidade SQL Synapse da Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428709"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Schemas definidos pelo utilizador dentro do Synapse SQL

Nas secções abaixo, encontrará várias dicas para utilizar schemas definidos pelo utilizador T-SQL para desenvolver soluções dentro do SYnapse SQL.

## <a name="schemas-for-application-boundaries"></a>Esquemios para limites de aplicação

A arquitetura de análise tradicional usa frequentemente bases de dados separadas para criar limites de aplicação baseados na carga de trabalho, domínio ou segurança. Por exemplo, uma infraestrutura de análise tradicional do SQL Server pode incluir uma base de dados de encenação, uma base de dados de análise e bases de dados de data mart. Nesta topologia, cada base de dados funciona como uma carga de trabalho e limite de segurança na arquitetura.

Em vez disso, o Synapse SQL executa toda a carga de trabalho de análise dentro de uma base de dados. Não são permitidas juntas de dados cruzadas. A Synapse SQL espera que todas as tabelas utilizadas pelo armazém sejam armazenadas dentro de uma base de dados.

> [!NOTE]
> As piscinas SQL não suportam consultas de base de dados cruzadas de qualquer tipo. Consequentemente, as implementações analíticas que alavancam este padrão terão de ser revistas. SQL on-demand (pré-visualização) suporta consultas de base de dados cruzadas.

## <a name="user-defined-schema-recommendations"></a>Recomendações de esquema definidas pelo utilizador

Incluem-se recomendações para consolidar cargas de trabalho, segurança, domínio e limites funcionais utilizando esquemas definidos pelo utilizador:

- Use uma base de dados para executar toda a sua carga de trabalho de análise.
- Consolidar o seu ambiente de análise existente para utilizar uma base de dados.
- Alavancar **os esquemas definidos pelo utilizador** para fornecer o limite anteriormente implementado utilizando bases de dados.

Se os schemas definidos pelo utilizador não tiverem sido utilizados anteriormente, então tem uma ficha limpa. Utilize o nome da base de dados antiga como base para os seus schemas definidos pelo utilizador na base de dados SYnapse SQL.

Se os schemas já foram usados, então você tem algumas opções:

- Remova os nomes do esquema legado e comece de novo
- Mantenha os nomes do esquema legado por pré-pendente do nome do esquema legado para o nome da mesa
- Mantenha os nomes do esquema legado implementando pontos de vista sobre a mesa num esquema extra, que recria a antiga estrutura do esquema.

> [!NOTE]
> Na primeira inspeção, a opção 3 pode parecer a escolha mais apelativa. As vistas são lidas apenas em Synapse SQL. Quaisquer dados ou modificações de tabelas teriam de ser efetuadas contra a tabela base. A opção 3 também introduz uma camada de vistas no seu sistema. Você pode querer dar a este algum pensamento adicional se você já está usando vistas na sua arquitetura.
> 
> 

### <a name="examples"></a>Exemplos

Implementar schemas definidos pelo utilizador com base em nomes de bases de dados.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha os nomes do esquema legado, aguardando-os ao nome da mesa. Use esquemia para o limite da carga de trabalho.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha os nomes do esquema antigo usando vistas.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> Qualquer alteração na estratégia de esquema requer uma revisão do modelo de segurança para a base de dados. Em muitos casos, você pode ser capaz de simplificar o modelo de segurança atribuindo permissões ao nível do esquema.

Se forem necessárias mais permissões granulares, pode utilizar funções de base de dados. Para mais informações sobre as funções de base de dados, consulte as funções de base de [dados e artigo dos utilizadores.](../../analysis-services/analysis-services-database-users.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a visão geral do [desenvolvimento da Synapse SQL](develop-overview.md).
