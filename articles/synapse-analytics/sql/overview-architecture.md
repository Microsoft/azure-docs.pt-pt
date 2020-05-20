---
title: Arquitetura Azure Synapse SQL
description: Saiba como o Azure Synapse SQL combina processamento maciço paralelo (MPP) com o Armazenamento Azure para alcançar um alto desempenho e escalabilidade.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d073bc82b3a9e42e443caa5d3e7855fd4eb5b98b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658909"
---
# <a name="azure-synapse-sql-architecture"></a>Arquitetura Azure Synapse SQL 

Este artigo descreve os componentes de arquitetura do Synapse SQL.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Componentes de arquitetura SqL synapse

Synapse SQL aproveita uma arquitetura de escala para distribuir o processamento computacional de dados em vários nódosos. A computação é separada do armazenamento, o que lhe permite escalar a computação independentemente dos dados do seu sistema. 

Para o pool SQL, a unidade de escala é uma abstração do poder da computação que é conhecida como uma unidade de armazém de [dados.](resource-consumption-models.md) 

Para o SQL a pedido, sendo desempregada, a escala é feita automaticamente para acomodar os requisitos de recursos de consulta. À medida que a topologia muda ao longo do tempo adicionando, removendo nós ou falhas, adapta-se a alterações e garante que a sua consulta tem recursos suficientes e termina com sucesso. Por exemplo, a imagem abaixo mostra sQL on-demand utilizando 4 nós de computação para executar uma consulta.

