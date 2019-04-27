---
title: Gerir recursos de computação no Azure SQL Data Warehouse | Documentos da Microsoft
description: Saiba mais sobre o dimensionamento de desempenho de recursos do Azure SQL Data Warehouse. Aumentar horizontalmente ao ajustar as DWUs ou custos mais baixos por colocar em pausa o armazém de dados.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 47be738a4e5dcec144d482c28e39cbe950bba3e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748939"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Gerir a computação no Azure SQL Data Warehouse
Saiba mais sobre a gestão de recursos de computação no Azure SQL Data Warehouse. Reduza os custos ao armazém de dados de colocar em pausa ou dimensionar o armazém de dados para satisfazer as necessidades de desempenho. 

## <a name="what-is-compute-management"></a>O que é a gestão de computação?
A arquitetura do SQL Data Warehouse separa o armazenamento e computação, permitindo que cada dimensionar de forma independente. Como resultado, pode dimensionar a computação para satisfazer as necessidades de desempenho independentes do armazenamento de dados. Também pode colocar em pausa e retomar recursos de computação. Uma conseqüência natural desta arquitetura é que [faturação](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) para computação e armazenamento está separado. Se não precisar de utilizar o seu armazém de dados durante algum tempo, pode salvar os custos de computação por colocar em pausa computação. 

## <a name="scaling-compute"></a>Dimensionar a computação
Pode aumentar ou reduzir horizontalmente back-computação ao ajustar a [unidades de armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md) definição para o seu armazém de dados. Desempenho de consulta e de carregamento pode aumentar de maneira linear à medida que adiciona mais unidades de armazém de dados. 

