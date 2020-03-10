---
title: Arquitetura Azure Synapse Analytics (ex-SQL DW)
description: Saiba como o Azure Synapse Analytics (anteriormente SQL DW) combina processamento maciço paralelo (MPP) com o Armazenamento Azure para alcançar um alto desempenho e escalabilidade.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6e4b5bc6d30f6caa2809b7aa1e72be70ae12dbc1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396614"
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

![Arquitetura SQL Analytics](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

A SQL Analytics usa uma arquitetura baseada no nó. As aplicações ligam e emitem comandos T-SQL a um nó de Controlo, que é o ponto único de entrada para o SQL Analytics. O nó de Controlo funciona com o motor MPP, que otimiza as consultas para processamento paralelo, e depois passa as operações para os nós da Compute para fazer o seu trabalho em paralelo. 

Os nós computacionais armazenam todos os dados dos utilizadores no Armazenamento Azure e executam as consultas paralelas. O Serviço de Movimento de Dados (DMS) é um serviço interno ao nível do sistema que movimenta dados através dos nós, conforme necessário para executar consultas paralelas e devolver resultados precisos. 

Com armazenamento e computação dissociados, ao utilizar o SQL Analytics pode-se:

* Potência computacional de tamanho independente, independentemente das suas necessidades de armazenamento.
* Cresça ou diminua a potência da computação, dentro de um pool SQL (armazém de dados), sem dados em movimento.
* Pausa na capacidade da computação enquanto deixa os dados intactos, por isso só paga pelo armazenamento.
* Retomar a capacidade de computação durante as horas de funcionamento.

### <a name="azure-storage"></a>Storage do Azure

A SQL Analytics aproveita o Armazenamento Azure para manter os dados dos seus utilizadores seguros.  Uma vez que os seus dados são armazenados e geridos pelo Azure Storage, existe uma taxa separada para o seu consumo de armazenamento. Os dados são **distribuídos** para otimizar o desempenho do sistema. Pode escolher qual o padrão mais grave a utilizar para distribuir os dados quando definir a tabela. Estes padrões de sharding são suportados:

* Hash
* Round Robin
* Replicar

### <a name="control-node"></a>Nó de controlo

O nó de Controlo é o cérebro da arquitetura. É o front-end que interage com todas as ligações e aplicações. O motor MPP funciona no nó de Controlo para otimizar e coordenar consultas paralelas. Quando submete uma consulta T-SQL à SQL Analytics, o nó de Controlo transforma-o em consultas que correm contra cada distribuição em paralelo.

### <a name="compute-nodes"></a>Nósodes computados

Os nódosos Computacionais fornecem o poder computacional. Mapa de distribuição para os nódosos computacionais para processamento. À medida que paga por mais recursos de computação, a SQL Analytics remapeia as distribuições para os nós computados disponíveis. O número de nós computacionais varia de 1 a 60, e é determinado pelo nível de serviço para sQL Analytics.

Cada nó computacional tem um ID de nó que é visível nas vistas do sistema. Você pode ver o ID do nó computado procurando a coluna node_id em vistas do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista destas vistas do sistema, consulte [as vistas do sistema MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Serviço de Movimento de Dados
O Serviço de Movimento de Dados (DMS) é a tecnologia de transporte de dados que coordena o movimento de dados entre os nós da Compute. Algumas consultas requerem movimento de dados para garantir que as consultas paralelas retribuem resultados precisos. Quando o movimento de dados é necessário, o DMS garante que os dados certos estão no local certo. 

## <a name="distributions"></a>Distribuição

Uma distribuição é a unidade básica de armazenamento e processamento para consultas paralelas que funcionam em dados distribuídos. Quando o SQL Analytics executa uma consulta, o trabalho é dividido em 60 consultas menores que correm em paralelo. 

Cada uma das 60 consultas menores é de uma das distribuições de dados. Cada nó Compute gere uma ou mais das 60 distribuições. Um pool SQL com recursos de computação máximo tem uma distribuição por nó computacional. Um pool SQL com recursos mínimos de computação tem todas as distribuições em um nó de cálculo.  

## <a name="hash-distributed-tables"></a>Mesas distribuídas por hash
Uma tabela distribuída por hash pode oferecer o maior desempenho de consulta para juntas e agregações em mesas grandes. 

Para estoirar os dados numa tabela distribuída por hash, a SQL Analytics utiliza uma função hash para atribuir deterministicamente cada linha a uma distribuição. Na definição da tabela, uma das colunas é designada como coluna de distribuição. A função hash utiliza os valores na coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama seguinte ilustra como uma mesa completa (mesa não distribuída) é armazenada como uma tabela distribuída por hash. 

![Mesa distribuída](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Mesa distribuída")  

* Cada linha pertence a uma distribuição.  
* Um algoritmo de hash determinístico atribui cada linha a uma distribuição.  
* O número de linhas de mesa por distribuição varia como mostrado pelos diferentes tamanhos das tabelas.

Existem considerações de desempenho para a seleção de uma coluna de distribuição, tais como a distinção, a distorção de dados e os tipos de consultas que funcionam no sistema.

## <a name="round-robin-distributed-tables"></a>Mesas distribuídas por robin redondo
Uma mesa de robin redondo é a mesa mais simples para criar e proporciona um desempenho rápido quando usado como uma mesa de preparação para cargas.

Uma tabela distribuída por rodada distribui dados uniformemente por toda a mesa, mas sem qualquer otimização adicional. Uma distribuição é escolhida pela primeira vez aleatoriamente e, em seguida, os tampão de linhas são atribuídos a distribuições sequencialmente. É rápido carregar dados numa mesa de rodapé, mas o desempenho da consulta pode muitas vezes ser melhor com tabelas distribuídas por hash. A adesão às mesas de rodapé requer a remodelação dos dados, o que leva mais tempo.


## <a name="replicated-tables"></a>Tabelas Replicadas
Uma tabela replicada proporciona o desempenho de consulta mais rápido para pequenas tabelas.

Uma tabela que é replicada caches uma cópia completa da tabela em cada nó de cálculo. Consequentemente, replicar uma tabela remove a necessidade de transferir dados entre nós de cálculo antes de uma agregação ou agregação. As mesas replicadas são melhor utilizadas com pequenas mesas. É necessário armazenamento extra e há despesas adicionais que são incorridas na escrita de dados, que tornam as grandes tabelas impraticáveis.  

O diagrama abaixo mostra uma tabela replicada que é cached na primeira distribuição em cada nó de cálculo.  

![Mesa replicada](media/sql-data-warehouse-distributed-data/replicated-table.png "Mesa replicada") 

## <a name="next-steps"></a>Passos seguintes
Agora que sabe um pouco sobre o Azure Synapse, aprenda a criar rapidamente [um pool SQL](./sql-data-warehouse-get-started-provision.md) e carregue dados de [amostras.](./sql-data-warehouse-load-sample-databases.md) Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure](../azure-glossary-cloud-terminology.md) à medida que encontra terminologia nova. Ou olhe para alguns destes outros Recursos Synapse Azure.  

* [Histórias de sucesso de clientes](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
* [Blogues](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Pedidos de funcionalidades](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Criar pedido de suporte](./sql-data-warehouse-get-started-create-support-ticket.md)
* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* [Fórum do Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)

