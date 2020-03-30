---
title: Arquitetura Azure Synapse Analytics (ex-SQL DW)
description: Saiba como o Azure Synapse Analytics (anteriormente SQL DW) combina processamento maciço paralelo (MPP) com o Armazenamento Azure para alcançar um alto desempenho e escalabilidade.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f389bb83f63ac97dd10d6366bd37981faf4a9e11
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346546"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Arquitetura Azure Synapse Analytics (ex-SQL DW) 

O Azure Synapse é um serviço de análise ilimitado que junta o armazenamento de dados empresariais e a análise de macrodados. Dá-lhe a liberdade de consultar dados nos seus termos, através de recursos a pedido ou aprovisionados sem servidor, em escala. A Azure Synapse reúne estes dois mundos com uma experiência unificada para ingerir, preparar, gerir e servir dados para as necessidades imediatas de BI e machine learning.

 Azure Synapse tem quatro componentes:
- SQL Analytics: Análise completa baseada em T-SQL 
    - Piscina SQL (pagamento por DWU provisionado) – Geralmente Disponível
    - SQL on-demand (pagamento por TB processado) – (Pré-visualização)
- Faísca: Faísca Apache profundamente integrada (Pré-visualização)
- Integração de Dados: Integração de dados híbridos (Pré-visualização)
- Estúdio: experiência unificada do utilizador.  (Pré-visualização)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>Componentes de arquitetura MPP sQL Analytics

[A SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) aproveita uma arquitetura de escala para distribuir o processamento computacional de dados em vários nódosos. A unidade de escala é uma abstração do poder da computação que é conhecida como uma unidade de armazém de [dados.](what-is-a-data-warehouse-unit-dwu-cdwu.md) A computação é separada do armazenamento, o que lhe permite escalar a computação independentemente dos dados do seu sistema.

