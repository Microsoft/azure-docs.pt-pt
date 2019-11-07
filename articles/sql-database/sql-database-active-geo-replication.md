---
title: Replicação geográfica ativa-banco de dados SQL do Azure
description: Use a replicação geográfica ativa para criar bancos de dados secundários legíveis de bancos de dados individuais no mesmo ou em data center diferentes (região).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 07/09/2019
ms.openlocfilehash: 74cbb9fa5a00b287746afd92fe74f50bfa19110b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691318"
---
# <a name="creating-and-using-active-geo-replication"></a>Criando e usando a replicação geográfica ativa

A replicação geográfica ativa é um recurso de banco de dados SQL do Azure que permite que você crie bancos de dados secundários legíveis de bancos de dados individuais em um servidor de banco de dados SQL no mesmo data center ou em uma região diferente.

> [!NOTE]
> A replicação geográfica ativa não tem suporte pela instância gerenciada. Para o failover geográfico de instâncias gerenciadas, use [grupos de failover automático](sql-database-auto-failover-group.md).

A replicação geográfica ativa é projetada como uma solução de continuidade de negócios que permite que o aplicativo execute a recuperação rápida de desastres de bancos de dados individuais no caso de um desastre regional ou uma interrupção em grande escala. Se a replicação geográfica estiver habilitada, o aplicativo poderá iniciar o failover para um banco de dados secundário em uma região diferente do Azure. Há suporte para até quatro secundários nas mesmas ou em regiões diferentes, e os secundários também podem ser usados para consultas de acesso somente leitura. O failover deve ser iniciado manualmente pelo aplicativo ou pelo usuário. Após o failover, o novo primário tem um ponto de extremidade de conexão diferente. O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando a replicação geográfica ativa.

