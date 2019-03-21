---
title: Georreplicação ativa - base de dados SQL do Azure | Documentos da Microsoft
description: Utilize a georreplicação ativa para criar bases de dados secundárias legíveis de bases de dados individuais no Centro de dados iguais ou diferentes (região).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: cb83f0c38f6860340444c15b6c5eef0b990d0ad0
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295254"
---
# <a name="creating-and-using-active-geo-replication"></a>Criar e utilizar a georreplicação ativa

Georreplicação ativa é a funcionalidade de base de dados do Azure SQL permite-lhe criar bases de dados secundárias legíveis de bases de dados individuais num servidor de base de dados SQL no Centro de dados de idêntica ou diferente (região).

> [!NOTE]
> Replicação geográfica activa não é suportada por instância gerida. Para a ativação pós-falha geográfica de instâncias geridas, utilize [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md).

Replicação geográfica activa foi concebida como uma solução de continuidade de negócio que permite que o aplicativo efetuar a recuperação após desastre rápida de bases de dados individuais em caso de um desastre regional ou interrupções de grande escala. Se a georreplicação está ativada, o aplicativo pode iniciar a ativação pós-falha para uma base de dados secundária numa região diferente do Azure. Até quatro bases de dados secundárias são suportados nas regiões idêntica ou diferentes e as bases de dados secundárias também podem ser utilizados para consultas de acesso só de leitura. A ativação pós-falha tem de ser iniciada manualmente pelo aplicativo ou pelo utilizador. Após a ativação pós-falha, a nova principal tem um ponto de final de ligação diferente. O diagrama seguinte ilustra uma configuração típica de um aplicativo de cloud com redundância geográfica usando a georreplicação ativa.

