---
title: Nós – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Saiba mais sobre os dois tipos de nós, coordenador e trabalhadores, em um grupo de servidores no banco de dados do Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974007"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nós no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

O tipo de Hospedagem de hiperescala (Citus) permite que os servidores do banco de dados do Azure para PostgreSQL (chamados de nós) se coordenem entre si em uma arquitetura "nada compartilhado". Os nós em um grupo de servidores contêm, coletivamente, mais dados e usam mais núcleos de CPU do que seria possível em um único servidor. A arquitetura também permite que o banco de dados seja dimensionado adicionando mais nós ao grupo de servidores.

## <a name="coordinator-and-workers"></a>Coordenador e trabalhadores

Cada grupo de servidores tem um nó de coordenador e vários trabalhadores. Os aplicativos enviam suas consultas para o nó de coordenador, que os retransmite para os trabalhadores relevantes e acumula seus resultados. Os aplicativos não podem se conectar diretamente aos trabalhadores.

Para cada consulta, o coordenador roteia-o para um único nó de trabalho ou paralelize-o entre vários dependendo de se os dados necessários residem em um único nó ou em vários. O coordenador decide o que fazer por meio das tabelas de metadados de consultoria. Essas tabelas acompanham os nomes DNS e a integridade dos nós de trabalho e a distribuição de dados entre nós.

## <a name="next-steps"></a>Passos seguintes
- Saiba como os nós armazenam [dados distribuídos](concepts-hyperscale-distributed-data.md)
