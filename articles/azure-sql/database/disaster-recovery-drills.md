---
title: Exercícios de recuperação de desastres
description: Aprenda orientação e boas práticas para usar a Base de Dados Azure SQL para realizar exercícios de recuperação de desastres.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: b2ba1d07983021e376a950fba789db0b51c11d4b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050430"
---
# <a name="performing-disaster-recovery-drill"></a>Executar broca de recuperação de desastres
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Recomenda-se que a validação da prontidão da aplicação para o fluxo de trabalho de recuperação seja efetuada periodicamente. Verificar o comportamento da aplicação e implicações da perda de dados e/ou a perturbação que o failover envolve é uma boa prática de engenharia. É também uma exigência da maioria dos padrões do setor como parte da certificação de continuidade de negócios.

A realização de um exercício de recuperação de desastres consiste em:

* Simulação da interrupção do nível de dados
* Recuperação
* Validar a integridade da aplicação após a recuperação

Dependendo da forma como concebeu a [sua aplicação para](business-continuity-high-availability-disaster-recover-hadr-overview.md)a continuidade do negócio, o fluxo de trabalho para executar a broca pode variar. Este artigo descreve as melhores práticas para realizar um exercício de recuperação de desastres no contexto da Base de Dados Azure SQL.

## <a name="geo-restore"></a>Georrestauro

Para evitar a potencial perda de dados ao realizar uma broca de recuperação de desastres, execute a broca utilizando um ambiente de ensaio, criando uma cópia do ambiente de produção e utilizando-a para verificar o fluxo de trabalho de failover da aplicação.

### <a name="outage-simulation"></a>Simulação de interrupção

Para simular a interrupção, pode mudar o nome da base de dados de origem. Esta mudança de nome causa falhas na conectividade da aplicação.

### <a name="recovery"></a>Recuperação

* Execute o geo-restauro da base de dados num servidor diferente, como descrito [aqui](disaster-recovery-guidance.md).
* Altere a configuração da aplicação para se ligar à base de dados recuperada e siga a [configuração de uma base](disaster-recovery-guidance.md) de dados após o guia de recuperação para completar a recuperação.

### <a name="validation"></a>Validação

Complete a broca verificando a integridade da aplicação após a recuperação (incluindo cordas de ligação, logins, testes básicos de funcionalidade ou outras validações parte dos procedimentos padrão de sinalização de aplicação).

## <a name="failover-groups"></a>Grupos de ativação pós-falha

Para uma base de dados protegida utilizando grupos failover, o exercício de perfuração envolve falha planeada para o servidor secundário. A falha planeada garante que as bases de dados primárias e secundárias do grupo failover permanecem sincronizadas quando as funções são trocadas. Ao contrário da falha não planeada, esta operação não resulta em perda de dados, pelo que a broca pode ser realizada no ambiente de produção.

### <a name="outage-simulation"></a>Simulação de interrupção

Para simular a interrupção, pode desativar a aplicação web ou a máquina virtual ligada à base de dados. Esta simulação de interrupção resulta nas falhas de conectividade para os clientes da web.

### <a name="recovery"></a>Recuperação

* Certifique-se de que a configuração da aplicação na região DR aponta para o secundário anterior, que se torna o novo primário totalmente acessível.
* Inicie [a falha planeada](scripts/setup-geodr-and-failover-database-powershell.md) do grupo failover a partir do servidor secundário.
* Siga a base de [dados configurar uma base](disaster-recovery-guidance.md) de dados após o guia de recuperação para completar a recuperação.

### <a name="validation"></a>Validação

Complete a broca verificando a integridade da aplicação após a recuperação (incluindo conectividade, teste de funcionalidade básica ou outras validações necessárias para as assinaturas de broca).

## <a name="next-steps"></a>Próximos passos

* Para conhecer cenários de continuidade de negócios, consulte [cenários de continuidade.](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Para saber sobre cópias de segurança automatizadas da Base de Dados Azure SQL, consulte cópias de segurança automatizadas da Base de [Dados SQL](automated-backups-overview.md)
* Para aprender a utilizar cópias de segurança automatizadas para recuperação, consulte [restaurar uma base de dados das cópias de segurança iniciadas pelo serviço](recovery-using-backups.md).
* Para aprender sobre opções de recuperação mais rápidas, consulte a [geo-replicação ativa](active-geo-replication-overview.md) e [os grupos de auto-failover](auto-failover-group-overview.md).
