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
ms.date: 06/04/2020
ms.openlocfilehash: fc2c8ea232004488664bc7f15b1d1bb3b83f2e7b
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609612"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Backups automatizados - Azure SQL Database & SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>O que é uma cópia de segurança da base de dados?

As cópias de dados são uma parte essencial de qualquer estratégia de continuidade de negócios e recuperação de desastres, porque protegem os seus dados contra corrupção ou eliminação.

Tanto a SQL Database como a SQL Managed Instance utilizam a tecnologia SQL Server para criar [cópias de segurança completas](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) todas as [semanas, cópias de segurança diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12-24 horas e [cópias de segurança](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) de registo de transações a cada 5 a 10 minutos. A frequência das cópias de segurança dos registos de transações baseia-se no tamanho do cálculo e na quantidade de atividade da base de dados.

Quando restaura uma base de dados, o serviço determina quais as cópias de segurança completas, diferenciais e de registo de transações que precisam de ser restauradas.

Estas cópias de segurança permitem que a base de dados restabeleça um ponto no tempo dentro do período de retenção configurado. As cópias de segurança são armazenadas como [bolhas de armazenamento RA-GRS](../../storage/common/storage-redundancy.md) que são replicadas numa [região emparelhada](../../best-practices-availability-paired-regions.md) para proteção contra falhas que impactam o armazenamento de backup na região primária. 

Se as suas regras de proteção de dados exigirem que as suas cópias de segurança estejam disponíveis por um período prolongado (até 10 anos), pode configurar [a retenção a longo prazo](long-term-retention-overview.md) para bases de dados individuais e agimidas.

Pode utilizar estas cópias de segurança para:

- [Restaurar uma base de dados existente a um ponto no tempo no passado](recovery-using-backups.md#point-in-time-restore) dentro do período de retenção, utilizando o portal Azure PowerShell, Azure CLI ou REST API. Para bases de dados individuais e agrárias, esta operação criará uma nova base de dados no mesmo servidor que a base de dados original, mas com um nome diferente para evitar a sobreescrita da base de dados original. Uma vez concluída a restauração, pode eliminar ou [renomear](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database) a base de dados original e mudar o nome da base de dados restaurada para ter o nome de base de dados original. Num caso gerido, esta operação pode igualmente criar uma cópia da base de dados sobre o mesmo ou um caso gerido diferente sob a mesma subscrição e na mesma região.
- [Restaurar uma base de dados eliminada à hora de eliminação](recovery-using-backups.md#deleted-database-restore) ou a qualquer ponto no tempo dentro do período de retenção. A base de dados eliminada só pode ser restaurada no mesmo servidor ou caso gerido onde a base de dados original foi criada. Ao eliminar uma base de dados, o serviço recebe uma cópia de segurança final do registo de transações antes da eliminação, para evitar qualquer perda de dados.
- [Restaurar uma base de dados para outra região geográfica.](recovery-using-backups.md#geo-restore) O geo-restauro permite-lhe recuperar de um desastre geográfico quando não consegue aceder à sua base de dados ou cópias de segurança na região primária. Cria uma nova base de dados em qualquer servidor ou instância gerida existente, em qualquer região do Azure.
- [Restaurar uma base de dados a partir de uma cópia](long-term-retention-overview.md) de segurança específica a longo prazo de uma única base de dados ou de uma base de dados agrária, se a base de dados tiver sido configurada com uma política de retenção a longo prazo (LTR). O LTR permite restaurar uma versão antiga da base de dados utilizando [o portal Azure](long-term-backup-retention-configure.md#using-the-azure-portal) ou [a Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) para satisfazer um pedido de conformidade ou executar uma versão antiga da aplicação. Para obter mais informações, veja [Retenção de longa duração](long-term-retention-overview.md).

Para efetuar uma restauração, consulte [a base de dados Restaurar a partir de backups](recovery-using-backups.md).

> [!NOTE]
> No Azure Storage, o termo *replicação* refere-se à cópia de bolhas de um local para outro. Em SQL, *a replicação da base de dados* refere-se a várias tecnologias usadas para manter várias bases de dados secundárias sincronizadas com uma base de dados primária.

Pode tentar a configuração de backup e restaurar as operações utilizando os seguintes exemplos:

| | Portal do Azure | Azure PowerShell |
|---|---|---|
| Alterar a retenção de backup | [Base de dados única](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Instância gerida](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Base de dados única](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Alterar a retenção de backup a longo prazo | [Base de dados única](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Caso gerido - N/A  | [Base de dados única](long-term-backup-retention-configure.md)<br/>Caso gerido - N/A  |
| Restaurar uma base de dados a partir de um ponto no tempo | [Base de dados única](recovery-using-backups.md#point-in-time-restore) | [Base de dados única](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar uma base de dados eliminada | [Base de dados única](recovery-using-backups.md) | [Base de dados única](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância gerida](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurar uma base de dados a partir do armazenamento da Azure Blob | Base de dados única - N/A <br/>Caso gerido - N/A  | Base de dados única - N/A <br/>[Instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>Agendamento de backup

A primeira cópia de segurança completa é programada imediatamente após a criação ou restauro de uma nova base de dados. Esta cópia de segurança geralmente completa dentro de 30 minutos, mas pode demorar mais tempo quando a base de dados é grande. Por exemplo, a cópia de segurança inicial pode demorar mais tempo numa base de dados restaurada ou numa cópia de base de dados, que normalmente seria maior do que uma nova base de dados. Após o primeiro backup completo, todos os backups adicionais são agendados e geridos automaticamente. O tempo exato de todas as cópias de segurança da base de dados é determinado pelo serviço SQL Database ou SQL Managed Instance, uma vez que equilibra a carga de trabalho global do sistema. Não pode alterar o horário dos trabalhos de reserva ou desativá-los.

> [!IMPORTANT]
> Para uma nova base de dados, restaurada ou copiada, a capacidade de restauro pontual fica disponível a partir do momento em que a cópia de segurança inicial do registo de transações que segue a cópia de segurança inicial é criada.

## <a name="backup-storage-consumption"></a>Consumo de armazenamento de backup

Com a cópia de segurança do SQL Server e a tecnologia de restauro, restaurar uma base de dados a um ponto no tempo requer uma cadeia de backup ininterrupta composta por uma cópia de segurança completa, opcionalmente uma cópia de segurança diferencial e uma ou mais cópias de segurança de registo de transações. O horário de backup da SQL Database e sql Managed Instance inclui uma cópia de segurança completa todas as semanas. Por conseguinte, para permitir o PITR durante todo o período de retenção, o sistema deve armazenar cópias de segurança adicionais completas, diferenciais e de registo de transações por um período máximo de uma semana superior ao período de retenção configurado. 

Por outras palavras, durante o período de retenção, deve haver um backup completo mais antigo do que o tempo mais antigo do período de retenção, bem como uma cadeia ininterrupta de cópias de segurança diferenciais e de registo de transações a partir dessa cópia de segurança completa até ao próximo backup completo.

> [!NOTE]
> Para ativar o PITR, as cópias de segurança adicionais são armazenadas por mais uma semana do que o período de retenção configurado. O armazenamento de backup é cobrado ao mesmo ritmo para todos os backups. 

Para bases de dados individuais, esta equação é usada para calcular o uso total de armazenamento de backup:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

Para bases de dados em conjunto, o tamanho total de armazenamento de backup é agregado ao nível da piscina e é calculado da seguinte forma:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Para os casos geridos, o tamanho total do armazenamento de backup é agregado ao nível da instância e é calculado da seguinte forma:

`Total backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

As cópias de segurança que já não são necessárias para fornecer a funcionalidade PITR são automaticamente eliminadas. Como as cópias de segurança diferenciais e as cópias de segurança requerem uma cópia de segurança completa anterior para serem restauradoras, os três tipos de backup são purgados em conjuntos semanais.

Para todas as bases de dados, incluindo bases de dados [encriptadas do TDE,](transparent-data-encryption-tde-overview.md) as cópias de segurança são comprimidas para reduzir a compressão e custos de armazenamento de backup. A relação média de compressão de backup é de 3-4 vezes, no entanto pode ser significativamente menor ou maior dependendo da natureza dos dados e se a compressão de dados é usada na base de dados.

A BASE de Dados SQL e a SQL Managed Instance calculam o armazenamento total de backup usado como valor cumulativo. A cada hora, este valor é reportado ao oleoduto de faturação Azure, que é responsável por agregar este uso de hora a hora para calcular o seu consumo no final de cada mês. Após a base de dados ser eliminada, o consumo diminui à medida que as cópias de segurança envelhecem e são eliminadas. Uma vez que todas as cópias de segurança são eliminadas e o PITR já não é possível, a faturação para.
   
> [!IMPORTANT]
> As cópias de segurança de uma base de dados são mantidas para permitir o PITR, mesmo que a base de dados tenha sido eliminada. Embora a eliminação e recriação de uma base de dados possa poupar custos de armazenamento e computação, pode aumentar os custos de armazenamento de backup, uma vez que o serviço retém cópias de segurança para cada base de dados eliminada, sempre que é eliminada. 

### <a name="monitor-consumption"></a>Monitorizar o consumo

Para bases de dados vCore, o armazenamento consumido por cada tipo de cópia de segurança (completa, diferencial e log) é reportado na lâmina de monitorização da base de dados como uma métrica separada. O diagrama seguinte mostra como monitorizar o consumo de armazenamento de cópia de segurança para uma única base de dados. Esta funcionalidade não se encontra disponível para casos geridos.

![Monitorize o consumo de backup da base de dados no portal Azure](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Consumo de armazenamento de backup de afinação afinada

O consumo de armazenamento de backup até ao tamanho máximo de dados para uma base de dados não é cobrado. O consumo excessivo de armazenamento de backup dependerá da carga de trabalho e do tamanho máximo das bases de dados individuais. Considere algumas das seguintes técnicas de afinação para reduzir o seu consumo de armazenamento de backup:

- Reduza o [período de retenção de backup](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) ao mínimo possível para as suas necessidades.
- Evite fazer grandes operações de escrita, como reconstruções de índices, com mais frequência do que o necessário.
- Para grandes operações de carga de dados, considere a utilização de [índices de lojas de colunas agrupadas](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes) e seguir [as melhores práticas relacionadas,](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)e/ou reduzir o número de índices não agrupados.
- No nível de serviço de Finalidade Geral, o armazenamento de dados forvisionado é mais barato do que o preço do armazenamento de backup. Se tiver custos de armazenamento de backup em excesso continuamente elevados, poderá considerar aumentar o armazenamento de dados para economizar no armazenamento de backup.
- Utilize o TempDB em vez de tabelas permanentes na lógica de aplicação para armazenar resultados temporários e/ou dados transitórios.

## <a name="backup-retention"></a>Retenção da cópia de segurança

Para todas as bases de dados novas, restauradas e copiadas, a Base de Dados Azure SQL e a Azure SQL Managed Instance conservam cópias de segurança suficientes para permitir o PITR nos últimos 7 dias por padrão. Com exceção das bases de dados hyperscale, pode alterar o período de [retenção de backup](#change-the-pitr-backup-retention-period) por base de dados na faixa de 1-35 dias. Conforme descrito no [consumo de armazenamento de backup,](#backup-storage-consumption)as cópias de segurança armazenadas para permitir o PITR podem ser mais antigas do que o período de retenção.

Se eliminar uma base de dados, o sistema mantém as cópias de segurança da mesma forma que faria para uma base de dados online com o seu período de retenção específico. Não é possível alterar o período de retenção de cópias de segurança para uma base de dados eliminada.

> [!IMPORTANT]
> Se eliminar um servidor ou uma instância gerida, todas as bases de dados desse servidor ou instância gerida também são eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor apagado ou uma instância gerida. Mas se tivesse configurado a retenção a longo prazo (LTR) para uma base de dados ou caso gerido, as cópias de segurança de retenção a longo prazo não são eliminadas, podendo ser usadas para restaurar bases de dados num servidor diferente ou caso gerido na mesma subscrição, a um ponto do tempo em que foi tomada uma cópia de segurança de retenção a longo prazo.

A retenção de backup para fins de PITR nos últimos 1-35 dias é por vezes chamada de retenção de backup de curto prazo. Se precisar de manter as cópias de segurança durante mais tempo do que o período máximo de retenção de curto prazo de 35 dias, pode permitir a [retenção a longo prazo](long-term-retention-overview.md).

### <a name="long-term-retention"></a>Retenção de longa duração

Para bases de dados individuais e aginhadas e casos geridos, pode configurar a retenção de longo prazo (LTR) de cópias de segurança completas até 10 anos no armazenamento Azure Blob. Se ativar uma política LTR, as cópias de segurança completas semanais são automaticamente copiadas para um recipiente de armazenamento RA-GRS diferente. Para satisfazer vários requisitos de conformidade, pode selecionar diferentes períodos de retenção para backups completos semanais, mensais e/ou anualmente. O consumo de armazenamento depende da frequência selecionada de backups LTR, e do período ou períodos de retenção. Pode utilizar a [calculadora de preços LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento ltr.

Tal como as cópias de segurança pitr, as cópias de segurança LTR estão protegidas com armazenamento geo-redundante. Para obter mais informações, veja [Redundância do Armazenamento do Microsoft Azure](../../storage/common/storage-redundancy.md).

Para obter mais informações sobre o LTR, consulte [a retenção de backup a longo prazo.](long-term-retention-overview.md)

## <a name="storage-costs"></a>Custos de armazenamento

O preço de armazenamento varia consoante esteja a utilizar o modelo DTU ou o modelo vCore.

### <a name="dtu-model"></a>Modelo de DTU

No modelo DTU, não há custo adicional para armazenamento de backup para bases de dados e piscinas elásticas. O preço do armazenamento de backup é uma parte da base de dados ou do preço da piscina.

### <a name="vcore-model"></a>Modelo de vCore

Para bases de dados individuais na Base de Dados SQL, é fornecida uma quantidade de armazenamento de backup igual a 100% do tamanho máximo de armazenamento de dados para a base de dados, sem custos adicionais. Para piscinas elásticas e instâncias geridas, é fornecida uma quantia de armazenamento de backup igual a 100% do armazenamento máximo de dados para o pool ou o tamanho máximo de armazenamento de instância, respectivamente, é fornecido sem custos adicionais. 

O consumo adicional de armazenamento de backup, se houver, será cobrado em GB/mês. Este consumo adicional dependerá da carga de trabalho e do tamanho de bases de dados individuais, piscinas elásticas e instâncias geridas. As bases de dados fortemente modificadas têm maior diferencial e cópias de segurança de registo, porque o tamanho destas cópias de segurança é proporcional à quantidade de alterações de dados. Portanto, estas bases de dados terão taxas de backup mais elevadas.

A SQL Database e a SQL Managed Instance calculam o seu armazenamento total de backup como um valor cumulativo em todos os ficheiros de backup. A cada hora, este valor é reportado ao oleoduto de faturação Azure, que agrega este uso de hora a hora para obter o seu consumo de armazenamento de reserva no final de cada mês. Se uma base de dados for eliminada, o consumo de armazenamento de cópias de segurança diminuirá gradualmente à medida que as cópias de segurança mais antigas envelhecem e são eliminadas. Como as cópias de segurança diferenciais e as cópias de segurança requerem uma cópia de segurança completa anterior para serem restauradoras, os três tipos de backup são purgados em conjuntos semanais. Uma vez que todos os backups são apagados, a faturação para. 

Como exemplo simplificado, assuma que uma base de dados acumulou 744 GB de armazenamento de backup e que este valor permanece constante durante um mês inteiro porque a base de dados está completamente inativa. Para converter este consumo acumulado de armazenamento para uso horário, divida-o por 744,0 (31 dias por mês * 24 horas por dia). A SQL Database informará ao gasoduto de faturação da Azure que a base de dados consumia 1 GB de backup PITR a cada hora, a uma taxa constante. A faturação do Azure irá agregar este consumo e mostrar um uso de 744 GB durante todo o mês. O custo basear-se-á na taxa de valor/GB/mês na sua região.

Agora, um exemplo mais complexo. Suponha que a mesma base de dados ociosa tenha a sua retenção aumentada de 7 dias para 14 dias em meados do mês. Este aumento resulta na duplicação total do armazenamento de backup para 1.488 GB. A SQL Database reportaria 1 GB de utilização durante as horas 1 a 372 (a primeira metade do mês). Reportaria o uso como 2 GB durante as horas 373 a 744 (a segunda metade do mês). Esta utilização seria agregada a uma nota final de 1.116 GB/mês.

Os cenários reais de faturação de backup são mais complexos. Como a taxa de alterações na base de dados depende da carga de trabalho e é variável ao longo do tempo, o tamanho de cada diferencial e backup de registo também variará, fazendo com que o consumo de armazenamento de backup horária oscile em conformidade. Além disso, cada cópia de segurança diferencial contém todas as alterações feitas na base de dados desde a última cópia de segurança completa, pelo que o tamanho total de todas as cópias de segurança diferenciais aumenta gradualmente ao longo de uma semana, e depois cai acentuadamente uma vez que um conjunto mais antigo de cópias de segurança completas, diferenciais e registos envelhece. Por exemplo, se uma intensa atividade de escrita, como a reconstrução de índices, tiver sido executada logo após a conclusão de uma cópia de segurança completa, então as modificações efetuadas pela reconstrução do índice serão incluídas nas cópias de segurança do registo de transações tomadas durante a duração da reconstrução, na próxima cópia de segurança diferencial e em cada cópia de segurança diferencial tomada até que o próximo backup completo ocorra. Para este último cenário em bases de dados maiores, uma otimização no serviço cria uma cópia de segurança completa em vez de uma cópia de segurança diferencial se uma cópia de segurança diferencial for excessivamente grande de outra forma. Isto reduz o tamanho de todas as cópias de segurança diferenciais até ao seguinte backup completo.

Pode monitorizar o consumo total de armazenamento de backup para cada tipo de cópia de segurança (registo completo, diferencial, de transação) ao longo do tempo, conforme descrito no [consumo do Monitor](#monitor-consumption).

### <a name="monitor-costs"></a>Monitorizar os custos

Para compreender os custos de armazenamento de backup, vá à **Gestão de Custos + Faturação** no portal Azure, selecione **Cost Management**e, em seguida, selecione Análise **de Custos**. Selecione a subscrição desejada como **Scope**e, em seguida, filtre pelo período de tempo e serviço em que está interessado.

Adicione um filtro para **o nome de serviço**e, em seguida, selecione a base de **dados sql** na lista de drop-down. Utilize o filtro **da subcategoria** do medidor para escolher o contador de faturação para o seu serviço. Para uma única base de dados ou uma piscina de base de dados elástica, selecione **um armazenamento de backup de pitr de piscina único/elástico**. Para uma instância gerida, selecione **o armazenamento de backup mi pitr**. As subcategorias **de Armazenamento** e **Computação** também podem interessar-lhe, mas não estão associados a custos de armazenamento de backup.

![Análise de custos de armazenamento de backup](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="encrypted-backups"></a>Backups encriptados

Se a sua base de dados estiver encriptada com TDE, as cópias de segurança são automaticamente encriptadas em repouso, incluindo cópias de segurança LTR. Todas as novas bases de dados em Azure SQL estão configuradas com TDE ativado por padrão. Para obter mais informações sobre o TDE, consulte [a Encriptação de Dados Transparente com a Base de Dados SQL & SQL Managed Instance](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integridade de backup

De forma contínua, a equipa de engenharia Azure SQL testa automaticamente a restauração de backups automáticos de bases de dados. (Este teste não está atualmente disponível em SQL Managed Instance.) Após a restauração pontual, as bases de dados também recebem verificações de integridade do DBCC CHECKDB.

Quaisquer problemas encontrados durante a verificação de integridade resultarão num alerta para a equipa de engenharia. Para obter mais informações, consulte [a Integridade dos Dados na Base de Dados SQL.](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

Todas as cópias de segurança da base de dados são tomadas com a opção CHECKSUM para fornecer integridade adicional de backup.

## <a name="compliance"></a>Conformidade

Quando migra a sua base de dados de um nível de serviço baseado em DTU para um nível de serviço baseado em vCore, a retenção PITR é preservada para garantir que a política de recuperação de dados da sua aplicação não seja comprometida. Se a retenção predefinida não cumprir os seus requisitos de conformidade, pode alterar o período de retenção pitr. Para obter mais informações, consulte [alterar o período de retenção de backup pitr](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Alterar o período de retenção de backup pitr

Pode alterar o período de retenção de backup pitr predefinido utilizando o portal Azure, PowerShell ou a API REST. Os exemplos a seguir ilustram como alterar a retenção pitr para 28 dias.

> [!WARNING]
> Se reduzir o período de retenção atual, perde-se a capacidade de restaurar pontos no tempo mais antigo do que o novo período de retenção. São eliminadas cópias de segurança que já não são necessárias para fornecer PITR durante o novo período de retenção. Se aumentar o período de retenção atual, não obterá imediatamente a capacidade de restaurar para pontos mais antigos no tempo dentro do novo período de retenção. Ganha-se essa capacidade ao longo do tempo, à medida que o sistema começa a reter backups por mais tempo.

> [!NOTE]
> Estas APIs afetarão apenas o período de retenção pitr. Se configurar o LTR para a sua base de dados, não será afetado. Para obter informações sobre como alterar os períodos de retenção do LTR, consulte [a retenção a longo prazo](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Alterar o período de retenção de backup PITR utilizando o portal Azure

Para alterar o período de retenção de backup pitr utilizando o portal Azure, vá ao servidor ou caso gerido com as bases de dados cujo período de retenção pretende alterar. 

#### <a name="sql-database"></a>[Base de Dados SQL](#tab/single-database)

As alterações à retenção de backup pitr para base de dados SQL são feitas na página do servidor no portal. Para alterar a retenção pitr para bases de dados num servidor, aceda à lâmina de visão geral do servidor. **Selecione Gerir backups** no painel esquerdo, selecione as bases de dados no âmbito da sua alteração e, em seguida, selecione **a retenção** de configuração na parte superior do ecrã:

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