![replicação geográfica ativa](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> O banco de dados SQL também dá suporte a grupos de failover automático. Para obter mais informações, consulte usando [grupos de failover automático](sql-database-auto-failover-group.md). Além disso, a replicação geográfica ativa não tem suporte para bancos de dados criados em um Instância Gerenciada. Considere o uso de [grupos de failover](sql-database-auto-failover-group.md) com instâncias gerenciadas.

Se, por algum motivo, o banco de dados primário falhar ou simplesmente precisar ser colocado offline, você poderá iniciar o failover para qualquer um dos seus bancos de dados secundários. Quando o failover é ativado para um dos bancos de dados secundários, todos os outros secundários são automaticamente vinculados ao novo primário.

Você pode gerenciar a replicação e o failover de um banco de dados individual ou de um conjunto de dados em um servidor ou em um pool elástico usando a replicação geográfica ativa. Você pode fazer isso usando:

- O [portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell: banco de dados individual](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: pool elástico](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: banco de dados individual ou pool elástico](/sql/t-sql/statements/alter-database-azure-sql-database)
- [API REST: banco de dados individual](https://docs.microsoft.com/rest/api/sql/replicationlinks)


A replicação geográfica ativa aproveita a tecnologia de [Always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) de SQL Server para replicar de forma assíncrona transações confirmadas no banco de dados primário para um banco de dados secundário usando isolamento de instantâneo. Os grupos de failover automático fornecem a semântica do grupo sobre a replicação geográfica ativa, mas o mesmo mecanismo de replicação assíncrona é usado. Embora em qualquer momento determinado, o banco de dados secundário pode estar um pouco atrás do banco de dados primário, a garantia de que nunca tenha transações parciais. A redundância entre regiões permite que os aplicativos se recuperem rapidamente de uma perda permanente de um datacenter inteiro ou de partes de um datacenter causados por desastres naturais, erros humanos catastróficos ou atos mal-intencionados. Os dados de RPO específicos podem ser encontrados em [visão geral da continuidade dos negócios](sql-database-business-continuity.md).

> [!NOTE]
> Se houver uma falha de rede entre duas regiões, repetiremos a cada 10 segundos para restabelecer as conexões.
> [!IMPORTANT]
> Para garantir que uma alteração crítica no banco de dados primário seja replicada para um secundário antes do failover, você pode forçar a sincronização para garantir a replicação de alterações críticas (por exemplo, atualizações de senha). A sincronização forçada afeta o desempenho porque bloqueia o thread de chamada até que todas as transações confirmadas sejam replicadas. Para obter detalhes, consulte [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Para monitorar o atraso de replicação entre o banco de dados primário e o secundário geográfico, consulte [Sys. dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

A figura a seguir mostra um exemplo de replicação geográfica ativa configurada com um primário na região do Norte EUA Central e secundária na região do Sul EUA Central.

![relacionamento de replicação geográfica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Como os bancos de dados secundários são legíveis, eles podem ser usados para descarregar cargas de trabalho somente leitura, como trabalhos de relatório. Se você estiver usando a replicação geográfica ativa, é possível criar o banco de dados secundário na mesma região com o primário, mas ele não aumenta a resiliência do aplicativo para falhas catastróficas. Se você estiver usando grupos de failover automático, o banco de dados secundário será sempre criado em uma região diferente.

Além da recuperação de desastres, a replicação geográfica ativa pode ser usada nos seguintes cenários:

- **Migração de banco de dados**: você pode usar a replicação geográfica ativa para migrar um banco de dados de um servidor para outro online com tempo de inatividade mínimo.
- **Atualizações de aplicativos**: você pode criar um secundário extra como uma cópia de failback durante as atualizações do aplicativo.

Para obter continuidade de negócios real, a adição de redundância de banco de dados entre data centers é apenas parte da solução. Recuperar um aplicativo (serviço) de ponta a ponta após uma falha catastrófica exige a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. Exemplos desses componentes incluem o software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) do seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas adequadas para garantir que seu serviço funcione durante o failover dos serviços dos quais ela depende. Para obter mais informações sobre como criar soluções para recuperação de desastres, consulte [Projetando soluções de nuvem para recuperação de desastres usando a replicação geográfica ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Recursos e terminologia de replicação geográfica ativa

- **Replicação assíncrona automática**

  Você só pode criar um banco de dados secundário adicionando a um banco de dados existente. O secundário pode ser criado em qualquer servidor de banco de dados SQL do Azure. Depois de criado, o banco de dados secundário é populado com o dado copiado do banco de dados primário. Esse processo é conhecido como propagação. Depois que o banco de dados secundário for criado e propagado, as atualizações para o banco de dados primário serão replicadas de forma assíncrona para o banco de dados secundário automaticamente. Replicação assíncrona significa que as transações são confirmadas no banco de dados primário antes de serem replicadas para o banco de dados secundário.

- **Bancos de dados secundários legíveis**

  Um aplicativo pode acessar um banco de dados secundário para operações somente leitura usando as mesmas ou entidades de segurança diferentes usadas para acessar o banco de dados primário. Os bancos de dados secundários operam no modo de isolamento de instantâneo para garantir que a replicação das atualizações do primário (reprodução de log) não seja atrasada por consultas executadas no secundário.

> [!NOTE]
> A repetição de log será atrasada no banco de dados secundário se houver atualizações de esquema no primário. O último requer um bloqueio de esquema no banco de dados secundário.
> [!IMPORTANT]
> Você pode usar a replicação geográfica para criar um banco de dados secundário na mesma região que o primário. Você pode usar esse secundário para balancear a carga de cargas de trabalho somente leitura na mesma região. No entanto, um banco de dados secundário na mesma região não fornece resiliência de falha adicional e, portanto, não é um destino de failover adequado para recuperação de desastres. Ele também não garantirá o isolamento da zona de disponibilidade. Use a camada de serviço comercialmente crítica ou Premium com [configuração com redundância de zona](sql-database-high-availability.md#zone-redundant-configuration) para obter isolamento de zona de disponibilidade.   
>

- **Failover planejado**

  O failover planejado alterna as funções dos bancos de dados primário e secundário após a conclusão da sincronização completa. É uma operação online que não resulta em perda de dados. A hora da operação depende do tamanho do log de transações no primário que precisa ser sincronizado. O failover planejado foi projetado para os seguintes cenários: (a) para executar análises de DR em produção quando a perda de dados não for aceitável; (b) para realocar o banco de dados para uma região diferente; e (c) para retornar o banco de dados para a região primária após a interrupção ser mitigada (failback).

- **Failover não planejado**

  O failover não planejado ou forçado alterna imediatamente o secundário para a função primária sem nenhuma sincronização com o primário. Todas as transações confirmadas no primário, mas não replicadas no secundário, serão perdidas. Essa operação é projetada como um método de recuperação durante interrupções quando a primária não está acessível, mas a disponibilidade do banco de dados deve ser restaurada rapidamente. Quando o primário original estiver online novamente, ele será reconectado automaticamente e se tornará um novo secundário. Todas as transações não sincronizadas antes do failover serão preservadas no arquivo de backup, mas não serão sincronizadas com o novo primário para evitar conflitos. Essas transações precisarão ser mescladas manualmente com a versão mais recente do banco de dados primário.
 
- **Vários secundários legíveis**

  Até 4 bancos de dados secundários podem ser criados para cada primário. Se houver apenas um banco de dados secundário e ele falhar, o aplicativo ficará exposto a um risco maior até que um novo banco de dados secundário seja criado. Se houver vários bancos de dados secundários, o aplicativo permanecerá protegido mesmo se um dos bancos de dados secundários falhar. Os secundários adicionais também podem ser usados para expandir as cargas de trabalho somente leitura

  > [!NOTE]
  > Se você estiver usando a replicação geográfica ativa para criar um aplicativo distribuído globalmente e precisar fornecer acesso somente leitura aos dados em mais de quatro regiões, poderá criar um secundário de um secundário (um processo conhecido como encadeamento). Dessa forma, você pode obter uma escala praticamente ilimitada da replicação de banco de dados. Além disso, o encadeamento reduz a sobrecarga de replicação do banco de dados primário. A desvantagem é o aumento do atraso de replicação nos bancos de dados secundários da maioria das folhas.

- **Replicação geográfica de bancos de dados em um pool elástico**

  Cada banco de dados secundário pode participar separadamente de um pool elástico ou não estar em nenhum pool elástico. A opção de pool para cada banco de dados secundário é separada e não depende da configuração de qualquer outro banco de dados secundário (se primário ou secundário). Cada pool elástico está contido em uma única região, portanto, vários bancos de dados secundários na mesma topologia nunca podem compartilhar um pool elástico.


- **Failover controlado pelo usuário e failback**

  Um banco de dados secundário pode ser explicitamente alternado para a função primária a qualquer momento pelo aplicativo ou pelo usuário. Durante uma interrupção real, a opção "não planejada" deve ser usada, o que promove imediatamente um secundário para ser o primário. Quando o primário com falha é recuperado e está disponível novamente, o sistema marca automaticamente o primário recuperado como secundário e o coloca atualizado com o novo primário. Devido à natureza assíncrona da replicação, uma pequena quantidade de dados pode ser perdida durante failovers não planejados se um primário falhar antes de replicar as alterações mais recentes para o secundário. Quando um primário com vários secundários falha, o sistema reconfigura automaticamente os relacionamentos de replicação e vincula os secundários restantes ao primário promovido recentemente, sem a necessidade de qualquer intervenção do usuário. Após a interrupção que causou o failover ser mitigada, pode ser desejável retornar o aplicativo para a região primária. Para fazer isso, o comando de failover deve ser invocado com a opção "planejado".

## <a name="preparing-secondary-database-for-failover"></a>Preparando banco de dados secundário para failover

Para garantir que seu aplicativo possa acessar imediatamente o novo primário após o failover, verifique se os requisitos de autenticação do servidor secundário e do banco de dados estão configurados corretamente. Para obter detalhes, consulte [segurança do banco de dados SQL após a recuperação de desastre](sql-database-geo-replication-security-config.md). Para garantir a conformidade após o failover, verifique se a política de retenção de backup no banco de dados secundário corresponde à do primário. Essas configurações não fazem parte do banco de dados e não são replicadas. Por padrão, o secundário será configurado com um período de retenção de PITR padrão de sete dias. Para obter detalhes, consulte [backups automatizados do banco de dados SQL](sql-database-automated-backups.md).

> [!IMPORTANT]
> Se o banco de dados for membro de um grupo de failover, você não poderá iniciar seu failover usando o comando faiover de replicação geográfica. Considere o uso do comando de failover para o grupo. Se precisar fazer failover de um banco de dados individual, você deverá removê-lo primeiro do grupo de failover. Consulte [grupos de failover](sql-database-auto-failover-group.md) para obter detalhes. 


## <a name="configuring-secondary-database"></a>Configurando banco de dados secundário

Os bancos de dados primário e secundário precisam ter a mesma camada de serviço. Também é altamente recomendável que o banco de dados secundário seja criado com o mesmo tamanho de computação (DTUs ou vCores) que o primário. Se o banco de dados primário estiver com uma carga de trabalho de gravação pesada, um secundário com tamanho de computação inferior poderá não ser capaz de acompanhar o problema. Isso causará o atraso de restauração no secundário e em potencial indisponibilidade. Uma base de dados secundária que esteja atrasada face à primária também arrisca uma grande perda de dados, caso seja preciso uma ativação pós-falha forçada. Para atenuar esses riscos, a replicação geográfica ativa em vigor limitará a taxa de log primária para permitir que seus secundários se acompanhem. A outra consequência de uma configuração secundária desbalanceada é que, após o failover, o desempenho do aplicativo será afetado devido à capacidade de computação insuficiente do novo primário. Será necessário atualizar para uma computação mais alta para o nível necessário, o que não será possível até que a interrupção seja atenuada. 


> [!IMPORTANT]
> O RPO publicado = 5 s não pode ser garantido, a menos que o banco de dados secundário esteja configurado com o mesmo tamanho de computação que o primário. 


Se você decidir criar o secundário com o tamanho de computação inferior, o gráfico de porcentagem de e/s de log em portal do Azure fornecerá uma boa maneira de estimar o tamanho mínimo de computação do secundário necessário para manter a carga de replicação. Por exemplo, se seu banco de dados primário for P6 (1000 DTU) e seu percentual de e/s de log for 50%, o secundário precisará ser pelo menos P4 (500 DTU). Você também pode recuperar os dados de e/s de log usando as exibições de banco [Sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ou [Sys. dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) .  A limitação é relatada como um estado de espera HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO nas exibições de banco de dados [Sys. dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [Sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) . 

Para obter mais informações sobre os tamanhos de computação do banco de dados SQL, consulte [o que são as camadas de serviço do banco de dados SQL](sql-database-purchase-models.md).

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Mantendo credenciais e regras de firewall em sincronia

É recomendável usar [regras de firewall de IP de nível de banco de dados](sql-database-firewall-configure.md) para bancos de dados replicados geograficamente para que essas regras possam ser replicadas com o banco de dados para garantir que todos os bancos de dados secundários tenham as mesmas regras de firewall IP que o primário. Essa abordagem elimina a necessidade de os clientes configurarem e manterem manualmente as regras de firewall em servidores que hospedam os bancos de dados primário e secundário. Da mesma forma, o uso de [usuários de banco](sql-database-manage-logins.md) de dados independente para o acesso ao dado garante que os bancos de dados primários e secundários sempre tenham as mesmas credenciais de usuário, portanto, durante um failover, não há interrupções devido a incompatibilidades com logons e senhas. Com a adição de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), os clientes podem gerenciar o acesso do usuário a bancos de dados primários e secundários e eliminar a necessidade de gerenciar credenciais em bancos de dados completamente.

## <a name="upgrading-or-downgrading-primary-database"></a>Atualizando ou fazendo downgrade do banco de dados primário

Você pode atualizar ou fazer downgrade de um banco de dados primário para um tamanho de computação diferente (dentro da mesma camada de serviço, não entre Uso Geral e Comercialmente Crítico) sem desconectar nenhum banco de dados secundário. Ao atualizar, recomendamos que você atualize primeiro o banco de dados secundário e, em seguida, atualize o primário. Ao fazer downgrade, inverta a ordem: faça o downgrade do primário primeiro e, em seguida, faça o downgrade do secundário. Quando você atualiza ou faz downgrade do banco de dados para uma camada de serviço diferente, essa recomendação é imposta.

> [!NOTE]
> Se você criou o banco de dados secundário como parte da configuração do grupo de failover, não é recomendável fazer downgrade do banco de dados secundário. Isso é para garantir que a camada de dados tenha capacidade suficiente para processar a carga de trabalho regular após a ativação do failover.

> [!IMPORTANT]
> O banco de dados primário em um grupo de failover não pode ser dimensionado para uma camada superior, a menos que o banco de dados secundário seja dimensionado primeiro para a camada superior. Se você tentar dimensionar o banco de dados primário antes que o banco de dados secundário seja dimensionado, você poderá receber o seguinte erro:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Evitando a perda de dados críticos

Devido à alta latência de redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. A replicação assíncrona torna inevitável alguma perda de dados se ocorrer uma falha. No entanto, alguns aplicativos podem não exigir perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativos pode chamar o procedimento do sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após confirmar a transação. Chamar **sp_wait_for_database_copy_sync** bloqueia o thread de chamada até que a última transação confirmada seja transmitida para o banco de dados secundário. No entanto, ele não aguarda que as transações transmitidas sejam reproduzidas e confirmadas no secundário. **sp_wait_for_database_copy_sync** está no escopo de um link de cópia contínua específico. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

> [!NOTE]
> **sp_wait_for_database_copy_sync** impede a perda de dados após o failover, mas não garante a sincronização completa para acesso de leitura. O atraso causado por uma chamada de procedimento **sp_wait_for_database_copy_sync** pode ser significativo e depende do tamanho do log de transações no momento da chamada.

## <a name="monitoring-geo-replication-lag"></a>Monitorando o retardo da replicação geográfica

Para monitorar o atraso em relação ao RPO, use *replication_lag_sec* coluna de [Sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) no banco de dados primário. Ele mostra o retardo em segundos entre as transações confirmadas no primário e persistido no secundário. Por exemplo, Se o valor da latência for de 1 segundo, isso significará que, se o primário for afetado por uma interrupção neste momento e o failover for iniciado, 1 segundo das transições mais recentes não será salva. 

Para medir o retardo em relação às alterações no banco de dados primário que foram aplicadas no secundário, ou seja, disponíveis para leitura do secundário, compare *last_commit* tempo no banco de dados secundário com o mesmo valor no banco de dados primário.

> [!NOTE]
> Às vezes *replication_lag_sec* no banco de dados primário tem um valor nulo, o que significa que o primário atualmente não sabe o quanto o secundário é.   Isso normalmente ocorre depois que o processo é reiniciado e deve ser uma condição transitória. Considere alertar o aplicativo se o *replication_lag_sec* retornar nulo por um longo período de tempo. Isso indicaria que o banco de dados secundário não pode se comunicar com o primário devido a uma falha de conectividade permanente. Também há condições que podem causar a diferença entre *last_commit* tempo no secundário e no banco de dados primário se tornarem grandes. Por exemplo, se uma confirmação for feita no primário após um longo período de nenhuma alteração, a diferença saltará para um valor grande antes de retornar rapidamente para 0. Considere uma condição de erro quando a diferença entre esses dois valores permanecer grande por um longo tempo.


## <a name="programmatically-managing-active-geo-replication"></a>Gerenciando programaticamente a replicação geográfica ativa

Como discutido anteriormente, a replicação geográfica ativa também pode ser gerenciada programaticamente usando Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis. A replicação geográfica ativa inclui um conjunto de APIs de Azure Resource Manager para gerenciamento, incluindo a [API REST do banco de dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/) e os [cmdlets Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs exigem o uso de grupos de recursos e oferecem suporte a RBAC (segurança baseada em função). Para obter mais informações sobre como implementar funções de acesso, consulte [controle de acesso baseado em função do Azure](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: gerenciar failover de bancos de dados individuais e em pool

> [!IMPORTANT]
> Esses comandos Transact-SQL se aplicam somente à replicação geográfica ativa e não se aplicam a grupos de failover. Como tal, eles também não se aplicam a instâncias gerenciadas, pois oferecem suporte apenas a grupos de failover.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Use o argumento adicionar secundário no servidor para criar um banco de dados secundário para um banco de dado existente e iniciar a replicação |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Usar FAILOVER ou FORCE_FAILOVER_ALLOW_DATA_LOSS para alternar um banco de dados secundário para primário para iniciar o failover |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Use remover secundário no servidor para encerrar uma replicação de dados entre um banco de dados SQL e o banco de dado secundário especificado. |
| [sys. geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Retorna informações sobre todos os links de replicação existentes para cada banco de dados no servidor de banco de dados SQL do Azure. |
| [sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtém a última hora de replicação, a última latência de replicação e outras informações sobre o link de replicação para um determinado banco de dados SQL. |
| [sys. dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Mostra o status de todas as operações de banco de dados, incluindo o status dos links de replicação. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |faz com que o aplicativo aguarde até que todas as transações confirmadas sejam replicadas e confirmadas pelo banco de dados secundário ativo. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: gerenciar failover de bancos de dados individuais e em pool

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

| Cmdlet | Descrição |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Cria uma base de dados secundária para uma base de dados existente e começa a replicação de dados. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Muda uma base de dados secundária para primária, para iniciar a ativação pós-falha. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Termina a replicação de dados entre uma Base de Dados SQL e a base de dados secundária especificada. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Obtém as ligações de georreplicação entre uma Base de Dados SQL do Azure e um grupo de recursos ou o SQL Server. |
|  | |

> [!IMPORTANT]
> Para scripts de exemplo, consulte [configurar e fazer failover de um banco de dados individual usando a replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) e [configurar e fazer failover de um banco de dados em pool usando a replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>API REST: gerenciar failover de bancos de dados individuais e em pool

| API | Descrição |
| --- | --- |
| [Criar ou atualizar banco de dados (= restaurar)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Cria, atualiza ou restaura um banco de dados primário ou secundário. |
| [Obter o status de criação ou atualização do banco de dados](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Retorna o status durante uma operação de criação. |
| [Definir banco de dados secundário como primário (failover planejado)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Define qual banco de dados secundário é primário ao fazer failover do banco de dados primário atual. **Não há suporte para essa opção para Instância Gerenciada.**|
| [Definir banco de dados secundário como primário (failover não planejado)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Define qual banco de dados secundário é primário ao fazer failover do banco de dados primário atual. Essa operação pode resultar em perda de dados. **Não há suporte para essa opção para Instância Gerenciada.**|
| [Obter link de replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Obtém um link de replicação específico para um determinado banco de dados SQL em uma parceria de replicação geográfica. Ele recupera as informações visíveis na exibição do catálogo sys. geo_replication_links. **Não há suporte para essa opção para Instância Gerenciada.**|
| [Links de replicação-listar por banco de dados](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Obtém todos os links de replicação para um determinado banco de dados SQL em uma parceria de replicação geográfica. Ele recupera as informações visíveis na exibição do catálogo sys. geo_replication_links. |
| [Excluir link de replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Exclui um link de replicação de banco de dados. Não pode ser feito durante o failover. |
|  | |

## <a name="next-steps"></a>Passos seguintes

- Para scripts de exemplo, consulte:
  - [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Configurar e efetuar a ativação pós-falha de uma base de dados de conjunto através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- O banco de dados SQL também dá suporte a grupos de failover automático. Para obter mais informações, consulte usando [grupos de failover automático](sql-database-auto-failover-group.md).
- Para uma visão geral e cenários de continuidade de negócios, consulte [visão geral da continuidade de negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do banco [de dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre como usar backups automatizados para recuperação, consulte [restaurar um banco de dados dos backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre os requisitos de autenticação para um novo servidor primário e banco de dados, consulte [segurança do banco de dados SQL após a recuperação de desastre](sql-database-geo-replication-security-config.md).
