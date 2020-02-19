---
title: Backups automáticos e geo-redundantes
description: A Base de Dados SQL cria automaticamente uma cópia de dados local a cada poucos minutos e utiliza armazenamento geo-redundante de acesso à leitura azure para geo-redundância.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 16ee8c1e271f0aa3e6565322f9a4a422dd90b8b8
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461782"
---
# <a name="automated-backups"></a>Cópias de segurança automatizadas

A Base de Dados SQL cria automaticamente as cópias de segurança da base de dados que são mantidas durante o período de retenção configurada e utiliza o armazenamento geo-redundante de acesso à leitura azure [(RA-GRS)](../storage/common/storage-redundancy.md) para garantir que são preservadas mesmo que o centro de dados não esteja disponível. Estas cópias de segurança são criadas automaticamente. Os backups de bases de dados são uma parte essencial de qualquer estratégia de continuidade do negócio e recuperação de desastres porque protegem os seus dados de corrupção acidental ou eliminação. Se as suas regras de segurança exigirem que as suas cópias de segurança estejam disponíveis por um longo período de tempo (até 10 anos), pode configurar uma [retenção a longo prazo](sql-database-long-term-retention.md) nas bases de dados singleton e piscinas elásticas.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é uma cópia de segurança da Base de Dados SQL

