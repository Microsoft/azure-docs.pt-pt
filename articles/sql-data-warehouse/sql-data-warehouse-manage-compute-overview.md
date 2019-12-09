---
title: Gerenciar recurso de computação
description: Saiba mais sobre os recursos de escala horizontal de desempenho no Azure SQL Data Warehouse. Escale horizontalmente ajustando DWUs ou reduza os custos ao pausar o data warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 305b17a9118bddac53b19462cb8c3be887395311
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923605"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Gerenciar a computação no Azure SQL Data Warehouse
Saiba mais sobre como gerenciar recursos de computação no Azure SQL Data Warehouse. Reduza os custos pausando o data warehouse ou dimensione o data warehouse para atender às demandas de desempenho. 

## <a name="what-is-compute-management"></a>O que é o gerenciamento de computação?
A arquitetura do SQL Data Warehouse faz a separação entre armazenamento e computação, permitindo o dimensionamento independente de cada. Como resultado, pode dimensionar a computação para satisfazer as necessidades de desempenho independentemente do armazenamento de dados. Também pode colocar em pausa e retomar recursos de computação. Uma consequência natural dessa arquitetura é que a [cobrança](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) pela computação e pelo armazenamento é separada. Se não precisar de utilizar o armazém de dados durante algum tempo, pode poupar nos custos de computação ao colocar em pausa a computação. 

## <a name="scaling-compute"></a>Escala de computação
Você pode escalar horizontalmente ou dimensionar a computação de volta ajustando a configuração de [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) para seu data warehouse. O desempenho de consulta e de carregamento pode aumentar de forma linear à medida que adiciona mais unidades de armazém de dados. 

Para obter as etapas de expansão, consulte os guias de início rápido do [portal do Azure](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)ou [T-SQL](quickstart-scale-compute-tsql.md) . Você também pode executar operações de expansão com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Para executar uma operação de dimensionamento, o SQL Data Warehouse primeiro interrompe todas as consultas de entrada e, em seguida, reverte as transações para garantir um estado consistente. O dimensionamento só ocorre depois de concluída a reversão de transação. Para uma operação de dimensionamento, o sistema desanexa a camada de armazenamento dos nós de computação, adiciona nós de computação e, em seguida, volta a anexar a camada de armazenamento à camada de computação. Cada armazém de dados é armazenada como 60 distribuições, que são distribuídas uniformemente pelos nós de computação. Ao adicionar mais nós de computação, adiciona mais capacidade de computação. À medida que o número de nós de computação aumenta, o número de distribuições por nó de computação diminui, fornecendo mais capacidade de computação para as suas consultas. Da mesma forma, a redução de unidades de data warehouse reduz o número de nós de computação, o que reduz os recursos de computação para consultas.

A tabela a seguir mostra como o número de distribuições por nó de computação é alterado à medida que as unidades de data warehouse são alteradas.  O DWU6000 fornece 60 nós de computação e Obtém um desempenho de consulta muito maior do que o DWU100. 

| Unidades do armazém de dados  | \# de nós de computação | \# de distribuições por nó |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Localizando o tamanho certo das unidades de data warehouse

Para ver os benefícios de desempenho do dimensionamento, especialmente para unidades de data warehouse maiores, você deseja usar pelo menos um conjunto de dados de 1 TB. Para encontrar o melhor número de unidades de data warehouse para seu data warehouse, tente escalar verticalmente e reduzir verticalmente. Execute algumas consultas com números diferentes de unidades de data warehouse depois de carregar os dados. Como o dimensionamento é rápido, você pode experimentar vários níveis de desempenho em uma hora ou menos. 

Recomendações para encontrar o melhor número de unidades de data warehouse:

- Para um data warehouse em desenvolvimento, comece selecionando um número menor de unidades de data warehouse.  Um bom ponto de partida é DW400c ou DW200c.
- Monitore o desempenho do aplicativo, observando o número de unidades de data warehouse selecionadas em comparação com o desempenho que você observar.
- Assuma uma escala linear e determine quanto você precisa para aumentar ou diminuir as unidades de data warehouse. 
- Continue fazendo ajustes até alcançar um nível de desempenho ideal para seus requisitos de negócios.

## <a name="when-to-scale-out"></a>Quando escalar horizontalmente
A expansão das unidades de data warehouse impacta esses aspectos do desempenho:

- Melhora linearmente o desempenho do sistema para verificações, agregações e instruções CTAS.
- Aumenta o número de leitores e gravadores para carregar dados.
- Número máximo de consultas simultâneas e slots de simultaneidade.

Recomendações para quando escalar horizontalmente data warehouse unidades:

