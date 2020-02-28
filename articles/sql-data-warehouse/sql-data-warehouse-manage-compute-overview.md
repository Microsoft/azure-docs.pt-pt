---
title: Gerir o recurso computacional
description: Saiba mais sobre as capacidades de desempenho no Azure SQL Data Warehouse. Esternação ajustando DWUs, ou reduzindo os custos através da pausa no armazém de dados.
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
ms.openlocfilehash: c715b2328f66c58fa744235c8762b31fd0b30d1f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669494"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Gerir a computação no Armazém de Dados Azure SQL
Saiba mais sobre a gestão de recursos computacionais no Azure SQL Data Warehouse. Custos mais baixos, fazendo uma pausa no armazém de dados, ou dimensionando o armazém de dados para satisfazer as exigências de desempenho. 

## <a name="what-is-compute-management"></a>O que é a gestão do cálculo?
A arquitetura do SQL Data Warehouse separa o armazenamento e a computação, permitindo que cada um escale de forma independente. Como resultado, pode escalar a computação para satisfazer as exigências de desempenho independentes do armazenamento de dados. Também pode parar e retomar os recursos da computação. Uma consequência natural desta arquitetura é que [a faturação](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) para computação e armazenamento é separada. Se não precisar de usar o seu armazém de dados durante algum tempo, pode poupar os custos da computação através da pausa na computação. 

## <a name="scaling-compute"></a>Cálculo de escala
Pode escalar ou escalar a computação para trás, ajustando a definição das [unidades](what-is-a-data-warehouse-unit-dwu-cdwu.md) de armazém de dados para o seu armazém de dados. O desempenho de carregamento e consulta pode aumentar linearmente à medida que adiciona mais unidades de armazém de dados. 

Para os passos de escala, consulte o [portal Azure,](quickstart-scale-compute-portal.md) [PowerShell](quickstart-scale-compute-powershell.md)ou [quickstarts T-SQL.](quickstart-scale-compute-tsql.md) Também pode realizar operações de escala com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Para realizar uma operação de escala, o SQL Data Warehouse primeiro mata todas as consultas de entrada e, em seguida, reverte as transações para garantir um estado consistente. A escala só ocorre quando a reversão da transação estiver concluída. Para uma operação de escala, o sistema desprende a camada de armazenamento dos nós computacionais, adiciona nós computados e, em seguida, volta a ligar a camada de armazenamento à camada Compute. Cada armazém de dados é armazenado como 60 distribuições, que são distribuídas uniformemente para os nós da Compute. Adicionar mais nós computais adiciona mais poder de computação. À medida que o número de nós computacionais aumenta, o número de distribuições por nó computacional diminui, proporcionando mais poder de computação para as suas consultas. Da mesma forma, a diminuição das unidades de armazéns de dados reduz o número de nós computacionais, o que reduz os recursos computacionais para consultas.

O quadro seguinte mostra como o número de distribuições por nó computacional muda à medida que as unidades de armazém de dados mudam.  DW30000c fornece 60 nódosos computados e obtém um desempenho de consulta muito maior do que DW100c. 

| Unidades do armazém de dados  | \# dos nódos computados | \# de distribuições por nó |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Encontrar o tamanho certo das unidades de armazém de dados

Para ver os benefícios de desempenho da escala, especialmente para unidades de armazém de dados maiores, você quer usar pelo menos um conjunto de dados de 1-TB. Para encontrar o melhor número de unidades de armazém de dados para o seu armazém de dados, tente escalar para cima e para baixo. Ecorra algumas consultas com diferentes números de unidades de armazém de dados depois de carregar os seus dados. Uma vez que a escala é rápida, pode experimentar vários níveis de desempenho em uma hora ou menos. 

Recomendações para encontrar o melhor número de unidades de armazém de dados:

- Para um armazém de dados em desenvolvimento, comece por selecionar um número menor de unidades de armazém de dados.  Um bom ponto de partida é DW400c ou DW200c.
- Monitorize o desempenho da sua aplicação, observando o número de unidades de armazém de dados selecionadas em comparação com o desempenho observado.
- Assuma uma escala linear e determine o quanto precisa para aumentar ou diminuir as unidades de armazém de dados. 
- Continue a fazer ajustes até atingir um nível de desempenho ideal para os seus requisitos de negócio.

## <a name="when-to-scale-out"></a>Quando escalar para fora
A redução das unidades de armazém de dados tem impacto nestes aspetos do desempenho:

- A Lialmost melhora o desempenho do sistema para exames, agregações e declarações CTAS.
- Aumenta o número de leitores e escritores para carregar dados.
- Número máximo de consultas simultâneas e slots de moedas.

Recomendações para quando escalar unidades de armazém de dados:

- Antes de realizar uma operação de carregamento ou transformação de dados pesados, esforce-se para disponibilizar os dados mais rapidamente.
- Durante o pico do horário comercial, escamas para acomodar um maior número de consultas simultâneas. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>E se o escalonamento não melhorar o desempenho?

