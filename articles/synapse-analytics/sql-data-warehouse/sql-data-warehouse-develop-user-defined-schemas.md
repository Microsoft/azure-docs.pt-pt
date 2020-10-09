---
title: Utilização de esquemas definidos pelo utilizador
description: Dicas para utilizar esquemas definidos pelo utilizador T-SQL para desenvolver soluções na piscina Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fc5e035215e7cabd02861c6ee2498cadd1ef0534
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213368"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Esquemas definidos pelo utilizador na piscina Sinaapse SQL
Este artigo centra-se em fornecer várias dicas para a utilização de esquemas definidos pelo utilizador T-SQL para desenvolver soluções na piscina Synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Esquemas para limites de aplicação

Os armazéns de dados tradicionais usam frequentemente bases de dados separadas para criar limites de aplicação baseados na carga de trabalho, domínio ou segurança. 

Como exemplo, um armazém de dados tradicional do SQL Server pode incluir uma base de dados de encenação, uma base de dados de data warehouse e algumas bases de dados do mercado de dados. Nesta topologia, cada base de dados funciona como uma linha de trabalho e limite de segurança na arquitetura.

Em contraste, o pool SQL executa toda a carga de trabalho do armazém de dados dentro de uma base de dados. Não são permitidas juntas de base de dados cruzadas. A piscina SQL espera que todas as mesas usadas pelo armazém sejam armazenadas dentro de uma base de dados.

> [!NOTE]
> O pool SQL não suporta consultas de base de dados cruzadas de qualquer tipo. Consequentemente, as implementações de armazéns de dados que alavancam este padrão terão de ser revistas.
> 
> 

## <a name="recommendations"></a>Recomendações
Seguem-se recomendações para a consolidação de cargas de trabalho, segurança, domínio e limites funcionais utilizando esquemas definidos pelo utilizador:

- Use uma base de dados de piscinas SQL para executar toda a sua carga de trabalho do armazém de dados.
- Consolidar o ambiente existente de armazém de dados para utilizar uma base de dados de piscinas SQL.
- Alavancar **os esquemas definidos pelo utilizador** para fornecer o limite previamente implementado através de bases de dados.

Se os esquemas definidos pelo utilizador não tiverem sido utilizados anteriormente, então tem uma ficha limpa. Utilize o nome da base de dados antiga como base de base para os seus esquemas definidos pelo utilizador na base de dados da piscina SQL.

Se os esquemas já foram usados, então tem algumas opções:

- Remova os nomes do esquema antigo e comece de novo.
- Mantenha os nomes do esquema antigos antes de aguardar o nome do esquema do legado para o nome da mesa.
- Mantenha os nomes do esquema legado implementando pontos de vista sobre a mesa num esquema extra para recriar a antiga estrutura de esquemas.

> [!NOTE]
> Na primeira opção de inspeção 3 pode parecer a opção mais apelativa. No entanto, o diabo está nos detalhes. As vistas são lidas apenas na piscina SQL. Qualquer modificação de dados ou tabela teria de ser efetuada contra a tabela base. A opção 3 também introduz uma camada de pontos de vista no seu sistema. Você pode querer dar um pensamento adicional se você já está usando vistas na sua arquitetura.
> 
> 

### <a name="examples"></a>Exemplos:
Implementar esquemas definidos pelo utilizador com base em nomes de bases de dados:

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

Mantenha os nomes do esquema antigos, pré-pendentes, para o nome da mesa. Utilize esquemas para o limite da carga de trabalho:

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

Mantenha os nomes de esquemas antigos usando vistas:

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
> Qualquer alteração na estratégia de esquema precisa de uma revisão do modelo de segurança para a base de dados. Em muitos casos, poderá simplificar o modelo de segurança atribuindo permissões ao nível do esquema. Se forem necessárias mais permissões granulares, pode utilizar funções de base de dados.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para obter mais dicas de desenvolvimento, consulte [a visão geral do desenvolvimento.](sql-data-warehouse-overview-develop.md)