- Antes de executar uma operação de transformação ou carregamento de dados pesados, escale horizontalmente para disponibilizar os dados mais rapidamente.
- Durante o horário comercial de pico, escale horizontalmente para acomodar números maiores de consultas simultâneas. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>E se a expansão não melhorar o desempenho?

Adicionar unidades de data warehouse aumentando o paralelismo. Se o trabalho for dividido uniformemente entre os nós de computação, o paralelismo adicional melhorará o desempenho da consulta. Se a expansão não estiver mudando seu desempenho, há alguns motivos pelos quais isso pode acontecer. Seus dados podem estar distorcidos entre as distribuições ou as consultas podem estar apresentando uma grande quantidade de movimentação de dados. Para investigar problemas de desempenho de consulta, consulte [solução de problemas de desempenho](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pausando e retomando a computação
Pausar a computação faz com que a camada de armazenamento se desanexe dos nós de computação. Os recursos de computação são liberados da sua conta. Você não será cobrado pela computação enquanto a computação estiver em pausa. Retomar a computação reanexa o armazenamento aos nós de computação e retoma os encargos para a computação. Ao pausar uma data warehouse:

* Os recursos de computação e memória são retornados para o pool de recursos disponíveis no data center
* Os custos unitários do data warehouse são zero para a duração da pausa.
* O armazenamento de dados não é afetado e seus dados permanecem intactos. 
* SQL Data Warehouse cancela todas as operações em execução ou em fila.

Ao retomar uma data warehouse:

* SQL Data Warehouse adquire recursos de memória e computação para sua configuração de unidades de data warehouse.
* Os encargos de computação para suas unidades de data warehouse retomam.
* Seus dados ficam disponíveis.
* Depois que o data warehouse estiver online, você precisará reiniciar suas consultas de carga de trabalho.

Se você sempre quiser que seu data warehouse acessível, considere dimensioná-lo para o menor tamanho, em vez de pausar. 

Para ver as etapas de pausa e retomada, consulte os guias de início rápido do [portal do Azure](pause-and-resume-compute-portal.md)ou do [PowerShell](pause-and-resume-compute-powershell.md) . Você também pode usar a [API REST de pausa](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) ou a [API REST de retomada](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Drenar transações antes de colocar em pausa ou dimensionar
É recomendável permitir que as transações existentes sejam concluídas antes de iniciar uma operação de pausa ou de escala.

Ao colocar em pausa ou dimensionar o SQL Data Warehouse, em segundo plano, as consultas são canceladas quando iniciar o pedido de colocação em pausa ou dimensionamento.  Cancelar uma consulta SELECT simples é uma operação rápida e quase não tem impacto sobre o tempo que demora a colocar em pausa ou a dimensionar a sua instância.  No entanto, as consultas transacionais que modificam os seus dados ou a estrutura dos mesmos, podem não ser paradas rapidamente.  **As consultas transacionais, por definição, têm de ser concluídas na sua totalidade ou têm de reverter as alterações.**  A reversão do trabalho concluído por uma consulta transacional pode demorar tanto tempo, ou mais, como a alteração original que a consulta aplicou.  Por exemplo, se cancelar uma consulta que estava a eliminar linhas e que já estiva a ser executada há uma hora, o sistema pode demorar uma hora a inserir novamente as linhas que foram eliminadas.  Se colocar em pausa ou dimensionar enquanto as transações estiverem a decorrer, a colocação em pausa ou o dimensionamento podem demorar muito tempo, uma vez que a colocação em pausa e o dimensionamento têm de aguardar que a reversão esteja concluída para poderem continuar.

Consulte também [noções básicas sobre transações](sql-data-warehouse-develop-transactions.md)e [otimização de transações](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatizando o gerenciamento de computação
Para automatizar as operações de gerenciamento de computação, consulte [gerenciar a computação com o Azure Functions](manage-compute-with-azure-functions.md).

Cada uma das operações de expansão, pausa e retomada pode levar vários minutos para ser concluída. Se você estiver dimensionando, pausando ou retomando automaticamente, recomendamos implementar a lógica para garantir que determinadas operações tenham sido concluídas antes de prosseguir com outra ação. A verificação do estado de data warehouse por meio de vários pontos de extremidade permite que você implemente corretamente a automação dessas operações. 

Para verificar o estado do data warehouse, consulte o início rápido do [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) ou [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) . Você também pode verificar o estado de data warehouse com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Permissões

O dimensionamento da data warehouse requer as permissões descritas em [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Pause e resume exigem a permissão de [colaborador do BD SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) , especificamente Microsoft. SQL/Servers/debases/Action.


## <a name="next-steps"></a>Passos seguintes
Consulte o guia de instruções para [gerenciar computação](manage-compute-with-azure-functions.md) outro aspecto do gerenciamento de recursos de computação que está alocando diferentes recursos de computação para consultas individuais. Para obter mais informações, consulte [classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).
