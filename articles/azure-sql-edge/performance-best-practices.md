---
title: Melhores práticas de desempenho e diretrizes de configuração - Azure SQL Edge
description: Conheça as melhores práticas de desempenho e as diretrizes de configuração em Azure SQL Edge
keywords: SQL Edge, retenção de dados
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392016"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>Melhores práticas de desempenho e diretrizes de configuração

O Azure SQL Edge oferece várias funcionalidades e capacidades que podem ser usadas para melhorar o desempenho da sua implementação SQL Edge. Este artigo fornece algumas boas práticas e recomendações para maximizar o desempenho. 

## <a name="best-practices"></a>Melhores práticas 

### <a name="configure-multiple-tempdb-data-files"></a>Configure vários ficheiros de dados temporários

Azure SQL Edge por predefinição cria apenas um ficheiro de dados temporário como parte da inicialização do recipiente. Recomendamos que considere a criação de vários ficheiros de dados temporários após a implementação. Para obter mais informações, consulte as orientações do artigo, [Recomendações para reduzir a contenção de alocação na base de dados tempdb do SQL Server](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d).

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Utilizar índices de loja de colunas agrupados sempre que possível

Os dispositivos IoT e Edge tendem a gerar um elevado volume de dados que é tipicamente agregado ao longo de algum tempo para análise. As linhas de dados individuais raramente são usadas para qualquer análise. Os índices de loja de colunas são ideais para armazenar e consultar conjuntos de dados tão grandes. Este índice utiliza o armazenamento de dados baseado em colunas e o processamento de consultas para obter ganhos até 10 vezes o desempenho da consulta sobre o armazenamento tradicional orientado para a linha. Também pode obter ganhos até 10 vezes a compressão de dados sobre o tamanho de dados não comprimidos. Para mais informações, consulte [índices de loja de colunas](/sql/relational-databases/indexes/columnstore-indexes-overview)

Além disso, outras funcionalidades do Azure SQL Edge, como o streaming de dados e a retenção de dados, beneficiam das otimizações da loja de colunas em torno da inserção de dados e da remoção de dados. 

### <a name="simple-recovery-model"></a>Modelo de recuperação simples

Uma vez que o armazenamento pode ser limitado em dispositivos de borda, todas as bases de dados do utilizador em Azure SQL Edge utilizam o modelo de Recuperação Simples por padrão. O modelo de recuperação simples recupera automaticamente o espaço de log para manter os requisitos de espaço pequenos, essencialmente eliminando a necessidade de gerir o espaço de registo de transações. Em dispositivos de borda com armazenamento limitado disponível, isto pode ser útil. Para obter mais informações sobre o modelo de recuperação simples e outros modelos de recuperação disponíveis, consulte [Modelos de Recuperação](/sql/relational-databases/backup-restore/recovery-models-sql-server)

Operações como o Log Shipping e os restauros pontuais, que requerem cópias de segurança de registo de transações não são suportadas pelo modelo simples de recuperação.  

## <a name="advanced-configuration"></a>Configuração avançada 

### <a name="setting-memory-limits"></a>Definição de limites de memória

O Azure SQL Edge suporta até 64 GB de memória para o conjunto tampão, enquanto a memória adicional pode ser necessária por processos de satélite que estejam a decorrer dentro do recipiente SQL Edge. Em dispositivos de borda mais pequena com memória limitada, é aconselhável limitar a memória disponível aos recipientes SQL Edge, de modo a que o hospedeiro e outros processos ou módulos de borda possam funcionar corretamente. A memória total disponível para SQL Edge pode ser controlada utilizando os seguintes mecanismos. 

- Limitação da memória disponível para os recipientes sql edge: A memória total disponível para o recipiente SQL Edge pode ser limitada utilizando a opção de configuração do tempo de funcionamento do contentor `--memory` . Para obter mais informações sobre a limitação da memória disponível para o recipiente SQL Edge, consulte [as opções de tempo de execução com memória, CPUs e GPUs](https://docs.docker.com/config/containers/resource_constraints/).

- Limitação da memória disponível para o processo SQL dentro do recipiente: Por padrão, o processo SQL dentro do recipiente utiliza apenas 80% da RAM física disponível. Para a maioria das implementações, a configuração predefinida será boa. No entanto, pode haver cenários em que poderá ser necessária memória adicional para o streaming de dados e os processos ONNX que correm dentro dos contentores SQL Edge. Nesses cenários, a memória disponível para o processo SQL pode ser controlada utilizando a `memory.memorylimitmb` definição no ficheiro mssql-conf. Para obter mais informações, consulte [o Configure utilizando o ficheiro mssql.conf](configure.md#configure-by-using-an-mssqlconf-file).

Ao definir os limites de memória, tenha cuidado para não definir este valor demasiado baixo. Se não definir memória suficiente para o processo SQL, pode impactar severamente o desempenho do SQL.

### <a name="delayed-durability"></a>Durabilidade atrasada

As transações em Azure SQL Edge podem ser totalmente duráveis, o padrão do SQL Server ou o atraso durável (também conhecido como compromisso preguiçoso).

Os compromissos de transação totalmente duráveis são sincronizados e reportam um compromisso como bem sucedido e devolvam o controlo ao cliente apenas após os registos de registo da transação serem escritos em disco. Os compromissos de transações duradouras retardados são assíncronos e reportam um compromisso como bem sucedido antes que os registos de registo da transação sejam escritos em disco. A escrita das entradas de registo de transações no disco é necessária para que uma transação seja duradoura. As transações duradouras atrasadas tornam-se duráveis quando as entradas de registo de transações são lavadas para o disco. 

Em implementações onde **alguma perda de dados** pode ser tolerada ou em dispositivos de borda com armazenamento lento, a durabilidade retardada pode ser usada para otimizar a ingestão de dados e a limpeza baseada na retenção de dados. Para obter mais informações, consulte [a Durabilidade da Transação de Controlo.](/sql/relational-databases/logs/control-transaction-durability)


### <a name="linux-os-configurations"></a>Configurações linux OS 

Considere utilizar as seguintes [definições de configuração do Sistema Operativo Linux](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) para experimentar o melhor desempenho para uma instalação SQL.