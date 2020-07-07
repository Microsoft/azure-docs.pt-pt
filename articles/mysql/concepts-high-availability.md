---
title: Alta disponibilidade - Base de Dados Azure para MySQL
description: Este tópico fornece informações de alta disponibilidade ao utilizar a Base de Dados Azure para o MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a793de35ffff84009d362f005e599b4419f0763f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79532778"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Conceitos de alta disponibilidade na Base de Dados Azure para o MySQL
A Base de Dados Azure para o serviço MySQL proporciona um elevado nível de disponibilidade garantido. O contrato de nível de serviços com apoio financeiro (SLA) é de 99,99% em termos de disponibilidade geral. Não existe praticamente nenhum tempo de insusição de aplicação ao utilizar este serviço.

## <a name="high-availability"></a>Elevada disponibilidade
O modelo de alta disponibilidade (HA) baseia-se em mecanismos incorporados de fail-over quando ocorre uma interrupção do nível do nó. Uma interrupção do nível do nó pode ocorrer devido a uma falha de hardware ou em resposta a uma implementação de serviço.

Em todos os momentos, as alterações efetuadas a uma base de dados Azure para o servidor de base de dados MySQL ocorrem no contexto de uma transação. As alterações são registadas sincronizadamente no armazenamento do Azure quando a transação é efecída. Se ocorrer uma interrupção do nível do nó, o servidor de base de dados cria automaticamente um novo nó e anexa o armazenamento de dados ao novo nó. Quaisquer ligações ativas são retiradas e quaisquer transações de bordo não são cometidas.

## <a name="application-retry-logic-is-essential"></a>A lógica de relemisão da aplicação é essencial
É importante que as aplicações de base de dados do MySQL sejam construídas para detetar e revarrar ligações e transações falhadas. Quando a aplicação é retrácente, a ligação da aplicação é redirecionada de forma transparente para o caso recém-criado, que assume o exemplo falhado.

Internamente em Azure, um gateway é usado para redirecionar as ligações para o novo exemplo. Após uma interrupção, todo o processo de fail-over normalmente leva dezenas de segundos. Uma vez que o redirecionamento é manuseado internamente pelo gateway, a cadeia de ligação externa permanece a mesma para as aplicações do cliente.

## <a name="scaling-up-or-down"></a>Escalonamento para cima ou para baixo
Semelhante ao modelo HA, quando uma Base de Dados Azure para o MySQL é dimensionada para cima ou para baixo, uma nova instância do servidor com o tamanho especificado é criada. O armazenamento de dados existente é separado da instância original, e anexado à nova instância.

Durante a operação de escala, ocorre uma interrupção das ligações da base de dados. As aplicações do cliente estão desligadas e as transações abertas não comprometidas são canceladas. Uma vez que a aplicação do cliente recauchuta a ligação, ou faz uma nova ligação, o gateway direciona a ligação para a nova instância de tamanho. 

## <a name="next-steps"></a>Passos seguintes
- Saiba como [lidar com erros de conectividade transitórios](concepts-connectivity.md)
- Saiba como [replicar os seus dados com réplicas lidas](howto-read-replicas-portal.md)
