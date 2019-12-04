---
title: Alta disponibilidade-banco de dados do Azure para MariaDB
description: Este tópico fornece informações de alta disponibilidade ao usar o banco de dados do Azure para MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 797a94a561351ac7f5317f2f215b56f6944c023f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772531"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Conceitos de alta disponibilidade no banco de dados do Azure para MariaDB
O banco de dados do Azure para o serviço MariaDB fornece um alto nível de disponibilidade garantido. O SLA (contrato de nível de serviço) com suporte financeiro é de 99,99% na disponibilidade geral. Praticamente não há nenhum tempo de inatividade de aplicativo ao usar esse serviço.

## <a name="high-availability"></a>Elevada disponibilidade
O modelo de alta disponibilidade (HA) é baseado em mecanismos de failover internos quando ocorre uma interrupção no nível do nó. Uma interrupção no nível de nó pode ocorrer devido a uma falha de hardware ou em resposta a uma implantação de serviço.

Em todos os momentos, as alterações feitas em um servidor de banco de dados do Azure para MariaDB ocorrem no contexto de uma transação. As alterações são gravadas de forma síncrona no armazenamento do Azure quando a transação é confirmada. Se ocorrer uma interrupção no nível do nó, o servidor de banco de dados criará automaticamente um novo nó e anexará o armazenamento de dados ao novo nó. Todas as conexões ativas são descartadas e todas as transações de em andamento não são confirmadas.

## <a name="application-retry-logic-is-essential"></a>A lógica de repetição do aplicativo é essencial
É importante que os aplicativos de banco de dados MariaDB sejam criados para detectar e tentar descartar conexões e transações com falha. Quando o aplicativo tenta novamente, a conexão do aplicativo é redirecionada de forma transparente para a instância recém-criada, que assume o controle da instância com falha.

Internamente no Azure, um gateway é usado para redirecionar as conexões para a nova instância. Após uma interrupção, todo o processo de failover normalmente leva dezenas de segundos. Como o redirecionamento é manipulado internamente pelo gateway, a cadeia de conexão externa permanece a mesma para os aplicativos cliente.

## <a name="scaling-up-or-down"></a>Aumentando ou reduzindo
Semelhante ao modelo de alta disponibilidade, quando um banco de dados do Azure para MariaDB é dimensionado ou reduzido verticalmente, uma nova instância de servidor com o tamanho especificado é criada. O armazenamento de dados existente é desanexado da instância original e anexado à nova instância.

Durante a operação de dimensionamento, ocorre uma interrupção nas conexões de banco de dados. Os aplicativos cliente são desconectados e as transações não confirmadas abertas são canceladas. Depois que o aplicativo cliente tentar novamente a conexão ou fizer uma nova conexão, o gateway direcionará a conexão para a instância de tamanho mais recente.

## <a name="next-steps"></a>Passos seguintes
- Para obter uma visão geral do serviço, consulte [visão geral do banco de dados do Azure para MariaDB](overview.md)
