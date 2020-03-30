---
title: Alta disponibilidade - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Conceitos de alta disponibilidade e recuperação de desastres
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975538"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Alta disponibilidade em Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

A elevada disponibilidade (HA) evita o tempo de inatividade da base de dados mantendo réplicas de cada nó num grupo de servidores. Se um nó descer, a Hyperscale muda as ligações de entrada desde o nó falhado até ao seu standby. Failover acontece em poucos minutos, e os nós promovidos têm sempre dados frescos através da replicação sincronizada de streaming postgresQL.

Para tirar partido do HA no nó coordenador, as aplicações de base de dados precisam de detetar e retentar ligações abandonadas e transações falhadas. O novo coordenador será acessível com a mesma corda de ligação.

A recuperação pode ser dividida em três fases: deteção, falha e recuperação total.  A hiperescala faz verificações de saúde periódicas em cada nó, e depois de quatro verificações falhadas, determina que um nó está em baixo. A hiperescala promove então um estado de prontidão para o nó primário (failover), e prevê um novo standby-to-be.
A replicação em streaming começa, trazendo o novo nó atualizado.  Quando todos os dados foram replicados, o nó alcançou a recuperação total.

### <a name="next-steps"></a>Passos seguintes

- Saiba como permitir uma [alta disponibilidade](howto-hyperscale-high-availability.md) num grupo de servidores de Hiperescala.
