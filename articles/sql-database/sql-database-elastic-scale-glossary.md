---
title: Glossário de ferramentas de banco de dados elástico
description: Explicação dos termos usados para ferramentas de banco de dados elástico
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: ab972db78cd213497fb96486b3e16b01f2c4c6eb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823620"
---
# <a name="elastic-database-tools-glossary"></a>Glossário de ferramentas de banco de dados elástico

Os termos a seguir são definidos para as [ferramentas de banco de dados elástico](sql-database-elastic-scale-introduction.md), um recurso do banco de dados SQL do Azure. As ferramentas são usadas para gerenciar [mapas de fragmentos](sql-database-elastic-scale-shard-map-management.md)e incluem a [biblioteca de cliente](sql-database-elastic-database-client-library.md), a [ferramenta de divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md), os [pools elásticos](sql-database-elastic-pool.md)e as [consultas](sql-database-elastic-query-overview.md). 

Esses termos são usados na [adição de um fragmento usando ferramentas de banco de dados elástico](sql-database-elastic-scale-add-a-shard.md) e [usando a classe RecoveryManager para corrigir problemas de mapa de fragmentos](sql-database-elastic-database-recovery-manager.md).

![Termos de escala elástica][1]

**Banco de dados**: um banco de dados SQL do Azure. 

**Roteamento Dependente de dados**: a funcionalidade que permite que um aplicativo se conecte a um fragmento, considerando uma chave de fragmentação específica. Consulte [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Compare com **[consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md)** .

**Mapa de fragmentos global**: o mapa entre as chaves de fragmentação e seus respectivos fragmentos em um **conjunto de fragmentos**. O mapa de fragmentos global é armazenado no **Gerenciador de mapa de fragmentos**. Comparar com o **mapa de fragmentos local**.

**Listar mapa de fragmentos**: um mapa de fragmentos no qual as chaves de fragmentação são mapeadas individualmente. Comparar com o **mapa de fragmentos de intervalo**.   

**Mapa de fragmentos local**: armazenado em um fragmento, o mapa de fragmentos local contém mapeamentos para o shardlets que residem no fragmento.

**Consulta de vários fragmentos**: a capacidade de emitir uma consulta em relação a vários fragmentos; os conjuntos de resultados são retornados usando a semântica UNION ALL (também conhecida como "consulta de Fan-out"). Comparar com o **Roteamento Dependente de dados**.

**Multilocatário** e **locatário único**: mostra um banco de dados de locatário único e um banco de dados de vários locatários:

![Bancos de dados únicos e multilocatários](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Aqui está uma representação de bancos de dados de vários locatários **fragmentados** e únicos. 

![Bancos de dados únicos e multilocatários](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapa de fragmentos de intervalo**: um mapa de fragmentos no qual a estratégia de distribuição de fragmentos é baseada em vários intervalos de valores contíguos. 

**Tabelas de referência**: tabelas que não são fragmentadas, mas são replicadas entre fragmentos. Por exemplo, CEPs podem ser armazenados em uma tabela de referência. 

**Fragmento**: um banco de dados SQL do Azure que armazena dados de um conjunto de dado fragmentado. 

**Elasticidade de fragmento**: a capacidade de executar o **dimensionamento horizontal** e o **dimensionamento vertical**.

**Tabelas fragmentadas**: tabelas que são fragmentadas, ou seja, cujos dados são distribuídos entre fragmentos com base em seus valores de chave de fragmentação. 

**Chave de fragmentação**: um valor de coluna que determina como os dados são distribuídos entre fragmentos. O tipo de valor pode ser um dos seguintes: **int**, **bigint**, **varbinary**ou **uniqueidentifier**. 

**Conjunto de fragmentos**: a coleção de fragmentos que são atribuídos ao mesmo mapa de fragmentos no Gerenciador de mapa de fragmentos.  

**Shardlet**: todos os dados associados a um único valor de uma chave de fragmentação em um fragmento. Um shardlet é a menor unidade de movimentação de dados possível ao redistribuir tabelas fragmentadas. 

**Mapa de fragmentos**: o conjunto de mapeamentos entre chaves de fragmentação e seus respectivos fragmentos.

**Gerenciador de mapa de fragmentos**: um objeto de gerenciamento e um repositório de dados que contém o (s) mapa (es) de fragmentos, locais de fragmento e mapeamentos para um ou mais conjuntos de fragmentos.

![Mapeamentos][2]

## <a name="verbs"></a>Verbos
**Dimensionamento horizontal**: o ato de expandir (ou em) uma coleção de fragmentos adicionando ou removendo fragmentos em um mapa de fragmentos, como mostrado abaixo.

![Dimensionamento horizontal e vertical][3]

**Mesclagem**: o ato de mover shardlets de dois fragmentos para um fragmento e atualizar o mapa de fragmentos de acordo.

**Shardlet mover**: o ato de mover um único Shardlet para um fragmento diferente. 

**Fragmento**: o ato de particionar horizontalmente dados estruturados de forma idêntica em vários bancos de dado com base em uma chave de fragmentação.

**Divisão**: o ato de mover várias shardlets de um fragmento para outro (normalmente novo) fragmento. Uma chave de fragmentação é fornecida pelo usuário como o ponto de divisão.

**Dimensionamento vertical**: o ato de expandir (ou reduzir) o tamanho de computação de um fragmento individual. Por exemplo, alterar um fragmento de Standard para Premium (o que resulta em mais recursos de computação). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

