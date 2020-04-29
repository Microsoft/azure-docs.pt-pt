---
title: Alta disponibilidade - Base de Dados Azure para MariaDB
description: Este tópico fornece informações de elevada disponibilidade ao utilizar a Base de Dados Azure para mariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a87646f6195a06cf0a5382cb248efa5516c953f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79531996"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Conceitos de alta disponibilidade na Base de Dados Azure para MariaDB
A Base de Dados Azure para o serviço MariaDB proporciona um elevado nível de disponibilidade garantido. O acordo de nível de serviço sanções financeiramente apoiado (SLA) é de 99,99% mediante disponibilidade geral. Não há praticamente nenhum tempo de paragem de aplicação ao utilizar este serviço.

## <a name="high-availability"></a>Elevada disponibilidade
O modelo de alta disponibilidade (HA) baseia-se em mecanismos incorporados de falha quando ocorre uma interrupção do nível do nó. Uma interrupção ao nível do nó pode ocorrer devido a uma falha de hardware ou em resposta a uma implementação de serviço.

Em todos os momentos, as alterações feitas numa Base de Dados Azure para servidor de base de dados MariaDB ocorrem no contexto de uma transação. As alterações são registadas sincronizadamente no armazenamento do Azure quando a transação é cometida. Se ocorrer uma interrupção do nível do nó, o servidor de base de dados cria automaticamente um novo nó e anexa o armazenamento de dados ao novo nó. Quaisquer ligações ativas são retiradas e quaisquer transações de bordo não são cometidas.

## <a name="application-retry-logic-is-essential"></a>A lógica de retry da aplicação é essencial
É importante que as aplicações de base de dados MariaDB sejam construídas para detetar e retentar ligações abandonadas e transações falhadas. Quando a aplicação se retenta, a ligação da aplicação é redirecionada de forma transparente para a instância recém-criada, que assume a instância falhada.

Internamente em Azure, é utilizado um portal para redirecionar as ligações para a nova instância. Após uma interrupção, todo o processo de falha normalmente leva dezenas de segundos. Uma vez que o redirecionamento é manuseado internamente pelo portal, a cadeia de ligação externa permanece a mesma para as aplicações do cliente.

## <a name="scaling-up-or-down"></a>Escalando para cima ou para baixo
Semelhante ao modelo HA, quando uma Base de Dados Azure para MariaDB é dimensionada para cima ou para baixo, é criada uma nova instância de servidor com o tamanho especificado. O armazenamento de dados existente é separado da instância original, e anexado à nova instância.

Durante o funcionamento da escala, ocorre uma interrupção das ligações da base de dados. As aplicações do cliente são desligadas e as transações abertas não comprometidas são canceladas. Uma vez que a aplicação do cliente retenta a ligação, ou faz uma nova ligação, o gateway direciona a ligação à instância de tamanho recente.

## <a name="next-steps"></a>Passos seguintes
- Para uma visão geral do serviço, consulte a Base de [Dados Azure para a visão geral](overview.md) do MariaDB
