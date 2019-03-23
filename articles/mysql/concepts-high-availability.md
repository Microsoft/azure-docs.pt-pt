---
title: Conceitos de elevada disponibilidade no banco de dados do Azure para MySQL
description: Este tópico fornece informações de elevada disponibilidade, ao utilizar a base de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 055727695bfa1ce8a6bb160a7e071c2a161afb3b
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351036"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Conceitos de elevada disponibilidade no banco de dados do Azure para MySQL
A base de dados do Azure para o serviço MySQL fornece um nível elevado de disponibilidade garantido. O contrato de nível de serviço com suporte financeiro (SLA) é 99,99% após disponibilidade geral. Praticamente nenhuma aplicação período de indisponibilidade quando utilizar este serviço.

## <a name="high-availability"></a>Elevada disponibilidade
O modelo de elevada disponibilidade (HA) baseia-se a mecanismos internos de ativação pós-falha quando ocorre uma interrupção de nível de nó. Uma interrupção de nível de nó pode ocorrer devido a uma falha de hardware ou em resposta a uma implementação de serviço.

Durante todo o tempo, as alterações efetuadas numa base de dados do Azure para o servidor de base de dados MySQL ocorrerem no contexto de uma transação. As alterações são registradas de forma síncrona no armazenamento do Azure quando a transação será confirmada. Se ocorrer uma interrupção de nível de nó, o servidor de base de dados é automaticamente cria um novo nó e anexa o armazenamento de dados para o novo nó. Todas as ligações ativas são ignoradas e todas as transações em utilização não são confirmadas.

## <a name="application-retry-logic-is-essential"></a>A lógica de repetição do aplicativo é essencial
É importante que MySQL as aplicações de base de dados são criadas para detetar e tente novamente remover ligações e falha transações. Quando o aplicativo repete, ligação da aplicação é redirecionada de forma transparente para a instância recém-criado, que assume para a instância com falha.

Internamente no Azure, um gateway é utilizado para redirecionar as ligações para a nova instância. Após uma interrupção, normalmente, todo o processo de ativação pós-falha se dezenas de segundos. Uma vez que o redirecionamento é tratado internamente pelo gateway, a cadeia de ligação externa permanece o mesmo para as aplicações cliente.

## <a name="scaling-up-or-down"></a>Aumentar ou reduzir verticalmente
Semelhante ao modelo de HA, quando uma base de dados do Azure para MySQL é aumentado vertical ou horizontalmente, é criada uma nova instância de servidor com o tamanho especificado. O armazenamento de dados existente é desligado da instância original e anexado à instância de novo.

Durante a operação de dimensionamento, ocorre uma interrupção para as ligações de base de dados. As aplicações de cliente estão ligado à Internet e abrir as transações não consolidadas serão canceladas. Assim que a aplicação de cliente a ligação, ou faz com que uma nova ligação, o gateway direciona a ligação à instância do tamanho recentemente. 

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [tratamento de erros de conectividade transitório](concepts-connectivity.md)
- Saiba como [replicar os seus dados com réplicas de leitura](howto-read-replicas-portal.md)
