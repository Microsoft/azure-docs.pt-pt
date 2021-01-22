---
title: Arquitetura dedicada à piscina SQL (anteriormente SQL DW)
description: Saiba como a piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics combina capacidades de processamento de consulta distribuídas com o Azure Storage para alcançar um alto desempenho e escalabilidade.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0e87451531750e502f67dc30e6fbd26c8c944d22
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678598"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Arquitetura dedicada da piscina SQL (anteriormente SQL DW) em Azure Synapse Analytics

O Azure Synapse Analytics é um serviço de análise que combina o armazenamento de dados empresariais e a análise de Macrodados. Dá-lhe a liberdade de consultar dados sobre os seus termos.

> [!NOTE]
>Explore a [documentação Azure Synapse Analytics](../overview-what-is.md).
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Componentes de arquitetura Sinapse SQL

[O pool de SQL dedicado (anteriormente SQL DW)](sql-data-warehouse-overview-what-is.md) aproveita uma arquitetura de escala para distribuir o processamento computacional de dados através de múltiplos nós. A unidade de escala é uma abstração do poder computacional que é conhecida como uma [unidade de armazém de dados.](what-is-a-data-warehouse-unit-dwu-cdwu.md) O cálculo é separado do armazenamento, o que lhe permite escalar o cálculo independentemente dos dados do seu sistema.