![Arquitetura SQL Analytics](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

A SQL Analytics usa uma arquitetura baseada no nó. As aplicações ligam e emitem comandos T-SQL a um nó de Controlo, que é o ponto único de entrada para o SQL Analytics. O nó de Controlo funciona com o motor MPP, que otimiza as consultas para processamento paralelo, e depois passa as operações para os nós da Compute para fazer o seu trabalho em paralelo. 

Os nós de computação armazenam todos os dados de utilizador no Armazenamento do Microsoft Azure e executam as consultas paralelas. O Serviço de Movimento de Dados (DMS – Data Movement Service) é um serviço interno ao nível do sistema que move os dados em todos os nós, conforme necessário, para executar consultas em paralelo e devolver resultados precisos. 

Com armazenamento e computação dissociados, ao utilizar o SQL Analytics pode-se:

* Potência computacional de tamanho independente, independentemente das suas necessidades de armazenamento.
* Cresça ou diminua a potência da computação, dentro de um pool SQL (armazém de dados), sem dados em movimento.
* Colocar a capacidade de computação em pausa, mantendo os dados intactos, pelo que só paga pelo armazenamento.
* Retomar a capacidade de computação durante as horas de funcionamento.

### <a name="azure-storage"></a>Storage do Azure

A SQL Analytics aproveita o Armazenamento Azure para manter os dados dos seus utilizadores seguros.  Uma vez que os seus dados são armazenados e geridos pelo Azure Storage, existe uma taxa separada para o seu consumo de armazenamento. Os dados são **distribuídos** para otimizar o desempenho do sistema. Pode escolher qual o padrão mais grave a utilizar para distribuir os dados quando definir a tabela. Estes padrões de sharding são suportados:

* Hash
* Round Robin
* Replicar

### <a name="control-node"></a>Nó de controlo

O nó de Controlo é o cérebro da arquitetura. É o front-end que interage com todas as ligações e aplicações. O motor de MPP é executado no nó de Controlo para otimizar e coordenar as consultas paralelas. Quando submete uma consulta T-SQL à SQL Analytics, o nó de Controlo transforma-o em consultas que correm contra cada distribuição em paralelo.

### <a name="compute-nodes"></a>Nós de computação

Os nós de computação conferem poder de computação. Mapa de distribuição para os nódosos computacionais para processamento. À medida que paga por mais recursos de computação, a SQL Analytics remapeia as distribuições para os nós computados disponíveis. O número de nós computacionais varia de 1 a 60, e é determinado pelo nível de serviço para sQL Analytics.

Cada nó computacional tem um ID de nó que é visível nas vistas do sistema. Você pode ver o ID do nó computado procurando a coluna node_id em vistas do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista destas vistas do sistema, consulte [as vistas do sistema MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Serviço de Movimento de Dados
O Serviço de Movimento de Dados (DMS) é a tecnologia de transporte de dados que coordena o movimento de dados entre os nós da Compute. Algumas consultas requerem movimento de dados para garantir que as consultas paralelas retribuem resultados precisos. Quando o movimento de dados é necessário, o DMS garante que os dados certos estão no local certo. 

## <a name="distributions"></a>Distribuição

As distribuições são as unidades básicas de armazenamento e processamento de consultas paralelas que são executadas em dados distribuídos. Quando o SQL Analytics executa uma consulta, o trabalho é dividido em 60 consultas menores que correm em paralelo. 

Cada uma das 60 consultas menores é de uma das distribuições de dados. Cada nó Compute gere uma ou mais das 60 distribuições. Um pool SQL com recursos de computação máximo tem uma distribuição por nó computacional. Um pool SQL com recursos mínimos de computação tem todas as distribuições em um nó de cálculo.  

## <a name="hash-distributed-tables"></a>Tabelas distribuídas com hash
Uma tabela distribuída com hash pode proporcionar o mais elevado desempenho de consulta para associações e agregações em tabelas grandes. 

Para estoirar os dados numa tabela distribuída por hash, a SQL Analytics utiliza uma função hash para atribuir deterministicamente cada linha a uma distribuição. Na definição da tabela, uma das colunas será a coluna de distribuição. A função hash utiliza os valores da coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama seguinte ilustra como uma mesa completa (mesa não distribuída) é armazenada como uma tabela distribuída por hash. 

![Mesa distribuída](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Mesa distribuída")  

* Cada linha pertence a uma distribuição.  
* Um algoritmo de hash determinístico atribui cada linha a uma distribuição.  
* O número de linhas de mesa por distribuição varia como mostrado pelos diferentes tamanhos das tabelas.

Existem considerações de desempenho para a seleção de uma coluna de distribuição, tais como a distinção, a distorção de dados e os tipos de consultas que funcionam no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas com round robin
Uma mesa de robin redondo é a mesa mais simples para criar e proporciona um desempenho rápido quando usado como uma mesa de preparação para cargas.

As tabelas distribuídas com round robin distribuem uniformemente os dados por uma tabela, mas sem qualquer otimização adicional. Uma distribuição é escolhida pela primeira vez aleatoriamente e, em seguida, os tampão de linhas são atribuídos a distribuições sequencialmente. O carregamento de dados para tabelas round robin é rápido, mas o desempenho das consultas é, muitas vezes, superior se as tabelas forem distribuídas com hash. A adesão às mesas de rodapé requer a remodelação dos dados, o que leva mais tempo.


## <a name="replicated-tables"></a>Tabelas replicadas
As tabelas replicadas proporcionam o desempenho de consulta mais rápido para tabelas pequenas.

Uma tabela que é replicada caches uma cópia completa da tabela em cada nó de cálculo. Por conseguinte, a replicação de uma tabela elimina a necessidade de transferir dados entre nós de computação antes de se proceder a uma associação ou agregação. Idealmente, as tabelas replicadas devem ser utilizadas com tabelas pequenas. É necessário armazenamento extra e há despesas adicionais que são incorridas na escrita de dados, que tornam as grandes tabelas impraticáveis.  

O diagrama abaixo mostra uma tabela replicada que é cached na primeira distribuição em cada nó de cálculo.  

![Tabela replicada](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Tabela replicada") 

## <a name="next-steps"></a>Passos seguintes
Agora que sabe um pouco sobre o Azure Synapse, aprenda a criar rapidamente [um pool SQL](create-data-warehouse-portal.md) e carregue dados de [amostras.](load-data-from-azure-blob-storage-using-polybase.md) Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure](../../azure-glossary-cloud-terminology.md) quando se deparar com terminologia nova. Ou olhe para alguns destes outros Recursos Synapse Azure.  

* [Histórias de sucesso de clientes](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
* [Blogues](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Pedidos de funcionalidades](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Criar pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md)
* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* [Fórum do Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)

