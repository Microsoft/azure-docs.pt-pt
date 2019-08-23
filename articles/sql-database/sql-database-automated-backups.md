---
title: Banco de dados SQL do Azure automático, backups com redundância geográfica | Microsoft Docs
description: O banco de dados SQL cria automaticamente um backup de banco de dados local a cada poucos minutos e usa o armazenamento com redundância geográfica com acesso de leitura do Azure para redundância geográfica.
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
ms.date: 08/22/2019
ms.openlocfilehash: 551c2c02af7b996a34a138586fd91a77a0455d92
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904328"
---
# <a name="automated-backups"></a>Cópias de segurança automatizadas

O banco de dados SQL cria automaticamente os backups de banco de dados que são mantidos entre 7 e 35 dias e usa o [armazenamento com redundância geográfica com acesso de leitura do Azure (ra-grs)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) para garantir que eles sejam preservados mesmo que o Data Center não esteja disponível. Esses backups são criados automaticamente. Os backups de banco de dados são uma parte essencial de qualquer estratégia de continuidade de negócios e recuperação de desastres, pois eles protegem seus dados contra corrupção ou exclusão acidental. Se suas regras de segurança exigirem que seus backups estejam disponíveis por um longo período de tempo (até 10 anos), você poderá configurar uma [retenção de longo prazo](sql-database-long-term-retention.md) em bancos de dados singleton e pools elásticos.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é um backup do banco de dados SQL