![Arquitetura dedicada à piscina SQL (anteriormente SQL DW)](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Piscina de SQL dedicada (anteriormente SQL DW) usa uma arquitetura baseada em nó. As aplicações ligam e emitem comandos T-SQL a um nó de controlo. O nó de Controlo acolhe o motor de consulta distribuído, que otimiza consultas para processamento paralelo, e depois passa as operações para os nós compute para fazer o seu trabalho em paralelo.

Os nós de computação armazenam todos os dados de utilizador no Armazenamento do Microsoft Azure e executam as consultas paralelas. O Serviço de Movimento de Dados (DMS – Data Movement Service) é um serviço interno ao nível do sistema que move os dados em todos os nós, conforme necessário, para executar consultas em paralelo e devolver resultados precisos.

Com armazenamento e cálculo dissociados, quando se utiliza uma piscina SQL dedicada (anteriormente SQL DW) pode-se:

- Potência de computação de tamanho independente, independentemente das suas necessidades de armazenamento.
- Cresça ou encolhe a potência de computação, dentro de uma piscina SQL dedicada (anteriormente SQL DW), sem dados móveis.
- Colocar a capacidade de computação em pausa, mantendo os dados intactos, pelo que só paga pelo armazenamento.
- Retomar a capacidade de computação durante as horas de funcionamento.

### <a name="azure-storage"></a>Storage do Azure

A piscina SQL dedicada SQL (anteriormente SQL DW) aproveita o Azure Storage para manter os dados do utilizador seguros.  Uma vez que os seus dados são armazenados e geridos pela Azure Storage, existe uma taxa separada para o seu consumo de armazenamento. Os dados são **fragmentos** em distribuições para otimizar o desempenho do sistema. Pode escolher o padrão de fragmentação que será utilizado para distribuir os dados, ao definir a tabela. Estes padrões de fragmentos são suportados:

- Hash
- Round Robin
- Replicar

### <a name="control-node"></a>Nó de controlo

O nó de Controlo é o cérebro da arquitetura. É o front-end que interage com todas as ligações e aplicações. O motor de consulta distribuído funciona no nó de Controlo para otimizar e coordenar consultas paralelas. Quando submete uma consulta T-SQL, o nó de Controlo transforma-o em consultas que vão contra cada distribuição em paralelo.

### <a name="compute-nodes"></a>Nós de computação

Os nós de computação conferem poder de computação. Mapa de distribuição para nó de Computação para processamento. À medida que paga por mais recursos computacional, as distribuições são rempeitadas aos nós de Computação disponíveis. O número de nós de computação varia de 1 a 60, e é determinado pelo nível de serviço para Synapse SQL.

Cada nó computacional tem um nó ID que é visível nas vistas do sistema. Você pode ver o ID do nó compute procurando a coluna node_id nas vistas do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista destas vistas do sistema, consulte [as vistas do sistema Synapse SQL](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="data-movement-service"></a>Serviço de Movimento de Dados

Data Movement Service (DMS) é a tecnologia de transporte de dados que coordena o movimento de dados entre os nós compute. Algumas consultas requerem movimento de dados para garantir que as consultas paralelas retornem resultados precisos. Quando o movimento de dados é necessário, o DMS garante que os dados certos chegarão ao local certo.

## <a name="distributions"></a>Distribuições

As distribuições são as unidades básicas de armazenamento e processamento de consultas paralelas que são executadas em dados distribuídos. Quando o Synapse SQL faz uma consulta, a obra é dividida em 60 consultas menores que funcionam em paralelo.

Cada uma das 60 consultas menores funciona numa das distribuições de dados. Cada nó compute gere uma ou mais das 60 distribuições. Um pool SQL dedicado (anteriormente SQL DW) com recursos de computação máximo tem uma distribuição por nó Compute. Um pool DE SQL dedicado (anteriormente SQL DW) com recursos mínimos de computação tem todas as distribuições em um nó de computação.  

## <a name="hash-distributed-tables"></a>Tabelas distribuídas com hash

Uma tabela distribuída com hash pode proporcionar o mais elevado desempenho de consulta para associações e agregações em tabelas grandes.

Para obter dados fragmentos numa tabela distribuída por haxixe, é utilizada uma função de haxixe para atribuir deterministicamente cada linha a uma distribuição. Na definição da tabela, uma das colunas será a coluna de distribuição. A função hash utiliza os valores da coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama seguinte ilustra como uma tabela completa (não distribuída) é armazenada como uma mesa distribuída por haxixe.

![Tabela distribuída](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Tabela distribuída")  

- Cada linha pertence a uma distribuição.  
- Um algoritmo de haxixe determinístico atribui cada linha a uma distribuição.  
- O número de filas de mesa por distribuição varia como mostrado pelos diferentes tamanhos das tabelas.

Existem considerações de desempenho para a seleção de uma coluna de distribuição, tais como distinção, distorção de dados, e os tipos de consultas que funcionam no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas com round robin

Uma mesa de rodapé é a mesa mais simples para criar e proporciona um desempenho rápido quando usado como uma mesa de preparação para cargas.

As tabelas distribuídas com round robin distribuem uniformemente os dados por uma tabela, mas sem qualquer otimização adicional. Uma distribuição é escolhida pela primeira vez aleatoriamente e, em seguida, os tampão de linhas são atribuídos a distribuições sequencialmente. O carregamento de dados para tabelas round robin é rápido, mas o desempenho das consultas é, muitas vezes, superior se as tabelas forem distribuídas com hash. As juntas em mesas de rodapé requerem dados de remodelação, o que leva mais tempo.

## <a name="replicated-tables"></a>Tabelas replicadas

As tabelas replicadas proporcionam o desempenho de consulta mais rápido para tabelas pequenas.

Uma tabela que é replicada caches uma cópia completa da tabela em cada nó de cálculo. Por conseguinte, a replicação de uma tabela elimina a necessidade de transferir dados entre nós de computação antes de se proceder a uma associação ou agregação. Idealmente, as tabelas replicadas devem ser utilizadas com tabelas pequenas. É necessário um armazenamento extra e há sobrecargas adicionais que são incorridos ao escrever dados, que tornam as grandes tabelas impraticáveis.  

O diagrama abaixo mostra uma tabela replicada que é em cache na primeira distribuição em cada nó de computação.  

![Tabela replicada](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Tabela replicada")

## <a name="next-steps"></a>Passos seguintes

Agora que sabe um pouco sobre a Azure Synapse, aprenda a criar rapidamente [uma piscina SQL dedicada (anteriormente SQL DW)](create-data-warehouse-portal.md) e [carregue dados de amostras.](./load-data-from-azure-blob-storage-using-copy.md) Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) quando se deparar com terminologia nova. Ou olhe para alguns destes outros Recursos Azure Synapse.  

- [Histórias de sucesso de clientes](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogues](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Pedidos de funcionalidades](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Criar pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md)
- [Microsoft Q&Uma página de perguntas](/answers/topics/azure-synapse-analytics.html)
- [Fórum de Transbordamento de Pilhas](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)