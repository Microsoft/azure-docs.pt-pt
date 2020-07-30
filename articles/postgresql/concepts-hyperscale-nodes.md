---
title: Nós – Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Conheça os dois tipos de nós, coordenador e trabalhadores, num grupo de servidores na Base de Dados Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382802"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nós na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

O tipo de hospedagem Hyperscale (Citus) permite que a Base de Dados Azure para servidores PostgreSQL (chamados nós) coordene uns com os outros numa arquitetura "nada partilhada". Os nós de um grupo de servidores possuem coletivamente mais dados e utilizam mais núcleos de CPU do que seria possível num único servidor. A arquitetura também permite que a base de dados se dimensione adicionando mais nós ao grupo de servidores.

## <a name="coordinator-and-workers"></a>Coordenador e trabalhadores

Cada grupo de servidores tem um nó coordenador e vários trabalhadores. As candidaturas enviam as suas consultas ao nó coordenador, que o transmite aos trabalhadores relevantes e acumula os seus resultados. As aplicações não podem ligar-se diretamente aos trabalhadores.

A Hiperescala (Citus) permite ao administrador da base de dados *distribuir* tabelas, armazenando diferentes linhas em diferentes nós de trabalhadores. As tabelas distribuídas são a chave para o desempenho da Hyperscale. Não distribuir mesas deixa-as inteiramente no nó coordenador e não pode tirar partido do paralelismo entre máquinas.

Para cada consulta em tabelas distribuídas, o coordenador ou o encaminha para um único nó de trabalhador, ou o paralelo em vários dependendo se os dados necessários vivem num único nó ou múltiplo. O coordenador decide o que fazer consultando tabelas de metadados. Estas tabelas acompanham os nomes do DNS e a saúde dos nós dos trabalhadores, e a distribuição de dados através dos nós.

## <a name="next-steps"></a>Próximos passos
- Saiba mais [sobre tabelas distribuídas](concepts-hyperscale-distributed-data.md)