![Georreplicação ativa](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> Base de dados SQL também oferece suporte a grupos de ativação pós-falha automática. Para obter mais informações, consulte usando [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md). Além disso, a georreplicação ativa não é suportada para bases de dados criadas dentro de uma instância gerida. Considere a utilização [grupos de ativação pós-falha](sql-database-auto-failover-group.md) com instâncias geridas.

Se, para qualquer razão a falha da base de dados primária, ou simplesmente precisam de ficar offline, pode iniciar a ativação pós-falha para qualquer uma das suas bases de dados secundários. Quando a ativação pós-falha é ativada para uma das bases de dados secundárias, todas as outras bases de dados secundárias são automaticamente associados para a nova principal.

Pode gerir a replicação e ativação pós-falha de uma base de dados individual ou um conjunto de bases de dados num servidor ou num conjunto elástico com georreplicação ativa. Pode fazer o que o uso:

- O [portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell: Base de dados](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Conjunto elástico](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Base de dados ou conjunto elástico](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Base de dados](https://docs.microsoft.com/rest/api/sql/replicationlinks)

Após a ativação pós-falha, certifique-se de que os requisitos de autenticação para o seu servidor e base de dados são configurados na nova principal. Para obter detalhes, consulte [segurança de base de dados SQL após a recuperação após desastre](sql-database-geo-replication-security-config.md).

Tira partido da georreplicação ativa a [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) tecnologia do SQL Server para a replicação assíncrona de transações consolidadas na base de dados primária para uma base de dados secundária, usando o isolamento de instantâneo. Os grupos de ativação pós-falha automática fornecem a semântica de grupo por cima de georreplicação ativa, mas o mesmo mecanismo de replicação assíncrona é utilizado. Embora num determinado período, a base de dados secundária pode ser um pouco por trás da base de dados primária, os dados secundários são garantidos que nunca terá transações parciais. Redundância entre regiões habilita aplicativos para recuperar rapidamente de uma perda permanente de todo o datacenter ou partes de um datacenter causado por desastres naturais, catastróficos erros humanos ou atos mal-intencionados. Os dados específicos de RPO que podem ser encontrados em [descrição geral da continuidade do negócio](sql-database-business-continuity.md).

> [!NOTE]
> Se houver uma falha de rede entre duas regiões, podemos Repetir cada 10 segundos para voltar a estabelecer ligações.
> [!IMPORTANT]
> Para garantir que uma alteração fundamental na base de dados primário é replicada para uma secundária antes da ativação pós-falha é, pode forçar a sincronização para garantir que a replicação das alterações críticas (por exemplo, atualizações de palavra-passe). Sincronização forçada afeta o desempenho, porque ele bloqueia o thread de chamada até que todas as transações consolidadas são replicadas. Para obter detalhes, consulte [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Para monitorizar o desfasamento de replicação entre a base de dados primária e geo-secundária, consulte [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

A figura seguinte mostra um exemplo de georreplicação ativa configurada com um site primário na região e.u.a. Centro-Norte e secundárias na região Centro-Sul.

![relação de replicação geográfica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Como as bases de dados secundárias legíveis, pode servir para a descarga de cargas de trabalho de só de leitura como tarefas de relatório. Se estiver a utilizar georreplicação ativa, é possível criar a base de dados secundário na mesma região com o principal, mas não aumenta maior resiliência o aplicativo às falhas catastróficas. Se estiver a utilizar grupos de ativação pós-falha automática, a sua base de dados secundária é sempre criado numa região diferente.

Além de desastre, recuperação georreplicação ativa pode ser usada nos seguintes cenários:

- **Migração de base de dados**: Pode utilizar a georreplicação ativa para migrar uma base de dados de um servidor para outro online com tempo de inatividade mínimo.
- **As atualizações de aplicações**: Pode criar um secundário extra como uma cópia de back-falhar durante as atualizações de aplicações.

Para alcançar a continuidade do negócio real, a adição de redundância da base de dados entre datacenters é apenas uma parte da solução. Recuperação de uma aplicativo (serviço)-a-ponto após uma falha catastrófica requer a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. Exemplos desses componentes incluem o software de cliente (por exemplo, um navegador com um JavaScript personalizada), o front-ends web, o armazenamento e o DNS. É fundamental que todos os componentes são resilientes a falhas do mesmo e fiquem disponíveis dentro de objetivo de tempo de recuperação (RTO) da sua aplicação. Portanto, precisa identificar todos os serviços dependentes e compreender as garantias e capacidades que fornecem. Em seguida, tem de seguir passos adequados para garantir que as suas funções de serviço durante a ativação pós-falha dos serviços do qual depende. Para obter mais informações sobre as soluções de conceção para recuperação após desastre, veja [conceber soluções de Cloud para recuperação de desastres usando replicação geográfica activa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Terminologia de georreplicação ativa e capacidades

- **Replicação assíncrona automática**

  Só pode criar uma base de dados secundária ao adicionar a base de dados existente. A secundária pode ser criada em qualquer servidor de base de dados do Azure SQL. Depois de criado, a base de dados secundária é preenchido com os dados copiados da base de dados primária. Este processo é conhecido como seeding. Depois de base de dados secundária foi criado e implantado, as atualizações para a base de dados primário são replicadas de forma assíncrona a base de dados secundária automaticamente. Replicação assíncrona significa que as transações são consolidadas no banco de dados primário antes de eles serem replicados para a base de dados secundária.

- **Bases de dados secundárias legíveis**

  Um aplicativo pode acessar um banco de dados secundário para operações só de leitura, usando os princípios de segurança de idêntica ou diferente usados para acessar o banco de dados primário. As bases de dados secundários operam no modo de isolamento do instantâneo para garantir a replicação das atualizações dos principais (repetição do log) não é atrasada por consultas executadas no secundário.

  > [!NOTE]
  > A repetição do log está atrasada no banco de dados secundário se existem atualizações de esquema na primária. A segunda opção requer um bloqueio de esquema na base de dados secundário.

- **Ativação pós-falha planeada**

  Ativação pós-falha planeada executa uma sincronização completa entre bases de dados primárias e secundárias antes dos comutadores secundários para a função primária. Isso garante sem perda de dados. Ativação pós-falha planeada é utilizada nos seguintes cenários: (a) para executar o DR explorações produção quando a perda de dados não é aceitável; (b) pode mudar para a base de dados para uma região diferente. e (c) para devolver a base de dados para a região primária após a falha foi Atenuado (reativação pós-falha).

- **Ativação pós-falha não planeada**

  Ativação pós-falha não planeada ou forçada muda imediatamente o secundário para a função principal sem qualquer sincronização com o principal. Esta operação irá resultar na perda de dados. Ativação pós-falha não planeada é utilizada como um método de recuperação durante as falhas quando o principal não está acessível. Quando o original principal esteja novamente online, irá automaticamente voltar a ligar sem sincronização e se tornar um secundário novo.

- **Várias bases de dados secundárias legíveis**

  Até 4 bases de dados secundárias pode ser criado para cada principal. Se há apenas uma base de dados secundária, e este falhar, o aplicativo é exposto ao risco mais alto até que seja criada uma nova base de dados secundário. Se existirem várias bases de dados secundários, o aplicativo permanece protegido mesmo que uma das bases de dados secundários falhar. Secundários adicionais também podem ser utilizados para ampliar cargas de trabalho só de leitura

  > [!NOTE]
  > Se estiver a utilizar georreplicação ativa para criar uma aplicação distribuída globalmente e tem de fornecer acesso só de leitura aos dados em mais de quatro regiões, pode criar secundário de uma secundária (um processo conhecido como encadeamento). Desta forma, que pode atingir dimensionamento praticamente ilimitado de replicação de base de dados. Além disso, o encadeamento reduz a sobrecarga da replicação da base de dados primário. A desvantagem é o desfasamento de aumento nas bases de dados mais folha secundários.

- **Georreplicação de bancos de dados num conjunto elástico**

  Cada base de dados secundária pode participar em separado de um conjunto elástico ou não estar em qualquer conjunto elástico em todos os. A opção de agrupamento para cada base de dados secundária está separada e não dependem da configuração de quaisquer outra secundária da base de dados (se primário ou secundário). Cada conjunto elástico está contido numa única região, por isso várias bases de dados secundários na mesma topologia nunca podem partilhar um conjunto elástico.

- **Tamanho de computação configuráveis do banco de dados secundário**

  Bases de dados primários e secundários têm de ter a mesma camada de serviço. É também vivamente recomendado que essa base de dados secundária é criada com o mesmo tamanho de computação (DTUs ou vCores) como principal. Uma secundária com tamanho inferior de computação está em risco de um atraso de replicação maior, indisponibilidade potencial de secundário e, consequentemente, um risco de perda de dados substancial após uma ativação pós-falha. Como resultado, o RPO publicado = 5 s não pode ser garantida. O risco de outro é que após a ativação pós-falha do desempenho do aplicativo será afetado devido à falta de capacidade de computação da nova principal até que ele é atualizado para um tamanho de computação mais elevado. A hora da atualização depende do tamanho de base de dados. Além disso, atualmente essa atualização requer que as bases de dados primárias e secundárias estão online e, portanto, não podem ser concluídas até que a falha é atenuada. Se optar por criar o secundário com o menor tamanho de computação, o gráfico de percentagem de e/s de registo no portal do Azure fornece uma boa forma de estimar o tamanho mínimo de computação da secundária que é necessário para sustentar a carga de replicação. Por exemplo, se a sua base de dados primária é P6 (1000 DTUS) e o respetivo registo de % de e/s é de 50% secundário tem de ser, pelo menos, P4 (500 DTU). Também pode obter os dados de e/s de registo usando [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ou [DM db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vistas de base de dados.  Para obter mais informações sobre os tamanhos de computação de base de dados SQL, consulte [quais são os escalões de serviço de base de dados SQL](sql-database-purchase-models.md).

- **Ativação pós-falha controlada pelo utilizador e a reativação pós-falha**

  Uma base de dados secundária pode explicitamente ser mudado para a função primária em qualquer altura, o aplicativo ou o utilizador. Durante uma falha real a opção "não planeada" deve ser usada, que promove imediatamente uma secundária para ser o primário. Quando o primário falhou recupera e estiver novamente disponível, o sistema marca primário como um secundário recuperado automaticamente e colocá-lo atualizado com a nova principal. Devido à natureza assíncrona de replicação, uma pequena quantidade de dados pode ser perdida durante as ativações pós-falha não planeadas se falhar de um site primário antes de ser replicado as alterações mais recentes para o secundário. Quando um site primário com várias bases de dados secundárias falha, o sistema é automaticamente reconfigura as relações de replicação e ligações restantes secundários para primária recentemente promovida sem exigir qualquer intervenção do utilizador. Assim que a falha que causou a ativação pós-falha é minimizada, talvez seja desejável para a aplicação volte para a região primária. Para tal, o comando de ativação pós-falha deve ser invocado com a opção "planeada".

- **Mantendo credenciais e as regras de firewall em sincronia**

Recomendamos que utilize [regras de firewall do IP de nível de base de dados](sql-database-firewall-configure.md) para georreplicado bases de dados para que estas regras podem ser replicadas com a base de dados para garantir que todas as bases de dados secundárias têm as mesmas regras de firewall do IP como principal. Essa abordagem elimina a necessidade dos clientes manualmente configurar e manter regras de firewall nos servidores que alojam as bases de dados primárias e secundárias. Da mesma forma, usando [utilizadores de base de dados contidos](sql-database-manage-logins.md) para dados de acesso garante que as bases de dados primários e secundários têm sempre a mesma as credenciais de utilizador durante uma ativação pós-falha, não há nenhum interrupções devido a incompatibilidades com inícios de sessão e palavras-passe. Com a adição da [do Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), os clientes podem gerir o acesso de utilizador para bases de dados primárias e secundárias e eliminando a necessidade de gerenciamento de credenciais em bases de dados completamente.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou fazer downgrade uma base de dados primária

Pode atualizar ou mudar a versão de uma base de dados principal para um tamanho de computação diferentes (dentro do mesmo escalão de serviço, não entre fins gerais e crítico para a empresa) sem desligar a quaisquer bases de dados secundários. Ao atualizar, recomendamos que Atualize primeiro o banco de dados secundário e, em seguida, atualizar o primário. Ao fazer downgrade, inverter a ordem: mudar para a versão principal primeiro e, em seguida, mudar o secundário. Quando atualizar ou mudar para a versão da base de dados para um escalão de serviço diferente, esta recomendação é imposta.

> [!NOTE]
> Se criou a base de dados secundária como parte da configuração do grupo de ativação pós-falha não é recomendado para mudar para a versão da base de dados secundário. Isso é para garantir que sua camada de dados tem capacidade suficiente para processar a carga de trabalho regular depois de ativação pós-falha está ativado.

> [!IMPORTANT]
> A base de dados primária num grupo de ativação pós-falha não é possível dimensionar para um escalão superior, a menos que a base de dados secundário em primeiro lugar é dimensionada ao escalão mais elevado. Se tentar dimensionar a base de dados primária antes da base de dados secundária é dimensionada, pode receber o erro seguinte:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Impedir a perda de dados críticos

Devido a alta latência das redes de longa distância, a cópia contínua utiliza um mecanismo de replicação assíncrona. Replicação assíncrona faz alguma perda de dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir sem perda de dados. Para proteger estas atualizações críticas, um desenvolvedor de aplicativos pode chamar o [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) procedimento de sistema imediatamente após a confirmação da transação. A invocar **sp_wait_for_database_copy_sync** bloqueia o thread de chamada até que a última transação ocorrida foi transmitida para a base de dados secundário. No entanto, não espere para as transações transmitidas para serem reproduzidos e confirmada no secundário. **sp_wait_for_database_copy_sync** tem um âmbito para uma ligação de cópia contínua específico. Qualquer utilizador com os direitos de ligação para base de dados primária pode chamar este procedimento.

> [!NOTE]
> **sp_wait_for_database_copy_sync** impede que a perda de dados após a ativação pós-falha, mas não garante a sincronização completa para acesso de leitura. O atraso causado por um **sp_wait_for_database_copy_sync** chamada de procedimento pode ser significativa e depende do tamanho do registo de transação no momento da chamada.

## <a name="monitoring-geo-replication-lag"></a>Monitorização de desfasamento de georreplicação

Para monitorizar o atraso em relação ao RPO, utilize *replication_lag_sec* coluna da [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) na base de dados primária. Ele mostra o atraso em segundos, entre as transações consolidadas principal e persistido no secundário. Por exemplo, Se o valor da lentidão é de 1 segundo, isso significa que se o principal é afetado por uma falha neste momento e ativação pós-falha é intiated, um segundo da mais recente transtions não será guardado. 

Para medir a latência relativamente a alterações na base de dados primária, que foram aplicadas no secundário, ou seja, disponível para ser lido a partir do secundário, comparar *last_commit* tempo na base de dados secundário com o mesmo valor principal base de dados.

> [!NOTE]
> Às vezes *replication_lag_sec* na base de dados principal tem um valor nulo, o que significa que o primário não conhece atualmente é a distância do secundário.   Esta situação ocorre normalmente depois do processo de ser reiniciado e deve ser uma condição transitória. Considere o aplicativo de alerta se o *replication_lag_sec* devolve NULL por um longo período de tempo. Indicará que a base de dados secundária não é possível comunicar com o principal devido a uma falha de conectividade permanente. Também existem condições que podem causar a diferença entre *last_commit* tempo secundário e na base de dados primária se torne grande. Por exemplo, Se uma consolidação é efetuada na primária após um longo período de nenhuma alteração, a diferença saltará até um valor grande antes de retornar rapidamente como 0. Considerá-lo uma condição de erro quando a diferença entre esses dois valores permanece grande durante muito tempo.


## <a name="programmatically-managing-active-geo-replication"></a>Gerir programaticamente os replicação geográfica activa

Como discutido anteriormente, georreplicação ativa também pode ser gerida através de programação com o Azure PowerShell e a API REST. As tabelas seguintes descrevem o conjunto de comandos disponíveis. Replicação geográfica activa inclui um conjunto de APIs do Azure Resource Manager para a gestão, incluindo o [API REST da base de dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/) e [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs requerem a utilização de grupos de recursos e suportam a segurança baseada em funções (RBAC). Para obter mais informações sobre como implementar funções de acesso, consulte [controlo de acesso](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Gerir a ativação pós-falha de bases de dados individuais e em pool

> [!IMPORTANT]
> Estes comandos Transact-SQL só se aplicam a georreplicação ativa e não se aplicam a grupos de ativação pós-falha. Como tal, eles também não se aplicam às instâncias de geridas, porque só suportam grupos de ativação pós-falha.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Utilize o argumento de servidor para adicionar SECUNDÁRIO ON para criar uma base de dados secundária para uma base de dados existente e começa a replicação de dados |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Utilize a ativação pós-falha ou FORCE_FAILOVER_ALLOW_DATA_LOSS para mudar de uma base de dados secundária para primária para iniciar a ativação pós-falha |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Utilize remover no servidor SECUNDÁRIO para terminar uma replicação de dados entre uma base de dados SQL e a base de dados secundária especificada. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Devolve informações sobre todas as ligações de replicação existente para cada base de dados no servidor de base de dados do Azure SQL. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtém a última vez que a replicação, última desfasamento e outras informações sobre a ligação de replicação para uma determinada base de dados do SQL. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Mostra o estado de todas as operações de banco de dados, incluindo o estado das ligações de replicação. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |faz com que o aplicativo esperar até que todas as transações consolidadas são replicadas e confirmadas pela base de dados secundário Active Directory. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Gerir a ativação pós-falha de bases de dados individuais e em pool

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

| Cmdlet | Descrição |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Cria uma base de dados secundária para uma base de dados existente e começa a replicação de dados. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Muda uma base de dados secundária para primária, para iniciar a ativação pós-falha. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Termina a replicação de dados entre uma Base de Dados SQL e a base de dados secundária especificada. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Obtém as ligações de georreplicação entre uma Base de Dados SQL do Azure e um grupo de recursos ou o SQL Server. |
|  | |

> [!IMPORTANT]
> Para scripts de exemplo, consulte [configurar e ativação pós-falha de uma base de dados com georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) e [configurar e ativação pós-falha de uma base de dados agrupada com a georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: Gerir a ativação pós-falha de bases de dados individuais e em pool

| API | Descrição |
| --- | --- |
| [Criar ou atualizar banco de dados (createMode = restauro)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Cria, atualiza ou restaura a base de dados secundário ou de um site primário. |
| [Começar a criar ou atualizar o estado da base de dados](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Devolve o estado durante uma operação de criação. |
| [Definir a base de dados secundária como principal (ativação pós-falha planeada)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Conjuntos de base de dados secundário é principal ao realizar a ativação pós-falha da base de dados primária atual. **Esta opção não é suportada para a instância gerida.**|
| [Definir a base de dados secundária como principal (ativação pós-falha não planeada)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Conjuntos de base de dados secundário é principal ao realizar a ativação pós-falha da base de dados primária atual. Esta operação poderá resultar em perda de dados. **Esta opção não é suportada para a instância gerida.**|
| [Obter a ligação de replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Obtém uma ligação de replicação específicos para um determinado banco de dados do SQL numa parceria de replicação geográfica. Obtém as informações visíveis na vista de catálogo sys.geo_replication_links. **Esta opção não é suportada para a instância gerida.**|
| [Ligações de replicação - lista pela base de dados](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Obtém todas as ligações de replicação para uma determinada base de dados do SQL numa parceria de replicação geográfica. Obtém as informações visíveis na vista de catálogo sys.geo_replication_links. |
| [Eliminar ligação de replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Elimina uma ligação de replicação de base de dados. Não pode ser feito durante a ativação pós-falha. |
|  | |

## <a name="next-steps"></a>Passos Seguintes

- Para scripts de exemplo, consulte:
  - [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Configurar e efetuar a ativação pós-falha de uma base de dados agrupada através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- Base de dados SQL também oferece suporte a grupos de ativação pós-falha automática. Para obter mais informações, consulte usando [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md).
- Para uma visão geral de continuidade de negócio e cenários, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md)
- Para saber mais sobre SQL do Azure, base de dados automatizada de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre a utilização de cópias de segurança automatizadas para recuperação, veja [restaurar uma base de dados a partir de cópias de segurança iniciadas pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre os requisitos de autenticação para um novo servidor primário e a base de dados, veja [segurança de base de dados SQL após a recuperação após desastre](sql-database-geo-replication-security-config.md).