Para obter os passos de escalamento horizontal, consulte a [portal do Azure](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), ou [T-SQL](quickstart-scale-compute-tsql.md) inícios rápidos. Também pode efetuar operações de escalamento horizontal com um [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Para efetuar uma operação de dimensionamento, o SQL Data Warehouse primeiro interrompe todas as consultas de entrada e, em seguida, reverte transações para garantir um estado consistente. Dimensionamento apenas ocorre depois de concluída a reversão de transação. Para uma operação de dimensionamento, o sistema desliga a camada de armazenamento de nós de computação, adiciona nós de computação e, em seguida, volta a ligar a camada de armazenamento para a camada de computação. Cada armazém de dados é armazenada como 60 distribuições, que são distribuídas para os nós de computação. Adicionar mais nós de computação, adiciona que mais poder de computação. À medida que aumenta o número de nós de computação, diminui o número de distribuições por nó de computação, fornecendo mais poder de computação para as suas consultas. Da mesma forma, diminuindo a unidades de armazém de dados reduz o número de nós de computação, o que reduz os recursos de computação para consultas.

A tabela seguinte mostra como alterar o número de distribuições por alterações de nó de computação como as unidades de armazém de dados.  DWU6000 fornece 60 nós de computação e atinge o desempenho de consulta muito maior que DWU100. 

| Unidades do armazém de dados  | \# de nós de computação | \# distribuições por nó |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Localizar a dimensão certa de unidades de armazém de dados

Para ver os benefícios de desempenho de aumentar horizontalmente, especialmente para unidades de armazém de dados maiores, que pretende utilizar, pelo menos, um conjunto de dados de 1 TB. Para encontrar o melhor número de unidades de armazém de dados para o seu armazém de dados, experimente aumentar e reduzir verticalmente. Execute algumas consultas com diferentes números de unidades de armazém de dados depois de carregar os seus dados. Uma vez que o dimensionamento é rápido, pode experimentar vários níveis de desempenho numa hora ou menos. 

Recomendações para encontrar o melhor número de dados do armazém de unidades:

- Para um armazém de dados no desenvolvimento, comece por selecionar um menor número de unidades do data warehouse.  Um ponto de partida é DW400 ou DW200.
- Monitorize o desempenho da aplicação, o número de unidades de armazém de dados selecionadas em comparação comparado o desempenho que observar a observar.
- Suponha que uma escala linear e determine o quanto precisa aumentar ou diminuir as unidades de armazém de dados. 
- Continue a fazer ajustes até chegar um nível de desempenho ideal para os seus requisitos de negócios.

## <a name="when-to-scale-out"></a>Quando deve aumentar horizontalmente
Aumentar horizontalmente unidades do data warehouse tem impacto sobre esses aspectos de desempenho:

- Linearmente melhora o desempenho do sistema para análises, agregações e instruções de CTAS.
- Aumenta o número de leitores e gravadores de carregamento de dados.
- Número máximo de consultas em simultâneo e blocos de simultaneidade.

Recomendações para quando dimensionar os dados do armazém de unidades:

- Antes de executar uma operação de transformação ou de carregamento de grande quantidade de dados, aumente horizontalmente para disponibilizar os dados mais rapidamente.
- Durante o horário de pico, aumente horizontalmente para acomodar o número maior de consultas em simultâneo. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>E se aumentar horizontalmente não melhora desempenho?

Adicionar unidades do data warehouse, aumentando o paralelismo. Se o trabalho for dividido uniformemente entre os nós de computação, o paralelismo adicional melhora o desempenho de consulta. Se o aumento horizontal não está a mudar o seu desempenho, existem algumas razões por que isso poderia acontecer. Os dados podem ser desviados para as distribuições ou consultas poderão apresentar uma grande quantidade de movimento de dados. Para investigar problemas de desempenho de consulta, consulte [resolução de problemas de desempenho](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Colocar em pausa e retoma da computação
Computação em pausa faz com que a camada de armazenamento para anular a exposição de nós de computação. Os recursos de computação são lançados a partir da sua conta. Não são cobradas para computação, enquanto a computação é colocada em pausa. A retomar a computação volta a ligar de armazenamento para os nós de computação e retoma os custos de computação. Quando colocar em pausa um armazém de dados:

* Recursos de computação e memória são retornados ao pool de recursos disponíveis no Centro de dados
* Custos de unidade do armazém de dados seja igual a zero durante a colocação em pausa.
* Armazenamento de dados não é afetado e seus dados permanecem intactos. 
* O SQL Data Warehouse cancela todas as operações em execução ou em fila.

Quando é retomar um armazém de dados:

* O SQL Data Warehouse adquire recursos de computação e memória para unidades de armazém de dados a definição.
* Custos para seu currículo de unidades de armazém de dados de computação.
* Os dados ficam disponíveis.
* Depois do armazém de dados está online, terá de reiniciar as suas consultas de carga de trabalho.

Se sempre queira seu armazém de dados acessível, considere reduzir verticalmente para o tamanho mais pequeno, em vez de colocar em pausa. 

Para colocar em pausa e retomar a passos, consulte a [portal do Azure](pause-and-resume-compute-portal.md), ou [PowerShell](pause-and-resume-compute-powershell.md) inícios rápidos. Também pode utilizar o [colocar em pausa a REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) ou o [retomar a REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Drenar transações antes de colocar em pausa ou dimensionar
Recomendamos que permite que transações existentes sejam concluídas antes de iniciar uma operação de colocar em pausa ou dimensionamento.

Ao colocar em pausa ou dimensionar o SQL Data Warehouse, em segundo plano, as consultas são canceladas quando iniciar o pedido de colocação em pausa ou dimensionamento.  Cancelar uma consulta SELECT simples é uma operação rápida e quase não tem impacto sobre o tempo que demora a colocar em pausa ou a dimensionar a sua instância.  No entanto, as consultas transacionais que modificam os seus dados ou a estrutura dos mesmos, podem não ser paradas rapidamente.  **As consultas transacionais, por definição, têm de ser concluídas na sua totalidade ou têm de reverter as alterações.**  A reversão do trabalho concluído por uma consulta transacional pode demorar tanto tempo, ou mais, como a alteração original que a consulta aplicou.  Por exemplo, se cancelar uma consulta que estava a eliminar linhas e que já estiva a ser executada há uma hora, o sistema pode demorar uma hora a inserir novamente as linhas que foram eliminadas.  Se colocar em pausa ou dimensionar enquanto as transações estiverem a decorrer, a colocação em pausa ou o dimensionamento podem demorar muito tempo, uma vez que a colocação em pausa e o dimensionamento têm de aguardar que a reversão esteja concluída para poderem continuar.

Consulte também [compreender as transações](sql-data-warehouse-develop-transactions.md), e [otimizar as transações](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatizando o gerenciamento de computação
Para automatizar as operações de gestão de computação, consulte [gerir computação com as funções do Azure](manage-compute-with-azure-functions.md).

Cada um o Escalamento horizontal, colocar em pausa e operações de retoma pode demorar vários minutos a concluir. Se estiver dimensionar, colocar em pausa, ou retomar automaticamente, recomendamos a implementação lógica para garantir que determinadas operações de concluir antes de continuar com outra ação. A verificar o estado do armazém de dados por meio de vários pontos de extremidade permite-lhe implementar corretamente a automatização destas operações. 

Para verificar o estado do armazém de dados, consulte a [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) ou [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) início rápido. Também pode verificar o estado do armazém de dados com um [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Permissões

Dimensionar o armazém de dados exige as permissões descritas [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Necessitam de colocar em pausa e retomar a [contribuinte da BD SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) permissão, especificamente Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Passos Seguintes
Outro aspecto do gerenciamento de recursos de computação é alocar recursos de computação diferentes para consultas individuais. Para obter mais informações, consulte [classes de recursos para a gestão da carga de trabalho](resource-classes-for-workload-management.md).
