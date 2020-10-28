---
title: Arquitetura SQL do Synapse
description: Saiba como o Azure Synapse SQL combina capacidades de processamento de consulta distribuídas com o Azure Storage para alcançar um alto desempenho e escalabilidade.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: ae3b54ca72c92722dffa370b0b8be1ca2c490f97
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476013"
---
# <a name="azure-synapse-sql-architecture"></a>Arquitetura Azure Synapse SQL 

Este artigo descreve os componentes de arquitetura da Synapse SQL.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Componentes de arquitetura Sinapse SQL

O Sinaapse SQL aproveita uma arquitetura de escala para distribuir o processamento computacional de dados através de múltiplos nós. O cálculo é separado do armazenamento, o que lhe permite escalar o cálculo independentemente dos dados do seu sistema. 

Para a piscina SQL, a unidade de escala é uma abstração de poder computacional que é conhecida como uma [unidade de armazém de dados.](resource-consumption-models.md) 

Para o SQL a pedido, sendo sem servidor, o dimensionamento é feito automaticamente para acomodar os requisitos de recursos de consulta. À medida que a topologia muda ao longo do tempo adicionando, removendo nós ou failovers, adapta-se a alterações e garante que a sua consulta tem recursos e acabamentos suficientes com sucesso. Por exemplo, a imagem abaixo mostra SQL on demand usando 4 nós de computação para executar uma consulta.

