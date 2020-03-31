---
title: Alta disponibilidade - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo fornece informações sobre alta disponibilidade na Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 80229ff78c4570db583f1218d5d2f72da2dec388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768576"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Conceitos de alta disponibilidade na Base de Dados Azure para PostgreSQL - Servidor Único
A Base de Dados Azure para o serviço PostgreSQL proporciona um elevado nível de disponibilidade garantido. O acordo de nível de serviço sanções financeiramente apoiado (SLA) é de 99,99% mediante disponibilidade geral. Não há praticamente nenhum tempo de paragem de aplicação ao utilizar este serviço.

## <a name="high-availability"></a>Elevada disponibilidade
O modelo de alta disponibilidade (HA) baseia-se em mecanismos de falha incorporados quando ocorre uma interrupção do nível do nó. Uma interrupção ao nível do nó pode ocorrer devido a uma falha de hardware ou em resposta a uma implementação de serviço.

Em todos os momentos, as alterações feitas numa base de dados Azure para servidor de base de dados PostgreSQL ocorrem no contexto de uma transação. As alterações são registadas sincronizadamente no armazenamento do Azure quando a transação é cometida. Se ocorrer uma interrupção do nível do nó, o servidor de base de dados cria automaticamente um novo nó e anexa o armazenamento de dados ao novo nó. Quaisquer ligações ativas são retiradas e quaisquer transações de bordo não são cometidas.

## <a name="application-retry-logic-is-essential"></a>A lógica de retry da aplicação é essencial
É importante que as aplicações de base de dados PostgreSQL sejam construídas para detetar e retentar ligações abandonadas e transações falhadas. Quando a aplicação se retenta, a ligação da aplicação é redirecionada de forma transparente para a instância recém-criada, que assume a instância falhada.

Internamente em Azure, é utilizado um portal para redirecionar as ligações para a nova instância. Após uma interrupção, todo o processo de falha normalmente leva dezenas de segundos. Uma vez que o redirecionamento é manuseado internamente pelo portal, a cadeia de ligação externa permanece a mesma para as aplicações do cliente.

## <a name="scaling-up-or-down"></a>Escalando para cima ou para baixo
Semelhante ao modelo HA, quando uma Base de Dados Azure para PostgreSQL é dimensionada para cima ou para baixo, é criada uma nova instância de servidor com o tamanho especificado. O armazenamento de dados existente é separado da instância original, e anexado à nova instância.

Durante o funcionamento da escala, ocorre uma interrupção das ligações da base de dados. As aplicações do cliente são desligadas e as transações abertas não comprometidas são canceladas. Uma vez que a aplicação do cliente retenta a ligação, ou faz uma nova ligação, o gateway direciona a ligação à instância de tamanho recente. 

## <a name="next-steps"></a>Passos seguintes
- Aprenda sobre lidar com erros de [conectividade transitória](concepts-connectivity.md)
- Saiba como [replicar os seus dados com réplicas de leitura](howto-read-replicas-portal.md)
