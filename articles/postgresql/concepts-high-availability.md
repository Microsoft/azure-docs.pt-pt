---
title: Conceitos de elevada disponibilidade no banco de dados do Azure para PostgreSQL - servidor único
description: Este artigo fornece informações de elevada disponibilidade, ao utilizar a base de dados do Azure para o PostgreSQL – único servidor.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f54c83099957b4d8795c4049be52d70e8a0e2a61
ms.sourcegitcommit: f4469b7bb1f380bf9dddaf14763b24b1b508d57c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "65073441"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Conceitos de elevada disponibilidade no banco de dados do Azure para PostgreSQL - servidor único
A base de dados do Azure para o serviço PostgreSQL oferece um nível elevado de disponibilidade garantido. O contrato de nível de serviço com suporte financeiro (SLA) é 99,99% após disponibilidade geral. Praticamente nenhuma aplicação período de indisponibilidade quando utilizar este serviço.

## <a name="high-availability"></a>Elevada disponibilidade
O modelo de elevada disponibilidade (HA) baseia-se em mecanismos de ativação pós-falha incorporada quando ocorre uma interrupção de nível de nó. Uma interrupção de nível de nó pode ocorrer devido a uma falha de hardware ou em resposta a uma implementação de serviço.

Durante todo o tempo, as alterações efetuadas numa base de dados do Azure para o servidor de base de dados do PostgreSQL ocorrerem no contexto de uma transação. As alterações são registradas de forma síncrona no armazenamento do Azure quando a transação será confirmada. Se ocorrer uma interrupção de nível de nó, o servidor de base de dados é automaticamente cria um novo nó e anexa o armazenamento de dados para o novo nó. Todas as ligações ativas são ignoradas e todas as transações em utilização não são confirmadas.

## <a name="application-retry-logic-is-essential"></a>A lógica de repetição do aplicativo é essencial
É importante que PostgreSQL as aplicações de base de dados são criadas para detetar e tente novamente remover ligações e falha transações. Quando o aplicativo repete, ligação da aplicação é redirecionada de forma transparente para a instância recém-criado, que assume para a instância com falha.

Internamente no Azure, um gateway é utilizado para redirecionar as ligações para a nova instância. Após uma interrupção, normalmente, todo o processo de ativação pós-falha se dezenas de segundos. Uma vez que o redirecionamento é tratado internamente pelo gateway, a cadeia de ligação externa permanece o mesmo para as aplicações cliente.

## <a name="scaling-up-or-down"></a>Aumentar ou reduzir verticalmente
Semelhante ao modelo de HA, quando uma base de dados do Azure para PostgreSQL é aumentado vertical ou horizontalmente, é criada uma nova instância de servidor com o tamanho especificado. O armazenamento de dados existente é desligado da instância original e anexado à instância de novo.

Durante a operação de dimensionamento, ocorre uma interrupção para as ligações de base de dados. As aplicações de cliente estão ligado à Internet e abrir as transações não consolidadas serão canceladas. Assim que a aplicação de cliente a ligação, ou faz com que uma nova ligação, o gateway direciona a ligação à instância do tamanho recentemente. 

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [tratamento de erros de conectividade transitório](concepts-connectivity.md)
- Saiba como [replicar os seus dados com réplicas de leitura](howto-read-replicas-portal.md)