![Arquitetura SQL do Synapse](./media//overview-architecture/sql-architecture.png)

O SYNAPSE SQL utiliza uma arquitetura baseada em nó. As aplicações ligam e emitem comandos T-SQL a um nó de controlo, que é o único ponto de entrada para o Sinaapse SQL. 

O nó de controlo Azure Synapse SQL utiliza um motor de consulta distribuído para otimizar consultas de processamento paralelo e, em seguida, passa as operações para os nós compute para fazer o seu trabalho em paralelo. 

O nó de controlo on-demand SQL utiliza o motor distributed query processing (DQP) para otimizar e orquestrar a execução distribuída da consulta do utilizador, dividindo-o em consultas menores que serão executadas nos nós compute. Cada pequena consulta é chamada tarefa e representa unidade de execução distribuída. Lê ficheiros(s) a partir de armazenamento, junta resultados de outras tarefas, grupos ou dados de encomendas obtidos de outras tarefas. 

Os nós de computação armazenam todos os dados de utilizador no Armazenamento do Microsoft Azure e executam as consultas paralelas. O Serviço de Movimento de Dados (DMS – Data Movement Service) é um serviço interno ao nível do sistema que move os dados em todos os nós, conforme necessário, para executar consultas em paralelo e devolver resultados precisos. 

Com armazenamento e cálculo dissociados, ao utilizar o Synapse SQL pode-se beneficiar de um tamanho independente de poder de computação, independentemente das suas necessidades de armazenamento. Para o sql on demand o dimensionamento é feito automaticamente, enquanto para a piscina SQL uma lata:

* Cresça ou encolhe a potência de computação, dentro de um pool SQL (data warehouse), sem mover dados.
* Colocar a capacidade de computação em pausa, mantendo os dados intactos, pelo que só paga pelo armazenamento.
* Retomar a capacidade de computação durante as horas de funcionamento.

## <a name="azure-storage"></a>Storage do Azure

O Synapse SQL aproveita o Azure Storage para manter os dados do utilizador seguros. Uma vez que os seus dados são armazenados e geridos pela Azure Storage, existe uma taxa separada para o seu consumo de armazenamento. 

O SQL on demand permite-lhe consultar ficheiros no seu lago de dados apenas de forma a ler, enquanto o pool SQL permite-lhe ingerir dados também. Quando os dados são ingeridos no pool SQL, os dados são fragmentos em **distribuições** para otimizar o desempenho do sistema. Pode escolher qual o padrão de fragmentos a utilizar para distribuir os dados quando define a tabela. Estes padrões de fragmentos são suportados:

* Hash
* Round Robin
* Replicar

## <a name="control-node"></a>Nó de controlo

O nó de Controlo é o cérebro da arquitetura. É o front-end que interage com todas as ligações e aplicações. 

No Synapse SQL, o motor de consulta distribuído funciona no nó de Controlo para otimizar e coordenar consultas paralelas. Quando submete uma consulta T-SQL à piscina SQL, o nó de Controlo transforma-o em consultas que vão contra cada distribuição em paralelo.

No SQL on demand, o motor DQP funciona no nó de Controlo para otimizar e coordenar a execução distribuída da consulta do utilizador, dividindo-a em consultas menores que serão executadas nos nós compute. Também atribui conjuntos de ficheiros a serem processados por cada nó.

## <a name="compute-nodes"></a>Nós de computação

Os nós de computação conferem poder de computação. 

Na piscina SQL, mapa de distribuição para nós compute para processamento. À medida que paga por mais recursos computacional, o pool remaps as distribuições para os nós computacional disponíveis. O número de nós computativos varia de 1 a 60, e é determinado pelo nível de serviço para piscina SQL. Cada nó computacional tem um nó ID que é visível nas vistas do sistema. Você pode ver o ID do nó compute procurando a coluna node_id nas vistas do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista destas vistas do sistema, consulte [as vistas do sistema Synapse SQL](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

Em SQL on demand, cada nó compute é atribuído tarefa e conjunto de ficheiros para executar tarefa. A tarefa é distribuída unidade de execução de consulta, que na verdade faz parte do utilizador de consulta submetido. O dimensionamento automático está em vigor para garantir que os nós computacional suficientes são utilizados para executar a consulta do utilizador.

## <a name="data-movement-service"></a>Serviço de Movimento de Dados

Data Movement Service (DMS) é a tecnologia de transporte de dados no pool SQL que coordena o movimento de dados entre os nós compute. Algumas consultas requerem movimento de dados para garantir que as consultas paralelas retornem resultados precisos. Quando o movimento de dados é necessário, o DMS garante que os dados certos chegarão ao local certo.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distribuições

Uma distribuição é a unidade básica de armazenamento e processamento para consultas paralelas que funcionam em dados distribuídos na piscina SQL. Quando a piscina SQL executa uma consulta, o trabalho é dividido em 60 consultas menores que funcionam em paralelo. 

Cada uma das 60 consultas menores funciona numa das distribuições de dados. Cada nó compute gere uma ou mais das 60 distribuições. Uma piscina SQL com recursos de computação máximo tem uma distribuição por nó Compute. Uma piscina SQL com recursos mínimos de computação tem todas as distribuições em um nó compute. 

## <a name="hash-distributed-tables"></a>Tabelas distribuídas com hash
Uma tabela distribuída com hash pode proporcionar o mais elevado desempenho de consulta para associações e agregações em tabelas grandes. 

Para obter dados em uma tabela distribuída por haxixe, o pool SQL usa uma função de haxixe para atribuir deterministicamente cada linha a uma distribuição. Na definição da tabela, uma das colunas será a coluna de distribuição. A função hash utiliza os valores da coluna de distribuição para atribuir cada linha a uma distribuição.

O diagrama seguinte ilustra como uma tabela completa (não distribuída) é armazenada como uma mesa distribuída por haxixe. 

![Tabela distribuída](media//overview-architecture/hash-distributed-table.png "Tabela distribuída") 

* Cada linha pertence a uma distribuição. 
* Um algoritmo de haxixe determinístico atribui cada linha a uma distribuição. 
* O número de filas de mesa por distribuição varia como mostrado pelos diferentes tamanhos das tabelas.

Existem considerações de desempenho para a seleção de uma coluna de distribuição, tais como distinção, distorção de dados, e os tipos de consultas que funcionam no sistema.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas com round robin

Uma mesa de rodapé é a mesa mais simples para criar e proporciona um desempenho rápido quando usado como uma mesa de preparação para cargas.

As tabelas distribuídas com round robin distribuem uniformemente os dados por uma tabela, mas sem qualquer otimização adicional. Uma distribuição é escolhida pela primeira vez aleatoriamente e, em seguida, os tampão de linhas são atribuídos a distribuições sequencialmente. O carregamento de dados para tabelas round robin é rápido, mas o desempenho das consultas é, muitas vezes, superior se as tabelas forem distribuídas com hash. As juntas em mesas de rodapé requerem dados de remodelação, o que leva mais tempo.

## <a name="replicated-tables"></a>Tabelas replicadas
As tabelas replicadas proporcionam o desempenho de consulta mais rápido para tabelas pequenas.

Uma tabela que é replicada caches uma cópia completa da tabela em cada nó de cálculo. Por conseguinte, a replicação de uma tabela elimina a necessidade de transferir dados entre nós de computação antes de se proceder a uma associação ou agregação. Idealmente, as tabelas replicadas devem ser utilizadas com tabelas pequenas. É necessário um armazenamento extra e há sobrecargas adicionais que são incorridos ao escrever dados, que tornam as grandes tabelas impraticáveis. 

O diagrama abaixo mostra uma tabela replicada que é em cache na primeira distribuição em cada nó de computação. 

![Tabela replicada](media/overview-architecture/replicated-table.png "Tabela replicada") 

## <a name="next-steps"></a>Passos seguintes

Agora que sabe um pouco sobre o Sinaapse SQL, aprenda a criar rapidamente [uma piscina SQL](../quickstart-create-sql-pool-portal.md) e [carregue dados de amostra](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md). Ou começa a [utilizar o SQL a pedido.](../quickstart-sql-on-demand.md) Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure](../../azure-glossary-cloud-terminology.md) quando se deparar com terminologia nova. 
