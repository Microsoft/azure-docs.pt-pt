---
title: Escolha as colunas de distribuição na base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)
description: Boas opções para colunas de distribuição em cenários comuns de hiperescala
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65078989"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Escolha as colunas de distribuição na base de dados do Azure para PostgreSQL – Hiperescala (Citus) (pré-visualização)

Escolher coluna de distribuição de cada tabela é **uma das mais importantes** decisões de modelagem. Hiperescala armazena linhas em partições horizontais com base no valor de coluna de distribuição das linhas.

Os grupos de opção correta relacionados a consulta de dados em conjunto em nós físicos de mesmas, tornando rápido e adicionando suporte para todas as funcionalidades do SQL. Faz uma seleção incorreta o sistema será executado lentamente e não dará suporte a todas as funcionalidades do SQL em todos os nós.

Esta secção fornece distribuição dicas de coluna para dois cenários mais comuns de Hiperescala.

### <a name="multi-tenant-apps"></a>Aplicações multi-inquilino

A arquitetura de multi-inquilino utiliza um formulário da base de dados hierárquica de modelagem para distribuir consultas em todos os nós no grupo de servidor.  Parte superior da hierarquia de dados é conhecida como o *ID de inquilino*e tem de ser armazenada numa coluna em cada tabela.

Hiperescala inspeciona consultas para ver qual ID do inquilino que envolvem e localiza a correspondência das partições horizontais de tabela. Encaminha a consulta para um nó de trabalho única que contém a partição horizontal. Executar uma consulta com todos os dados relevantes colocados no mesmo nó é chamado de colocalização.

O diagrama seguinte ilustra a colocalização no modelo de dados de multi-inquilino. Ele contém duas tabelas, contas e campanhas, cada distribuído por `account_id`. As caixas sombreadas representam as partições horizontais, cada um dos cuja cor representa o nó de trabalho contém-lo. As partições horizontais verde são armazenadas no nó de um trabalho e azul em outro. Observe como uma consulta de junção entre as contas e campanhas de ter todos os dados necessários em conjunto num nó quando restringir ambas as tabelas para a mesma conta\_id.

