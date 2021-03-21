---
title: Glossário de ferramentas elásticas de base de dados
description: Explicação dos termos utilizados para ferramentas de base de dados elásticas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 166e365a4ead8ad6d0f7e543c081161ebff5f027
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330466"
---
# <a name="elastic-database-tools-glossary"></a>Glossário de ferramentas elásticas de base de dados
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

São definidos os seguintes termos para as [ferramentas Elastic Database](elastic-scale-introduction.md). As ferramentas são usadas para gerir [mapas de fragmentos,](elastic-scale-shard-map-management.md)e incluem a biblioteca do [cliente,](elastic-database-client-library.md)a [ferramenta de fusão dividida,](elastic-scale-overview-split-and-merge.md) [piscinas elásticas](elastic-pool-overview.md)e [consultas.](elastic-query-overview.md) 

Estes termos são utilizados na [Adição de um fragmento utilizando ferramentas elásticas de base de dados](elastic-scale-add-a-shard.md) e [utilizando a classe RecoveryManager para corrigir problemas de mapa de fragmentos](elastic-database-recovery-manager.md).

![Termos de Escala Elástica][1]

**Base de dados**: Uma base de dados na Base de Dados Azure SQL. 

**Encaminhamento dependente de dados**: A funcionalidade que permite que uma aplicação se conecte a um fragmento dada uma chave de fragmentos específica. Consulte [o encaminhamento dependente de dados](elastic-scale-data-dependent-routing.md). Compare-se com a **[consulta multi-fragmentos.](elastic-scale-multishard-querying.md)**

**Mapa global do fragmento**: O mapa entre as teclas de caco e os respetivos fragmentos dentro de um **conjunto de fragmentos**. O mapa global de fragmentos está armazenado no **gestor de mapas de fragmentos.** Compare com o **mapa de fragmentos local.**

**Mapa de fragmentos de** lista : Um mapa de fragmentos no qual as teclas de fragmentos são mapeadas individualmente. Compare-se com **o Mapa de Fragmentos de Alcance.**   

**Mapa de fragmentos local**: Armazenado em um fragmento, o mapa de fragmentos local contém mapeamentos para os fragmentos que residem no fragmento.

**Consulta multi-fragmentos**: A capacidade de emitir uma consulta contra vários fragmentos; os conjuntos de resultados são devolvidos usando a semântica UNION ALL (também conhecida como "consulta de fan-out"). Compare com o **encaminhamento dependente de dados**.

**Multi-inquilino** e **inquilino único:** Isto mostra uma base de dados de inquilinos únicos e uma base de dados multi-inquilino:

![Screenshot que mostra uma base de dados de inquilinos únicos e uma base de dados de vários inquilinos.](./media/elastic-scale-glossary/multi-single-simple.png)

Aqui está uma representação de bases de **dados** de solteiros e multi-inquilinos. 

![Bases de dados individuais e multi-arrendatários](./media/elastic-scale-glossary/shards-single-multi.png)

**Mapa de fragmentos de gama**: Um mapa de fragmentos no qual a estratégia de distribuição de fragmentos se baseia em múltiplas gamas de valores contíguos. 

**Tabelas de referência**: Tabelas que não são cacadas mas que são replicadas através de fragmentos. Por exemplo, os códigos postais podem ser armazenados numa tabela de referência. 

**Fragmento**: Uma base de dados na Base de Dados Azure SQL que armazena dados de um conjunto de dados fragmentos. 

**Elasticidade do** fragmento : A capacidade de **efetuar a escala horizontal** e **a escala vertical.**

**Tabelas de cacos**: Tabelas que são ressartadas, ou seja, cujos dados são distribuídos por fragmentos com base nos seus valores-chave de fragmentos. 

**Chave de fragmentos**: Um valor de coluna que determina como os dados são distribuídos através de fragmentos. O tipo de valor pode ser um dos seguintes: **int,** **bigint,** **varbinário,** ou **uniqueidentifier**. 

**Conjunto** de fragmentos : A coleção de fragmentos que são atribuídos ao mesmo mapa de fragmentos no gestor do mapa de fragmentos.  

**Fragmento :** Todos os dados associados a um único valor de uma chave de fragmentos num fragmento. Um fragmento é a menor unidade de movimento de dados possível ao redistribuir tabelas de fragmentos. 

**Mapa de** fragmentos : O conjunto de mapeamentos entre as teclas de caco e os respetivos fragmentos.

**Gestor de mapas de** fragmentos : Um objeto de gestão e uma loja de dados que contém os mapas de fragmentos, localizações de fragmentos e mapeamentos para um ou mais conjuntos de fragmentos.

![O diagrama mostra um gestor de mapas associado a shardmaps_global, shards_global e shard_mappings_global.][2]

## <a name="verbs"></a>Verbos
**Escala horizontal**: O ato de escalonar (ou entrar) uma coleção de fragmentos adicionando ou removendo fragmentos a um mapa de fragmentos, como mostrado abaixo.

![Escala horizontal e vertical][3]

**Fusão**: O ato de mover as rodas de dois fragmentos para um fragmento e atualizar o mapa de fragmentos em conformidade.

**Movimento de caca**: O ato de mover uma única sílva para um fragmento diferente. 

**Fragmento**: O ato de partição horizontal de dados estruturados de forma idêntica em várias bases de dados com base numa chave de fragmentos.

**Divisão**: O ato de mover várias shardlets de um fragmento para outro (tipicamente novo) fragmento. Uma chave de cacos é fornecida pelo utilizador como ponto de divisão.

**Escala vertical**: O ato de escalonamento (ou para baixo) do tamanho do cálculo de um fragmento individual. Por exemplo, mudar um fragmento de Standard para Premium (o que resulta em mais recursos informáticos). 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-glossary/glossary.png
[2]: ./media/elastic-scale-glossary/mappings.png
[3]: ./media/elastic-scale-glossary/h_versus_vert.png

