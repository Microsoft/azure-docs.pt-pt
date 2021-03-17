---
title: Gerir recurso computacional para piscina SQL dedicada (anteriormente SQL DW)
description: Saiba mais sobre as capacidades de escala de desempenho para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics. Dimensione ajustando OS DWUs, ou reduzindo os custos, fazendo uma pausa na piscina de SQL dedicada (anteriormente SQL DW).
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 20087fbf4d5c37f1501df08cc294a10ddb9118e1
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601819"
---
# <a name="manage-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Gerir o cálculo para piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics

Saiba mais sobre a gestão de recursos computativos dedicados à piscina SQL (anteriormente SQL DW) em Azure Synapse Analytics. Custos mais baixos fazendo uma pausa na piscina dedicada SQL, ou escala a piscina dedicada SQL para atender às exigências de desempenho.

## <a name="what-is-compute-management"></a>O que é a gestão da computação?

A arquitetura da piscina dedicada SQL (anteriormente SQL DW) separa o armazenamento e o cálculo, permitindo que cada um escalar de forma independente. Como resultado, pode dimensionar a computação para satisfazer as necessidades de desempenho independentemente do armazenamento de dados. Também pode colocar em pausa e retomar recursos de computação. Uma consequência natural desta arquitetura é que [a faturação](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) para computação e armazenamento é separada. Se não precisar de utilizar a sua piscina SQL dedicada (anteriormente SQL DW) durante algum tempo, pode economizar custos de cálculo fazendo uma pausa no cálculo.

## <a name="scaling-compute"></a>Cálculo de escala

Pode escalar ou escalar o cálculo de trás ajustando a definição de unidades de armazém de [dados](what-is-a-data-warehouse-unit-dwu-cdwu.md) para a sua piscina DE SQL dedicada (anteriormente SQL DW). O desempenho de consulta e de carregamento pode aumentar de forma linear à medida que adiciona mais unidades de armazém de dados.

Para obter passos de escala, consulte o [portal Azure,](quickstart-scale-compute-portal.md) [PowerShell](quickstart-scale-compute-powershell.md)ou [T-SQL](quickstart-scale-compute-tsql.md) quickstarts. Também pode realizar operações de escala com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Para realizar uma operação de escala, o pool DE SQL dedicado (anteriormente SQL DW) primeiro mata todas as consultas recebidas e, em seguida, reverte transações para garantir um estado consistente. O dimensionamento só ocorre depois de concluída a reversão de transação. Para uma operação em escala, o sistema separa a camada de armazenamento dos nós de computação, adiciona nós computacional e, em seguida, reacagre a camada de armazenamento para a camada de Computação. Cada piscina SQL dedicada (anteriormente SQL DW) é armazenada como 60 distribuições, que são distribuídas uniformemente para os nós computativos. Adicionar mais nós de computação adiciona mais poder de computação. À medida que o número de nós de computação aumenta, o número de distribuições por nó de computação diminui, proporcionando mais poder de computação para as suas consultas. Da mesma forma, a diminuição das unidades de armazém de dados reduz o número de nós de computação, o que reduz os recursos de computação para consultas.

A tabela seguinte mostra como o número de distribuições por nó compute muda à medida que as unidades de armazém de dados mudam.  O DW30000c fornece 60 nós compute e obtém um desempenho de consulta muito superior ao DO DW100c.

| Unidades do armazém de dados  | \# de nó de computação | \# de distribuições por nó |
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

Para ver os benefícios de desempenho da escala, especialmente para unidades de armazém de dados maiores, você quer usar pelo menos um conjunto de dados de 1-TB. Para encontrar o melhor número de unidades de armazém de dados para a sua piscina DE SQL dedicada (anteriormente SQL DW), tente escalar para cima e para baixo. Execute algumas consultas com diferentes números de unidades de armazém de dados depois de carregar os seus dados. Uma vez que o dimensionamento é rápido, você pode experimentar vários níveis de desempenho em uma hora ou menos.

Recomendações para encontrar o melhor número de unidades de armazém de dados:

- Para uma piscina DE SQL dedicada (anteriormente SQL DW) em desenvolvimento, comece por selecionar um número menor de unidades de armazém de dados.  Um bom ponto de partida é DW400c ou DW200c.
- Monitorize o desempenho da sua aplicação, observando o número de unidades de armazém de dados selecionadas em comparação com o desempenho que observa.
- Assuma uma escala linear e determine quanto precisa para aumentar ou diminuir as unidades de armazém de dados.
- Continue a fazer ajustes até atingir um nível de desempenho ideal para os requisitos do seu negócio.

## <a name="when-to-scale-out"></a>Quando escalar para fora

A escala das unidades de armazém de dados tem impacto nestes aspetos do desempenho:

- Melhora linearmente o desempenho do sistema para digitalizações, agregações e declarações ctas.
- Aumenta o número de leitores e escritores para o carregamento de dados.
- Número máximo de consultas simultâneas e slots de conuncy.

Recomendações para quando escalar as unidades de armazém de dados:

- Antes de realizar uma operação de carregamento ou transformação de dados pesados, dimensione para tornar os dados disponíveis mais rapidamente.
- Durante as horas de ponta, escalone para acomodar um maior número de consultas simultâneas.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>E se a escala não melhorar o desempenho?