O banco de dados SQL usa a tecnologia SQL Server para criar [backups completos](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) toda semana, [backups diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12 horas e backups de log de [Transações](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) a cada 5-10 minutos. Os backups são armazenados em blobs de [armazenamento ra-grs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) que são replicados para um [Data Center emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma interrupção Data Center. Quando você restaura um banco de dados, o serviço descobre quais backups completos, diferenciais e de log de transações precisam ser restaurados.

Você pode usar esses backups para:

- **Restaure um banco de dados existente para um ponto no tempo no passado** dentro do período de retenção usando o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST. Em um banco de dados individual e pools elásticos, essa operação criará um novo banco de dados no mesmo servidor que o banco de dados original. No Instância Gerenciada, essa operação pode criar uma cópia do banco de dados ou o mesmo Instância Gerenciada ou diferente na mesma assinatura.
  - **[Altere o período de retenção de backup](#how-to-change-the-pitr-backup-retention-period)** entre 7 a 35 dias para configurar sua política de backup.
  - **Altere a política de retenção de longo prazo até 10 anos** em banco de dados individual e pools elásticos usando [o portal do Azure](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) ou [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups).
- **Restaure um banco de dados excluído para a hora em que ele foi excluído** ou a qualquer momento dentro do período de retenção. O banco de dados excluído só pode ser restaurado no mesmo servidor lógico ou Instância Gerenciada em que o banco de dados original foi criado.
- **Restaurar um banco de dados para outra região geográfica**. A restauração geográfica permite que você se recupere de um desastre geográfico quando não é possível acessar o servidor e o banco de dados. Ele cria um novo banco de dados em qualquer servidor existente em qualquer lugar do mundo.
- **Restaure um banco de dados de um backup específico de longo prazo** no Banco de Dados Individual ou pool elástico se o banco de dados tiver sido configurado com uma EPD (política de retenção de longo prazo). EPD permite que você restaure uma versão antiga do banco de dados usando [o portal do Azure](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) ou [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups) para atender a uma solicitação de conformidade ou para executar uma versão antiga do aplicativo. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).
- Para executar uma restauração, consulte [restaurar banco de dados de backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se à cópia de arquivos de um local para outro. A *replicação de banco de dados* do SQL refere-se a manter vários bancos de dados secundários sincronizados com um banco de dados primário.

Você pode experimentar algumas dessas operações usando os seguintes exemplos:

| | O portal do Azure | Azure PowerShell |
|---|---|---|
| Alterar retenção de backup | [Banco de Dados Individual](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Instância Gerenciada](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [Banco de Dados Individual](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Alterar retenção de backup de longo prazo | [Banco de dados individual](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Instância Gerenciada-N/A  | [Banco de Dados Individual](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)<br/>Instância Gerenciada-N/A  |
| Restaurar o banco de dados do ponto no tempo | [Banco de dados individual](sql-database-recovery-using-backups.md#point-in-time-restore) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar uma base de dados eliminada | [Banco de dados individual](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurar banco de dados do armazenamento de BLOBs do Azure | Banco de dados único-N/A <br/>Instância Gerenciada-N/A  | Banco de dados único-N/A <br/>[Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Por quanto tempo os backups são mantidos

Todos os bancos de dados SQL do Azure (bancos de dados de instância única, em pool e gerenciados) têm um período de retenção de backup padrão de **sete** dias. Você pode [alterar o período de retenção de backup de até 35 dias](#how-to-change-the-pitr-backup-retention-period).

Se você excluir um banco de dados, o banco de dados SQL manterá os backups da mesma forma que faria para um banco de dados online. Por exemplo, se você excluir um banco de dados básico que tem um período de retenção de sete dias, um backup com quatro dias de idade será salvo por mais três dias.

Se você precisar manter os backups por mais tempo do que o período máximo de retenção, poderá modificar as propriedades de backup para adicionar um ou mais períodos de retenção de longo prazo ao seu banco de dados. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se você excluir o SQL Server do Azure que hospeda bancos de dados SQL, todos os pools elásticos e bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Não é possível restaurar um servidor excluído. Mas se você configurou a retenção de longo prazo, os backups dos bancos de dados com EPD não serão excluídos e esses bancos de dados poderão ser restaurados.

## <a name="how-often-do-backups-happen"></a>Com que frequência os backups acontecem

### <a name="backups-for-point-in-time-restore"></a>Backups para restauração pontual

O banco de dados SQL dá suporte a autoatendimento para PITR (restauração pontual) criando automaticamente backup completo, backups diferenciais e backups de log de transações. Os backups completos de banco de dados são criados semanalmente, os backups de banco de dados diferenciais são geralmente criados a cada 12 horas, e os backups de log de transações geralmente são criados a cada 5-10 minutos, com a frequência com base no tamanho e na quantidade de atividades de O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Ele geralmente é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tiver um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo em um banco de dados restaurado ou uma cópia de banco de dados. Após o primeiro backup completo, todos os backups adicionais são agendados automaticamente e gerenciados silenciosamente em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do banco de dados SQL, pois ele equilibra a carga de trabalho geral do sistema. Você não pode alterar ou desabilitar os trabalhos de backup. 

Os backups do PITR são com redundância geográfica e protegidos pela [replicação entre regiões do armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Para obter mais informações, consulte [restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Backups para retenção de longo prazo

Bancos de dados únicos e em pool oferecem a opção de configurar a indireção de retenção de longo prazo (EPD) de backups completos por até 10 anos no armazenamento de BLOBs do Azure. Se a política EPD estiver habilitada, os backups completos semanais serão copiados automaticamente para um contêiner de armazenamento RA-GRS diferente. Para atender a diferentes requisitos de conformidade, você pode selecionar períodos de retenção diferentes para backups semanais, mensais e/ou anuais. O consumo de armazenamento depende da frequência de backups e dos períodos de retenção selecionados. Você pode usar a [calculadora de preços EPD](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento EPD.

Assim como o PITR, os backups EPD são com redundância geográfica e protegidos pela [replicação entre regiões do armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Para obter mais informações, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Custos de armazenamento
Para bancos de dados individuais, um valor de armazenamento de backup mínimo igual a 100% do tamanho do banco de dados é fornecido sem custo adicional. Para pools elásticos, um valor de armazenamento de backup mínimo igual a 100% do armazenamento de dados alocado para o pool é fornecido sem custo adicional. O consumo adicional do armazenamento de cópias de segurança será cobrado em GB/mês. Esse consumo adicional dependerá da carga de trabalho e do tamanho dos bancos de dados individuais.

Para obter mais informações sobre preços de armazenamento, consulte a página de [preços](https://azure.microsoft.com/pricing/details/sql-database/single/) . 

## <a name="are-backups-encrypted"></a>Os backups são criptografados

Se o banco de dados for criptografado com TDE, os backups serão criptografados automaticamente em repouso, incluindo backups LTR. Quando o TDE está habilitado para um banco de dados SQL do Azure, os backups também são criptografados. Todos os novos bancos de dados SQL do Azure são configurados com TDE habilitado por padrão. Para obter mais informações sobre o TDE, consulte [Transparent Data Encryption com o banco de dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Como a Microsoft garante a integridade do backup

Em uma base contínua, a equipe de engenharia do banco de dados SQL do Azure testa automaticamente a restauração de backups automatizados de banco de dados de bancos que são colocados em servidores lógicos e pools elásticos (isso não está disponível no Instância Gerenciada). Na restauração pontual, os bancos de dados também recebem verificações de integridade usando DBCC CHECKDB.

Instância gerenciada usa o backup inicial automático `CHECKSUM` com os bancos de dados restaurados usando `RESTORE` o comando nativo ou o serviço de migração de data quando a migração é concluída.

Quaisquer problemas encontrados durante a verificação de integridade resultarão em um alerta para a equipe de engenharia. Para obter mais informações sobre a integridade de dados no banco de dados SQL do Azure, consulte [integridade de dados no banco de dados SQL do Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Como os backups automatizados impactam a conformidade

Quando você migra seu banco de dados de uma camada de serviço baseada em DTU com a retenção de PITR padrão de 35 dias para uma camada de serviço baseada em vCore, a retenção de PITR é preservada para garantir que a política de recuperação de dados do aplicativo não seja comprometida. Se a retenção padrão não atender aos seus requisitos de conformidade, você poderá alterar o período de retenção do PITR usando o PowerShell ou a API REST. Para obter mais informações, consulte [alterar período de retenção de backup](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Como alterar o período de retenção do backup PITR

Você pode alterar o período de retenção de backup PITR padrão usando o portal do Azure, o PowerShell ou a API REST. Os valores com suporte são: 7, 14, 21, 28 ou 35 dias. Os exemplos a seguir ilustram como alterar a retenção de PITR para 28 dias.

> [!WARNING]
> Se você reduzir o período de retenção atual, todos os backups existentes mais antigos que o novo período de retenção não estarão mais disponíveis. Se você aumentar o período de retenção atual, o banco de dados SQL manterá os backups existentes até que o período de retenção mais longo seja atingido.

> [!NOTE]
> Essas APIs afetarão apenas o período de retenção do PITR. Se você configurou EPD para seu banco de dados, ele não será afetado. Para obter mais informações sobre como alterar o período de retenção EPD, consulte retenção de [longo prazo](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Alterar o período de retenção de backup PITR usando o portal do Azure

Para alterar o período de retenção de backup PITR usando o portal do Azure, navegue até o objeto de servidor cujo período de retenção você deseja alterar no portal e, em seguida, selecione a opção apropriada com base no objeto de servidor que você está modificando.

#### <a name="change-pitr-for-a-sql-database-server"></a>Alterar PITR para um servidor de banco de dados SQL

![Alterar PITR portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Alterar PITR para um Instância Gerenciada

![Alterar PITR portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Alterar o período de retenção de backup PITR usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Alterar o período de retenção do PITR usando a API REST

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

Código de status: 200

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

Para obter mais informações, consulte [API REST de retenção de backup](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Passos Seguintes

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de continuidade de negócios e recuperação de desastres, pois eles protegem seus dados contra corrupção ou exclusão acidental. Para saber mais sobre as outras soluções de continuidade de negócios do banco de dados SQL do Azure, confira [visão geral](sql-database-business-continuity.md)da continuidade
- Para restaurar para um ponto no tempo usando o portal do Azure, consulte [restaurar banco de dados para um ponto no tempo usando o portal do Azure](sql-database-recovery-using-backups.md).
- Para restaurar para um ponto no tempo usando o PowerShell, consulte [restaurar banco de dados para um ponto no tempo usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados no armazenamento de BLOBs do Azure usando o portal do Azure, consulte [gerenciar retenção de backup de longo prazo usando o portal do Azure](sql-database-long-term-backup-retention-configure.md).
- Para configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados no armazenamento de BLOBs do Azure usando o PowerShell, consulte [gerenciar retenção de backup de longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).
