---
title: Armazenar backups por até 10 anos
description: Saiba como a Base de Dados Azure SQL suporta armazenar cópias de dados completas durante um máximo de 10 anos.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 15a2d58d2fc14c370c41d5454d62c74a5b66ad42
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77499968"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Backups de base de dados Azure SQL por um n.º 10 anos

Muitas aplicações têm fins regulamentares, de conformidade ou outros fins comerciais que exigem que você mantenha cópias de dados para além dos 7-35 dias fornecidos pela Base de Dados Azure SQL [cópias automáticas](sql-database-automated-backups.md)de backups . Utilizando a funcionalidade de retenção a longo prazo (LTR), pode armazenar cópias de segurança completas especificadas na base de dados SQL no armazenamento Azure Blob com armazenamento geo-redundante de acesso de leitura até 10 anos. Em seguida, pode restaurar qualquer cópia de segurança como nova base de dados. Para obter mais informações sobre a redundância do Armazenamento Azure, consulte o [despedimento do Azure Storage.](../storage/common/storage-redundancy.md)

> [!NOTE]
> O LTR pode ser ativado para bases de dados individuais e reunidas. Ainda não está disponível, por exemplo, em bases de dados em Casos Geridos. Pode utilizar trabalhos do Agente SQL para agendar [cópias de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como alternativa ao LTR para além de 35 dias.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Como funciona a retenção a longo prazo da Base de Dados SQL

A retenção de backup a longo prazo (LTR) aproveita as cópias de segurança completas que são [criadas automaticamente](sql-database-automated-backups.md) para permitir a restauração do tempo de ponto (PITR). Se uma política LTR estiver configurada, estas cópias de backup são copiadas para diferentes bolhas para armazenamento a longo prazo. A cópia é um trabalho de fundo que não tem impacto no desempenho na carga de trabalho da base de dados. A política LTR para cada base de dados SQL também pode especificar a frequência com que as cópias de segurança LTR são criadas.

Para ativar o LTR, pode definir uma política utilizando uma combinação de quatro parâmetros: retenção semanal de backup (W), retenção mensal de backup (M), retenção anual de backup (Y) e semana do ano (WeekOfYear). Se especificar W, uma cópia de reserva todas as semanas será copiada para o armazenamento a longo prazo. Se especificar M, a primeira cópia de cada mês será copiada para o armazenamento a longo prazo. Se especificar Y, uma cópia de segurança durante a semana especificada pelo WeekOfYear será copiada para o armazenamento a longo prazo. Se o weekofyear especificado for no passado quando a política estiver configurada, o primeiro backup LTR será criado no ano seguinte. Cada cópia de segurança será mantida no armazenamento a longo prazo de acordo com os parâmetros de política que são configurados quando a cópia de segurança LTR é criada.

> [!NOTE]
> Qualquer alteração à política LTR aplica-se apenas a futuros backups. Por exemplo, se a retenção semanal de backup (W), retenção mensal de backup (M) ou retenção anual de backup (Y) for modificada, a nova definição de retenção aplicar-se-á apenas a novas cópias de segurança. A retenção de cópias de segurança existentes não será modificada. Se a sua intenção é eliminar cópias de segurança LTR antigas antes de expirar o seu período de retenção, terá de [eliminar manualmente as cópias de segurança](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Exemplos da política LTR:

-  W=0, M=0, Y=5, WeekOfYear=3

   O terceiro reforço completo de cada ano será mantido por cinco anos.
   
- W=0, M=3, Y=0

   O primeiro reforço completo de cada mês será mantido por três meses.

- W=12, M=0, Y=0

   Cada cópia de segurança semanal será mantida durante 12 semanas.

- W=6, M=12, Y=10, WeekOfYear=16

   Cada cópia de segurança semanal será mantida durante seis semanas. Exceto o primeiro reforço completo de cada mês, que será mantido por 12 meses. Exceto o reforço total da 16ª semana do ano, que será mantido por 10 anos. 

O quadro seguinte ilustra a cadência e a caducidade dos backups a longo prazo para a seguinte política:

W=12 semanas (84 dias), M=12 meses (365 dias), Y=10 anos (3650 dias), WeekOfYear=15 (semana após 15 de abril)

   ![exemplo de trtr](./media/sql-database-long-term-retention/ltr-example.png)



Se modificar a política acima e definir W=0 (sem cópias de reserva semanais), a cadência das cópias de cópias de reserva mudará como mostrado na tabela acima pelas datas realçadas. O valor de armazenamento necessário para manter estas cópias de segurança reduziria em conformidade. 

> [!IMPORTANT]
> O tempo das cópias de segurança LTR individuais é controlado pela Base de Dados Azure SQL. Não é possível criar manualmente uma cópia de segurança LTR ou controlar o tempo da criação de backup. Depois de configurar uma política LTR, pode demorar até 7 dias até que a primeira cópia de segurança LTR apareça na lista de cópias de segurança disponíveis.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Geo-replicação e retenção de backup a longo prazo

Se estiver a utilizar grupos de geo-replicação ou failover ativos como solução de continuidade do negócio, deve preparar-se para eventuais falhas e configurar a mesma política LTR na base de dados geosecundária. O seu custo de armazenamento LTR não aumentará, uma vez que as cópias de segurança não são geradas a partir dos secundários. Só quando o secundário se tornar primário, as cópias de segurança serão criadas. Garante a geração não interrompida dos backups LTR quando a falha é desencadeada e a primeira desloca-se para a região secundária. 

> [!NOTE]
> Quando a base de dados primária original recuperar de uma paragem que causou a falha, tornar-se-á uma nova secundária. Por conseguinte, a criação de backup não será retomada e a atual política lTR não entrará em vigor até que se torne novamente a primária. 

## <a name="configure-long-term-backup-retention"></a>Configurar a retenção de cópias de segurança de longa duração

Para aprender a configurar a retenção a longo prazo utilizando o portal Azure ou powerShell, consulte a retenção de backup a longo prazo da [Manage Azure SQL](sql-database-long-term-backup-retention-configure.md)Database .

## <a name="restore-database-from-ltr-backup"></a>Restaurar a base de dados da cópia de segurança LTR

Para restaurar uma base de dados do armazenamento LTR, pode selecionar uma cópia de segurança específica com base na sua marcação de tempo. A base de dados pode ser restaurada a qualquer servidor existente sob a mesma subscrição que a base de dados original. Para aprender a restaurar a sua base de dados a partir de uma cópia de segurança LTR, utilizando o portal Azure, ou PowerShell, consulte a retenção de backup a longo prazo da [Manage Azure SQL](sql-database-long-term-backup-retention-configure.md)Database .

## <a name="next-steps"></a>Passos Seguintes

Como as cópias de dados protegem os dados de corrupção acidental ou eliminação, são uma parte essencial de qualquer continuidade de negócios e estratégia de recuperação de desastres. Para conhecer as outras soluções de continuidade de negócio da SQL Database, consulte a [visão geral da continuidade do Negócio.](sql-database-business-continuity.md)
