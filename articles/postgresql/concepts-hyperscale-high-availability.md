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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74975538"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Alta disponibilidade em Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

A alta disponibilidade (HA) evita o tempo de inatividade da base de dados mantendo réplicas de espera de cada nó num grupo de servidores. Se um nó se desligar, a Hyperscale muda as ligações de entrada do nó falhado para o seu standby. O failover acontece dentro de poucos minutos, e os nós promovidos têm sempre dados frescos através da replicação sincronizada de streaming pós-SQL.

Para tirar partido do HA no nó coordenador, as aplicações de base de dados precisam de detetar e voltar a tentar que as ligações e as transações falhadas sejam retiradas. O coordenador recém-promovido estará acessível com a mesma cadeia de ligação.

A recuperação pode ser dividida em três fases: deteção, falha e recuperação total.  A hiperescala verifica a saúde periódica em cada nó, e depois de quatro verificações falhadas, determina que um nó está em baixo. A hiperescala então promove um estado de espera para o estado do nó primário (failover), e prevê um novo standby-to-be.
A replicação do streaming começa, trazendo o novo nó atualizado.  Quando todos os dados foram replicados, o nó atingiu a recuperação total.

### <a name="next-steps"></a>Próximos passos

- Saiba como [ativar](howto-hyperscale-high-availability.md) a alta disponibilidade num grupo de servidores Hyperscale.