![colocalização de multi-inquilino](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Para aplicar esta estrutura no seu próprio esquema, identifique o que constitui um inquilino na sua aplicação. Instâncias de comuns incluem a empresa, conta, organização ou cliente. O nome da coluna será algo como `company_id` ou `customer_id`. Examinar cada uma das suas consultas e pergunte-se: funcionaria se ele tivesse adicionais cláusulas WHERE para restringir a todas as tabelas envolvidas a linhas com o mesmo ID de inquilino?
Consultas no modelo de multi-inquilino estão confinadas a um inquilino, por exemplo seriam confinar consultas em vendas ou de inventário num determinado arquivo.

#### <a name="best-practices"></a>Melhores práticas

-   **Partição distribuídas a tabelas por um inquilino comuns\_coluna de id.** Por exemplo, numa aplicação SaaS em que os inquilinos são as empresas, o inquilino\_id deverá ser empresa\_id.
-   **Converta tabelas de entre inquilinos pequenas às tabelas de referência.** Quando vários inquilinos partilham uma pequena tabela de informações, distribuí-la como uma tabela de referência.
-   **Restringir o filtro de consulta de todos os aplicativos por inquilino\_id.** Cada consulta deve solicitar informações para um inquilino ao mesmo tempo.

Leitura a [multi-inquilino tutorial](./tutorial-design-database-hyperscale-multi-tenant.md) para obter um exemplo de criar esse tipo de aplicativo.

### <a name="real-time-apps"></a>Aplicações em tempo real

A arquitetura de multi-inquilino apresenta uma estrutura hierárquica e utiliza a colocalização de dados para consultas de rota por inquilino. Por outro lado, as arquiteturas em tempo real dependem das propriedades de distribuição específico dos seus dados para alcançar o processamento altamente paralelo.

Utilizamos "ID de entidade" como um termo para colunas de distribuição no modelo em tempo real. As entidades típicas são usuários, anfitriões ou dispositivos.

Consultas em tempo real, normalmente, solicite ao agregações numéricas agrupadas por data ou categoria. Hiperescala envia estas consultas para cada partição horizontal para resultados parciais e monta a resposta final no nó coordenador. As consultas são executadas mais rápida quando vários nós contribuem possível e quando nenhum nó único deve fazer uma quantidade desproporcionada de trabalho.

#### <a name="best-practices"></a>Melhores práticas

-   **Escolha uma coluna com cardinalidade elevada, como a coluna de distribuição.** Para comparação, um \"estado\" campo numa tabela de ordem com valores de "new", "paga" e "enviado" é uma escolha fraca de coluna de distribuição. Presume que esses valores alguns, que limita o número de partições horizontais que pode conter os dados e o número de nós que pode processá-lo. Entre colunas com cardinalidade elevada, é bom, além disso escolher as que são usados com freqüência em cláusulas group by ou como chaves de associação.
-   **Escolha uma coluna com a distribuição uniforme.** Se distribuir uma tabela numa coluna inclinada para determinados valores comuns, em seguida, dados na tabela serão tendem a se acumular em determinadas as partições horizontais. Os nós que contém nessas partições horizontais acabará fazendo o trabalho mais do que outros nós.
-   **Distribua tabelas de fatos e dimensão em suas colunas comuns.**
    A tabela de fatos pode ter apenas uma chave de distribuição. Tabelas que se Junte-se no outra chave não será foi colocalizadas com a tabela de fatos. Escolha uma dimensão colocalizar com base na frequência com a qual está associado e o tamanho das linhas da junção.
-   **Altere algumas tabelas de dimensão para tabelas de referência.** Se uma tabela de dimensão não é possível colocalizar com a tabela de fatos, pode melhorar o desempenho das consultas ao distribuir cópias da tabela de dimensão para todos os nós sob a forma de uma tabela de referência.

Leitura a [tutorial de dashboard em tempo real](./tutorial-design-database-hyperscale-realtime.md) para obter um exemplo de criar esse tipo de aplicativo.

### <a name="timeseries-data"></a>Dados de série de tempo

Na carga de trabalho série de tempo, aplicativos consultar informações recentes ao arquivamento informações antigas.

O erro mais comum na modelagem de informações de série de tempo em Hiperescala está a utilizar o carimbo de hora como uma coluna de distribuição. Uma distribuição de hash com base no tempo vai distribuir vezes aparentemente aleatoriamente em partições horizontais diferentes, em vez de manter os intervalos de tempo em conjunto em partições horizontais. Intervalos de tempo de referência de consultas que em geral, envolvem tempo (por exemplo os dados mais recentes), portanto, esse uma distribuição hash poderia levar a rede sobrecarga.

#### <a name="best-practices"></a>Melhores práticas

-   **Não escolha um carimbo como a coluna de distribuição.** Escolha uma coluna de distribuição diferente. Numa aplicação multi-inquilino, utilize o ID de inquilino ou numa aplicação em tempo real, utilize o ID de entidade.
-   **Utilize PostgreSQL particionamento de tabela para a hora em vez disso.** Use o particionamento de tabela para dividir uma grande tabela de dados ordenados por tempo em várias tabelas herdadas com cada um contendo os intervalos de tempo diferentes.  Distribuição de uma tabela particionada Postgres numa Hiperescala cria partições horizontais para as tabelas herdadas.

Leitura a [tutorial da série de tempo](https://aka.ms/hyperscale-tutorial-timeseries) para obter um exemplo de criar esse tipo de aplicativo.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como [colocalização](concepts-hyperscale-colocation.md) entre as consultas são executadas rapidamente de ajuda de dados distribuídos
