---
title: Backups do Azure de automática, georredundante de base de dados SQL | Documentos da Microsoft
description: Base de dados SQL cria uma cópia de segurança da base de dados local intervalos de poucos minutos e utiliza o armazenamento georredundante de acesso de leitura do Azure para redundância geográfica automaticamente.
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
ms.openlocfilehash: 4a3b48be893c596f1a86bbf308a97868ed546006
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313927"
---
# <a name="automated-backups"></a>Cópias de segurança automatizadas

Base de dados SQL automaticamente cria cópias de segurança da base de dados que são mantidas entre 7 e 35 dias e utiliza o armazenamento com redundância geográfica do Azure do acesso de leitura (RA-GRS) para se certificar de que eles preservados, mesmo que o Centro de dados não está disponível. Estas cópias de segurança são criadas automaticamente e sem encargos adicionais. Não precisa de fazer nada para que aconteçam e pode [alterar o período de retenção de cópia de segurança](#how-to-change-the-pitr-backup-retention-period). Cópias de segurança da base de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade comercial, porque eles protegem os dados de danos acidentais ou eliminação. Se as regras de segurança exigem que as cópias de segurança estão disponíveis por um longo período de tempo (até 10 anos), pode configurar uma [retenção a longo prazo](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é uma cópia de segurança da base de dados SQL

Base de dados SQL utiliza a tecnologia do SQL Server para criar [completo](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server), [diferencial](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server), e [log de transação](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) (PITR) de restauro de cópias de segurança para os objetivos de ponto no tempo. Os backups de log de transação ocorram, geralmente, a cada 5 a 10 minutos e cópias de segurança diferenciais ocorram, geralmente, a cada 12 horas, com a frequência com base no tamanho de computação e quantidade de atividade da base de dados. Cópias de segurança completas, diferenciais e transações de registos permitem-lhe restaurar uma base de dados para um específico ponto anterior no tempo para o mesmo servidor que aloja a base de dados. As cópias de segurança são armazenadas em blobs de armazenamento RA-GRS, que são replicados para uma [Centro de dados emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma falha do Centro de dados. Ao restaurar uma base de dados, o serviço descobre qual log completas, diferenciais e transação cópias de segurança tem de ser restaurada.

Pode utilizar estas cópias de segurança para:

- Restaure uma base de dados para um ponto anterior no tempo dentro do período de retenção. Esta operação irá criar uma nova base de dados no mesmo servidor que a base de dados original.
- Restaure uma base de dados eliminada para o tempo que tiver sido eliminado ou a qualquer altura dentro do período de retenção. Só pode ser restaurado a base de dados eliminada no mesmo servidor onde a base de dados original foi criado.
- Restaure uma base de dados para outra região geográfica. O restauro geográfico permite-lhe recuperar a partir de um desastre geográfico ao não é possível aceder ao seu servidor e base de dados. Ele cria uma nova base de dados em qualquer servidor existente em qualquer lugar no mundo.
- Restaure uma base de dados a partir de uma cópia de segurança de longo prazo específica se a base de dados tiver sido configurado com uma política de retenção de longo prazo (LTR). LTR permite-lhe restaurar uma versão antiga da base de dados para satisfazer uma solicitação de conformidade ou para executar uma versão antiga da aplicação. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).
- Para efetuar um restauro, consulte [restaurar a base de dados de cópias de segurança](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se para copiar os arquivos de uma localização para outra. Do SQL *replicação de base de dados* refere-se ao manter várias bases de dados secundários sincronizados com uma base de dados primária.

## <a name="how-long-are-backups-kept"></a>Quanto tempo as cópias de segurança permanecem

Cada base de dados do SQL tem um período de retenção de cópia de segurança padrão entre 7 e 35 dias que depende do modelo de compra e a camada de serviços. Pode atualizar a cópia de segurança) período de retenção para uma base de dados no servidor de base de dados SQL. Para obter mais informações, consulte [período de retenção de cópia de segurança de alteração](#how-to-change-the-pitr-backup-retention-period).

Se eliminar uma base de dados, base de dados SQL manterá as cópias de segurança da mesma forma que faria para uma base de dados online. Por exemplo, se eliminar uma base de dados básico que tem um período de retenção de sete dias, é guardada uma cópia de segurança que é de quatro dias de antiguidade por três dias mais.

Se precisar de manter as cópias de segurança por mais tempo do que o período de retenção máxima, pode modificar as propriedades de cópia de segurança para adicionar um ou mais períodos de retenção longo prazo para a base de dados. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se eliminar o servidor de SQL do Azure que aloja as bases de dados SQL, tudo de dados elásticas e bases de dados que pertencem ao servidor também são eliminados e não podem ser recuperados. Não é possível restaurar um servidor foi eliminado. Mas se tiver configurado a retenção de longa duração, as cópias de segurança das bases de dados com LTR não serão eliminadas e esses bancos de dados podem ser restaurados.

### <a name="default-backup-retention-period"></a>Período de retenção de cópia de segurança predefinido

#### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

O período de retenção predefinido para uma base de dados criada com o modelo de compra baseado em DTU depende do escalão de serviço:

- O escalão de serviço básico é 1 semana.
- Camada de serviço Standard é cinco semanas.
- Escalão de serviço Premium é cinco semanas.

#### <a name="vcore-based-purchasing-model"></a>Modelo de compras baseado em vCore

Se estiver a utilizar o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), o período de retenção de cópia de segurança predefinido é de 7 dias (para único, agrupados e bases de dados de instância). Para todas as bases de dados SQL do Azure (único, agrupados, e bases de dados de instância, pode [alterar o período de retenção de cópia de segurança 35 dias](#how-to-change-the-pitr-backup-retention-period).

> [!WARNING]
> Se reduzir o período de retenção atual, todas as cópias de segurança existentes mais antigas do que o período de retenção do novo período são já não estar disponível. Se aumentar o período de retenção atual, base de dados SQL manterá as cópias de segurança existentes até que o período de retenção mais longo for atingido.

## <a name="how-often-do-backups-happen"></a>Com que frequência as cópias de segurança acontecem

### <a name="backups-for-point-in-time-restore"></a>Cópias de segurança para restauro para ponto no tempo

Base de dados SQL suporta o Self-Service para o restauro de ponto no tempo (PITR) ao criar automaticamente a cópia de segurança completa, backups diferenciais e backups de log de transação. Cópias de segurança completa da base de dados são criadas semanalmente e cópias de segurança da base de dados diferenciais são geralmente criadas a cada 12 horas para backups de log de transação em geral, são criados a cada 5-10 minutos, com a frequência com base no tamanho de computação e quantidade de atividade da base de dados. A primeira cópia de segurança completa está agendada imediatamente depois de criar uma base de dados. Ela normalmente fica concluída no prazo de 30 minutos, mas pode demorar mais tempo quando a base de dados é um tamanho significativo. Por exemplo, a cópia de segurança inicial pode demorar mais tempo numa base de dados restaurada ou copiar uma base de dados. Após a primeira cópia de segurança completa, todas as cópias de segurança adicionais são agendadas automaticamente e gerenciadas automaticamente em segundo plano. O tempo certo de todas as cópias de segurança da base de dados é determinado pelo serviço de base de dados SQL, como ele faz o balanceamento de carga de trabalho geral do sistema.

As cópias de segurança PITR são georredundante e protegido por [replicação entre regiões de armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Para obter mais informações, consulte [restaurodepontonotempoda](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Cópias de segurança de retenção de longa duração

Bases de dados únicos e em pool oferecem a opção de configurar a retenção de longo prazo (LTR) de cópias de segurança completas para até 10 anos no armazenamento de Blobs do Azure. Se a política LTR estiver ativada, as cópias de segurança completas semanais são copiadas automaticamente para um contentor de armazenamento RA-GRS diferente. Para cumprir o requisito de conformidade diferentes, pode selecionar os períodos de retenção diferentes para cópias de segurança semanais, mensais e/ou anuais. O consumo de armazenamento depende na frequência de cópias de segurança e a retenção period(s) selecionada. Pode utilizar o [Calculadora de preços de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para calcular o custo de armazenamento LTR.

Como PITR, as cópias de segurança LTR são georredundante e estão protegidas pela [replicação do armazenamento do Azure entre regiões](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Para obter mais informações, consulte [retenção de cópia de segurança de longo prazo](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Custos de armazenamento
7 dias de cópias de segurança automatizadas das suas bases de dados são copiados para o armazenamento de blobs Standard RA-GRS por predefinição. O armazenamento é utilizado por cópias de segurança completas semanais, cópias de segurança diferenciais diárias e cópias de segurança de registo de transações copiadas a cada 5 minutos. O tamanho do registo de transação depende da taxa de alteração da base de dados. É dada uma quantidade mínima de armazenamento igual a 100% do tamanho da base de dados sem custos extra. O consumo adicional do armazenamento de cópias de segurança será cobrado em GB/mês.

Para obter mais informações sobre os preços de armazenamento, consulte a [preços](https://azure.microsoft.com/pricing/details/sql-database/single/) página. 

## <a name="are-backups-encrypted"></a>As cópias de segurança são encriptadas

Se a sua base de dados é encriptado com TDE, as cópias de segurança são encriptadas automaticamente em repouso, incluindo cópias de segurança LTR. Quando a TDE está ativada para uma base de dados SQL do Azure, cópias de segurança também são encriptadas. Todas as novas bases de dados do SQL do Azure são configuradas com o TDE ativado por predefinição. Para obter mais informações sobre a TDE, consulte [encriptação de dados transparente com a base de dados do Azure SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Como a que a Microsoft garante a integridade de cópia de segurança

De forma contínua, a equipe de engenharia do Azure SQL Database automaticamente testa o restauro de cópias de segurança da base de dados automatizada das bases de dados em todo o serviço. Após o restauro, a bases de dados também recebem as verificações de integridade com o DBCC CHECKDB. Quaisquer problemas encontrados durante a verificação de integridade irão resultar num alerta para a equipa de engenharia. Para obter mais informações sobre a integridade dos dados na base de dados do Azure SQL, consulte [integridade dos dados na base de dados do Azure SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Como cópias de segurança automáticas afetar conformidade

Quando migra a base de dados de um escalão de serviço baseado em DTU com o período de retenção PITR padrão de 35 dias, para um escalão de serviço baseado em vCore, o período de retenção PITR é preservado para se certificar de que a política de recuperação de dados do seu aplicativo não é comprometida. Se o período de retenção predefinida não cumprir os requisitos de conformidade, pode alterar o período de retenção PITR com o PowerShell ou a REST API. Ver [período de retenção de cópia de segurança de alteração](#how-to-change-the-pitr-backup-retention-period) para obter mais detalhes.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Como alterar o período de retenção de cópia de segurança de PITR

Pode alterar o período de retenção de cópia de segurança de PITR predefinido a utilizar o Portal do Azure, PowerShell ou a API REST. Os valores suportados são: 7, 14, 21, 28 ou 35 dias. Os exemplos seguintes mostram como alterar a retenção PITR 28 dias.

> [!NOTE]
> Essas APIs só terá impacto sobre o período de retenção PITR. Se tiver configurado LTR da base de dados, não será afetada. Para obter mais informações sobre como alterar o period(s) de retenção LTR, consulte [retenção a longo prazo](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Alterar o período de retenção de cópia de segurança de PITR no portal do Azure

Para alterar o período de retenção de cópia de segurança de PITR no portal do Azure, navegue para o objeto de servidor cujo período de retenção que deseja alterar no Portal e, em seguida, selecione a opção adequada com base em qual objeto de servidor que está modificando.

#### <a name="change-pitr-for-a-sql-database-server"></a>Alteração PITR para um servidor de base de dados SQL

![Portal do PITR Azure de alteração](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Alteração PITR para uma instância gerida

![Portal do PITR Azure de alteração](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Alterar o período de retenção de cópia de segurança de PITR com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Alterar o período de retenção PITR com a REST API

#### <a name="sample-request"></a>Pedido de Amostra

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corpo do Pedido

```json
{
  "properties":{  
      "retentionDays":28
   }
}
```

#### <a name="sample-response"></a>Pedido de Resposta

Código de estado: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Para obter mais informações, consulte [API de REST de retenção de cópia de segurança](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Passos Seguintes

- Cópias de segurança da base de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade comercial, porque eles protegem os dados de danos acidentais ou eliminação. Para saber mais sobre as outras base de dados do Azure SQL soluções de continuidade empresarial, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md).
- Para restaurar para um ponto no tempo, com o portal do Azure, consulte [restaurar base de dados para um ponto no tempo com o portal do Azure](sql-database-recovery-using-backups.md).
- Para restaurar para um ponto no tempo com o PowerShell, consulte [restaurar base de dados para um ponto no tempo com o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, gerir e restaurar a partir de retenção de longa duração de cópias de segurança no armazenamento de Blobs do Azure com o portal do Azure automatizadas, consulte [gerir a retenção de cópia de segurança de longa duração com o portal do Azure](sql-database-long-term-backup-retention-configure.md).
- Para configurar, gerir e restaurar a partir de retenção de longa duração de cópias de segurança no armazenamento de Blobs do Azure com o PowerShell automatizadas, consulte [gerir a retenção de cópia de segurança de longa duração com o PowerShell](sql-database-long-term-backup-retention-configure.md).
