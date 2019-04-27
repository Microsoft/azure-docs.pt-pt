---
title: Migrar o seu esquema para o SQL Data Warehouse | Documentos da Microsoft
description: Dicas para migrar o seu esquema para o Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
origin.date: 04/17/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 4139ea776f6947eeacf4620c3676606d6535dd2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748157"
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migrar seus esquemas para o SQL Data Warehouse
Orientação para migrar seus esquemas do SQL ao SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Planear a migração de esquema

Quando planear uma migração, consulte a [descrição geral da tabela] [ table overview] para se familiarizar com considerações de design de tabela, como estatísticas, distribuição, criação de partições e indexação.  Ele também apresenta algumas [tabela de funcionalidades não suportadas] [ unsupported table features] e suas soluções.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Use esquemas definidos pelo utilizador para consolidar as bases de dados

A carga de trabalho existente provavelmente tem mais do que uma base de dados. Por exemplo, um armazém de dados do SQL Server pode incluir uma base de dados de teste, uma base de dados do armazém de dados e algumas bases de dados do data mart. Nesta topologia, a cada base de dados é executado como uma carga de trabalho separada, com as políticas de segurança separados.

Por outro lado, o SQL Data Warehouse é executada a carga de trabalho de armazém de dados inteiro dentro de uma base de dados. Cruzada da base de dados não são permitidas associações. Por conseguinte, o SQL Data Warehouse espera todas as tabelas utilizadas pelo armazém de dados sejam armazenados dentro de uma base de dados.

Recomendamos que utilize os esquemas definidos pelo utilizador a consolidação de sua carga de trabalho existente num banco de dados. Para obter exemplos, consulte [esquemas definidos pelo utilizador](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Usar tipos de dados compatível
Modificar seus tipos de dados para ser compatível com o SQL Data Warehouse. Para obter uma lista dos tipos de dados suportados e não suportados, consulte [tipos de dados][data types]. Esse tópico oferece soluções alternativas para os tipos não suportados. Ele também fornece uma consulta para identificar os tipos existentes que não são suportados no SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimizar o tamanho de linha
Para obter melhor desempenho, minimize o comprimento de linha de suas tabelas. Uma vez que o mais curtos comprimentos de linha levam a melhor desempenho, utilize os tipos de dados mais pequenas que funcionam para os seus dados. 

Para a largura da linha de tabela, o PolyBase tem um limite de 1 MB.  Se pretender carregar dados para o SQL Data Warehouse com o PolyBase, Atualize as suas tabelas ter larguras de linha máximo de menos de 1 MB. 

## <a name="specify-the-distribution-option"></a>Especificar a opção de distribuição
O SQL Data Warehouse é um sistema de base de dados distribuída. Cada tabela é distribuída ou replicada em todos os nós de computação. Há uma opção de tabela que permite que especifique como distribuir os dados. As opções são round robin, replicados, ou distribuído de hash. Cada uma tem prós e contras. Se não especificar a opção de distribuição, o SQL Data Warehouse irá utilizar round robin como predefinição.

- Round robin é a predefinição. É o mais simples de usar e carrega os dados o mais rápidos possível, mas as associações exigirão o movimento de dados que reduz a velocidade de desempenho de consulta.
- Replicados armazena uma cópia da tabela em cada nó de computação. Tabelas replicadas são com bom desempenho, porque não necessitam de movimento de dados para associações e agregações. Eles necessitam de armazenamento extra e, portanto, funcionam melhor para tabelas de menores.
- Distribuídas com hash distribui as linhas em todos os nós por meio de uma função de hash. Tabelas distribuídas com hash são o coração do SQL Data Warehouse, uma vez que elas foram projetadas para fornecer desempenho de consulta elevado em tabelas grandes. Esta opção exige algum planejamento para selecionar a coluna de melhor em que pretende distribuir os dados. No entanto, se não escolher a melhor coluna na primeira vez, pode facilmente novamente distribuir os dados numa coluna diferente. 

Para escolher a melhor opção de distribuição para cada tabela, consulte [tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

## <a name="next-steps"></a>Passos Seguintes
Depois de ter migrado com êxito o seu esquema de banco de dados para o SQL Data Warehouse, avance para um dos seguintes artigos:

* [Migre os seus dados][Migrate your data]
* [Migrar o código][Migrate your code]

Para obter mais informações sobre as melhores práticas do SQL Data Warehouse, consulte a [melhores práticas] [ best practices] artigo.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->

<!--Other Web references-->

<!--Update_Description: update meta properties, add new content about Migrate schemas to SQL Data Warehouse -->