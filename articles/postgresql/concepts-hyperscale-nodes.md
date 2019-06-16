---
title: Nós na base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)
description: Os dois tipos de nós num grupo de servidores.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077279"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Nós na base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)

A Hiperescala (Citus) (pré-visualização) que aloja o tipo de permite que a base de dados do Azure para servidores PostgreSQL (denominados nós) para coordenar entre si numa arquitetura de "nada partilhado". Os nós num grupo de servidor coletivamente armazenar mais dados e utilizam mais núcleos de CPU do que seria possível num único servidor. A arquitetura também permite que a base de dados Dimensionar ao adicionar mais nós para o grupo de servidor.

## <a name="coordinator-and-workers"></a>Coordenador e funções de trabalho

Todos os grupos de servidor tem um nó coordenador e várias funções de trabalho. Aplicações enviam suas consultas para o nó coordenador, que retransmite-lo para os operadores de relevantes e acumula seus resultados. Aplicações não são possível ligar diretamente às funções de trabalho.

Para cada consulta, o coordenador ou encaminha o mesmo para um nó de trabalho única ou processa-lo em vários dependendo se os dados necessários residem num único nó ou múltiplos. O coordenador de decide o que fazer por tabelas de metadados de consultoria. Estas tabelas controlam os nomes DNS e estado de funcionamento de nós de trabalho e a distribuição dos dados em todos os nós.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como armazenam nós [em dados distribuídos](concepts-hyperscale-distributed-data.md)
