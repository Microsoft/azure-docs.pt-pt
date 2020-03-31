---
title: Níveis de serviço - modelo de compra baseado em DTU
description: Conheça os níveis de serviço no modelo de compra baseado em DTU para bases de dados individuais e agrupadas para fornecer tamanhos de computação e armazenamento.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/26/2019
ms.openlocfilehash: 2f316e57e407a0588e77f56d6e1fbe8c19ba5fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75562124"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Escalões de serviço no modelo de compra baseado em DTU

Os níveis de serviço no modelo de compra baseado em DTU são diferenciados por uma gama de tamanhos de computação com uma quantidade fixa de armazenamento incluído, período de retenção fixa para backups e preço fixo. Todos os níveis de serviço do modelo de compra baseado em DTU proporcionam flexibilidade para alterar tamanhos de computação com o mínimo de tempo de [inatividade;](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) no entanto, há uma mudança ao longo do período em que a conectividade é perdida para a base de dados por um curto período de tempo, que pode ser atenuada usando a lógica de retry. Bases de dados únicas e piscinas elásticas são faturadas de hora em hora com base no nível de serviço e no tamanho da computação.

> [!IMPORTANT]
> A instância gerida pela Base de Dados SQL não suporta um modelo de compra baseado em DTU. Para mais informações, consulte a Base de [Dados Azure SQL Managed Instance](sql-database-managed-instance.md).
> [!NOTE]
> Para obter informações sobre os níveis de serviço baseados em vCore, consulte [os níveis de serviço baseados em vCore](sql-database-service-tiers-vcore.md). Para obter informações sobre a diferenciação dos níveis de serviço baseados em DTU e os níveis de serviço baseados em vCore, consulte os modelos de compra da Base de [Dados Azure SQL](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Compare os níveis de serviço baseados em DTU

A escolha de um nível de serviço depende principalmente dos requisitos de continuidade, armazenamento e desempenho do negócio.

||Básico|Standard|Premium|
| :-- | --: |--:| --:|
|Carga de trabalho alvo|Desenvolvimento e produção|Desenvolvimento e produção|Desenvolvimento e produção|
|Uptime SLA|99,99%|99,99%|99,99%|
|Retenção máxima de reserva|7 dias|35 dias|35 dias|
|CPU|Baixa|Baixo, Médio, Alto|Médio, Alto|
|Entrada IO (aproximada) |1-5 IOPS por DTU| 1-5 IOPS por DTU | 25 IOPS por DTU|
|Latência IO (aproximada)|5 ms (ler), 10 ms (escrever)|5 ms (ler), 10 ms (escrever)|2 ms (ler/escrever)|
|Indexação de colunas |N/D|S3 e acima|Suportado|
|OLTP em memória|N/D|N/D|Suportado|
|||||

> [!IMPORTANT]
> Os níveis de serviço Basic, Standard S0, S1 e S2 fornecem menos de um vCore (CPU).  Para cargas de trabalho intensivas em CPU, recomenda-se um nível de serviço de S3 ou superior. 
>
>No que diz respeito ao armazenamento de dados, os níveis de serviço Basic, Standard S0 e S1 são colocados nas Standard Page Blobs. Standard Page Blobs usam suportes de armazenamento baseados em disco rígido (HDD) e são mais adequados para desenvolvimento, teste e outras cargas de trabalho pouco acedidas que são menos sensíveis à variabilidade do desempenho.
>

> [!NOTE]
> Pode obter uma base de dados Azure SQL gratuita no nível de serviço Basic em conjunto com uma conta azure gratuita para explorar o Azure. Para obter informações, consulte Criar uma base de dados de [nuvem gerida com a sua conta gratuita Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>DTU de base de dados única e limites de armazenamento

Os tamanhos computacionais são expressos em termos de Unidades de Transação de Base de Dados (DTUs) para bases de dados únicas e unidades de transação elásticas de base de dados (eDTUs) para piscinas elásticas. Para mais informações sobre DTUs e eDTUs, consulte [o modelo de compra baseado em DTU](sql-database-purchase-models.md#dtu-based-purchasing-model).

||Básico|Standard|Premium|
| :-- | --: | --: | --: |
| Tamanho máximo do armazenamento | 2GB | 1 TB | 4 TB  |
| DTUs máximo | 5 | 3.000 | 4000 | 
|||||

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elástico piscina eDTU, armazenamento e limites de base de dados com piscina

| | **Básico** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| Tamanho máximo de armazenamento por base de dados  | 2GB | 1 TB | 1 TB |
| Tamanho máximo de armazenamento por piscina | 156 GB | 4 TB | 4 TB |
| Máxima semetada por base de dados | 5 | 3.000 | 4000 |
| Máxima sem eDTUs por piscina | 1600 | 3.000 | 4000 |
| Número máximo de bases de dados por piscina | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China Leste, China North, Alemanha Central, Alemanha Nordeste, Oeste Dos EUA, regiões do DoD dos EUA e Central do Governo dos EUA. Nestas regiões, o máximo de armazenamento no nível Premium está limitado a 1 TB.  Para mais informações, consulte as [limitações atuais p11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte [a gestão do espaço de ficheiros na Base de Dados Azure SQL](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>Referência DTU

As características físicas (CPU, memória, IO) associadas a cada medida DTU são calibradas utilizando uma referência que simula a carga de trabalho da base de dados do mundo real.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlacionar resultados de referência para o desempenho da base de dados do mundo real

É importante compreender que todos os critérios de referência são representativos e indicativos apenas. As taxas de transação alcançadas com a aplicação de referência não serão as mesmas que poderão ser alcançadas com outras aplicações. O benchmark compreende uma recolha de diferentes tipos de transações contra um esquema que contém uma gama de tabelas e tipos de dados. Embora o benchmark exerça as mesmas operações básicas que são comuns a todas as cargas de trabalho oLTP, não representa nenhuma classe específica de base de dados ou aplicação. O objetivo do benchmark é fornecer um guia razoável para o desempenho relativo de uma base de dados que se possa esperar ao escalonar para cima ou para baixo entre tamanhos de computação. Na realidade, as bases de dados são de diferentes tamanhos e complexidade, encontram diferentes misturas de cargas de trabalho, e responderão de diferentes maneiras. Por exemplo, uma aplicação intensiva da OI pode atingir os limiares da OI mais cedo, ou uma aplicação intensiva de CPU pode atingir os limites do CPU mais cedo. Não há garantias de que uma determinada base de dados irá escalar da mesma forma que o referencial sob carga crescente.

O benchmark e a sua metodologia são descritos mais detalhadamente abaixo.

### <a name="benchmark-summary"></a>Resumo de referência

O benchmark mede o desempenho de uma mistura de operações básicas de base de dados que ocorrem mais frequentemente no processamento de transações online (OLTP). Embora o benchmark seja projetado com a computação em nuvem em mente, o esquema de base de dados, a população de dados e as transações foram projetados para ser amplamente representativos dos elementos básicos mais utilizados nas cargas de trabalho oLTP.

### <a name="schema"></a>Esquema

O esquema foi concebido para ter variedade e complexidade suficientes para suportar um vasto leque de operações. O benchmark é contra uma base de dados composta por seis tabelas. As tabelas enquadram-se em três categorias: tamanho fixo, escalagem e crescimento. Existem duas mesas de tamanho fixo; três mesas de escala; e uma mesa de cultivo. As tabelas de tamanho fixo têm um número constante de linhas. As tabelas de escala têm uma cardinalidade proporcional ao desempenho da base de dados, mas não muda durante o referencial. A tabela de cultivo é dimensionada como uma tabela de escalas na carga inicial, mas depois a cardinalidade muda no decorrer da corrida à medida que as linhas são inseridas e apagadas.

O esquema inclui uma mistura de tipos de dados, incluindo inteiro, numérico, carácter e data/hora. O esquema inclui chaves primárias e secundárias, mas não chaves estrangeiras - isto é, não existem restrições de integridade referencial entre as mesas.

Um programa de geração de dados gera os dados para a base de dados inicial. Dados inteiros e numéricos são gerados com várias estratégias. Em alguns casos, os valores são distribuídos aleatoriamente ao longo de uma gama. Noutros casos, um conjunto de valores é permutado aleatoriamente para garantir a manutenção de uma distribuição específica. Os campos de texto são gerados a partir de uma lista ponderada de palavras para produzir dados realistas de aparência.

A base de dados é dimensionada com base num "fator de escala". O fator de escala (abreviado como SF) determina a cardinalidade das tabelas de escala e crescimento. Como descrito abaixo na secção Utilizadores e Pacing, o tamanho da base de dados, o número de utilizadores e o desempenho máximo em proporção entre si.

### <a name="transactions"></a>Transações

A carga de trabalho consiste em nove tipos de transações, como indicado no quadro abaixo. Cada transação foi concebida para destacar um conjunto particular de características do sistema no motor de base de dados e hardware do sistema, com um elevado contraste das outras transações. Esta abordagem facilita a avaliação do impacto de diferentes componentes para o desempenho global. Por exemplo, a transação "Read Heavy" produz um número significativo de operações de leitura a partir do disco.

| Tipo de Transação | Descrição |
| --- | --- |
| Ler Lite |SELECIONE; na memória; leitura-apenas |
| Ler Meio |SELECIONE; principalmente na memória; leitura-apenas |
| Ler Pesado |SELECIONE; principalmente não na memória; leitura-apenas |
| Atualizar Lite |ATUALIZAÇÃO; na memória; ler-escrever |
| Atualizar Pesado |ATUALIZAÇÃO; principalmente não na memória; ler-escrever |
| Inserir Lite |INSERIR; na memória; ler-escrever |
| Inserir Pesado |INSERIR; principalmente não na memória; ler-escrever |
| Eliminar |ELIMINAR; mistura de memória e não de memória; ler-escrever |
| CPU Pesado |SELECIONE; na memória; carga de CPU relativamente pesada; leitura-apenas |

### <a name="workload-mix"></a>Mistura de carga de trabalho

As transações são selecionadas aleatoriamente a partir de uma distribuição ponderada com a seguinte mistura geral. A mistura geral tem uma relação leitura/escrita de aproximadamente 2:1.

| Tipo de Transação | % da Mix |
| --- | --- |
| Ler Lite |35 |
| Ler Meio |20 |
| Ler Pesado |5 |
| Atualizar Lite |20 |
| Atualizar Pesado |3 |
| Inserir Lite |3 |
| Inserir Pesado |2 |
| Eliminar |2 |
| CPU Pesado |10 |

### <a name="users-and-pacing"></a>Utilizadores e ritmo

A carga de trabalho de referência é impulsionada de uma ferramenta que submete transações através de um conjunto de ligações para simular o comportamento de vários utilizadores simultâneos. Apesar de todas as ligações e transações serem geradas por máquinas, para a simplicidade referimo-nos a estas ligações como "utilizadores". Embora cada utilizador opere de forma independente de todos os outros utilizadores, todos os utilizadores realizam o mesmo ciclo de etapas abaixo indicado:

1. Estabeleça uma ligação à base de dados.
2. Repita até ser sinalizado para sair:
   - Selecione uma transação aleatoriamente (a partir de uma distribuição ponderada).
   - Execute a transação selecionada e meça o tempo de resposta.
   - Espere por um atraso no ritmo.
3. Feche a ligação da base de dados.
4. A saída.

O atraso de ritmo (na etapa 2c) é selecionado aleatoriamente, mas com uma distribuição que tem uma média de 1,0 segundos. Assim, cada utilizador pode, em média, gerar no máximo uma transação por segundo.

### <a name="scaling-rules"></a>Regras de escala

O número de utilizadores é determinado pelo tamanho da base de dados (em unidades de fator de escala). Há um utilizador para cada cinco unidades de fator de escala. Devido ao atraso no ritmo, um utilizador pode gerar, no máximo, uma transação por segundo, em média.

Por exemplo, uma base de dados de 500 (SF=500) terá 100 utilizadores e pode atingir uma taxa máxima de 100 TPS. Para impulsionar uma taxa de TPS mais elevada requer mais utilizadores e uma base de dados maior.

### <a name="measurement-duration"></a>Duração da medição

Um prazo de referência válido requer uma duração de medição de estado estável de pelo menos uma hora.

### <a name="metrics"></a>Métricas

As métricas-chave no benchmark são o tempo de entrada e resposta.

- A entrada é a medida de desempenho essencial no benchmark. A entrada é reportada em transações por unidade de tempo, contando todos os tipos de transações.
- O tempo de resposta é uma medida de previsibilidade do desempenho. O tempo de resposta varia com classe de serviço, com classes de serviço mais altas com um requisito de tempo de resposta mais rigoroso, como mostrado abaixo.

| Classe de Serviço | Medida de entrada | Requisito de tempo de resposta |
| --- | --- | --- |
| Premium |Transações por segundo |Percentil 95 a 0,5 segundos |
| Standard |Transações por minuto |Percentil 90 a 1,0 segundos |
| Básico |Transações por hora |80º percentil a 2,0 segundos |

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre tamanhos específicos de cálculo e escolhas de tamanho de armazenamento disponíveis para bases de dados únicas, consulte [os limites de recursos baseados em DTU baseados em Bases de Dados SQL para](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)bases de dados únicas .
- Para mais detalhes sobre tamanhos específicos de cálculo e escolhas de tamanho de armazenamento disponíveis para piscinas elásticas, consulte [os limites de recursos baseados em DTU baseados na Base de Dados SQL.](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)
