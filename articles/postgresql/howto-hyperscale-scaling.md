---
title: Grupo de servidores de escala - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Ajuste a memória do grupo do servidor, o disco e os recursos do CPU para lidar com o aumento da carga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/18/2020
ms.openlocfilehash: dd59d0b09a28febfc0afe35d9f008ba0e0ee19ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295719"
---
# <a name="server-group-size"></a>Tamanho do grupo de servidores

A opção de implementação hyperscale (Citus) utiliza servidores de base de dados cooperantes para paralelizar a execução de consultas e armazenar mais dados. O "tamanho" do grupo do servidor refere-se tanto ao número de servidores como aos recursos de hardware de cada um.

## <a name="picking-initial-size"></a>Escolher o tamanho inicial

O tamanho de um grupo de servidores, em termos de número de nós e da sua capacidade de hardware, é fácil de alterar[(ver abaixo).](#scale-a-hyperscale-citus-server-group) No entanto, ainda precisa de escolher um tamanho inicial para um novo grupo de servidores. Aqui estão algumas dicas para uma escolha razoável.

### <a name="multi-tenant-saas-use-case"></a>Use-Case De Multi-Inquilinos SaaS

Para aqueles que migram para Hyperscale (Citus) a partir de uma instância de base de dados postgresQL existente, recomendamos a escolha de um cluster onde o número de vCores e RAM de trabalhadores no total é igual ao da instância original. Nestes cenários, temos visto melhorias no desempenho de 2-3x porque o fragmento melhora a utilização dos recursos, permitindo índices menores, etc.

O número de vCores necessários para o nó coordenador depende da carga de trabalho existente (produção escrita/leitura). O nó coordenador não requer tanto RAM como nós de trabalhadores, mas a atribuição de RAM é determinada com base na contagem de vCore (como descrito nas opções de [configuração de Hyperscale (Citus)](concepts-hyperscale-configuration-options.md)), pelo que a contagem vCore é essencialmente a decisão real.

### <a name="real-time-analytics-use-case"></a>Real-Time Use-Case de Análise

Total de vCores: quando os dados de trabalho se enquadram na RAM, pode esperar uma melhoria linear do desempenho em Hyperscale (Citus) proporcional ao número de núcleos de trabalhadores. Para determinar o número certo de vCores para as suas necessidades, considere a latência atual para consultas na sua base de dados de nó único e a latência necessária em Hyperscale (Citus). Divida a latência atual pela latência desejada e arredonde o resultado.

RAM de trabalho: o melhor caso seria proporcionar memória suficiente para que a maioria do conjunto de trabalho se encaixe na memória. O tipo de consultas que a sua aplicação utiliza afeta os requisitos de memória. Pode executar EXPLAIN ANALYZE numa consulta para determinar a quantidade de memória necessária. Lembre-se que vCores e RAM são dimensionados em conjunto como descrito no artigo de opções de [configuração Hyperscale (Citus).](concepts-hyperscale-configuration-options.md)

## <a name="scale-a-hyperscale-citus-server-group"></a>Escalar um grupo de servidores de hiperescala (Citus)

A Azure Database for PostgreSQL - Hyperscale (Citus) fornece escala de autosserviço para lidar com o aumento da carga. O portal Azure facilita a adição de novos nós de trabalhadores e o aumento dos vCores dos nós existentes. A adição de nós não provoca tempo de inatividade, e mesmo mover fragmentos para os novos nós (chamado [reequilíbrio de fragmentos](#rebalance-shards)) acontece sem interromper consultas.

### <a name="add-worker-nodes"></a>Adicione os nó de trabalhador

Para adicionar nós, vá ao separador **de armazenamento Compute +** no seu grupo de servidor Hyperscale (Citus).  Arrastar o slider para **a contagem de nóiros** altera o valor.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Sliders de recursos":::

Clique no botão **Guardar** para fazer com que o valor alterado faça efeito.

> [!NOTE]
> Uma vez aumentado e guardado, o número de nós de trabalhador não pode ser diminuído usando o slider.

#### <a name="rebalance-shards"></a>Fragmentos de reequilíbrio

Para tirar partido dos nós recém-adicionados, é necessário reequilibrar os fragmentos de mesa [distribuídos,](concepts-hyperscale-distributed-data.md#shards)o que significa mover alguns fragmentos dos nós existentes para os novos. Verifique primeiro se os novos trabalhadores terminaram com êxito o fornecimento. Em seguida, inicie o reequilíbrio de fragmentos, ligando-o ao nó coordenador do cluster com o psql e a correr:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A `rebalance_table_shards` função reequilibra todas as tabelas do grupo de [colocação](concepts-hyperscale-colocation.md) da tabela nomeada no seu argumento. Assim, não é preciso chamar a função para cada mesa distribuída, basta chamá-la numa mesa representativa de cada grupo de colocação.

### <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentar ou diminuir vCores nos nos nos acenos

Além de adicionar novos nós, pode aumentar as capacidades dos nós existentes. Ajustar a capacidade de computação para cima e para baixo pode ser útil para experiências de desempenho, bem como alterações a curto ou longo prazo nas exigências de tráfego.

Para alterar os vCores para todos os nós dos trabalhadores, ajuste o slider **vCores** em **Configuração (por nó de trabalhador)**. Os vCores do nó coordenador podem ser ajustados de forma independente. Ajuste o slider **vCores** na  **Configuração (nó coordenador)**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as opções](concepts-hyperscale-configuration-options.md)de desempenho do grupo do servidor .
