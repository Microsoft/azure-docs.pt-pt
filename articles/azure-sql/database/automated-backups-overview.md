---
title: Backups automáticos e geo-redundantes
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database e Azure SQL Managed Instance criam automaticamente uma cópia de segurança da base de dados local a cada poucos minutos e utilizam o armazenamento geo-redundante de acesso de leitura Azure para geo-redundância.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 62e8bbbc3781c7e27671e8cb303ef2dcad2a30f3
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324339"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Backups automatizados - Azure SQL Database & SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tanto a Base de Dados Azure SQL como a Azure SQL Managed Instance criam cópias de dados de base de dados que são mantidas durante o período de retenção configurado. Utilizam o armazenamento geo-redundante de acesso de leitura Azure [(RA-GRS)](../../storage/common/storage-redundancy.md) para garantir que as cópias de segurança são preservadas mesmo que o datacenter não esteja disponível.

As cópias de dados são uma parte essencial de qualquer estratégia de continuidade de negócios e recuperação de desastres porque protegem os seus dados de corrupção acidental ou eliminação. Se as suas regras de segurança exigirem que as suas cópias de segurança estejam disponíveis por um período prolongado (até 10 anos), pode configurar [a retenção a longo prazo](long-term-retention-overview.md) para bases de dados individuais e agimidas.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é uma cópia de segurança da Base de Dados SQL?

