---
title: 'Camadas de serviço do banco de dados SQL do Azure-modelo de compra baseado em DTU '
description: Saiba mais sobre as camadas de serviço no modelo de compra baseado em DTU para bancos de dados individuais e em pool para fornecer tamanhos de computação e armazenamento.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 09/06/2019
ms.openlocfilehash: 43f4f9e0d1e5f33dde32e5274dddf9d17776db21
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687298"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Camadas de serviço no modelo de compra baseado em DTU

As camadas de serviço no modelo de compra baseado em DTU são diferenciadas por um intervalo de tamanhos de computação com uma quantidade fixa de armazenamento incluído, o período de retenção fixo para backups e o preço fixo. Todas as camadas de serviço no modelo de compra baseado em DTU fornecem flexibilidade para alterar os tamanhos de computação com [tempo de inatividade](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)mínimo; no entanto, há uma mudança no período em que a conectividade é perdida no banco de dados por um curto período de tempo, o que pode ser mitigado usando a lógica de repetição. Os bancos de dados individuais e os pools elásticos são cobrados por hora com base na camada de serviço e no tamanho da computação.

> [!IMPORTANT]
> A instância gerenciada do banco de dados SQL não dá suporte a um modelo de compra baseado em DTU. Para obter mais informações, consulte [instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance.md).
> [!NOTE]
> Para obter informações sobre camadas de serviço baseadas em vCore, consulte [camadas de serviço baseadas em VCORE](sql-database-service-tiers-vcore.md). Para obter informações sobre como diferenciar camadas de serviço baseadas em DTU e camadas de serviço baseadas em vCore, consulte [modelos de compra do banco de dados SQL do Azure](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Comparar as camadas de serviço baseadas em DTU

Escolher uma camada de serviço depende principalmente dos requisitos de continuidade dos negócios, armazenamento e desempenho.

||Básica|Standard|Premium|
| :-- | --: |--:| --:|
|Carga de trabalho de destino|Desenvolvimento e produção|Desenvolvimento e produção|Desenvolvimento e produção|
|SLA de Tempo de Atividade|99,99%|99,99%|99,99%|
|Retenção de backup máxima|7 dias|35 dias|35 dias|
|CPU|Baixa|Baixo, médio, alto|Médio, alto|
|Taxa de transferência de e/s (aproximada) |1-5 IOPS por DTU| 1-5 IOPS por DTU | 25 IOPS por DTU|
|Latência de e/s (aproximada)|5 ms (leitura), 10 ms (gravação)|5 ms (leitura), 10 ms (gravação)|2 ms (leitura/gravação)|
|Indexação Columnstore |N/D|S3 e acima|Suportado|
|OLTP na memória|N/D|N/D|Suportado|
|||||

> [!NOTE]
> Você pode obter um banco de dados SQL do Azure gratuito na camada de serviço básica em conjunto com uma conta gratuita do Azure para explorar o Azure. Para obter informações, consulte [criar um banco de dados gerenciado na nuvem com sua conta gratuita do Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Limites de armazenamento e DTU de banco de dados individual

Os tamanhos de computação são expressos em termos de DTUs (unidades de transação de banco de dados) para bancos de dados individuais e eDTUs (unidades de transação de banco de dados elástico) para pools elásticos. Para obter mais informações sobre DTUs e eDTUs, consulte [modelo de compra baseado em DTU](sql-database-purchase-models.md#dtu-based-purchasing-model)?

||Básica|Standard|Premium|
| :-- | --: | --: | --: |
| Tamanho máximo de armazenamento | 2GB | 1 TB | 4 TB  |
| Número máximo de DTUs | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Limites de pool elástico, armazenamento e banco de dados em pool

| | **Básica** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| Tamanho máximo de armazenamento por banco de dados  | 2GB | 1 TB | 1 TB |
| Tamanho máximo de armazenamento por pool | 156 GB | 4 TB | 4 TB |
| Máximo de eDTUs por banco de dados | 5 | 3000 | 4000 |
| Máximo de eDTUs por pool | 1600 | 3000 | 4000 |
| Número máximo de bancos de dados por pool | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Mais de 1 TB de armazenamento na camada Premium está disponível atualmente em todas as regiões, exceto: Leste da China, Norte da China, Alemanha central, Alemanha nordeste, Oeste EUA Central, regiões de US DoD e central do governo dos EUA. Noutras regiões, o armazenamento máximo no escalão Premium está limitado a 1 TB.  Para obter mais informações, consulte [limitações atuais do P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>Parâmetro de comparação de DTU

As características físicas (CPU, memória, e/s) associadas a cada medida de DTU são calibradas usando um parâmetro de comparação que simula a carga de trabalho do banco de dados do mundo real.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlacionar resultados de benchmark para desempenho de banco de dados real

É importante entender que todos os parâmetros de comparação são representativos e indicativos apenas. As taxas de transação obtidas com o aplicativo de benchmark não serão as mesmas que as que podem ser obtidas com outros aplicativos. O parâmetro de comparação consiste em uma coleção de tipos de transação diferentes executados em um esquema que contém um intervalo de tabelas e tipos de dados. Enquanto o parâmetro de comparação exercita as mesmas operações básicas que são comuns a todas as cargas de trabalho OLTP, ele não representa nenhuma classe específica de banco de dados ou aplicativo. O objetivo do benchmark é fornecer um guia razoável para o desempenho relativo de um banco de dados que pode ser esperado ao escalar ou reduzir verticalmente entre os tamanhos de computação. Na realidade, os bancos de dados são de diferentes tamanhos e complexidade, encontram diferentes combinações de cargas de trabalho e responderão de maneiras diferentes. Por exemplo, um aplicativo com uso intensivo de e/s pode atingir os limites de e/s mais cedo ou um aplicativo com uso intensivo de CPU pode atingir os limites de CPU mais cedo. Não há nenhuma garantia de que qualquer banco de dados específico será dimensionado da mesma maneira que o parâmetro de comparação sob o aumento da carga.

O benchmark e sua metodologia são descritos mais detalhadamente abaixo.

### <a name="benchmark-summary"></a>Resumo do parâmetro de comparação

O parâmetro de comparação mede o desempenho de uma combinação de operações básicas de banco de dados que ocorrem com mais frequência em cargas de trabalho de OLTP (processamento de transações online). Embora o parâmetro de comparação tenha sido projetado com a computação em nuvem em mente, o esquema de banco de dados, a população e as transações foram projetados para ser amplamente representativos dos elementos básicos mais comumente usados em cargas de trabalho OLTP.

### <a name="schema"></a>Esquema

O esquema foi projetado para ter uma variedade e complexidade suficientes para dar suporte a uma ampla gama de operações. O parâmetro de comparação é executado em um banco de dados composto por seis tabelas. As tabelas se enquadram em três categorias: tamanho fixo, dimensionamento e crescimento. Há duas tabelas de tamanho fixo; três tabelas de dimensionamento; e uma tabela crescente. As tabelas de tamanho fixo têm um número constante de linhas. Dimensionar tabelas tem uma cardinalidade que é proporcional ao desempenho do banco de dados, mas não é alterado durante o parâmetro de comparação. A tabela crescente é dimensionada como uma tabela de dimensionamento na carga inicial, mas, em seguida, a cardinalidade é alterada no decorrer da execução do parâmetro de comparação conforme as linhas são inseridas e excluídas.

O esquema inclui uma combinação de tipos de dados, incluindo inteiro, numérico, caractere e data/hora. O esquema inclui chaves primárias e secundárias, mas não as chaves estrangeiras, ou seja, não há restrições de integridade referencial entre as tabelas.

Um programa de geração de dados gera os dados para o banco de dado inicial. Os dados inteiros e numéricos são gerados com várias estratégias. Em alguns casos, os valores são distribuídos aleatoriamente em um intervalo. Em outros casos, um conjunto de valores é aleatoriamente permutado para garantir que uma distribuição específica seja mantida. Os campos de texto são gerados de uma lista ponderada de palavras para produzir dados de aparência real.

O banco de dados é dimensionado com base em um "fator de escala". O fator de escala (abreviado como it) determina a cardinalidade do dimensionamento e as tabelas crescentes. Conforme descrito abaixo na seção usuários e ritmo, o tamanho do banco de dados, o número de usuários e o desempenho máximo são dimensionados em proporção entre si.

### <a name="transactions"></a>Transações

A carga de trabalho consiste em nove tipos de transação, conforme mostrado na tabela a seguir. Cada transação é projetada para realçar um determinado conjunto de características do sistema no mecanismo de banco de dados e no hardware do sistema, com alto contraste das outras transações. Essa abordagem torna mais fácil avaliar o impacto de diferentes componentes para o desempenho geral. Por exemplo, a transação "Read Heavy" produz um número significativo de operações de leitura do disco.

| Tipo de transação | Descrição |
| --- | --- |
| Ler Lite |Não na memória; somente leitura |
| Ler média |Não principalmente na memória; somente leitura |
| Leitura intensa |Não Basicamente não na memória; somente leitura |
| Atualização Lite |CUMULATIVO na memória; leitura/gravação |
| Atualização pesada |CUMULATIVO Basicamente não na memória; leitura/gravação |
| Inserir Lite |INSERIDO na memória; leitura/gravação |
| Inserir pesado |INSERIDO Basicamente não na memória; leitura/gravação |
| Eliminar |APAGAR combinação de na memória e não na memória; leitura/gravação |
| CPU pesada |Não na memória; carga de CPU relativamente pesada; somente leitura |

### <a name="workload-mix"></a>Combinação de carga de trabalho

As transações são selecionadas aleatoriamente de uma distribuição ponderada com a combinação geral a seguir. A combinação geral tem uma proporção de leitura/gravação de aproximadamente 2:1.

| Tipo de transação | % da combinação |
| --- | --- |
| Ler Lite |35 |
| Ler média |20 |
| Leitura intensa |5 |
| Atualização Lite |20 |
| Atualização pesada |3 |
| Inserir Lite |3 |
| Inserir pesado |2 |
| Eliminar |2 |
| CPU pesada |10 |

### <a name="users-and-pacing"></a>Usuários e ritmo

A carga de trabalho de benchmark é controlada de uma ferramenta que envia transações em um conjunto de conexões para simular o comportamento de vários usuários simultâneos. Embora todas as conexões e transações sejam geradas pela máquina, para simplificar, nos referimos a essas conexões como "usuários". Embora cada usuário opere independentemente de todos os outros usuários, todos os usuários executam o mesmo ciclo de etapas mostrado abaixo:

1. Estabeleça uma conexão de banco de dados.
2. Repetir até que o sinal seja encerrado:
   - Selecione uma transação aleatoriamente (de uma distribuição ponderada).
   - Execute a transação selecionada e meça o tempo de resposta.
   - Aguarde um atraso de ritmo.
3. Feche a conexão de banco de dados.
4. Sido.

O atraso de ritmo (na etapa 2C) é selecionado aleatoriamente, mas com uma distribuição que tem uma média de 1,0 segundo. Portanto, cada usuário pode, em média, gerar no máximo uma transação por segundo.

### <a name="scaling-rules"></a>Regras de dimensionamento

O número de usuários é determinado pelo tamanho do banco de dados (em unidades de fator de escala). Há um usuário para cada cinco unidades de fator de escala. Devido ao atraso de ritmo, um usuário pode gerar no máximo uma transação por segundo, em média.

Por exemplo, um fator de escala do banco de dados 500 (it = 500) terá 100 usuários e pode atingir uma taxa máxima de 100 TPS. Para impulsionar uma taxa de TPS maior, é necessário mais usuários e um banco de dados maior.

### <a name="measurement-duration"></a>Duração da medição

Uma execução de parâmetro de comparação válida requer uma duração de medição de estado estável de pelo menos uma hora.

### <a name="metrics"></a>Métricas

As principais métricas no parâmetro de comparação são a taxa de transferência e o tempo de resposta.

- A taxa de transferência é a medida de desempenho essencial no parâmetro de comparação. A taxa de transferência é relatada em transações por unidade de tempo, contando todos os tipos de transação.
- O tempo de resposta é uma medida de previsibilidade de desempenho. A restrição de tempo de resposta varia de acordo com a classe de serviço, com classes mais altas de serviço com um requisito de tempo de resposta mais estrito, conforme mostrado abaixo.

| Classe de serviço | Medida de taxa de transferência | Requisito de tempo de resposta |
| --- | --- | --- |
| Premium |Transações por segundo |95 º percentil às 0,5 segundos |
| Standard |Transações por minuto |90 º percentil às 1,0 segundos |
| Básica |Transações por hora |80 º percentil às 2,0 segundos |

## <a name="next-steps"></a>Passos seguintes

- Para obter detalhes sobre tamanhos de computação específicos e opções de tamanho de armazenamento disponíveis para bancos de dados individuais, consulte [limites de recursos baseados em DTU do banco de dados SQL para bancos de dados individuais](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Para obter detalhes sobre tamanhos de computação específicos e opções de tamanho de armazenamento disponíveis para pools elásticos, consulte [limites de recursos baseados em DTU do banco de dados SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