![Arquitetura SQL do Synapse](./media//overview-architecture/sql-architecture.png)

Synapse SQL usa uma arquitetura baseada no nó. As aplicações ligam e emitem comandos T-SQL a um nó de controlo, que é o ponto único de entrada para synapse SQL. 

O nó de controlo de piscina SQL utiliza o motor MPP para otimizar consultas para processamento paralelo, e depois passa operações para nós da Compute para fazer o seu trabalho em paralelo. 

O nó de Controlo a pedido da SQL utiliza o motor Deprocessamento de Consulta Distribuída (DQP) para otimizar e orquestrar a execução distribuída da consulta do utilizador, dividindo-a em consultas menores que serão executadas em nós computacionais. Cada pequena consulta é chamada de tarefa e representa unidade de execução distribuída. Lê ficheiros(s) de armazenamento, junta resultados de outras tarefas, grupos ou dados de encomendas recuperados de outras tarefas. 

Os nós de computação armazenam todos os dados de utilizador no Armazenamento do Microsoft Azure e executam as consultas paralelas. O Serviço de Movimento de Dados (DMS – Data Movement Service) é um serviço interno ao nível do sistema que move os dados em todos os nós, conforme necessário, para executar consultas em paralelo e devolver resultados precisos. 

Com armazenamento e computação dissociados, ao utilizar o SQL Synapse pode beneficiar-se do dimensionamento independente do poder computacional, independentemente das suas necessidades de armazenamento. Para a escalação on-demand SQL é feita automaticamente, enquanto para a piscina SQL pode-se:

* Cresça ou diminua a potência da computação, dentro de um pool SQL (armazém de dados), sem dados em movimento.
* Colocar a capacidade de computação em pausa, mantendo os dados intactos, pelo que só paga pelo armazenamento.
* Retomar a capacidade de computação durante as horas de funcionamento.

## <a name="azure-storage"></a>Armazenamento do Azure

Synapse SQL aproveita o Armazenamento Azure para manter os dados dos seus utilizadores seguros. Uma vez que os seus dados são armazenados e geridos pelo Azure Storage, existe uma taxa separada para o seu consumo de armazenamento. 

A SQL on-demand permite-lhe consultar ficheiros no seu lago de dados de forma apenas de leitura, enquanto o pool SQL permite-lhe ingerir dados também. Quando os dados são ingeridos no pool SQL, os dados são estonteados em **distribuições** para otimizar o desempenho do sistema. Pode escolher qual o padrão mais grave a utilizar para distribuir os dados quando definir a tabela. Estes padrões de sharding são suportados:

* Hash
* Round Robin
* Replicar

## <a name="control-node"></a>Nó de controlo

O nó de Controlo é o cérebro da arquitetura. É o front-end que interage com todas as ligações e aplicações. 

Na piscina SQL, o motor MPP funciona no nó de Controlo para otimizar e coordenar consultas paralelas. Quando submete uma consulta T-SQL à piscina SQL, o nó de Controlo transforma-o em consultas que correm contra cada distribuição em paralelo.

No SQL a pedido, o motor DQP funciona no nó de Controlo para otimizar e coordenar a execução distribuída da consulta do utilizador, dividindo-a em consultas menores que serão executadas em nós computacionais. Também atribui conjuntos de ficheiros a serem processados por cada nó.

## <a name="compute-nodes"></a>Nós de computação

Os nós de computação conferem poder de computação. 

Na piscina SQL, distribui o mapa para os nós da Compute para processamento. À medida que paga por mais recursos de computação, o pool remapeie as distribuições para os nós da Compute disponíveis. O número de nós computacionais varia de 1 a 60, e é determinado pelo nível de serviço para piscina SQL. Cada nó computacional tem um ID de nó que é visível nas vistas do sistema. Você pode ver o ID do nó computado procurando a coluna node_id em vistas do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista destas vistas do sistema, consulte [as vistas do sistema MPP](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

No SQL a pedido, cada nó Compute é atribuída tarefa e conjunto de ficheiros para executar tarefa. A tarefa é distribuída unidade de execução de consulta, que na verdade faz parte da consulta que o utilizador submeteu. A escala automática está em vigor para garantir que os nódos computados são utilizados suficientes para executar a consulta do utilizador.

## <a name="data-movement-service"></a>Serviço de Movimento de Dados

O Serviço de Movimento de Dados (DMS) é a tecnologia de transporte de dados no pool SQL que coordena o movimento de dados entre os nós da Compute. Algumas consultas requerem movimento de dados para garantir que as consultas paralelas retribuem resultados precisos. Quando o movimento de dados é necessário, o DMS garante que os dados certos estão no local certo.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distribuição

Uma distribuição é a unidade básica de armazenamento e processamento para consultas paralelas que funcionam em dados distribuídos em piscina SQL. Quando a piscina SQL executa uma consulta, o trabalho é dividido em 60 consultas menores que correm em paralelo. 

Cada uma das 60 consultas menores é de uma das distribuições de dados. Cada nó Compute gere uma ou mais das 60 distribuições. Um pool SQL com recursos de computação máximo tem uma distribuição por nó computacional. Um pool SQL com recursos mínimos de computação tem todas as distribuições em um nó de cálculo. 

## <a name="hash-distributed-tables"></a>Tabelas distribuídas com hash
Uma tabela distribuída com hash pode proporcionar o mais elevado desempenho de consulta para associações e agregações em tabelas grandes. 

Para juntar dados numa tabela distribuída por hash, o pool SQL utiliza uma função hash para atribuir deterministicamente cada linha a uma distribuição. Na definição da tabela, uma das colunas será a coluna de distribuição. A função hash utiliza os valores da coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama seguinte ilustra como uma mesa completa (mesa não distribuída) é armazenada como uma tabela distribuída por hash. 

![Mesa distribuída](media//overview-architecture/hash-distributed-table.png "Mesa distribuída") 

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

![Tabela replicada](media/overview-architecture/replicated-table.png "Tabela replicada") 

## <a name="next-steps"></a>Passos seguintes

Agora que sabe um pouco sobre o Synapse SQL, aprenda a criar rapidamente [um pool SQL](../quickstart-create-sql-pool-portal.md) e [carregue dados](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) de amostra (./sql-data-warehouse-load-sample-databases.md). Ou começa a [usar sQL a pedido](../quickstart-sql-on-demand.md). Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure](../../azure-glossary-cloud-terminology.md) quando se deparar com terminologia nova. 