Adicionando unidades de armazém de dados aumentando o paralelismo. Se o trabalho for dividido uniformemente entre os nós compute, o paralelismo adicional melhora o desempenho da consulta. Se a escalada não está a mudar o seu desempenho, existem algumas razões pelas quais isso pode acontecer. Os seus dados podem ser distorcidos através das distribuições, ou as consultas podem estar a introduzir uma grande quantidade de movimentos de dados. Para investigar problemas de desempenho de consulta, consulte [a resolução de problemas de desempenho](sql-data-warehouse-troubleshoot.md#performance).

## <a name="pausing-and-resuming-compute"></a>Pausar e retomar computação

A estação de pausa faz com que a camada de armazenamento se desprender dos nós computacional. Os recursos computacional são libertados da sua conta. Não é cobrado por computação enquanto o cálculo é interrompido. O reinício do cálculo recandidata o armazenamento aos nós compute, e retoma os encargos para o Compute.
Quando você pausa uma piscina SQL dedicada (anteriormente SQL DW):

- Os recursos de computação e memória são devolvidos ao conjunto de recursos disponíveis no centro de dados
- Os custos unitários do armazém de dados são zero durante a pausa.
- O armazenamento de dados não é afetado e os seus dados permanecem intactos.
- Todas as operações de corrida ou fila são canceladas.

Quando retomar uma piscina SQL dedicada (anteriormente SQL DW):

- O pool de SQL dedicado (anteriormente SQL DW) adquire recursos de computação e memória para a definição de unidades de armazém de dados.
- Os encargos de cálculo para as suas unidades de armazém de dados retomam.
- Os seus dados ficam disponíveis.
- Depois de a piscina SQL dedicada (anteriormente SQL DW) estar online, você precisa reiniciar suas consultas de carga de trabalho.

Se sempre quiser que a sua piscina SQL dedicada (anteriormente SQL DW) esteja acessível, considere escaloná-la até ao tamanho mais pequeno em vez de fazer uma pausa.

Para pausar e retomar os passos, consulte o [portal Azure,](pause-and-resume-compute-portal.md)ou [o PowerShell](pause-and-resume-compute-powershell.md) quickstarts. Também pode utilizar a pausa [REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) ou o [currículo REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Drenar transações antes de colocar em pausa ou dimensionar

Recomendamos que as transações existentes terminem antes de iniciar uma operação de pausa ou escala.

Quando pausa ou escala a sua piscina DE SQL dedicada (anteriormente SQL DW), nos bastidores as suas consultas são canceladas quando inicia a pausa ou o pedido de escala. Cancelar uma consulta SELECT simples é uma operação rápida e quase não tem impacto sobre o tempo que demora a colocar em pausa ou a dimensionar a sua instância.  No entanto, as consultas transacionais que modificam os seus dados ou a estrutura dos mesmos, podem não ser paradas rapidamente. **As consultas transacionais, por definição, têm de ser concluídas na sua totalidade ou têm de reverter as alterações.** A reversão do trabalho concluído por uma consulta transacional pode demorar tanto tempo, ou mais, como a alteração original que a consulta aplicou. Por exemplo, se cancelar uma consulta que estava a eliminar linhas e que já estiva a ser executada há uma hora, o sistema pode demorar uma hora a inserir novamente as linhas que foram eliminadas. Se colocar em pausa ou dimensionar enquanto as transações estiverem a decorrer, a colocação em pausa ou o dimensionamento podem demorar muito tempo, uma vez que a colocação em pausa e o dimensionamento têm de aguardar que a reversão esteja concluída para poderem continuar.

Consulte também [as transações de compreensão](sql-data-warehouse-develop-transactions.md)e [otimização de transações.](sql-data-warehouse-develop-best-practices-transactions.md)

## <a name="automating-compute-management"></a>Automatização da gestão do cálculo

Para automatizar as operações de gestão de cálculo, consulte [Gerir o cálculo com as funções Azure](manage-compute-with-azure-functions.md).

Cada uma das operações de escala, pausa e retoma pode demorar vários minutos a ser concluída. Se estiver a escalar, fazer uma pausa ou a retomar automaticamente, recomendamos a lógica de implementação para garantir que determinadas operações tenham terminado antes de prosseguir com outra ação. Verificar o estado dedicado da piscina SQL (anteriormente SQL DW) através de vários pontos finais permite-lhe implementar corretamente a automatização de tais operações.

Para verificar o estado dedicado da piscina SQL (anteriormente SQL DW), consulte o arranque rápido [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) ou [T-SQL.](quickstart-scale-compute-tsql.md#check-dedicated-sql-pool-formerly-sql-dw-state) Também pode verificar o estado dedicado da piscina SQL (anteriormente SQL DW) com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).

## <a name="permissions"></a>Permissões

A escala da piscina sql dedicada (anteriormente SQL DW) requer as permissões descritas na [BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Pausa e Currículo requerem a permissão [do Contribuinte DB SQL,](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) especificamente Microsoft.Sql/servidores/bases de dados/ação.

## <a name="next-steps"></a>Passos seguintes

Ver como orientar para [gerir o cálculo](manage-compute-with-azure-functions.md) Outro aspeto da gestão dos recursos computacional é a alocar diferentes recursos computacional para consultas individuais. Para obter mais informações, consulte [as classes de Recursos para a gestão da carga de trabalho.](resource-classes-for-workload-management.md)
