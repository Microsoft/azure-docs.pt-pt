---
title: Glossário de ferramentas de base de dados elástico | Documentos da Microsoft
description: Explicação dos termos utilizados para ferramentas de bases de dados elásticas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585630"
---
# <a name="elastic-database-tools-glossary"></a>Glossário de ferramentas de base de dados elástico

Os termos seguintes são definidos para o [ferramentas de bases de dados elásticas](sql-database-elastic-scale-introduction.md), uma funcionalidade de base de dados do Azure SQL. As ferramentas são utilizadas para gerir [mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md)e incluem o [biblioteca de cliente](sql-database-elastic-database-client-library.md), o [ferramenta de dividir / unir](sql-database-elastic-scale-overview-split-and-merge.md), [conjuntos elásticos](sql-database-elastic-pool.md)e o [consultas](sql-database-elastic-query-overview.md). 

Esses termos são utilizados no [adicionando uma partição horizontal com as ferramentas de bases de dados elásticas](sql-database-elastic-scale-add-a-shard.md) e [utilizar a classe RecoveryManager para corrigir problemas de mapa de partições horizontais](sql-database-elastic-database-recovery-manager.md).

![Termos de escala elásticos][1]

**Base de dados**: Uma base de dados SQL do Azure. 

**Encaminhamento dependente de dados**: A funcionalidade que permite que uma aplicação ligar a uma partição horizontal dada uma chave de fragmentação específico. Ver [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Compare com  **[consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md)** .

**Mapa de partições horizontais global**: O mapa entre chaves de fragmentação e seus respectivos shards dentro de um **conjunto de partições horizontais**. O mapa de partições horizontais global é armazenado na **Gestor de mapas de partições horizontais**. Compare com **mapa de partições horizontais local**.

**Mapa de partições horizontais de lista**: Um mapa de partições horizontais em que a fragmentação chaves são mapeadas individualmente. Compare com **mapa de partições horizontais de intervalo**.   

**Mapa de partições horizontais local**: Armazenados numa partição horizontal, o mapa de partições horizontais local contém mapeamentos para os shardlets que residem na partição horizontal.

**Consulta de vários fragmentos**: A capacidade de emitir uma consulta em relação a várias partições horizontais; conjuntos de resultados são devolvidos com semântica UNION ALL (também conhecido como "consulta de fan-out"). Compare com **encaminhamento dependente de dados**.

**Multi-inquilino** e **inquilino único**: Mostra uma base de dados de inquilino único e uma base de dados do multi-inquilino:

![Bases de dados únicos e multi-inquilinos](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Aqui está uma representação de **em partição horizontal** bases de dados únicos e multi-inquilinos. 

![Bases de dados únicos e multi-inquilinos](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapa de partições horizontais de intervalo**: Um mapa de partições horizontais em que a estratégia de distribuição de partições horizontais se baseia em vários intervalos de valores contíguos. 

**Tabelas de referência**: Tabelas que não estão em partição horizontal, mas são replicadas entre partições horizontais. Por exemplo, códigos postais podem ser armazenados numa tabela de referência. 

**Partição horizontal**: Base de dados SQL do Azure que armazena dados a partir de um conjunto de dados em partição horizontal. 

**Elasticidade de fragmentos**: A capacidade de executar ambos **dimensionamento horizontal** e **dimensionamento vertical**.

**Tabelas em partição horizontal**: Tabelas que estão em partição horizontal, ou seja, cujos dados são distribuídos por partições horizontais com base nos seus valores de chave de fragmentação. 

**Chave de fragmentação**: Um valor de coluna que determina a forma como os dados são distribuídos por partições horizontais. O tipo de valor pode ser um dos seguintes: **int**, **bigint**, **varbinary**, ou **uniqueidentifier**. 

**Conjunto de partições horizontais**: A coleção de partições horizontais que são atribuídos ao mesmo mapa de partições horizontais no Gestor de mapas de partições horizontais.  

**Shardlet**: Todos os dados associados um único valor de uma chave de fragmentação numa partição horizontal. Um shardlet é a menor unidade de movimento de dados possível, ao se redistribuir tabelas em partição horizontal. 

**Mapa de partições horizontais**: O conjunto de mapeamentos entre chaves de fragmentação e seus respectivos shards.

**Gestor de mapas de partições horizontais**: Um arquivo de dados e objetos da gestão que contém a partição horizontal map(s), localizações de partições horizontais e mapeamentos para um ou mais conjuntos de partição horizontal.

![Mapeamentos][2]

## <a name="verbs"></a>Verbos
**Dimensionamento horizontal**: O ato de aumentar horizontalmente (ou em) uma coleção de partições horizontais adicionando ou removendo partições horizontais para um mapa de partições horizontais, conforme mostrado abaixo.

![Dimensionamento horizontal e vertical][3]

**Intercalar**: O ato de mover os shardlets de partições horizontais de duas numa partição e a atualizar o mapa de partições horizontais em conformidade.

**Mudança de Shardlet**: O ato de mover um shardlet único noutra partição. 

**Partição horizontal**: O ato de criação de partições horizontais homônimo dados estruturados em várias bases de dados com base numa chave de fragmentação.

**Divisão**: O ato de mover várias shardlets de uma partição para outra (normalmente nova) partição horizontal. Uma chave de fragmentação é fornecida pelo utilizador como o ponto de divisão.

**Dimensionamento vertical**: O ato de aumentando (ou reduzindo), o tamanho de computação de uma partição horizontal individual. Por exemplo, alterando uma partição horizontal do Standard para Premium (o que resulta em mais recursos de computação). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