Tanto a SQL Database como a SQL Managed Instance utilizam a tecnologia SQL Server para criar [cópias de segurança completas](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) todas as [semanas, cópias de segurança diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12 horas e [cópias de segurança](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) de registo de transações a cada 5 a 10 minutos. As cópias de segurança são armazenadas em [bolhas de armazenamento RA-GRS](../../storage/common/storage-redundancy.md) que são replicadas num [datacenter emparelhado](../../best-practices-availability-paired-regions.md) para proteção contra uma falha do datacenter. Quando restaura uma base de dados, o serviço determina quais as cópias de segurança completas, diferenciais e de registo de transações que precisam de ser restauradas.

Pode utilizar estas cópias de segurança para:

- **Restaurar uma base de dados existente a um ponto no tempo no passado** dentro do período de retenção, utilizando o portal Azure PowerShell, Azure CLI ou a API REST. Para bases de dados individuais e agrárias, esta operação criará uma nova base de dados no mesmo servidor na mesma subscrição que a base de dados original. Num caso gerido, esta operação pode criar uma cópia da base de dados ou a mesma ou uma instância gerida diferente sob a mesma subscrição.
- **Restaurar uma base de dados eliminada à hora de eliminação** ou a qualquer momento dentro do período de retenção. A base de dados eliminada só pode ser restaurada no mesmo servidor ou caso gerido e na mesma subscrição onde a base de dados original foi criada.
- **Restaurar uma base de dados para outra região geográfica.** O geo-restauro permite-lhe recuperar de um desastre geográfico quando não consegue aceder ao seu servidor e base de dados. Cria uma nova base de dados em qualquer servidor existente, em qualquer parte do mundo.
- **Restaurar uma base de dados a partir de uma cópia de segurança específica a longo prazo** numa única base de dados ou numa base de dados agrária, se a base de dados estiver configurada com uma política de retenção a longo prazo (LTR). O LTR permite restaurar uma versão antiga da base de dados utilizando [o portal Azure](long-term-backup-retention-configure.md#using-the-azure-portal) ou [a Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) para satisfazer um pedido de conformidade ou executar uma versão antiga da aplicação. Para obter mais informações, veja [Retenção de longa duração](long-term-retention-overview.md).

Para efetuar uma restauração, consulte [a base de dados Restaurar a partir de backups](recovery-using-backups.md).

> [!NOTE]
> No Azure Storage, o termo *replicação* refere-se à cópia de ficheiros de um local para outro. Na Base de Dados Azure SQL e na SQL Managed Instance, a *replicação da base de dados* refere-se a manter várias bases de dados secundárias sincronizadas com uma base de dados primária.

Pode experimentar algumas destas operações utilizando os seguintes exemplos:

| | O portal do Azure | Azure PowerShell |
|---|---|---|
| Alterar a retenção de backup | [Base de dados única](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Instância gerida](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Base de dados única](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Alterar a retenção de backup a longo prazo | [Base de dados única](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Caso gerido - N/A  | [Base de dados única](long-term-backup-retention-configure.md)<br/>Caso gerido - N/A  |
| Restaurar uma base de dados a partir de um ponto no tempo | [Base de dados única](recovery-using-backups.md#point-in-time-restore) | [Base de dados única](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar uma base de dados eliminada | [Base de dados única](recovery-using-backups.md) | [Base de dados única](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurar uma base de dados a partir do armazenamento da Azure Blob | Base de dados única - N/A <br/>Caso gerido - N/A  | Base de dados única - N/A <br/>[Instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-frequency"></a>Frequência de cópia de segurança

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

A SQL Database e a SQL Managed Instance suportam o autosserviço para a restauração pontual (PITR), criando automaticamente cópias de segurança completas, cópias de segurança diferenciais e cópias de segurança de registo de transações. As cópias de segurança completas da base de dados são criadas semanalmente e as cópias de dados diferenciais são geralmente criadas a cada 12 horas. As cópias de segurança do registo de transações são geralmente criadas a cada 5 a 10 minutos. A frequência das cópias de segurança dos registos de transações baseia-se no tamanho do cálculo e na quantidade de atividade da base de dados.

A primeira cópia de segurança completa é agendada imediatamente após a criação de uma base de dados. Esta cópia de segurança geralmente completa dentro de 30 minutos, mas pode demorar mais tempo quando a base de dados é grande. Por exemplo, a cópia de segurança inicial pode demorar mais tempo numa base de dados restaurada ou numa cópia de base de dados. Depois da primeira cópia de segurança completa, todas as restantes cópias de segurança serão agendadas automaticamente e geridas silenciosamente em segundo plano. O tempo exato de todas as cópias de segurança da base de dados é determinado pela SQL Database ou pela SQL Managed Instance, uma vez que equilibra a carga de trabalho global do sistema. Não pode alterar ou desativar tarefas de cópia de segurança.

### <a name="default-backup-retention-period"></a>Período de retenção de backup predefinido

As cópias de segurança PITR estão protegidas com armazenamento geo-redundante. Para obter mais informações, veja [Redundância do Armazenamento do Microsoft Azure](../../storage/common/storage-redundancy.md).

Para obter mais informações sobre o PITR, consulte [a restauração do ponto-a-tempo](recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Retenção de longa duração

Para bases de dados individuais e aginhadas, pode configurar a retenção de longo prazo (LTR) de cópias de segurança completas até 10 anos no armazenamento Azure Blob. Se ativar a política LTR, as cópias de segurança completas semanais são automaticamente copiadas para um recipiente de armazenamento RA-GRS diferente. Para satisfazer vários requisitos de conformidade, pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anual. O consumo de armazenamento depende da frequência selecionada de backups e do período ou períodos de retenção. Pode utilizar a [calculadora de preços LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento ltr.

Tal como as cópias de segurança pitr, as cópias de segurança LTR estão protegidas com armazenamento geo-redundante. Para obter mais informações, veja [Redundância do Armazenamento do Microsoft Azure](../../storage/common/storage-redundancy.md).

Para obter mais informações sobre o LTR, consulte [a retenção de backup a longo prazo.](long-term-retention-overview.md)

## <a name="backup-storage-consumption"></a>Consumo de armazenamento de backup

Para bases de dados individuais e instâncias geridas, esta equação é usada para calcular o uso total de armazenamento de backup:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Para bases de dados em conjunto, o tamanho total de armazenamento de backup é agregado ao nível da piscina e é calculado da seguinte forma:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

As cópias de segurança que ocorrem antes do período de retenção são automaticamente purgadas com base na sua marca de tempo. Como as cópias de segurança diferenciais e os backups de registo requerem uma cópia de segurança completa anterior para serem úteis, são purgadas juntas em pedaços semanais.

A BASE de Dados SQL e a SQL Managed Instance calculam o armazenamento total de backup de retenção como um valor cumulativo. A cada hora, este valor é reportado ao oleoduto de faturação Azure, que é responsável por agregar este uso de hora a hora para calcular o seu consumo no final de cada mês. Depois da base de dados ser eliminada, o consumo diminui à medida que as cópias de segurança envelhecem. Depois de os backups ficarem mais velhos do que o período de retenção, a faturação para.
   
   > [!IMPORTANT]
   > As cópias de segurança de uma base de dados são mantidas durante o período de retenção especificado, mesmo que a base de dados tenha sido eliminada. Ao cair e recriar uma base de dados pode economizar frequentemente nos custos de armazenamento e computação, pode aumentar os custos de armazenamento de backup, uma vez que a Microsoft mantém uma cópia de segurança para o período de retenção especificado para cada base de dados abandonada, sempre que é eliminada. 

### <a name="monitor-consumption"></a>Monitorizar o consumo

Cada tipo de cópia de segurança (completa, diferencial e log) é reportado na lâmina de monitorização da base de dados como uma métrica separada. O diagrama seguinte mostra como monitorizar o consumo de armazenamento de cópia de segurança para uma única base de dados. Esta funcionalidade encontra-se atualmente indisponível para casos geridos.

![Monitorize o consumo de backup da base de dados no portal Azure](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Consumo de armazenamento de backup de afinação afinada

O consumo excessivo de armazenamento de backup dependerá da carga de trabalho e do tamanho das bases de dados individuais. Considere algumas das seguintes técnicas de afinação para reduzir o seu consumo de armazenamento de backup:

- Reduza o [período de retenção de backup](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) ao mínimo possível para as suas necessidades.
- Evite fazer grandes operações de escrita, como reconstruções de índices, com mais frequência do que o necessário.
- Para grandes operações de carga de dados, considere a utilização de [índices de lojas de colunas agrupadas,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)reduza o número de índices não agrupados e considere as operações de carga a granel com uma contagem de linhas de cerca de 1 milhão.
- No nível de serviço para fins gerais, o armazenamento de dados forvisionado é mais barato do que o preço do armazenamento de backup em excesso. Se tiver custos de armazenamento de backup em excesso continuamente elevados, poderá considerar aumentar o armazenamento de dados para economizar no armazenamento de backup.
- Utilize o TempDB em vez de tabelas permanentes na sua lógica ETL para armazenar resultados temporários. (Aplicável apenas à SQL Managed Instance.)
- Considere desligar a encriptação TDE para bases de dados que não contenham dados sensíveis (bases de dados de desenvolvimento ou teste, por exemplo). As cópias de segurança para bases de dados não encriptadas são normalmente comprimidas com uma relação de compressão mais elevada.

> [!IMPORTANT]
> Para o mercado de dados analíticos \ cargas de armazenamento de dados, recomendamos vivamente que utilize [índices de lojas de colunas agrupadas,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)reduza o número de índices não agrupados e considere operações de carga a granel com uma contagem de linhas de cerca de 1 milhão para reduzir o consumo excessivo de armazenamento de backup.

## <a name="storage-costs"></a>Custos de armazenamento

O preço de armazenamento varia consoante esteja a utilizar o modelo DTU ou o modelo vCore.

### <a name="dtu-model"></a>Modelo de DTU

Não há nenhum custo adicional para armazenamento de backup para bases de dados e piscinas de base de dados elásticas se estiver a usar o modelo DTU.

### <a name="vcore-model"></a>Modelo de vCore

Para bases de dados individuais na Base de Dados SQL, é fornecido um valor mínimo de armazenamento de backup igual a 100% do tamanho da base de dados sem custos adicionais. Para piscinas elásticas em SqL Database e caixas individuais e de instância em SQL Managed Instance, um valor mínimo de armazenamento de backup igual a 100 por cento do armazenamento de dados atribuído para o tamanho do pool ou exemplo, respectivamente, é fornecido sem custos adicionais. O consumo adicional de armazenamento de cópias de segurança será cobrado por GB/mês. Este consumo adicional dependerá da carga de trabalho e da dimensão das bases de dados individuais.

A BASE de Dados SQL e a SQL Managed Instance calcularão o seu armazenamento total de backup de retenção como um valor cumulativo. A cada hora, este valor é reportado ao oleoduto de faturação Azure, que é responsável por agregar este uso de hora a hora para obter o seu consumo no final de cada mês. Depois de a base de dados ser eliminada, a Microsoft diminui o consumo à medida que as cópias de segurança envelhecem. Depois de os backups ficarem mais velhos do que o período de retenção, a faturação para. Como todas as cópias de segurança e backups diferenciais são mantidas durante todo o período de retenção, as bases de dados fortemente modificadas terão custos de backup mais elevados.

Assuma que uma base de dados acumulou 744 GB de armazenamento de backup e que este valor permanece constante durante um mês inteiro. Para converter este consumo acumulado de armazenamento para uso horário, divida-o por 744,0 (31 dias por mês * 24 horas por dia). Assim, a SQL Database informará que a base de dados consumia 1 GB de backup pitr a cada hora. A faturação do Azure irá agregar este consumo e mostrar um uso de 744 GB durante todo o mês. O custo será baseado na taxa $/GB/mês na sua região.

Agora, um exemplo mais complexo. Suponha que a base de dados tenha a sua retenção aumentada para 14 dias em meados do mês. Assuma este aumento (hipoteticamente) resulta na duplicação total do armazenamento de backup para 1.488 GB. A BASE de Dados SQL reportaria 1 GB de utilização durante as horas 1 a 372. Reportaria o uso como 2 GB durante as horas 373 a 744. Esta utilização seria agregada a uma nota final de 1.116 GB/mês.

### <a name="monitor-costs"></a>Monitorizar os custos

Para compreender os custos de armazenamento de backup, vá à **Gestão de Custos + Faturação** no portal Azure, selecione **Cost Management**e, em seguida, selecione Análise **de Custos**. Selecione a subscrição desejada como **Scope**e, em seguida, filtre pelo período de tempo e serviço em que está interessado.

Adicione um filtro para **o nome de serviço**e, em seguida, selecione a base de **dados sql** na lista de drop-down. Utilize o filtro **da subcategoria** do medidor para escolher o contador de faturação para o seu serviço. Para uma única base de dados ou uma piscina de base de dados elástica, selecione **um armazenamento de backup de pitr de piscina único/elástico**. Para uma instância gerida, selecione **o armazenamento de backup mi pitr**. As subcategorias **de Armazenamento** e **Computação** também podem interessar-lhe, mas não estão associados a custos de armazenamento de backup.

![Análise de custos de armazenamento de backup](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Retenção da cópia de segurança

Todas as bases de dados em SQL Database e SQL Managed Instance têm um período de retenção de backup padrão de 7 dias. Pode [alterar o período de retenção de backup](#change-the-pitr-backup-retention-period) até 35 dias.

Se eliminar uma base de dados, o Azure mantém as cópias de segurança da mesma forma que para uma base de dados online. Por exemplo, se eliminar uma base de dados Básica que tenha um período de retenção de sete dias, uma cópia de segurança que tenha quatro dias é guardada por mais três dias.

Se precisar de manter as cópias de segurança durante mais tempo do que o período máximo de retenção, pode modificar as propriedades de backup para adicionar um ou mais períodos de retenção a longo prazo à sua base de dados. Para obter mais informações, veja [Retenção de longa duração](long-term-retention-overview.md).

> [!IMPORTANT]
> A configuração do período de retenção de backup para 1 dia (ou qualquer valor entre 1 - 7) só é suportada através da PowerShell ou da REST API neste momento. A versão mínima exigida do módulo Az.SQL é v2.6.0, ou pode ser executada através da CloudShell, que tem sempre a versão mais recente do Az.SQL.

> [!IMPORTANT]
> Se eliminar o servidor ou a instância gerida, todas as bases de dados geridas por esse servidor ou instância gerida também serão eliminadas. Não podem ser recuperados. Não é possível restaurar um servidor apagado ou uma instância gerida. Mas se configurar a retenção a longo prazo para a BASE de Dados SQL ou instâncias manged, as cópias de segurança para as bases de dados com LTR não serão eliminadas, e estas bases de dados podem ser restauradas.

## <a name="encrypted-backups"></a>Backups encriptados

Se a sua base de dados estiver encriptada com TDE, as cópias de segurança são automaticamente encriptadas em repouso, incluindo cópias de segurança LTR. Quando o TDE está ativado para a Base de Dados SQL ou para a Sql Managed Instance, as cópias de segurança também são encriptadas. Todas as novas bases de dados na Base de Dados SQL e na SQL Managed Instance estão configuradas com TDE ativada por padrão. Para obter mais informações sobre o TDE, consulte [a Encriptação de Dados Transparente com a Base de Dados SQL & SQL Managed Instance](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integridade de backup

De forma contínua, a equipa de engenharia Azure SQL testa automaticamente a restauração de cópias de dados automatizadas de bases de dados na Base de Dados SQL. (Este teste não está disponível em SQL Managed Instance.) Após a restauração pontual, as bases de dados também recebem verificações de integridade do DBCC CHECKDB.

A SQL Managed Instance recebe uma cópia de segurança inicial automática com `CHECKSUM` bases de dados restauradas com o comando nativo `RESTORE` ou com o Serviço de Migração de Dados Azure após a conclusão da migração.

Quaisquer problemas encontrados durante a verificação de integridade resultarão num alerta para a equipa de engenharia. Para obter mais informações, consulte [a Integridade dos Dados na Base de Dados SQL.](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="compliance"></a>Conformidade

Quando migra a sua base de dados de um nível de serviço baseado em DTU para um nível de serviço baseado em vCore, a retenção PITR é preservada para garantir que a política de recuperação de dados da sua aplicação não seja comprometida. Se a retenção predefinida não cumprir os seus requisitos de conformidade, pode alterar o período de retenção PITR utilizando o PowerShell ou a API REST. Para obter mais informações, consulte [alterar o período de retenção de backup pitr](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Alterar o período de retenção de backup pitr

Pode alterar o período de retenção de backup pitr predefinido utilizando o portal Azure, PowerShell ou a API REST. Os exemplos a seguir ilustram como alterar a retenção pitr para 28 dias.

> [!WARNING]
> Se reduzir o período de retenção atual, todas as cópias de segurança existentes que sejam mais antigas do que o novo período de retenção deixarão de estar disponíveis. Se aumentar o período de retenção atual, o Azure mantém as cópias de segurança existentes até ao fim do período de retenção mais longo.

> [!NOTE]
> Estas APIs afetarão apenas o período de retenção pitr. Se configurar o LTR para a sua base de dados, não será afetado. Para obter informações sobre como alterar os períodos de retenção do LTR, consulte [a retenção a longo prazo](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Alterar o período de retenção de backup PITR utilizando o portal Azure

Para alterar o período de retenção de backup PITR utilizando o portal Azure, aceda ao objeto do servidor cujo período de retenção pretende alterar no portal. Em seguida, selecione a opção apropriada com base no objeto do servidor que está a modificar.

#### <a name="sql-database"></a>[Base de Dados SQL](#tab/single-database)

As alterações à retenção de backup pitr para a Base de Dados SQL são efetuadas ao nível do servidor. As alterações efetuadas ao nível do servidor aplicam-se às bases de dados do servidor. Para alterar a retenção pitr para um servidor a partir do portal Azure, aceda à lâmina de visão geral do servidor. **Selecione Gerir backups** no painel esquerdo e, em seguida, selecione **Configure retenção** na parte superior do ecrã:

![Alterar a retenção pitr, nível de servidor](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[Instância Gerida do SQL](#tab/managed-instance)

As alterações à retenção de backup pitr para sql Managed Instance são feitas a um nível de base de dados individual. Para alterar a retenção de backup PITR para uma base de dados de casos a partir do portal Azure, aceda à lâmina de visão geral da base de dados individual. Em **seguida, selecione configurar a retenção de backup** na parte superior do ecrã:

![Alterar retenção PITR, instância gerida](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Altere o período de retenção de backup PITR utilizando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell AzureRM ainda é suportado pela SQL Database e pela SQL Managed Instance, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para mais informações, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az são substancialmente idênticos aos dos módulos AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Alterar o período de retenção de backup PITR utilizando a API REST

#### <a name="sample-request"></a>Pedido de amostra

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corpo do pedido

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Resposta de amostra

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

Para obter mais informações, consulte [a API de Retenção de Retenção de Reserva.](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)

## <a name="next-steps"></a>Próximos passos

- As cópias de dados são uma parte essencial de qualquer estratégia de continuidade de negócios e recuperação de desastres porque protegem os seus dados de corrupção acidental ou eliminação. Para conhecer as outras soluções de continuidade de negócios da SQL Database, consulte [a visão geral da continuidade do Negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Obtenha mais informações sobre como [restaurar uma base de dados a um ponto no tempo, utilizando o portal Azure](recovery-using-backups.md).
- Obtenha mais informações sobre como [restaurar uma base de dados a um ponto no tempo, utilizando o PowerShell](scripts/restore-database-powershell.md).
- Para obter informações sobre como configurar, gerir e restaurar a partir da retenção a longo prazo de backups automatizados no armazenamento Azure Blob utilizando o portal Azure, consulte Gerir a [retenção de backup a longo prazo utilizando o portal Azure](long-term-backup-retention-configure.md).
- Para obter informações sobre como configurar, gerir e restaurar a partir da retenção a longo prazo de cópias de segurança automatizadas no armazenamento de Azure Blob utilizando o PowerShell, consulte [Gerir a retenção de backup a longo prazo utilizando o PowerShell](long-term-backup-retention-configure.md).
