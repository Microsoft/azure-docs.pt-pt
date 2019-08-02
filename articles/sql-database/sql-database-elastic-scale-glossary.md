---
title: Glossário de ferramentas de banco de dados elástico | Microsoft Docs
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
ms.openlocfilehash: 3ed0cc9dce312cb9736b3e32ba46d2cb1cca3ef8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568458"
---
# <a name="elastic-database-tools-glossary"></a>Glossário de ferramentas de banco de dados elástico

Os termos a seguir são definidos para as [ferramentas de banco de dados elástico](sql-database-elastic-scale-introduction.md), um recurso do banco de dados SQL do Azure. As ferramentas são usadas para gerenciar [mapas de fragmentos](sql-database-elastic-scale-shard-map-management.md)e incluem a [biblioteca de cliente](sql-database-elastic-database-client-library.md), a [ferramenta de divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md), os pools [elásticos](sql-database-elastic-pool.md)e as [consultas](sql-database-elastic-query-overview.md). 

Esses termos são usados na [adição de um fragmento usando ferramentas de banco de dados elástico](sql-database-elastic-scale-add-a-shard.md) e [usando a classe RecoveryManager para corrigir problemas de mapa de fragmentos](sql-database-elastic-database-recovery-manager.md).

![Termos de escala elástica][1]

**Banco de dados**: Uma base de dados SQL do Azure. 

**Roteamento Dependente de dados**: A funcionalidade que permite que um aplicativo se conecte a um fragmento de acordo com uma chave de fragmentação específica. Consulte [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Compare com **[consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md)** .

**Mapa de fragmentos global**: O mapa entre as chaves de fragmentação e seus respectivos fragmentos em um **conjunto de fragmentos**. O mapa de fragmentos global é armazenado no **Gerenciador de mapa de fragmentos**. Comparar com o **mapa de fragmentos local**.

**Listar mapa de fragmentos**: Um mapa de fragmentos no qual as chaves de fragmentação são mapeadas individualmente. Comparar com o **mapa de fragmentos de intervalo**.   

**Mapa de fragmentos local**: Armazenados em um fragmento, o mapa de fragmentos local contém mapeamentos para o shardlets que residem no fragmento.

**Consulta de vários fragmentos**: A capacidade de emitir uma consulta em relação a vários fragmentos; os conjuntos de resultados são retornados usando a semântica UNION ALL (também conhecida como "consulta de Fan-out"). Comparar com o **Roteamento Dependente de dados**.

**Multilocatário** e **locatário único**: Isso mostra um banco de dados de locatário único e um banco de dados de vários locatários:

![Bancos de dados únicos e multilocatários](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Aqui está uma representação de bancos de dados de vários locatários fragmentados e únicos. 

![Bancos de dados únicos e multilocatários](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapa de fragmentos de intervalo**: Um mapa de fragmentos no qual a estratégia de distribuição de fragmentos se baseia em vários intervalos de valores contíguos. 

**Tabelas de referência**: Tabelas que não são fragmentadas, mas são replicadas entre fragmentos. Por exemplo, CEPs podem ser armazenados em uma tabela de referência. 

**Fragmento**: Um banco de dados SQL do Azure que armazena dados de um conjunto de dado fragmentado. 

**Elasticidade de fragmento**: A capacidade de executar o **dimensionamento horizontal** e o **dimensionamento vertical**.

**Tabelas fragmentadas**: Tabelas que são fragmentadas, ou seja, cujos dados são distribuídos entre fragmentos com base em seus valores de chave de fragmentação. 

**Chave**de fragmentação: Um valor de coluna que determina como os dados são distribuídos entre fragmentos. O tipo de valor pode ser um dos seguintes: **int**, **bigint**, **varbinary**ou **uniqueidentifier**. 

**Conjunto de fragmentos**: A coleção de fragmentos que são atribuídos ao mesmo mapa de fragmentos no Gerenciador de mapa de fragmentos.  

**Shardlet**: Todos os dados associados a um único valor de uma chave de fragmentação em um fragmento. Um shardlet é a menor unidade de movimentação de dados possível ao redistribuir tabelas fragmentadas. 

**Mapa de fragmentos**: O conjunto de mapeamentos entre chaves de fragmentação e seus respectivos fragmentos.

**Gerenciador de mapa de fragmentos**: Um objeto de gerenciamento e um armazenamento de dados que contém o (s) mapa (es) de fragmentos, locais de fragmentos e mapeamentos para um ou mais conjuntos de fragmentos.

![Mapeamentos][2]

## <a name="verbs"></a>Verbos
**Dimensionamento horizontal**: O ato de escalar horizontalmente (ou em) uma coleção de fragmentos adicionando ou removendo fragmentos em um mapa de fragmentos, conforme mostrado abaixo.

![Dimensionamento horizontal e vertical][3]

**Mesclar**: O ato de mover shardlets de dois fragmentos para um fragmento e atualizar o mapa de fragmentos de acordo.

**Shardlet mover**: O ato de mover um único shardlet para um fragmento diferente. 

**Fragmento**: O ato de particionar horizontalmente dados estruturados de forma idêntica em vários bancos de dados com base em uma chave de fragmentação.

**Divisão**: O ato de mover várias shardlets de um fragmento para outro (normalmente novo) fragmento. Uma chave de fragmentação é fornecida pelo usuário como o ponto de divisão.

**Dimensionamento vertical**: O ato de escalar verticalmente (ou reduzir) o tamanho de computação de um fragmento individual. Por exemplo, alterar um fragmento de Standard para Premium (o que resulta em mais recursos de computação). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

