---
title: Tamanho inicial do grupo do servidor - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Escolha o tamanho inicial certo para o seu caso de utilização
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026408"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Escolha o tamanho inicial para o grupo de servidor Hyperscale (Citus)

O tamanho de um grupo de servidores, tanto o seu número de nós como a sua capacidade de hardware, é [fácil de alterar).](howto-hyperscale-scale-grow.md) No entanto, ainda precisa de escolher um tamanho inicial para um novo grupo de servidores. Aqui estão algumas dicas para uma escolha razoável.

## <a name="multi-tenant-saas-use-case"></a>Caso de uso de SaaS multi-inquilino

Ao migrar para Hyperscale (Citus) de uma instância de base de dados postgresQL existente, escolha um cluster onde o número de vCores e RAM do trabalhador no total é igual ao da instância original. Nestes cenários, temos visto melhorias no desempenho de 2-3x porque o fragmento melhora a utilização dos recursos, permitindo índices menores, etc.

A contagem vCore é na verdade a única decisão. A atribuição de RAM é atualmente determinada com base na contagem vCore, conforme descrito na página de opções de [configuração Hyperscale (Citus).](concepts-hyperscale-configuration-options.md)
O nó coordenador não requer tanto RAM como os trabalhadores, mas não há como escolher RAM e vCores de forma independente.

## <a name="real-time-analytics-use-case"></a>Caso de utilização analítico em tempo real

Total de vCores: quando os dados de trabalho se enquadram na RAM, pode esperar uma melhoria linear do desempenho em Hyperscale (Citus) proporcional ao número de núcleos de trabalhadores. Para determinar o número certo de vCores para as suas necessidades, considere a latência atual para consultas na sua base de dados de nó único e a latência necessária em Hyperscale (Citus). Divida a latência atual pela latência desejada e arredonde o resultado.

RAM de trabalho: o melhor caso seria proporcionar memória suficiente para que a maioria do conjunto de trabalho se encaixe na memória. O tipo de consultas que a aplicação utiliza afeta os requisitos de memória. Pode executar EXPLAIN ANALYZE numa consulta para determinar a quantidade de memória necessária. Lembre-se que vCores e RAM são dimensionados em conjunto como descrito no artigo de opções de [configuração Hyperscale (Citus).](concepts-hyperscale-configuration-options.md)

## <a name="next-steps"></a>Passos seguintes

- [Dimensionar um grupo de servidores](howto-hyperscale-scale-grow.md)
- Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo do servidor .
