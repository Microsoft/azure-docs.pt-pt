---
title: Escolha colunas de distribuição – Hyperscale (Citus) - Base de Dados Azure para PostgreSQL
description: Saiba como escolher colunas de distribuição em cenários comuns de hiperescala na Base de Dados Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 8ced9767d81affceef851820ee587f4f3dd24deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975674"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Escolha colunas de distribuição na Base de Dados Azure para PostgreSQL – Hyperscale (Citus)

Escolher a coluna de distribuição de cada tabela é uma das decisões de modelação mais importantes que tomará. A Base de Dados Azure para PostgreSQL – Hyperscale (Citus) armazena filas em fragmentos com base no valor da coluna de distribuição das linhas.

Os grupos de escolha corretos relacionaram dados nos mesmos nós físicos, o que torna as consultas rápidas e adiciona suporte para todas as funcionalidades SQL. Uma escolha incorreta faz com que o sistema corra lentamente e não suporta todas as funcionalidades SQL em nós.

Este artigo dá dicas de colunade distribuição para os dois cenários mais comuns de Hiperescala (Citus).

### <a name="multi-tenant-apps"></a>Aplicações multi-inquilino

A arquitetura multi-inquilinos usa uma forma de modelação hierárquica de base de dados para distribuir consultas em nós do grupo de servidores. O topo da hierarquia de dados é conhecido como id do *inquilino* e precisa ser armazenado em uma coluna em cada mesa.

Hyperscale (Citus) inspeciona consultas para ver qual o ID do inquilino que envolvem e encontra o fragmento de mesa correspondente. Ele encaminha a consulta para um único nó de trabalhador que contém o fragmento. Fazer uma consulta com todos os dados relevantes colocados no mesmo nó chama-se co-location.

O diagrama seguinte ilustra a colocalização no modelo de dados multi-inquilinos. Contém duas tabelas, Contas e Campanhas, `account_id`cada uma distribuída por . As caixas sombreadas representam fragmentos. Os fragmentos verdes são guardados juntos num nó de trabalhador, e os fragmentos azuis são guardados em outro nó operário. Note como uma consulta de adesão entre Contas e Campanhas tem todos os dados\_necessários em conjunto num nó quando ambas as tabelas estão restritas ao mesmo id de conta.

