---
title: Níveis de serviço - modelo de compra baseado em DTU
description: Conheça os níveis de serviço no modelo de compra baseado em DTU para a Azure SQL Database para fornecer tamanhos de computação e armazenamento.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 10/15/2020
ms.reviewer: ''
ms.openlocfilehash: 19178359d1eeb935499a01828f7c53b123e17571
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793182"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Escalões de serviço no modelo de compra baseado em DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Os níveis de serviço no modelo de compra baseado em DTU são diferenciados por uma gama de tamanhos de cálculo com uma quantidade fixa de armazenamento incluído, período de retenção fixo para backups e preço fixo. Todos os níveis de serviço do modelo de compra baseado em DTU proporcionam flexibilidade de tamanhos de computação em mudança com tempo mínimo [de inatividade;](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) no entanto, há um interruptor ao longo do período em que a conectividade é perdida para a base de dados por um curto período de tempo, que pode ser atenuada usando a lógica de retrip. Bases de dados únicas e piscinas elásticas são faturadas por hora com base no nível de serviço e no tamanho do cálculo.

> [!IMPORTANT]
> [A Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) não suporta um modelo de compra baseado em DTU. 


> [!NOTE]
> Para obter informações sobre os níveis de serviço baseados em vCore, consulte [os níveis de serviço baseados em vCore](service-tiers-vcore.md). Para obter informações sobre a diferenciação dos níveis de serviço baseados em DTU e os níveis de serviço baseados em vCore, consulte [os modelos de compra](purchasing-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Compare os níveis de serviço baseados em DTU

A escolha de um nível de serviço depende principalmente da continuidade do negócio, armazenamento e requisitos de desempenho.

||Básica|Standard|Premium|
| :-- | --: |--:| --:|
|**Carga de trabalho de destino**|Desenvolvimento e produção|Desenvolvimento e produção|Desenvolvimento e produção|
|**Uptime SLA**|99,99%|99,99%|99,99%|
|**Retenção máxima de backup**|7 dias|35 dias|35 dias|
|**CPU**|Baixo|Baixo, Médio, Alto|Médio, Alto|
|**IOPS (aproximadamente)**\* |1-4 IOPS por DTU| 1-4 IOPS por DTU | 25 IOPS por DTU|
|**Latência IO (aproximada)**|5 ms (ler), 10 ms (escrever)|5 ms (ler), 10 ms (escrever)|2 ms (ler/escrever)|
|**Indexação de colunas** |N/D|S3 e acima|Suportado|
|**OLTP dentro da memória**|N/D|N/D|Suportado|

\* Todos leiam e escrevam IOPS contra ficheiros de dados, incluindo IO de fundo (checkpoint e escritor preguiçoso)

> [!IMPORTANT]
> Os objetivos de serviço Básico, S0, S1 e S2 fornecem menos de um vCore (CPU).  Para cargas de trabalho intensivas da CPU, recomenda-se um objetivo de serviço de S3 ou superior. 
>
> Nos objetivos de serviço Basic, S0 e S1, os ficheiros de base de dados são armazenados no Azure Standard Storage, que utiliza suportes de armazenamento baseados em discos rígidos (HDD). Estes objetivos de serviço são mais adequados para o desenvolvimento, testes e outras cargas de trabalho pouco frequentes que são menos sensíveis à variabilidade do desempenho.
>

> [!TIP]
> Para ver os limites reais [de governação dos recursos](resource-limits-logical-server.md#resource-governance) para uma base de dados ou piscina elástica, consulte a vista [sys.dm_user_db_resource_governance.](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)

> [!NOTE]
> Você pode obter uma base de dados gratuita na Base de Dados Azure SQL no nível de serviço Básico em conjunto com uma conta gratuita Azure para explorar Azure. Para obter informações, consulte [criar uma base de dados de nuvem gerida com a sua conta gratuita Azure.](https://azure.microsoft.com/free/services/sql-database/)

## <a name="single-database-dtu-and-storage-limits"></a>DTU de base de dados única e limites de armazenamento

Os tamanhos de cálculo são expressos em termos de Unidades de Transação de Dados de Base de Dados (DTUs) para bases de dados únicas e unidades elásticas de transação de bases de dados (eDTUs) para piscinas elásticas. Para mais informações sobre DTUs e eDTUs, consulte [o modelo de compra baseado em DTU.](purchasing-models.md#dtu-based-purchasing-model)

||Básica|Standard|Premium|
| :-- | --: | --: | --: |
| **Tamanho máximo de armazenamento** | 2 GB | 1 TB | 4 TB  |
| **DTUs máximos** | 5 | 3.000 | 4000 |

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>EDTU elástico de piscina, armazenamento e limites de base de dados em conjunto

|| **Básica** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| **Tamanho máximo de armazenamento por base de dados**  | 2 GB | 1 TB | 1 TB |
| **Tamanho máximo de armazenamento por piscina** | 156 GB | 4 TB | 4 TB |
| **Máximo de EDTUs por base de dados** | 5 | 3.000 | 4000 |
| **Máximo de EDTUs por piscina** | 1600 | 3.000 | 4000 |
| **Número máximo de bases de dados por piscina** | 500  | 500 | 100 |

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China Leste, China Norte, Alemanha Central e Alemanha Nordeste. Nestas regiões, o máximo de armazenamento no nível Premium é limitado a 1 TB.  Para obter mais informações, consulte [as limitações atuais do P11-P15.](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)  
> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

## <a name="dtu-benchmark"></a>DTU Benchmark

As características físicas (CPU, memória, IO) associadas a cada medida DTU são calibradas usando uma referência que simula a carga de trabalho da base de dados do mundo real.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Correlacionar resultados de referência com o desempenho da base de dados do mundo real

É importante compreender que todos os critérios de referência são apenas representativos e indicativos. As taxas de transação alcançadas com a aplicação de referência não serão as mesmas que podem ser alcançadas com outras aplicações. O benchmark compreende uma coleção de diferentes tipos de transações que se desrespõem a um esquema que contém uma gama de tabelas e tipos de dados. Embora o benchmark exesesse as mesmas operações básicas que são comuns a todas as cargas de trabalho OLTP, não representa nenhuma classe específica de base de dados ou aplicação. O objetivo do benchmark é fornecer um guia razoável para o desempenho relativo de uma base de dados que pode ser esperado ao escalar para cima ou para baixo entre tamanhos de cálculo. Na realidade, as bases de dados são de diferentes tamanhos e complexidade, encontram diferentes misturas de cargas de trabalho, e responderão de diferentes maneiras. Por exemplo, uma aplicação intensiva de IO pode atingir os limiares de IO mais cedo, ou uma aplicação intensiva de CPU pode atingir os limites do CPU mais cedo. Não há garantias de que uma determinada base de dados se escale da mesma forma que o benchmark sob o aumento da carga.

O benchmark e a sua metodologia são descritos em mais pormenor abaixo.

### <a name="benchmark-summary"></a>Resumo de referência

O benchmark mede o desempenho de uma mistura de operações básicas de base de dados que ocorrem mais frequentemente em cargas de trabalho de processamento de transações online (OLTP). Embora o benchmark seja projetado com a computação em nuvem em mente, o esquema de base de dados, a população de dados e transações foram projetados para ser amplamente representativo dos elementos básicos mais utilizados nas cargas de trabalho do OLTP.

### <a name="schema"></a>Esquema

O esquema foi concebido para ter variedade e complexidade suficientes para suportar uma ampla gama de operações. O benchmark vai contra uma base de dados composta por seis tabelas. As tabelas enquadram-se em três categorias: tamanho fixo, escala e crescimento. Existem duas tabelas de tamanho fixo; três mesas de escalonamento; e uma mesa de cultivo. As tabelas de tamanho fixo têm um número constante de linhas. As tabelas de escala têm uma cardinalidade proporcional ao desempenho da base de dados, mas não muda durante o referencial. A tabela de cultivo é dimensionada como uma mesa de escalonamento na carga inicial, mas depois a cardinalidade muda no decorrer da execução do benchmark à medida que as linhas são inseridas e apagadas.

O esquema inclui uma mistura de tipos de dados, incluindo inteiros, numéricos, carácter e data/hora. O esquema inclui chaves primárias e secundárias, mas não quaisquer chaves estrangeiras - ou seja, não existem restrições de integridade referencial entre tabelas.

Um programa de geração de dados gera os dados para a base de dados inicial. Os dados inteiros e numéricos são gerados com várias estratégias. Em alguns casos, os valores são distribuídos aleatoriamente por um intervalo. Noutros casos, um conjunto de valores é permutado aleatoriamente para garantir a manutenção de uma distribuição específica. Os campos de texto são gerados a partir de uma lista ponderada de palavras para produzir dados realistas.

A base de dados é dimensionada com base num "fator de escala". O fator de escala (abreviado como SF) determina a cardinalidade das tabelas de escala e crescimento. Como descrito abaixo na secção Utilizadores e Pacing, o tamanho da base de dados, o número de utilizadores e o desempenho máximo em proporção uns aos outros.

### <a name="transactions"></a>Transações

A carga de trabalho consiste em nove tipos de transações, conforme indicado no quadro abaixo. Cada transação foi concebida para destacar um conjunto específico de características do sistema no motor de base de dados e hardware do sistema, com elevado contraste das outras transações. Esta abordagem facilita a avaliação do impacto dos diferentes componentes no desempenho global. Por exemplo, a transação "Read Heavy" produz um número significativo de operações de leitura a partir do disco.

| Tipo de Transação | Descrição |
| --- | --- |
| Ler Lite |SELECIONE; na memória; read-only |
| Ler Médio |SELECIONE; principalmente na memória; read-only |
| Ler Pesado |SELECIONE; principalmente não na memória; read-only |
| Atualizar Lite |ATUALIZAÇÃO; na memória; ler-escrever |
| Atualizar Pesado |ATUALIZAÇÃO; principalmente não na memória; ler-escrever |
| Inserir Lite |INSERIR; na memória; ler-escrever |
| Inserir Pesado |INSERIR; principalmente não na memória; ler-escrever |
| Eliminar |EXCLUIR; mistura de memória e não na memória; ler-escrever |
| CPU Pesado |SELECIONE; na memória; Carga de CPU relativamente pesada; read-only |

### <a name="workload-mix"></a>Mistura de carga de trabalho

As transações são selecionadas aleatoriamente a partir de uma distribuição ponderada com a seguinte mistura global. A mistura geral tem uma relação de leitura/escrita de aproximadamente 2:1.

| Tipo de Transação | % do Mix |
| --- | --- |
| Ler Lite |35 |
| Ler Médio |20 |
| Ler Pesado |5 |
| Atualizar Lite |20 |
| Atualizar Pesado |3 |
| Inserir Lite |3 |
| Inserir Pesado |2 |
| Eliminar |2 |
| CPU Pesado |10 |

### <a name="users-and-pacing"></a>Utilizadores e ritmo

A carga de trabalho de referência é impulsionada a partir de uma ferramenta que submete transações através de um conjunto de ligações para simular o comportamento de vários utilizadores simultâneos. Apesar de todas as ligações e transações serem geradas por máquinas, para simplificar, referimo-nos a estas ligações como "utilizadores". Embora cada utilizador funcione independentemente de todos os outros utilizadores, todos os utilizadores executam o mesmo ciclo de etapas abaixo indicado:

1. Estabeleça uma ligação de base de dados.
2. Repita até que o sinal para a saída:
   - Selecione uma transação aleatoriamente (a partir de uma distribuição ponderada).
   - Efetue a transação selecionada e meça o tempo de resposta.
   - Espere um atraso no ritmo.
3. Feche a ligação da base de dados.
4. A saída.

O atraso de ritmo (na etapa 2c) é selecionado aleatoriamente, mas com uma distribuição que tem uma média de 1,0 segundos. Assim, cada utilizador pode, em média, gerar no máximo uma transação por segundo.

### <a name="scaling-rules"></a>Regras de escalonamento

O número de utilizadores é determinado pelo tamanho da base de dados (em unidades de fator de escala). Há um utilizador para cada cinco unidades de fator de escala. Devido ao atraso no ritmo, um utilizador pode gerar no máximo uma transação por segundo, em média.

Por exemplo, uma base de dados de 500 (SF=500) terá 100 utilizadores e pode atingir uma taxa máxima de 100 TPS. Para impulsionar uma taxa de TPS mais alta requer mais utilizadores e uma base de dados maior.

### <a name="measurement-duration"></a>Duração da medição

Uma execução de referência válida requer uma duração de medição de estado estável de pelo menos uma hora.

### <a name="metrics"></a>Métricas

As métricas-chave no benchmark são o tempo de produção e resposta.

- A produção é a medida de desempenho essencial no benchmark. A produção é reportada em transações por unidade de tempo, contando todos os tipos de transação.
- O tempo de resposta é uma medida de previsibilidade do desempenho. A restrição de tempo de resposta varia com classe de serviço, com classes de serviço mais altas tendo um requisito de tempo de resposta mais rigoroso, como mostrado abaixo.

| Classe de Serviço | Medida de produção | Requisito de tempo de resposta |
| --- | --- | --- |
| Premium |Transações por segundo |Percentil 95 a 0,5 segundos |
| Standard |Transações por minuto |Percentil 90 a 1,0 segundos |
| Básico |Transações por hora |Percentil 80 a 2,0 segundos |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre tamanhos específicos de cálculo e opções de tamanho de armazenamento disponíveis para bases de dados individuais, consulte [os limites de recursos baseados em DTU da Base de Dados SQL para bases de dados individuais.](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Para obter detalhes sobre tamanhos específicos de computação e opções de tamanho de armazenamento disponíveis para piscinas elásticas, consulte [os limites de recursos baseados em DTU da Base de Dados SQL](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).