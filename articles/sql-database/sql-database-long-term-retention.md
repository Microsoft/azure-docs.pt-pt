---
title: Store cópias de segurança da base de dados do Azure SQL para até 10 anos | Documentos da Microsoft
description: Saiba como o SQL Database do Azure suporta armazenar cópias de segurança completa da base de dados para até 10 anos.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 85757ace20501bea1db22ecfdd2fdb63284038d5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108751"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Store cópias de segurança da base de dados do Azure SQL para até 10 anos

Muitos aplicativos têm regulamentação, conformidade ou de outras empresas fins a que tem de manter cópias de segurança da base de dados além dos dias de 7-35 fornecidas pela base de dados do Azure SQL [cópias de segurança automáticas](sql-database-automated-backups.md). Ao utilizar a funcionalidade de retenção (LTR) de longo prazo, pode armazenar especificadas SQL da base de dados cópias de segurança completas na [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) armazenamento de BLOBs para até 10 anos. Pode, posteriormente, restaurar qualquer cópia de segurança como uma nova base de dados.

> [!NOTE]
> LTR pode ser ativada para bases de dados únicos e em pool. É ainda não disponível por exemplo as bases de dados em instâncias geridas. Pode usar trabalhos do SQL Agent para agendar [cópias de segurança da base de dados apenas de cópia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa ao LTR para além de 35 dias.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Como funciona a retenção de longa duração de base de dados SQL

Retenção de cópia de segurança de longo prazo (LTR) aproveita as cópias de segurança completa da base de dados que são [criada automaticamente](sql-database-automated-backups.md) para ativar o restauro de momento (PITR). Estas cópias de segurança são copiadas para blobs de armazenamento diferentes, se a política LTR estiver configurada.
Pode configurar uma política LTR para cada base de dados SQL e especifique a frequência com que precisa copiar as cópias de segurança para os blobs de armazenamento de longo prazo. Para ativar essa flexibilidade pode definir a política utilizando uma combinação de quatro parâmetros: retenção de cópia de segurança semanal (W), retenção de cópia de segurança mensal (M), a retenção de cópia de segurança anual (Y) e a semana do ano (WeekOfYear). Se especificar W, uma cópia de segurança todas as semanas será copiado para o armazenamento de longa duração. Se especificar M, uma cópia de segurança durante a primeira semana de cada mês será copiada para o armazenamento de longo prazo. Se especificar Y, uma cópia de segurança durante a semana especificada pelo WeekOfYear será copiada para o armazenamento de longo prazo. Cada cópia de segurança será mantida no armazenamento de longo prazo para o período especificado por estes parâmetros. 

Exemplos:

-  W=0, M=0, Y=5, WeekOfYear=3

   A cópia de segurança completa 3º de cada ano será mantida durante 5 anos.
- W=0, M=3, Y=0

   O primeiro backup completo de cada mês será mantido durante três meses.

- W=12, M=0, Y=0

   Cada cópia de segurança completa semanal será mantida para 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada cópia de segurança completa semanal será mantida durante 6 semanas. Exceto a primeira cópia de segurança completa de cada mês, que será mantido durante 12 meses. Exceto o backup completo, que foram tomado 16 semana do ano, que será mantido para 10 anos. 

A tabela seguinte ilustra a cadência e expiração de cópias de segurança de longo prazo para a seguinte política:

W = 12 semanas (84 dias), M = 12 meses (365 dias), Y = 10 anos (3650 dias), WeekOfYear = 15 (uma semana após 15 de Abril)

   ![exemplo de ltr](./media/sql-database-long-term-retention/ltr-example.png)



Se modificar a política acima e W = 0 (não existem cópias de segurança semanais), a cadência de conjunto de cópias de segurança seria alterado como mostrado na tabela acima, as datas realçadas. A quantidade de armazenamento necessária para manter estas cópias de segurança reduziria em conformidade. 

> [!NOTE]
> 1. As cópias LTR são criadas pelo serviço de armazenamento do Azure, para que o processo de cópia não tem qualquer impacto de desempenho na base de dados existente.
> 2. A política aplica-se para as cópias de segurança futuras. Por exemplo, Se for o WeekOfYear especificado no passado, quando a política está configurada, a primeira cópia de segurança LTR será criada próximo ano. 
> 3. Para restaurar uma base de dados a partir do armazenamento LTR, pode selecionar uma cópia de segurança específica com base na respetiva timestamp.   A base de dados pode ser restaurada a qualquer servidor existente na mesma subscrição da base de dados original. 

## <a name="geo-replication-and-long-term-backup-retention"></a>Georreplicação e retenção de cópia de segurança de longo prazo

Se estiver a utilizar grupos de replicação geográfica ou da ativação pós-falha Active Directory como a sua solução de continuidade do negócio deve se preparar para as ativações pós-falha eventual e configurar a mesma política LTR na base de dados geo-secundária. Isto não irá aumentar o custo de armazenamento LTR como cópias de segurança não são geradas a partir de bases de dados secundárias. Apenas quando o secundário se torna primário as cópias de segurança serão criadas. Desta forma, garantirá não interrompidos geração das cópias de segurança LTR quando a ativação pós-falha é acionada e o principal move para a região secundária. 

> [!NOTE]
> Quando recupera a base de dados primária original da falha do que fazer com que a ativação pós-falha, ela se tornará um secundário novo. Por conseguinte, a criação de cópia de segurança não será retomada e a política LTR existente não irá surtir efeito até que ele se torna o principal novamente. 

## <a name="configure-long-term-backup-retention"></a>Configurar a retenção de cópias de segurança de longa duração

Para saber como configurar a retenção de longa duração com o portal do Azure ou através do PowerShell, veja [configurar a retenção de cópia de segurança de longo prazo](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Passos Seguintes

Uma vez que as cópias de segurança da base de dados proteger os dados contra danos acidentais ou eliminação, eles são uma parte essencial de qualquer continuidade do negócio e a estratégia de recuperação após desastre. Para saber mais sobre as outras soluções de continuidade do negócio de base de dados SQL, veja [descrição geral da continuidade de negócio](sql-database-business-continuity.md).
