---
title: Testes de recuperação após desastre
description: Aprenda orientação e boas práticas para utilizar a Base de Dados Azure SQL para realizar exercícios de recuperação de desastres.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: f53a08a12c5afda8dbc3f25d9102f52b870ceea4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91321667"
---
# <a name="performing-disaster-recovery-drills"></a>Realização de exercícios de recuperação de desastres
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Recomenda-se que a validação da prontidão da aplicação para o fluxo de trabalho de recuperação seja realizada periodicamente. Verificar o comportamento da aplicação e as implicações da perda de dados e/ou a perturbação que o failover envolve é uma boa prática de engenharia. É também um requisito da maioria dos padrões do setor como parte da certificação de continuidade do negócio.

A realização de um exercício de recuperação de desastres consiste em:

* Simulação de interrupção do nível de dados
* Recuperação
* Validar a integridade da aplicação após a recuperação

Dependendo da forma como [concebeu a sua aplicação para a continuidade](business-continuity-high-availability-disaster-recover-hadr-overview.md)do negócio, o fluxo de trabalho para executar a broca pode variar. Este artigo descreve as melhores práticas para a realização de um exercício de recuperação de desastres no contexto da Base de Dados Azure SQL.

## <a name="geo-restore"></a>Georrestauro

Para evitar a perda de dados potenciais durante a realização de uma broca de recuperação de desastres, efetue a broca utilizando um ambiente de teste, criando uma cópia do ambiente de produção e utilizando-a para verificar o fluxo de trabalho de failover da aplicação.

### <a name="outage-simulation"></a>Simulação de paragem

Para simular a interrupção, pode mudar o nome da base de dados de origem. Esta alteração de nome causa falhas na conectividade da aplicação.

### <a name="recovery"></a>Recuperação

* Execute o geo-restauro da base de dados num servidor diferente, tal como descrito [aqui](disaster-recovery-guidance.md).
* Altere a configuração da aplicação para ligar à base de dados recuperada e siga a [base de dados configurar uma base de dados após](disaster-recovery-guidance.md) o guia de recuperação para concluir a recuperação.

### <a name="validation"></a>Validação

Complete a broca verificando a recuperação pós-recuperação da integridade da aplicação (incluindo cordas de ligação, logins, testes básicos de funcionalidade ou outras validações parte dos procedimentos de assinaturas de aplicação padrão).

## <a name="failover-groups"></a>Grupos de ativação pós-falha

Para uma base de dados protegida utilizando grupos de failover, o exercício de perfuração envolve falha planeada para o servidor secundário. O failover planeado garante que as bases de dados primárias e secundárias do grupo de failover permanecem sincronizadas quando as funções são trocadas. Ao contrário do failover não planeado, esta operação não resulta em perda de dados, pelo que a broca pode ser realizada no ambiente de produção.

### <a name="outage-simulation"></a>Simulação de paragem

Para simular a falha, pode desativar a aplicação web ou a máquina virtual ligada à base de dados. Esta simulação de interrupção resulta em falhas de conectividade para os clientes web.

### <a name="recovery"></a>Recuperação

* Certifique-se de que a configuração da aplicação na região dr aponta para o antigo secundário, que se torna o novo primário totalmente acessível.
* Iniciar [a falha planeada](scripts/setup-geodr-and-failover-database-powershell.md) do grupo de failover a partir do servidor secundário.
* Siga a [base de dados configurar uma base de dados após](disaster-recovery-guidance.md) o guia de recuperação para concluir a recuperação.

### <a name="validation"></a>Validação

Complete a broca verificando a recuperação pós-recuperação da integridade da aplicação (incluindo conectividade, testes básicos de funcionalidade ou outras validações necessárias para as sinalizações de broca).

## <a name="next-steps"></a>Passos seguintes

* Para conhecer os cenários de continuidade do negócio, consulte [cenários de continuidade.](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Para saber mais sobre backups automatizados da Base de Dados Azure SQL, consulte [backups automatizados da BASE de Dados SQL](automated-backups-overview.md)
* Para saber mais sobre a utilização de backups automatizados para recuperação, consulte [restaurar uma base de dados a partir das cópias de segurança iniciadas pelo serviço.](recovery-using-backups.md)
* Para saber mais rapidamente opções de recuperação, consulte os [grupos de geo-replicação ativa](active-geo-replication-overview.md) e [auto-failover](auto-failover-group-overview.md).
