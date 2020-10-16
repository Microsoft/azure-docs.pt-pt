---
title: Escolha colunas de distribuição - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Saiba como escolher colunas de distribuição em cenários comuns na Base de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 129eff8c954c0c5469d3607e6ae16ce3202630ed
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929341"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Escolha colunas de distribuição na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

Escolher a coluna de distribuição de cada tabela é uma das decisões de modelação mais importantes que irá tomar. A Azure Database for PostgreSQL – Hyperscale (Citus) armazena linhas em fragmentos com base no valor da coluna de distribuição das linhas.

A escolha correta agruba dados relacionados com os mesmos nós físicos, o que torna as consultas rápidas e adiciona suporte para todas as funcionalidades SQL. Uma escolha incorreta faz com que o sistema seja executado lentamente e não suporta todas as funcionalidades SQL em nós.

Este artigo dá dicas de coluna de distribuição para os dois cenários de Hiperescala mais comuns (Citus).

### <a name="multi-tenant-apps"></a>Aplicações multi-inquilino

A arquitetura multi-inquilino usa uma forma de modelação hierárquica de base de dados para distribuir consultas em nós no grupo de servidores. O topo da hierarquia de dados é conhecido como o *ID* do inquilino e precisa ser armazenado numa coluna em cada mesa.

A Hyperscale (Citus) inspeciona as consultas para ver qual o ID do inquilino que envolvem e encontra o fragmento da mesa correspondente. Ele encaminha a consulta para um único nó de trabalhador que contém o fragmento. Executar uma consulta com todos os dados relevantes colocados no mesmo nó é chamado de colocation.

O diagrama seguinte ilustra a colocação no modelo de dados multi-inquilino. Contém duas tabelas, Contas e Campanhas, cada uma distribuída por `account_id` . As caixas sombreadas representam fragmentos. Os fragmentos verdes são armazenados em conjunto num nó de um trabalhador, e os fragmentos azuis são armazenados em outro nó operário. Note como uma consulta de união entre Contas e Campanhas tem todos os dados necessários em conjunto num nó quando ambas as tabelas estão restritas à mesma \_ conta.

![Colocação multi-arrendatário](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Para aplicar este design no seu próprio esquema, identifique o que constitui um inquilino na sua aplicação. As instâncias comuns incluem empresa, conta, organização ou cliente. O nome da coluna será algo como `company_id` `customer_id` ou. Examine cada uma das suas consultas e pergunte a si mesmo, funcionaria se tivesse cláusulas WHERE adicionais para restringir todas as tabelas envolvidas a filas com o mesmo ID do inquilino?
As consultas no modelo multi-inquilino são procuradas por um inquilino. Por exemplo, as consultas sobre vendas ou inventário são procuradas numa determinada loja.

#### <a name="best-practices"></a>Melhores práticas

-   **Divisória distribuída tabelas por uma coluna de id inquilino \_ comum.** Por exemplo, num requerimento saaS em que os inquilinos são empresas, é provável que o id do inquilino \_ seja o id da \_ empresa.
-   **Converter pequenas mesas de inquilinos cruzados em mesas de referência.** Quando vários inquilinos partilham uma pequena tabela de informação, distribua-a como uma tabela de referência.
-   **Restringir filtrar todas as consultas de aplicação por \_ identificação do inquilino.** Cada consulta deve solicitar informações para um inquilino de cada vez.

Leia o [tutorial de vários inquilinos](./tutorial-design-database-hyperscale-multi-tenant.md) para um exemplo de como construir este tipo de aplicação.

### <a name="real-time-apps"></a>Aplicações em tempo real

A arquitetura multi-arrendatário introduz uma estrutura hierárquica e utiliza a colocação de dados para encaminhar consultas por inquilino. Em contraste, as arquiteturas em tempo real dependem de propriedades de distribuição específicas dos seus dados para alcançar um processamento altamente paralelo.

Utilizamos o "ID da entidade" como termo para colunas de distribuição no modelo em tempo real. As entidades típicas são utilizadores, anfitriões ou dispositivos.

Consultas em tempo real normalmente pedem agregados numéricos agrupados por data ou categoria. A Hiperescala (Citus) envia estas consultas a cada fragmento para obter resultados parciais e reúne a resposta final no nó coordenador. As consultas são mais rápidas quando o maior número possível de nós contribui, e quando nenhum nó deve fazer uma quantidade desproporcional de trabalho.

#### <a name="best-practices"></a>Melhores práticas

-   **Escolha uma coluna com elevado cardinalício como coluna de distribuição.** Para comparação, um campo de estado numa tabela de encomendas com valores Novos, Pagos e Enviados é uma má escolha da coluna de distribuição. Assume apenas esses poucos valores, o que limita o número de fragmentos que podem conter os dados, e o número de nós que podem processá-los. Entre colunas com elevado cardinalício, também é bom escolher as colunas que são frequentemente usadas em cláusulas de grupo ou como chaves de união.
-   **Escolha uma coluna com distribuição uniforme.** Se distribuir uma tabela numa coluna desviada para determinados valores comuns, os dados na tabela tendem a acumular-se em certos fragmentos. Os nós que seguram os fragmentos acabam por fazer mais trabalho do que outros nós.
-   **Distribua tabelas de factos e dimensões nas suas colunas comuns.**
    A sua tabela de factos só pode ter uma chave de distribuição. As mesas que se juntam a outra chave não serão apresentadas com a mesa de factos. Escolha uma dimensão para co-colocate com base na frequência com que se junta e no tamanho das linhas de junção.
-   **Mude algumas tabelas de dimensão em tabelas de referência.** Se uma tabela de dimensão não puder ser apresentada com a tabela de factos, pode melhorar o desempenho da consulta distribuindo cópias da tabela de dimensões a todos os nós sob a forma de uma tabela de referência.

Leia o tutorial do [dashboard em tempo real](./tutorial-design-database-hyperscale-realtime.md) para um exemplo de como construir este tipo de aplicação.

### <a name="time-series-data"></a>Dados da série temporal

Numa carga de trabalho em séries de tempo, as aplicações consultam informações recentes enquanto arquivam informações antigas.

O erro mais comum na modelação de informações de séries temporais em Hyperscale (Citus) é usar o próprio timetamp como coluna de distribuição. Uma distribuição de haxixe baseada no tempo distribui tempos aparentemente aleatórios em diferentes fragmentos em vez de manter as gamas de tempo juntas em fragmentos. Consultas que envolvem intervalos de tempo geralmente de referência, por exemplo, os dados mais recentes. Este tipo de distribuição de haxixe leva à sobrecarga da rede.

#### <a name="best-practices"></a>Melhores práticas

-   **Não escolha um tempotando como coluna de distribuição.** Escolha uma coluna de distribuição diferente. Numa aplicação multi-inquilina, use o ID do inquilino, ou numa aplicação em tempo real use o ID da entidade.
-   **Utilize a partição da mesa PostgreSQL durante algum tempo.** Utilize divisórias de mesa para quebrar uma grande tabela de dados encomendados pelo tempo em várias tabelas herdadas com cada tabela contendo diferentes intervalos de tempo. A distribuição de uma mesa de divisórias postgres em Hiperescala (Citus) cria fragmentos para as tabelas herdadas.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [a colocação](concepts-hyperscale-colocation.md) entre dados distribuídos ajuda as consultas a correr rapidamente.
