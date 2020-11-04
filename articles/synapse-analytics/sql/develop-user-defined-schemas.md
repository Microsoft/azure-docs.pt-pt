---
title: Esquemas definidos pelo utilizador dentro do SQL da Sinapse
description: Nas secções abaixo, encontrará várias dicas para utilizar esquemas definidos pelo utilizador T-SQL para desenvolver soluções com a capacidade Sinaapse SQL do Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: a21d2f0debb2ade8e46b0cdb5b3fa5b51f0a180c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323863"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Esquemas definidos pelo utilizador dentro do SQL da Sinapse

Nas secções abaixo, encontrará várias dicas para utilizar esquemas definidos pelo utilizador T-SQL para desenvolver soluções dentro do Sinaapse SQL.

## <a name="schemas-for-application-boundaries"></a>Esquemas para limites de aplicação

A arquitetura de análise tradicional usa frequentemente bases de dados separadas para criar limites de aplicação baseados na carga de trabalho, domínio ou segurança. Por exemplo, uma infraestrutura tradicional de análise de servidor SQL pode incluir uma base de dados de encenação, uma base de dados de análise e bases de dados de data mart. Nesta topologia, cada base de dados funciona como uma linha de trabalho e limite de segurança na arquitetura.

Em vez disso, o Synapse SQL executa toda a carga de trabalho analítica dentro de uma base de dados. Não são permitidas juntas de base de dados cruzadas. A Synapse SQL espera que todas as tabelas utilizadas pelo armazém sejam armazenadas dentro de uma base de dados.

> [!NOTE]
> Piscinas SQL dedicadas não suportam consultas de base de dados cruzadas de qualquer tipo. Consequentemente, as implementações analíticas que alavancam este padrão terão de ser revistas. O pool SQL sem servidor (pré-visualização) suporta consultas de base de dados cruzadas.

## <a name="user-defined-schema-recommendations"></a>Recomendações de esquema definidas pelo utilizador

Incluem-se recomendações para a consolidação de cargas de trabalho, segurança, domínio e limites funcionais utilizando esquemas definidos pelo utilizador:

- Use uma base de dados para executar toda a sua carga de trabalho de análise.
- Consolidar o seu ambiente de análise existente para utilizar uma base de dados.
- Alavancar **os esquemas definidos pelo utilizador** para fornecer o limite previamente implementado através de bases de dados.

Se os esquemas definidos pelo utilizador não tiverem sido usados anteriormente, então tem uma ficha limpa. Utilize o nome da base de dados antiga como base de base para os seus esquemas definidos pelo utilizador na base de dados Synapse SQL.

Se os esquemas já foram usados, então tem algumas opções:

- Remova os nomes do esquema do legado e comece de novo
- Mantenha os nomes do esquema do legado pré-pendente do nome do esquema do legado para o nome da mesa
- Mantenha os nomes do esquema legado implementando vistas sobre a mesa num esquema extra, que recria a antiga estrutura de esquemas.

> [!NOTE]
> Na primeira inspeção, a opção 3 pode parecer a escolha mais apelativa. As vistas são lidas apenas no SYNapse SQL. Qualquer modificação de dados ou tabela teria de ser efetuada contra a tabela base. A opção 3 também introduz uma camada de pontos de vista no seu sistema. Você pode querer dar um pensamento adicional se você já está usando vistas na sua arquitetura.
> 
> 

### <a name="examples"></a>Exemplos

Implementar esquemas definidos pelo utilizador com base em nomes de bases de dados.

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

Mantenha os nomes do esquema antigos, pré-pendentes, para o nome da mesa. Utilize esquemas para o limite da carga de trabalho.

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
> Qualquer alteração na estratégia de esquema requer uma revisão do modelo de segurança para a base de dados. Em muitos casos, poderá simplificar o modelo de segurança atribuindo permissões ao nível do esquema.

Se forem necessárias mais permissões granulares, pode utilizar funções de base de dados. Para obter mais informações sobre as funções de base de dados, consulte as [funções de base de dados Gerir e artigos de utilizadores.](../../analysis-services/analysis-services-database-users.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais dicas de desenvolvimento, consulte a visão geral do [desenvolvimento do Synapse SQL.](develop-overview.md)
