---
title: Análises de recuperação de desastre
description: Conheça as diretrizes e as práticas recomendadas para usar o banco de dados SQL do Azure para realizar análises de recuperação de desastre.
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
ms.openlocfilehash: 3ca00a03976ae38b7956616b8287220a7bc5998c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825857"
---
# <a name="performing-disaster-recovery-drill"></a>Executando a análise de recuperação de desastre

É recomendável que a validação da preparação do aplicativo para o fluxo de trabalho de recuperação seja executada periodicamente. Verificar o comportamento do aplicativo e as implicações de perda de dados e/ou a interrupção que o failover envolve é uma boa prática de engenharia. Ele também é um requisito da maioria dos padrões do setor como parte da certificação de continuidade de negócios.

A execução de uma análise de recuperação de desastre consiste em:

* Simulação da interrupção da camada de dados
* Recuperação
* Validar a integridade do aplicativo após a recuperação

Dependendo de como você [criou seu aplicativo para continuidade dos negócios](sql-database-business-continuity.md), o fluxo de trabalho para executar a análise pode variar. Este artigo descreve as práticas recomendadas para realizar uma análise de recuperação de desastre no contexto do banco de dados SQL do Azure.

## <a name="geo-restore"></a>Georrestauro

Para evitar a possível perda de dados ao realizar uma análise de recuperação de desastre, execute a análise usando um ambiente de teste criando uma cópia do ambiente de produção e usando-a para verificar o fluxo de trabalho de failover do aplicativo.

### <a name="outage-simulation"></a>Simulação de interrupção

Para simular a interrupção, você pode renomear o banco de dados de origem. Essa alteração de nome causa falhas de conectividade do aplicativo.

### <a name="recovery"></a>Recuperação

* Execute a restauração geográfica do banco de dados em um servidor diferente, conforme descrito [aqui](sql-database-disaster-recovery.md).
* Altere a configuração do aplicativo para se conectar ao banco de dados recuperado e siga o guia [configurar um banco de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

### <a name="validation"></a>Validação

Conclua a análise verificando a integridade do aplicativo após a recuperação (incluindo cadeias de conexão, logons, testes de funcionalidade básica ou outras validações que fazem parte dos procedimentos de aprovações do aplicativo padrão).

## <a name="failover-groups"></a>Grupos de ativação pós-falha

Para um banco de dados protegido usando grupos de failover, o exercício de análise envolve o failover planejado para o servidor secundário. O failover planejado garante que os bancos de dados primários e secundários no grupo de failover permaneçam sincronizados quando as funções forem alternadas. Ao contrário do failover não planejado, essa operação não resulta em perda de dados, portanto, a análise pode ser executada no ambiente de produção.

### <a name="outage-simulation"></a>Simulação de interrupção

Para simular a interrupção, você pode desabilitar o aplicativo Web ou a máquina virtual conectada ao banco de dados. Essa simulação de interrupção resulta em falhas de conectividade para os clientes Web.

### <a name="recovery"></a>Recuperação

* Verifique se a configuração do aplicativo na região de DR aponta para o secundário anterior, que se torna o novo primário totalmente acessível.
* Inicie o [failover planejado](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) do grupo de failover do servidor secundário.
* Siga o guia [configurar um banco de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

### <a name="validation"></a>Validação

Conclua a análise verificando a integridade do aplicativo após a recuperação (incluindo conectividade, teste de funcionalidade básica ou outras validações necessárias para as aprovações de análise).

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre cenários de continuidade de negócios, consulte [cenários de continuidade](sql-database-business-continuity.md).
* Para saber mais sobre os backups automatizados do banco de dados SQL do Azure, confira [backups automatizados](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados para recuperação, consulte [restaurar um banco de dados dos backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
* Para saber mais sobre as opções de recuperação mais rápidas, consulte [replicação geográfica ativa](sql-database-active-geo-replication.md) e [grupos de failover automático](sql-database-auto-failover-group.md).
