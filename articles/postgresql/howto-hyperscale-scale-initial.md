---
title: Tamanho inicial do grupo do servidor - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Escolha o tamanho inicial certo para o seu caso de utilização
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012511"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Escolha o tamanho inicial para o grupo de servidor Hyperscale (Citus)

O tamanho de um grupo de servidores, tanto o número de nós como a sua capacidade de hardware, é [fácil de alterar).](howto-hyperscale-scale-grow.md) No entanto, ainda precisa de escolher um tamanho inicial para um novo grupo de servidores. Aqui estão algumas dicas para uma escolha razoável.

## <a name="use-cases"></a>Casos de utilização

A hiperescala (Citus) é frequentemente utilizada nas seguintes formas.

### <a name="multi-tenant-saas"></a>SaaS multi-inquilino

Ao migrar para Hyperscale (Citus) de uma instância de base de dados postgresQL existente, escolha um cluster onde o número de vCores e RAM do trabalhador no total é igual ao da instância original. Nestes cenários, temos visto melhorias no desempenho de 2-3x porque o fragmento melhora a utilização dos recursos, permitindo índices menores, etc.

A contagem vCore é na verdade a única decisão. A atribuição de RAM é atualmente determinada com base na contagem vCore, conforme descrito na página de opções de [configuração Hyperscale (Citus).](concepts-hyperscale-configuration-options.md)
O nó coordenador não requer tanto RAM como os trabalhadores, mas não há como escolher RAM e vCores de forma independente.

### <a name="real-time-analytics"></a>Análise em tempo real

Total de vCores: quando os dados de trabalho se enquadram na RAM, pode esperar uma melhoria linear do desempenho em Hyperscale (Citus) proporcional ao número de núcleos de trabalhadores. Para determinar o número certo de vCores para as suas necessidades, considere a latência atual para consultas na sua base de dados de nó único e a latência necessária em Hyperscale (Citus). Divida a latência atual pela latência desejada e arredonde o resultado.

RAM operária: o melhor caso seria fornecer memória suficiente que a maioria do conjunto de trabalho se encaixa na memória. O tipo de consultas que a aplicação utiliza afeta os requisitos de memória. Pode executar EXPLAIN ANALYZE numa consulta para determinar a quantidade de memória necessária. Lembre-se que vCores e RAM são dimensionados em conjunto como descrito no artigo de opções de [configuração Hyperscale (Citus).](concepts-hyperscale-configuration-options.md)

## <a name="choosing-a-hyperscale-citus-tier"></a>Escolher um nível de hiperescala (Citus)

> [!IMPORTANT]
> O nível básico de Hiperescala (Citus) está atualmente em pré-visualização.  Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Pode ver uma lista completa de outras novidades em [funcionalidades de pré-visualização para Hyperscale (Citus)](hyperscale-preview-features.md).

As secções acima dão uma ideia de quantos vCores e quanto RAM são necessários para cada caso de utilização. Pode atender estas exigências através de uma escolha entre dois níveis de Hiperescala (Citus): o nível básico e o nível padrão.

O nível básico utiliza um único nó de base de dados para executar o processamento, enquanto o nível padrão permite mais nós. Os níveis são idênticos, oferecendo as mesmas características. Em alguns casos, o espaço vCores de um único nó pode ser dimensionado para ser suficiente, e em outros casos requer a cooperação de múltiplos nós.

Para uma comparação dos níveis, consulte a página de conceitos [de nível básico.](concepts-hyperscale-tiers.md)

## <a name="next-steps"></a>Passos seguintes

- [Dimensionar um grupo de servidores](howto-hyperscale-scale-grow.md)
- Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo do servidor .
