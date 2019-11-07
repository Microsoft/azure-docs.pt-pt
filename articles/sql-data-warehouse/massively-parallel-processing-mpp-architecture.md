---
title: Arquitetura do Azure Synapse Analytics (anteriormente conhecido como SQL DW)
description: Saiba como o Azure Synapse Analytics (anteriormente conhecido como SQL DW) combina o processamento paralelo maciço (MPP) com o armazenamento do Azure para obter alto desempenho e escalabilidade.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: ea9629c63fcab97ba8ba83cd88592c37ae41818a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646394"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Arquitetura do Azure Synapse Analytics (anteriormente conhecido como SQL DW) 

O Azure Synapse é um serviço de análise ilimitado que reúne conjunto de dados corporativos e análise de Big Data. Ele oferece a você a liberdade de consultar dados sobre seus termos, usando recursos sem servidor sob demanda ou provisionados, em escala. O Azure Synapse traz esses dois mundos junto com uma experiência unificada para ingerir, preparar, gerenciar e fornecer dados para necessidades imediatas de BI e aprendizado de máquina.

 O Azure Synapse tem quatro componentes:
- Análise de SQL: concluir análise baseada em T-SQL 
    - Pool do SQL (pague por DWU provisionado) – geralmente disponível
    - SQL sob demanda (pagamento por TB processado) – (versão prévia)
- Spark: Apache Spark profundamente integrados (versão prévia) 
- Integração de dados: integração de dados híbridas (versão prévia)
- Studio: experiência do usuário unificada.  (Pré-visualização)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>Componentes da arquitetura do SQL Analytics MPP

A [análise de SQL](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) aproveita uma arquitetura de expansão para distribuir o processamento computacional de dados em vários nós. A unidade de escala é uma abstração da potência de computação que é conhecida como uma [unidade de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). A computação é separada do armazenamento, o que permite que você dimensione a computação independentemente dos dados em seu sistema.