Adicionando unidades de armazém de dados aumentando o paralelismo. Se o trabalho for dividido uniformemente entre os nós computacionais, o paralelismo adicional melhora o desempenho da consulta. Se o escalonamento não mudar o seu desempenho, existem algumas razões para isso acontecer. Os seus dados podem ser desviados através das distribuições, ou as consultas podem estar a introduzir uma grande quantidade de movimento de dados. Para investigar problemas de desempenho de consulta, consulte [a resolução de problemas](sql-data-warehouse-troubleshoot.md#performance)do Desempenho. 

## <a name="pausing-and-resuming-compute"></a>Parasse e retomando o cálculo
A análise da pausa faz com que a camada de armazenamento se desaloda dos nódos computacionais. Os recursos da Compute são libertados da sua conta. Não é cobrado para a computação enquanto a computação é interrompida. Retomar a computação religa o armazenamento aos nós computacionais e retoma os encargos para a Compute. Quando você pausa um armazém de dados:

* Os recursos de computação e memória são devolvidos ao conjunto de recursos disponíveis no centro de dados
* Os custos da unidade de armazém de dados são zero durante a pausa.
* O armazenamento de dados não é afetado e os seus dados permanecem intactos. 
* O SQL Data Warehouse cancela todas as operações de execução ou filas.

Quando retoma um armazém de dados:

* O SQL Data Warehouse adquire recursos de computação e memória para a definição de unidades de armazém de dados.
* Os encargos computação para as suas unidades de armazém de dados retomam.
* Os seus dados ficam disponíveis.
* Depois de o armazém de dados estar online, precisa de reiniciar as suas consultas de carga de trabalho.

Se quiser sempre o seu armazém de dados acessível, considere escaloná-lo para o menor tamanho em vez de fazer uma pausa. 

Para pausae retomar os passos, consulte o [portal Azure](pause-and-resume-compute-portal.md), ou arranques rápidos [powerShell.](pause-and-resume-compute-powershell.md) Também pode utilizar a [pausa REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) ou o [currículo REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Drenar transações antes de colocar em pausa ou dimensionar
Recomendamos que as transações existentes terminem antes de iniciar uma operação de pausa ou escala.

Ao colocar em pausa ou dimensionar o SQL Data Warehouse, em segundo plano, as consultas são canceladas quando iniciar o pedido de colocação em pausa ou dimensionamento.  Cancelar uma consulta SELECT simples é uma operação rápida e quase não tem impacto sobre o tempo que demora a colocar em pausa ou a dimensionar a sua instância.  No entanto, as consultas transacionais que modificam os seus dados ou a estrutura dos mesmos, podem não ser paradas rapidamente.  **As consultas transacionais, por definição, têm de ser concluídas na sua totalidade ou têm de reverter as alterações.**  A reversão do trabalho concluído por uma consulta transacional pode demorar tanto tempo, ou mais, como a alteração original que a consulta aplicou.  Por exemplo, se cancelar uma consulta que estava a eliminar linhas e que já estiva a ser executada há uma hora, o sistema pode demorar uma hora a inserir novamente as linhas que foram eliminadas.  Se colocar em pausa ou dimensionar enquanto as transações estiverem a decorrer, a colocação em pausa ou o dimensionamento podem demorar muito tempo, uma vez que a colocação em pausa e o dimensionamento têm de aguardar que a reversão esteja concluída para poderem continuar.

Consulte também [a Compreensão](sql-data-warehouse-develop-transactions.md)de transações e otimização de [transações.](sql-data-warehouse-develop-best-practices-transactions.md)

## <a name="automating-compute-management"></a>Automatização da gestão de cálculos
Para automatizar as operações de gestão de cálculo, consulte [Gerir a computação com funções Azure](manage-compute-with-azure-functions.md).

Cada uma das operações de escala, pausa e retoma podem levar vários minutos para ser concluída. Se estiver a escalonar, fazer uma pausa ou retomar automaticamente, recomendamos a implementação da lógica para garantir que determinadas operações tenham sido concluídas antes de prosseguir com outra ação. Verificar o estado do armazém de dados através de vários pontos finais permite-lhe implementar corretamente a automatização de tais operações. 

Para verificar o estado do armazém de dados, consulte o [powerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) ou [o T-SQL.](quickstart-scale-compute-tsql.md#check-data-warehouse-state) Também pode verificar o estado do armazém de dados com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Permissões

A escala ção do armazém de dados requer as permissões descritas na [BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Pausa e Retoma requerem a permissão [do Contribuinte SQL DB,](../role-based-access-control/built-in-roles.md#sql-db-contributor) especificamente Microsoft.Sql/servers/bases de dados/ação.


## <a name="next-steps"></a>Passos seguintes
Veja como orientar a gestão da [computação](manage-compute-with-azure-functions.md) Outro aspeto da gestão dos recursos computacionais é alocar diferentes recursos computacionais para consultas individuais. Para mais informações, consulte [as classes de recursos para gestão](resource-classes-for-workload-management.md)da carga de trabalho.