A Base de Dados SQL utiliza a tecnologia SQL Server para criar cópias de [segurança completas](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) todas as semanas, [backups diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12 horas e backups de registo de [transações](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) a cada 5-10 minutos. As cópias de segurança são armazenadas em bolhas de [armazenamento RA-GRS](../storage/common/storage-redundancy.md) que são replicadas a um centro de [dados emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma falha no centro de dados. Quando restaura uma base de dados, o serviço calcula quais as cópias de segurança completas, diferenciais e de registo de transações que precisam de ser restauradas.

Pode utilizar estas cópias de segurança para:

- **Restaurar uma base de dados existente para um ponto-a-tempo no passado** no período de retenção utilizando o portal Azure, Azure PowerShell, Azure CLI ou REST API. Em bases de dados únicas e piscinas elásticas, esta operação criará uma nova base de dados no mesmo servidor que a base de dados original. Em Caso Gerido, esta operação pode criar uma cópia da base de dados ou mesma ou diferente Instância Gerida sob a mesma subscrição.
- **Restaurar uma base de dados eliminada até ao momento em que foi eliminada** ou a qualquer momento no período de retenção. A base de dados eliminada só pode ser restaurada no mesmo servidor lógico ou em caso de gestão onde a base de dados original foi criada.
- **Restaurar uma base de dados para outra região geográfica.** A geo-restauração permite-lhe recuperar de um desastre geográfico quando não consegue aceder ao seu servidor e base de dados. Cria uma nova base de dados em qualquer servidor existente em qualquer lugar do mundo.
- **Restaurar uma base de dados a partir de uma cópia** de segurança específica a longo prazo na Base de Dados Única ou no Pool Elástico se a base de dados tiver sido configurada com uma política de retenção a longo prazo (LTR). O LTR permite-lhe restaurar uma versão antiga da base de dados utilizando [o portal Azure](sql-database-long-term-backup-retention-configure.md#using-azure-portal) ou [o Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) para satisfazer um pedido de conformidade ou executar uma versão antiga da aplicação. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).
- Para efetuar um restauro, consulte restaurar a [base de dados a partir de cópias de segurança](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se a copiar ficheiros de um local para outro. A *replicação* da base de dados da SQL refere-se a manter várias bases de dados secundárias sincronizadas com uma base de dados primária.

Pode experimentar algumas destas operações utilizando os seguintes exemplos:

| | O portal do Azure | Azure PowerShell |
|---|---|---|
| Alterar a retenção de backup | [Base de Dados Única](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Instância gerida](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Base de Dados Única](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Alterar a retenção de backup a longo prazo | [Base de dados única](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Instância Gerida - N/A  | [Base de Dados Única](sql-database-long-term-backup-retention-configure.md)<br/>Instância Gerida - N/A  |
| Restaurar a base de dados do ponto-em-tempo | [Base de dados única](sql-database-recovery-using-backups.md#point-in-time-restore) | [Base de dados única](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar uma base de dados eliminada | [Base de dados única](sql-database-recovery-using-backups.md) | [Base de dados única](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurar a base de dados do Armazenamento De Blob Azure | Base de dados única - N/A <br/>Instância Gerida - N/A  | Base de dados única - N/A <br/>[Instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Frequência de cópia de segurança

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

A Base de Dados SQL suporta o self-service para restauro pontual (PITR) criando automaticamente cópias de segurança completas, cópias de segurança diferenciais e cópias de segurança de registo de transações. As cópias de segurança completas da base de dados são criadas semanalmente, as cópias de segurança diferenciais são geralmente criadas a cada 12 horas, e as cópias de segurança de registo de transações são geralmente criadas a cada 5 a 10 minutos, com a frequência baseada no tamanho da computação e quantidade de atividade de base de dados. O primeiro backup completo é agendado imediatamente após a criação de uma base de dados. Normalmente completa em 30 minutos, mas pode demorar mais tempo quando a base de dados tem um tamanho significativo. Por exemplo, a cópia inicial de cópia de segurança pode demorar mais tempo numa base de dados restaurada ou numa cópia da base de dados. Após a primeira cópia de segurança completa, todas as cópias de segurança são agendadas automaticamente e geridas silenciosamente em segundo plano. O tempo exato de todas as cópias de dados é determinado pelo serviço Debase SQL, uma vez que equilibra a carga de trabalho global do sistema. Não pode mudar ou desativar os trabalhos de reserva.

As cópias de segurança DA PITR estão protegidas com armazenamento geo-redundante. Para mais informações, consulte a [redundância do Armazenamento Azure.](../storage/common/storage-redundancy.md)

Para mais informações, consulte [a restauração](sql-database-recovery-using-backups.md#point-in-time-restore) do ponto no tempo

### <a name="long-term-retention"></a>Retenção de longa duração

Bases de dados individuais e reunidas oferecem a opção de configurar a retenção a longo prazo (LTR) de cópias de segurança completas por um período máximo de 10 anos no armazenamento da Blob Azure. Se a política LTR estiver ativada, as cópias de backup seletivas semanais são automaticamente copiadas para um recipiente de armazenamento RA-GRS diferente. Para satisfazer diferentes requisitos de conformidade, pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. O consumo de armazenamento depende da frequência selecionada de cópias de segurança e do período de retenção. Pode utilizar a calculadora de [preços LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento LTR.

Tal como o PITR, as cópias de segurança LTR estão protegidas com armazenamento geo-redundante. Para mais informações, consulte a [redundância do Armazenamento Azure.](../storage/common/storage-redundancy.md)

Para mais informações, consulte a [retenção de backup a longo prazo.](sql-database-long-term-retention.md)

## <a name="backup-storage-consumption"></a>Consumo de armazenamento de reserva 

Para bases de dados únicas, a utilização total de armazenamento de cópias de segurança é calculada da seguinte forma:   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`.

Para piscinas elásticas, o tamanho total de armazenamento de backup é agregado ao nível da piscina e é calculado da seguinte forma:   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`. 

As cópias de segurança mais antigas do que o período de retenção são automaticamente purgadas com base no seu carimbo temporal. Como as cópias de segurança diferenciais e as cópias de segurança de registo requerem uma cópia de segurança mais cedo para serem úteis, são purgadas juntas em pedaços semanais. 

A Base de Dados Azure SQL calculará o seu armazenamento total de reserva de retenção como um valor cumulativo. A cada hora, este valor é reportado ao oleoduto de faturação Azure, que é responsável por agregar este uso de hora em hora para calcular o seu consumo no final de cada mês. Após a queda da base de dados, o consumo diminui à medida que os backups envelhecem. Uma vez que os backups tornam-se mais velhos que o período de retenção, a faturação para. 

   > [!IMPORTANT]
   > As cópias de segurança de uma base de dados são retidas durante o período de retenção especificado, mesmo que a base de dados tenha sido retirada. Embora a queda e recriação de uma base de dados frequentemente possa economizar nos custos de armazenamento e cálculo, pode aumentar os custos de armazenamento de backup, uma vez que conservamos uma cópia de segurança para o período de retenção especificado (que é de 7 dias no mínimo) para cada base de dados abandonada, sempre que a sua queda. 



### <a name="monitor-consumption"></a>Monitorizar o consumo

Cada tipo de cópia de segurança (completa, diferencial e registo) é reportada na lâmina de monitorização da base de dados como uma métrica separada. O diagrama seguinte mostra como monitorizar o consumo de armazenamento de cópias de segurança para uma única base de dados. Esta funcionalidade não está atualmente disponível para casos geridos.

![Monitorize o consumo de backup na base de dados do portal Azure](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>Afinar o consumo de armazenamento de backup

O consumo excessivo de armazenamento de reserva dependerá da carga de trabalho e da dimensão das bases de dados individuais. Pode considerar implementar algumas das seguintes técnicas de afinação para reduzir ainda mais o seu consumo de armazenamento de backup:

* Reduza o período de [retenção](#change-pitr-backup-retention-period-using-azure-portal) de reserva para o mínimo possível para as suas necessidades.
* Evite realizar grandes operações de escrita com mais frequência do que o necessário, como as reconstruções de índices.
* Para grandes operações de carga de dados, considere a utilização de [índices de colunas agrupadas,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)reduza o número de índices não agrupados e considere também operações de carga a granel com contagem de fileiras em torno de um milhão.
* No nível de serviço de Uso Geral, o armazenamento de dados provisionado é menos caro do que o preço do armazenamento de cópia de segurança em excesso devido ao qual os clientes com custos de armazenamento de backup em excesso contínuamente elevados podem considerar aumentar o armazenamento de dados de forma a economizar no armazenamento de backup.
* Utilize o TempDB na sua lógica ETL para armazenar resultados temporários, em vez de tabelas permanentes (aplicável apenas à instância gerida).
* Considere desligar a encriptação TDE para bases de dados que não contenham dados sensíveis (bases de dados de desenvolvimento ou teste, por exemplo). As cópias de segurança para bases de dados não encriptadas são normalmente comprimidas com uma relação de compressão mais elevada.

> [!IMPORTANT]
> Para analítica, data mart \ cargas de trabalho de armazém de dados é fortemente recomendado usar índices de [colunas agrupadas,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)reduzir o número de índices não agrupados, e também considerar operações de carga a granel com contagem de fileiras em torno de um milhão para reduzir o consumo de armazenamento de backup em excesso.


## <a name="storage-costs"></a>Custos de armazenamento

O preço do armazenamento varia se estiver a usar o modelo DTU ou o modelo vCore. 

### <a name="dtu-model"></a>Modelo DTU

Não existe nenhuma taxa adicional para o armazenamento de cópias de dados e piscinas elásticas utilizando o modelo DTU. 

### <a name="vcore-model"></a>Modelo de vCore

Para bases de dados únicas, um valor mínimo de armazenamento de cópia seletiva igual a 100% do tamanho da base de dados não é fornecido sem custos adicionais. Para piscinas elásticas e instâncias geridas, um valor mínimo de armazenamento de backup igual a 100% do armazenamento de dados atribuídos para o pool ou tamanho da instância, respectivamente, não é fornecido a custo extra. O consumo adicional de armazenamento de reserva será cobrado em GB/mês. Este consumo adicional dependerá da carga de trabalho e da dimensão das bases de dados individuais.

O Azure SQL DB calculará o seu armazenamento total de reserva de retenção como um valor cumulativo. A cada hora, este valor é reportado ao oleoduto de faturação Azure, que é responsável por agregar este uso de hora em hora para obter o seu consumo no final de cada mês. Depois que a base de dados é retirada, diminuímos o consumo à medida que os backups envelhecem. Uma vez que se tornam mais velhos que o período de retenção, a faturação para. Como todas as cópias de segurança de registo e cópias de segurança diferenciais são retidas durante todo o período de retenção, as bases de dados fortemente modificadas terão encargos de backup mais elevados. 

Vamos supor que a base de dados acumulou 744 GB de armazenamento de reserva e este valor permanece constante durante um mês inteiro. Para converter este consumo acumulado de armazenamento para uma utilização horária, dividimo-lo em 744,0 (31 dias por mês * 24 horas por dia). Assim, o SQL DB informará que a base de dados consumia 1 GB de reserva PITR a cada hora. A faturação azure irá agregar isto e mostrar uma utilização de 744 GB durante todo o mês e o custo com base na taxa de $/GB/mo na sua região. 

Agora, um exemplo mais complexo. Suponha que a base de dados tenha a sua retenção aumentada para 14 dias a meio do mês e isso (hipoteticamente) resulta no armazenamento total de cópia supor que duplica para 1488 GB. O SQL DB reportaria 1 GB de utilização durante horas 1-372 e, em seguida, reportaria o uso como 2 GB durante as horas 373-744. Esta seria agregada para ser uma nota final de 1116 GB/mo. 

### <a name="monitor-costs"></a>Monitorizar os custos

Para compreender os custos de armazenamento de backup, vá à **gestão de custos + faturação** do portal Azure, selecione **Cost Management,** e depois selecione Análise de **Custos**. Selecione a subscrição desejada como **O Âmbito**, e, em seguida, filtrar durante o período de tempo e serviço que lhe interessa. 

Adicione um filtro para **o nome de serviço**e, em seguida, escolha a base de dados **sql** a partir da gota para baixo. Utilize o filtro **de subcategoria do medidor** para escolher o contador de faturação para o seu serviço. Para uma única base de dados ou uma piscina elástica, escolha o armazenamento de **reserva de reserva pitr de piscina única/elástica**. Para um caso gerido, escolha o armazenamento de **backup mi pitr**. **As** subcategorias de armazenamento e **cálculo** podem interessar-lhe também, embora não estejam associadas a custos de armazenamento de backup. 

![Análise de custos de armazenamento de backup](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)


## <a name="backup-retention"></a>Retenção da cópia de segurança

Todas as bases de dados Azure SQL (bases de dados únicas, reunidas e geridas) têm um período de retenção de backup padrão de **sete** dias. Pode alterar o período de retenção de cópias de [segurança até 35 dias](#change-pitr-backup-retention-period).

Se eliminar uma base de dados, a Base de Dados SQL manterá as cópias de segurança da mesma forma que seria para uma base de dados online. Por exemplo, se eliminar uma base de dados Básica com um período de retenção de sete dias, uma cópia de segurança com quatro dias de vida é guardada por mais três dias.

Se necessitar de manter as cópias de segurança por mais tempo do que o período máximo de retenção, pode modificar as propriedades de backup para adicionar um ou mais períodos de retenção a longo prazo à sua base de dados. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se eliminar o servidor Azure SQL que acolhe bases de dados SQL, todas as piscinas elásticas e bases de dados que pertencem ao servidor também são eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor apagado. Mas se configurar a retenção a longo prazo, as cópias de segurança das bases de dados com LTR não serão eliminadas e estas bases de dados podem ser restauradas.

## <a name="encrypted-backups"></a>Backups encriptados

Se a sua base de dados estiver encriptada com TDE, as cópias de segurança são automaticamente encriptadas em repouso, incluindo cópias de segurança LTR. Quando o TDE está ativado para uma base de dados Azure SQL, as cópias de segurança também são encriptadas. Todas as novas bases de dados Azure SQL estão configuradas com TDE ativado por padrão. Para obter mais informações sobre o TDE, consulte encriptação transparente de dados com base de [dados Azure SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integridade de backup

Numa base de dados Azure SQL testa automaticamente a restauração de cópias de dados automatizadas de bases de dados colocadas em servidores lógicos e piscinas elásticas (isto não está disponível em Instância Gerida). Após a restauração do ponto-a-dia, as bases de dados também recebem verificações de integridade utilizando o DBCC CHECKDB.

A Managed Instance recebe cópia seleção automática com `CHECKSUM` das bases de dados restauradas utilizando o comando de `RESTORE` nativo ou o Serviço de Migração de Dados assim que a migração estiver concluída.

Quaisquer problemas encontrados durante a verificação de integridade resultarão num alerta para a equipa de engenharia. Para obter mais informações sobre integridade de dados na Base de Dados Azure SQL, consulte a Integridade dos Dados na Base de [Dados Azure SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Conformidade

Quando migra a sua base de dados de um nível de serviço baseado em DTU para um nível de serviço baseado em vCore, a retenção PITR é preservada para garantir que a política de recuperação de dados da sua aplicação não está comprometida. Se a retenção por defeito não cumprir os seus requisitos de conformidade, pode alterar o período de retenção PITR utilizando a PowerShell ou a REST API. Para mais informações, consulte Alterar o Período de [Retenção](#change-pitr-backup-retention-period)de Backup .

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>Alterar o período de retenção de backup PITR

Pode alterar o período de retenção de backup PITR padrão utilizando o portal Azure, PowerShell ou a API REST. Os exemplos seguintes ilustram como mudar a retenção de PITR para 28 dias.

> [!WARNING]
> Se reduzir o atual período de retenção, todos os backups existentes mais antigos do que o novo período de retenção já não estão disponíveis. Se aumentar o atual período de retenção, a Base de Dados SQL manterá as cópias de segurança existentes até que o período de retenção mais longo seja atingido.

> [!NOTE]
> Estas APIs só afetarão o período de retenção do PITR. Se configurar o LTR para a sua base de dados, não será afetado. Para obter mais informações sobre como alterar o período de retenção LTR, consulte a [retenção a longo prazo](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Alterar o período de retenção de backup PITR utilizando o portal Azure

Para alterar o período de retenção de backup PITR utilizando o portal Azure, navegue para o objeto do servidor cujo período de retenção pretende alterar dentro do portal e, em seguida, selecione a opção adequada com base no objeto do servidor que está a modificar.

#### <a name="single-database--elastic-pools"></a>[Base de dados única e piscinas elásticas](#tab/single-database)

A alteração da retenção de backup PITR para bases de dados Únicas Azure SQL é realizada ao nível do servidor. A alteração feita ao nível do servidor aplica-se às bases de dados desse servidor. Para alterar o PITR para o servidor de base de dados Azure SQL a partir do portal Azure, navegar para a lâmina de visão geral do servidor, clicar em 'Gerir Backups' no menu de navegação e, em seguida, clicar na retenção de Configurar na barra de navegação.

![Alterar portal PITR Azure](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Instância gerida](#tab/managed-instance)

A alteração da retenção de backup PITR para a instância gerida pela Base de Dados SQL é realizada a nível individual de base de dados. Para alterar a retenção de backup PITR para uma base de dados por exemplo do portal Azure, navegue para a lâmina de visão geral da base de dados individual e clique em configurar a retenção de cópia de segurança na barra de navegação.

![Alterar portal PITR Azure](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Alterar o período de retenção de backup PITR utilizando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Alterar o período de retenção do PITR utilizando a API REST

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

Para mais informações, consulte [Backup Retention REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Passos seguintes

- Os backups de bases de dados são uma parte essencial de qualquer estratégia de continuidade do negócio e recuperação de desastres porque protegem os seus dados de corrupção acidental ou eliminação. Para conhecer as outras soluções de continuidade de negócio sql da Base de dados Azure, consulte a [visão geral da continuidade do Negócio.](sql-database-business-continuity.md)
- Para restaurar a um ponto no tempo utilizando o portal Azure, consulte restaurar a base de [dados a um ponto no tempo utilizando o portal Azure](sql-database-recovery-using-backups.md).
- Para restaurar até um ponto no tempo utilizando o PowerShell, consulte restaurar a base de [dados a um ponto de utilização](scripts/sql-database-restore-database-powershell.md)do PowerShell .
- Para configurar, gerir e restaurar a longo prazo a retenção de backups automatizados no armazenamento do Azure Blob utilizando o portal Azure, consulte gerir a retenção de backup a [longo prazo utilizando o portal Azure](sql-database-long-term-backup-retention-configure.md).
- Para configurar, gerir e restaurar a longo prazo a retenção de backups automatizados no armazenamento Do Blob azure utilizando powerShell, ver Gerir a [retenção de backup a longo prazo usando powerShell](sql-database-long-term-backup-retention-configure.md).