![Co-localização multi-inquilino](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Para aplicar este design no seu próprio esquema, identifique o que constitui um inquilino na sua aplicação. Casos comuns incluem empresa, conta, organização ou cliente. O nome da coluna `company_id` `customer_id`será algo como ou. Examine cada uma das suas perguntas e pergunte a si mesmo, funcionaria se tivesse cláusulas adicionais de ONDE para restringir todas as mesas envolvidas em filas com a mesma identificação de inquilino?
As consultas no modelo multi-inquilino são consultadas a um inquilino. Por exemplo, as consultas sobre vendas ou inventário são consultadas dentro de uma determinada loja.

#### <a name="best-practices"></a>Melhores práticas

-   **Tabelas distribuídas por\_uma coluna de identificação de inquilino comum.** Por exemplo, num pedido da SaaS onde\_os inquilinos são\_empresas, é provável que o id do arrendatário seja o id da empresa.
-   **Converta pequenas mesas de inquilinos cruzados em mesas de referência.** Quando vários inquilinos partilharem uma pequena tabela de informações, distribua-a como uma tabela de referência.
-   **Restringir o filtro de\_todas as consultas de inscrição por id inquilino.** Cada consulta deve solicitar informações para um inquilino de cada vez.

Leia o [tutorial multi-inquilino](./tutorial-design-database-hyperscale-multi-tenant.md) para um exemplo de como construir este tipo de aplicação.

### <a name="real-time-apps"></a>Aplicações em tempo real

A arquitetura multi-arrendatária introduz uma estrutura hierárquica e utiliza co-localização de dados para encaminhar consultas por inquilino. Em contraste, as arquiteturas em tempo real dependem de propriedades de distribuição específicas dos seus dados para obter um processamento altamente paralelo.

Usamos "ID de entidade" como termo para colunas de distribuição no modelo em tempo real. As entidades típicas são utilizadores, anfitriões ou dispositivos.

Consultas em tempo real normalmente pedem agregados numéricos agrupados por data ou categoria. A Hiperescala (Citus) envia estas consultas a cada fragmento para obter resultados parciais e reúne a resposta final no nó coordenador. As consultas são mais rápidas quando o maior número possível de nós contribui, e quando nenhum nó deve fazer uma quantidade desproporcional de trabalho.

#### <a name="best-practices"></a>Melhores práticas

-   **Escolha uma coluna com alta cardeal como coluna de distribuição.** Para comparação, um campo de Status numa tabela de encomendas com valores Novos, Pagos e Shipped é uma má escolha da coluna de distribuição. Assume apenas os poucos valores, que limitam o número de fragmentos que podem conter os dados, e o número de nós que podem processá-los. Entre colunas com alta cardinalidade, também é bom escolher as colunas que são frequentemente usadas em cláusulas de grupo ou como chaves de adesão.
-   **Escolha uma coluna com distribuição uniforme.** Se distribuir uma tabela sobre uma coluna distorcida para determinados valores comuns, os dados na tabela tendem a acumular-se em certos fragmentos. Os nós que seguram esses fragmentos acabam por fazer mais trabalho do que outros nós.
-   **Distribua tabelas de factos e dimensões nas suas colunas comuns.**
    A tua tabela de factos só pode ter uma chave de distribuição. As mesas que se juntam a outra chave não serão colocalizadas com a tabela de factos. Escolha uma dimensão para colocação com base na frequência com que se junta e no tamanho das linhas de junção.
-   **Mude algumas tabelas de dimensão em tabelas de referência.** Se uma tabela de dimensões não puder ser colonada com a tabela de factos, pode melhorar o desempenho da consulta distribuindo cópias da tabela de dimensões a todos os nós sob a forma de uma tabela de referência.

Leia o [tutorial do dashboard](./tutorial-design-database-hyperscale-realtime.md) em tempo real para um exemplo de como construir este tipo de aplicação.

### <a name="time-series-data"></a>Dados da série temporal

Numa carga de trabalho em série sem tempo, as aplicações consultam informações recentes enquanto arquivam informações antigas.

O erro mais comum na modelação de informações da série de tempo em Hyperscale (Citus) é usar o carimbo de tempo em si como coluna de distribuição. Uma distribuição de hash baseada no tempo distribui tempos aparentemente aleatoriamente em diferentes fragmentos em vez de manter intervalos de tempo juntos em fragmentos. Consultas que envolvem tempo geralmente referenciam intervalos de tempo, por exemplo, os dados mais recentes. Este tipo de distribuição de hash leva a sobrecarga da rede.

#### <a name="best-practices"></a>Melhores práticas

-   **Não escolha um carimbo de tempo como coluna de distribuição.** Escolha uma coluna de distribuição diferente. Numa aplicação multi-inquilino, use o ID do inquilino, ou numa aplicação em tempo real, use o ID da entidade.
-   **Utilize a partilha da tabela PostgreSQL durante o tempo.** Utilize a partilha de tabelas para quebrar uma grande tabela de dados encomendados pelo tempo em várias tabelas herdadas com cada tabela contendo diferentes intervalos de tempo. A distribuição de uma mesa dividida por Postgres em Hiperescala (Citus) cria fragmentos para as mesas herdadas.

Leia o [tutorial da série de tempo](https://aka.ms/hyperscale-tutorial-timeseries) para um exemplo de como construir este tipo de aplicação.

## <a name="next-steps"></a>Passos seguintes
- Saiba como a [colocalização](concepts-hyperscale-colocation.md) entre dados distribuídos ajuda as consultas a correr rapidamente.
