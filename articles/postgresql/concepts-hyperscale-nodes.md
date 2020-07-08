---
title: Nós – Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Conheça os dois tipos de nós, coordenador e trabalhadores, num grupo de servidores na Base de Dados Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74974007"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nós na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

O tipo de hospedagem Hyperscale (Citus) permite que a Base de Dados Azure para servidores PostgreSQL (chamados nós) coordene uns com os outros numa arquitetura "nada partilhada". Os nós de um grupo de servidores possuem coletivamente mais dados e utilizam mais núcleos de CPU do que seria possível num único servidor. A arquitetura também permite que a base de dados se dimensione adicionando mais nós ao grupo de servidores.

## <a name="coordinator-and-workers"></a>Coordenador e trabalhadores

Cada grupo de servidores tem um nó coordenador e vários trabalhadores. As candidaturas enviam as suas consultas ao nó coordenador, que o transmite aos trabalhadores relevantes e acumula os seus resultados. As aplicações não podem ligar-se diretamente aos trabalhadores.

Para cada consulta, o coordenador ou o encaminha para um único nó de trabalhador, ou o paralelo em vários dependendo se os dados necessários vivem num único nó ou múltiplo. O coordenador decide o que fazer consultando tabelas de metadados. Estas tabelas acompanham os nomes do DNS e a saúde dos nós dos trabalhadores, e a distribuição de dados através dos nós.

## <a name="next-steps"></a>Próximos passos
- Saiba como os nódes armazenam [dados distribuídos](concepts-hyperscale-distributed-data.md)