![Arquitetura de análise de SQL](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

O SQL Analytics usa uma arquitetura baseada em nó. Os aplicativos conectam e emitem comandos T-SQL para um nó de controle, que é o único ponto de entrada para a análise de SQL. O nó de controle executa o mecanismo MPP que otimiza as consultas para processamento paralelo e, em seguida, passa as operações para nós de computação para realizar seu trabalho em paralelo. 

Os nós de computação armazenam todos os dados do usuário no armazenamento do Azure e executam as consultas paralelas. O serviço de movimentação de dados (DMS) é um serviço interno de nível de sistema que move dados entre os nós conforme necessário para executar consultas em paralelo e retornar resultados precisos. 

Com o armazenamento e a computação separados, ao usar a análise do SQL, é possível:

* Capacidade de computação de tamanho independente, independentemente de suas necessidades de armazenamento.
* Aumente ou reduza a potência de computação em um pool do SQL (data warehouse), sem mover os dados.
* Pause a capacidade de computação deixando os dados intactos, para que você pague apenas pelo armazenamento.
* Retomar a capacidade de computação durante as horas de funcionamento.

### <a name="azure-storage"></a>Storage do Azure

A análise de SQL aproveita o armazenamento do Azure para manter os dados do usuário seguros.  Como os dados são armazenados e gerenciados pelo armazenamento do Azure, há um encargo separado para o consumo de armazenamento. Os dados em si são fragmentados em **distribuições** para otimizar o desempenho do sistema. Você pode escolher qual padrão de fragmentação usar para distribuir os dados ao definir a tabela. Esses padrões de fragmentação têm suporte:

* Hash
* Round Robin
* Replicar

### <a name="control-node"></a>Nó de controle

O nó de controle é o cérebro da arquitetura. É o front-end que interage com todas as ligações e aplicações. O mecanismo MPP é executado no nó de controle para otimizar e coordenar consultas paralelas. Quando você envia uma consulta T-SQL para a análise do SQL, o nó de controle a transforma em consultas executadas em cada distribuição em paralelo.

### <a name="compute-nodes"></a>Nós de computação

Os nós de computação fornecem a potência computacional. As distribuições são mapeadas para nós de computação para processamento. À medida que você paga mais recursos de computação, a análise do SQL mapeia novamente as distribuições para os nós de computação disponíveis. O número de nós de computação varia de 1 a 60 e é determinado pelo nível de serviço para análise de SQL.

Cada nó de computação tem uma ID de nó visível nas exibições do sistema. Você pode ver a ID do nó de computação procurando a coluna node_id nas exibições do sistema cujos nomes começam com sys. pdw_nodes. Para obter uma lista dessas exibições do sistema, consulte [exibições do sistema MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Serviço de movimentação de dados
O serviço de movimentação de dados (DMS) é a tecnologia de transporte de dados que coordena a movimentação de dados entre os nós de computação. Algumas consultas exigem a movimentação de dados para garantir que as consultas paralelas retornem resultados precisos. Quando a movimentação de dados é necessária, DMS garante que os dados corretos cheguem ao local certo. 

## <a name="distributions"></a>Distribuições

Uma distribuição é a unidade básica de armazenamento e processamento para consultas paralelas que são executadas em dados distribuídos. Quando o SQL Analytics executa uma consulta, o trabalho é dividido em 60 consultas menores que são executadas em paralelo. 

Cada uma das 60 consultas menores é executada em uma das distribuições de dados. Cada nó de computação gerencia uma ou mais das distribuições 60. Um pool SQL com recursos de computação máximo tem uma distribuição por nó de computação. Um pool SQL com recursos de computação mínimos tem todas as distribuições em um nó de computação.  

## <a name="hash-distributed-tables"></a>Tabelas distribuídas por hash
Uma tabela distribuída por hash pode fornecer o melhor desempenho de consulta para junções e agregações em tabelas grandes. 

Para fragmentar dados em uma tabela distribuída por hash, a análise do SQL usa uma função de hash para atribuir de forma determinística cada linha a uma distribuição. Na definição de tabela, uma das colunas é designada como a coluna de distribuição. A função de hash usa os valores na coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama a seguir ilustra como uma tabela completa (não distribuída) é armazenada como uma tabelas distribuídas por hash. 

![Tabela distribuída](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabela distribuída")  

* Cada linha pertence a uma distribuição.  
* Um algoritmo de hash determinístico atribui cada linha a uma distribuição.  
* O número de linhas de tabela por distribuição varia conforme mostrado pelos diferentes tamanhos de tabelas.

Há considerações de desempenho para a seleção de uma coluna de distribuição, como distinção, distorção de dados e os tipos de consultas executadas no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas Round Robin
Uma tabela Round Robin é a tabela mais simples de criar e fornecer desempenho rápido quando usada como uma tabela de preparo para cargas.

Uma tabela distribuída por Round Robin distribui os dados uniformemente na tabela, mas sem nenhuma otimização adicional. Uma distribuição é escolhida primeiro aleatoriamente e, em seguida, os buffers de linhas são atribuídos às distribuições sequencialmente. É rápido carregar dados em uma tabela Round Robin, mas o desempenho da consulta geralmente pode ser melhor com tabelas distribuídas por hash. Junções em tabelas Round Robin exigem dados embaralhando e isso leva mais tempo.


## <a name="replicated-tables"></a>Tabelas replicadas
Uma tabela replicada fornece o desempenho de consulta mais rápido para tabelas pequenas.

Uma tabela replicada armazena em cache uma cópia completa da tabela em cada nó de computação. Consequentemente, a replicação de uma tabela elimina a necessidade de transferir dados entre nós de computação antes de uma junção ou agregação. As tabelas replicadas são melhor utilizadas com tabelas pequenas. O armazenamento extra é necessário e há sobrecarga adicional incorrida ao gravar dados que tornam as tabelas grandes impraticável.  

O diagrama a seguir mostra uma tabela replicada que é armazenada em cache na primeira distribuição em cada nó de computação.  

![Tabela replicada](media/sql-data-warehouse-distributed-data/replicated-table.png "Tabela replicada") 

## <a name="next-steps"></a>Passos seguintes
Agora que você já sabe um pouco sobre o Azure Synapse, saiba como [criar rapidamente um pool do SQL][create a SQL pool] e [carregar dados de exemplo][load sample data]. Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure][Azure glossary] à medida que encontra terminologia nova. Ou então, veja alguns desses outros recursos do Azure Synapse.  

* [Histórias de sucesso de clientes]
* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da Equipa Customer Advisory]
* [Criar pedido de suporte]
* [Fórum do MSDN]
* [Fórum do Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar pedido de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL pool]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
