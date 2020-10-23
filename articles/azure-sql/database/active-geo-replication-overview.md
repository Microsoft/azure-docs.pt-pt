---
title: Georreplicação ativa
description: Utilize a geo-replicação ativa para criar bases de dados secundárias legíveis de bases de dados individuais na Base de Dados Azure SQL nas mesmas regiões ou diferentes do centro de dados.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/27/2020
ms.openlocfilehash: 344d4e6b57082eb9ccfcd0642732d05216ad3978
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426322"
---
# <a name="creating-and-using-active-geo-replication---azure-sql-database"></a>Criação e utilização de geo-replicação ativa - Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A geo-replicação ativa é uma funcionalidade de Base de Dados Azure SQL que permite criar bases de dados secundárias legíveis de bases de dados individuais num servidor no mesmo ou diferente centro de dados (região).

> [!NOTE]
> A geo-replicação ativa não é suportada pela Azure SQL Managed Instance. Para a falha geográfica de casos de SQL Managed Instance, utilize [grupos de falha automática](auto-failover-group-overview.md).

A geo-replicação ativa é projetada como uma solução de continuidade de negócio que permite que a aplicação realize uma rápida recuperação de desastres de bases de dados individuais em caso de desastre regional ou de uma paragem em larga escala. Se a geo-replicação estiver ativada, a aplicação pode iniciar falhas numa base de dados secundária numa região de Azure diferente. Até quatro secundários são apoiados nas mesmas ou diferentes regiões, e os secundários também podem ser usados para consultas de acesso apenas de leitura. A falha deve ser iniciada manualmente pela aplicação ou pelo utilizador. Depois do failover, o novo primário tem um ponto final de ligação diferente.

> [!NOTE]
> A geo-replicação ativa replica alterações através do registo de transações de bases de dados de streaming. Não está relacionado com a [replicação transacional,](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication)que replica alterações executando comandos DML (INSERT, UPDATE, DELETE).

O diagrama seguinte ilustra uma configuração típica de uma aplicação de nuvem geo-redundante utilizando a geo-replicação ativa.

![geo-replicação ativa](./media/active-geo-replication-overview/geo-replication.png )

> [!IMPORTANT]
> A BASE de Dados SQL também suporta grupos de falha automática. Para obter mais informações, consulte a utilização [de grupos de falha automática](auto-failover-group-overview.md).

Se, por qualquer razão, a sua base de dados primária falhar ou simplesmente precisar de ser desligada, pode iniciar falhas em qualquer uma das suas bases de dados secundárias. Quando o failover é ativado numa das bases de dados secundárias, todos os outros secundários estão automaticamente ligados à nova primária.

Pode gerir a replicação e falha de uma base de dados individual ou um conjunto de bases de dados num servidor ou numa piscina elástica utilizando a geo-replicação ativa. Pode fazê-lo utilizando:

- O [portal Azure](active-geo-replication-configure-portal.md)
- [PowerShell: Base de dados única](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Piscina elástica](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: Base de dados única ou piscina elástica](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Base de dados única](https://docs.microsoft.com/rest/api/sql/replicationlinks)

A geo-replicação ativa aproveita a tecnologia de [grupo de disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) do motor de base de dados para replicar assíncroneamente transações comprometidas na base de dados primária para uma base de dados secundária utilizando o isolamento instantâneo. Os grupos de falha automática fornecem a semântica do grupo em cima da geo-replicação ativa, mas é utilizado o mesmo mecanismo de replicação assíncrona. Embora, em qualquer momento, a base de dados secundária possa estar ligeiramente atrás da base de dados primária, os dados secundários são garantidos para nunca terem transações parciais. A redundância inter-região permite que as aplicações recuperem rapidamente de uma perda permanente de um centro de dados inteiro ou partes de um datacenter causado por desastres naturais, erros humanos catastróficos ou atos maliciosos. Os dados específicos da RPO podem ser encontrados na [Visão Geral da Continuidade do Negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

> [!NOTE]
> Se houver uma falha de rede entre duas regiões, recaímos a cada 10 segundos para restabelecer as ligações.

> [!IMPORTANT]
> Para garantir que uma alteração crítica na base de dados primária é replicada para um secundário antes de falhar, pode forçar a sincronização para garantir a replicação de alterações críticas (por exemplo, atualizações de palavras-passe). A sincronização forçada tem impacto no desempenho porque bloqueia o fio de chamada até que todas as transações comprometidas sejam replicadas. Para mais detalhes, consulte [sp_wait_for_database_copy_sync.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) Para monitorizar o desfasamento da replicação entre a base de dados primária e o geo-secundário, consulte [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

A seguinte figura mostra um exemplo de geo-replicação ativa configurada com uma primária na região norte-americana central e secundária na região centro-sul dos EUA.

![relação geo-replicação](./media/active-geo-replication-overview/geo-replication-relationship.png)

Como as bases de dados secundárias são legíveis, podem ser usadas para descarregar cargas de trabalho apenas de leitura, como relatórios de trabalho. Se estiver a utilizar a geo-replicação ativa, é possível criar a base de dados secundária na mesma região com as primárias, mas não aumenta a resiliência da aplicação a falhas catastróficas. Se estiver a utilizar grupos de falha automática, a sua base de dados secundária é sempre criada numa região diferente.

Para além da recuperação de desastres, a geo-replicação ativa pode ser utilizada nos seguintes cenários:

- **Migração da base de dados**: Pode utilizar a geo-replicação ativa para migrar uma base de dados de um servidor para outro online com tempo mínimo de inatividade.
- **Atualizações da aplicação**: Pode criar uma cópia extra secundária como cópia traseira durante as atualizações da aplicação.

Para alcançar a continuidade real do negócio, adicionar redundância de base de dados entre datacenters é apenas uma parte da solução. A recuperação de uma aplicação (serviço) de ponta a ponta após uma falha catastrófica requer a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. Exemplos destes componentes incluem o software do cliente (por exemplo, um navegador com um JavaScript personalizado), extremidades frontais web, armazenamento e DNS. É fundamental que todos os componentes sejam resistentes às mesmas falhas e estejam disponíveis dentro do objetivo de tempo de recuperação (RTO) da sua aplicação. Por isso, é necessário identificar todos os serviços dependentes e compreender as garantias e capacidades que proporcionam. Em seguida, deve tomar as medidas adequadas para garantir que o seu serviço funciona durante a falha dos serviços de que depende. Para obter mais informações sobre a conceção de soluções para a recuperação de [desastres, consulte a conceção de soluções cloud para recuperação de desastres utilizando a geo-replicação ativa.](designing-cloud-solutions-for-disaster-recovery.md)

## <a name="active-geo-replication-terminology-and-capabilities"></a>Terminologia e capacidades de geo-replicação ativas

- **Replicação assíncronea automática**

  Só é possível criar uma base de dados secundária adicionando uma base de dados existente. O secundário pode ser criado em qualquer servidor. Uma vez criada, a base de dados secundária é preenchida com os dados copiados da base de dados primária. Este processo é conhecido como sementeira. Após a criação e semeada base de dados secundárias, as atualizações para a base de dados primária são reproduzidas automaticamente na base de dados secundária. A replicação assíncronea significa que as transações são cometidas na base de dados primária antes de serem replicadas na base de dados secundária.

- **Bases de dados secundárias legíveis**

  Uma aplicação pode aceder a uma base de dados secundária para operações apenas de leitura utilizando os mesmos ou diferentes princípios de segurança utilizados para aceder à base de dados primária. As bases de dados secundárias funcionam em modo de isolamento instantâneo para garantir que a replicação das atualizações da primária (repetição de registo) não seja retardada por consultas executadas no secundário.

> [!NOTE]
> A repetição do registo é adiada na base de dados secundária se houver atualizações de esquemas na Primária. Este último requer um bloqueio de esquema na base de dados secundária.

> [!IMPORTANT]
> Pode utilizar a geo-replicação para criar uma base de dados secundária na mesma região que a principal. Você pode usar este secundário para equilibrar uma carga de carga apenas de carga na mesma região. No entanto, uma base de dados secundária na mesma região não proporciona uma resiliência adicional de falhas, pelo que não constitui um objetivo adequado para a recuperação de catástrofes. Também não garante o isolamento da zona de disponibilidade. Use o nível de serviço critical ou Premium do Negócio com [configuração redundante de zona de zona](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) ou [configuração redundante](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) de nível de serviço para obter isolamento de zona de disponibilidade.
>

- **Falha planeada**

  O failover planeado muda as funções das bases de dados primárias e secundárias após a sincronização total. É uma operação online que não resulta em perda de dados. O tempo da operação depende do tamanho do registo de transações na primária que precisa de ser sincronizado. A falha prevista destina-se a seguintes cenários: a Realizar exercícios DR na produção quando a perda de dados não for aceitável; b Transferir a base de dados para uma região diferente; e (c) devolver a base de dados à região primária após a interrupção ter sido atenuada (recuo).

- **Ativação pós-falha não planeada**

  O fracasso não planeado ou forçado muda imediatamente o secundário para o papel primário sem qualquer sincronização com o primário. Quaisquer transações comprometidas com o primário mas não replicadas para o secundário serão perdidas. Esta operação foi concebida como um método de recuperação durante as interrupções quando a primária não está acessível, mas a disponibilidade da base de dados deve ser rapidamente restaurada. Quando o primário original estiver de novo on-line, ele automaticamente reconecta-se e tornar-se-á um novo secundário. Todas as transações não sincronizadas antes da falha serão preservadas no ficheiro de reserva, mas não serão sincronizadas com as novas primárias para evitar conflitos. Estas transações terão de ser fundidas manualmente com a versão mais recente da base de dados primária.

- **Múltiplos secundários legíveis**

  Podem ser criadas até 4 bases de dados secundárias para cada primária. Se houver apenas uma base de dados secundária, e falhar, a aplicação fica exposta a um risco mais elevado até que seja criada uma nova base de dados secundária. Se existirem várias bases de dados secundárias, a aplicação permanece protegida mesmo que uma das bases de dados secundárias falhe. Os secundários adicionais também podem ser usados para escalar as cargas de trabalho apenas de leitura

  > [!NOTE]
  > Se estiver a utilizar a geo-replicação ativa para construir uma aplicação distribuída globalmente e precisar de fornecer acesso apenas de leitura aos dados em mais de quatro regiões, pode criar secundário de um secundário (um processo conhecido como chaining). Desta forma, pode obter uma escala praticamente ilimitada de replicação de bases de dados. Além disso, o acorrentamento reduz a sobrecarga de replicação da base de dados primária. A compensação é o aumento do atraso de replicação nas bases de dados secundárias mais secundárias.

- **Geo-replicação de bases de dados em piscina elástica**

  Cada base de dados secundária pode participar separadamente numa piscina elástica ou não estar em qualquer piscina elástica. A escolha do pool para cada base de dados secundária é separada e não depende da configuração de qualquer outra base de dados secundária (seja primária ou secundária). Cada piscina elástica está contida numa única região, pelo que várias bases de dados secundárias na mesma topologia nunca podem partilhar uma piscina elástica.

- **Falha controlada pelo utilizador e recuo**

  Uma base de dados secundária pode ser explicitamente alterada para a função principal a qualquer momento pela aplicação ou pelo utilizador. Durante uma paragem real, deve ser utilizada a opção "não planeada", que promove imediatamente um secundário para ser o principal. Quando o primário falhado recupera e está novamente disponível, o sistema marca automaticamente a primária recuperada como secundária e coloca-a atualizada com as novas primárias. Devido à natureza assíncronia da replicação, uma pequena quantidade de dados pode ser perdida durante as falhas não planeadas se uma falha primária falhar antes de replicar as alterações mais recentes para o secundário. Quando um primário com múltiplos secundários falha, o sistema reconfigura automaticamente as relações de replicação e liga os restantes secundários ao primário recém-promovido sem exigir qualquer intervenção do utilizador. Após a interrupção que causou a falência ser atenuada, pode ser desejável devolver o pedido à região primária. Para isso, o comando de failover deve ser invocado com a opção "planeada".

## <a name="preparing-secondary-database-for-failover"></a>Preparar base de dados secundária para falhas

Para garantir que a sua aplicação pode aceder imediatamente ao novo primário após o failover, certifique-se de que os requisitos de autenticação do seu servidor secundário e base de dados estão devidamente configurados. Para mais informações, consulte [a segurança da Base de Dados SQL após a recuperação de desastres.](active-geo-replication-security-configure.md) Para garantir o cumprimento após o incumprimento, certifique-se de que a política de retenção de backup na base de dados secundária corresponde à das primárias. Estas definições não fazem parte da base de dados e não são replicadas. Por predefinição, o secundário será configurado com um período de retenção pitr predefinido de sete dias. Para mais informações, consulte [as cópias de segurança automatizadas sql Database](automated-backups-overview.md).

> [!IMPORTANT]
> Se a sua base de dados for membro de um grupo de failover, não pode iniciar a sua falha utilizando o comando de falha de geo-replicação. Utilize o comando de falha para o grupo. Se precisar de falhar com uma base de dados individual, deve removê-la primeiro do grupo de failover. Consulte  [os grupos de failover](auto-failover-group-overview.md) para mais detalhes.

## <a name="configuring-secondary-database"></a>Configuração da base de dados secundária

As bases de dados primárias e secundárias são necessárias para ter o mesmo nível de serviço. Recomenda-se também vivamente que a base de dados secundária seja criada com o mesmo tamanho de armazenamento de backup e tamanho de cálculo (DTUs ou vCores) como o principal. Se a base de dados primária estiver a experimentar uma carga de trabalho de escrita pesada, um secundário com menor tamanho de cálculo pode não ser capaz de acompanhar. Isso fará com que o redo lag no secundário, e potencial indisponibilidade do secundário. Para mitigar estes riscos, a geo-replicação ativa acelerará a taxa de registo de transações primária, se necessário, para permitir que os seus secundários se apanhem.

Outra consequência de uma configuração secundária desequilibrada é que, após o failover, o desempenho da aplicação pode sofrer devido à capacidade de computação insuficiente do novo primário. Nesse caso, será necessário aumentar o objetivo do serviço de bases de dados ao nível necessário, o que pode demorar tempo significativo e recursos de computação, e exigirá uma [elevada redução](high-availability-sla.md) de disponibilidade no final do processo de escala.

Se decidir criar o secundário com menor tamanho de cálculo, o gráfico de percentagem de IO de registo no portal Azure fornece uma boa maneira de estimar o tamanho mínimo do cálculo do secundário que é necessário para sustentar a carga de replicação. Por exemplo, se a sua base de dados primária for P6 (1000 DTU) e a sua percentagem de registos for de 50%, o secundário precisa de ser pelo menos P4 (500 DTU). Para obter dados históricos de IO de registo, utilize a vista [sys.resource_stats.](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) Para obter dados recentes de gravação com maior granularidade que reflita melhor picos de curto prazo na taxa de registo, use [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vista.

A taxa de registo de transações que se reduz no principal devido ao menor tamanho do cálculo num secundário é reportada utilizando o tipo de espera HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO, visível nas [vistas de sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) base de dados.

Por predefinição, a redundância de armazenamento de backup do secundário é a mesma da base de dados primária. Pode optar por configurar o secundário com uma redundância de armazenamento de backup diferente. As cópias de segurança são sempre tomadas na base de dados primária. Se o secundário estiver configurado com uma redundância de armazenamento de backup diferente, após o failover quando o secundário for promovido ao primário, as cópias de segurança serão faturadas de acordo com a redundância de armazenamento selecionada na nova primária (secundária anterior). 

> [!NOTE]
> A taxa de registo de transações no primário pode ser acelerada por razões não relacionadas com o tamanho do cálculo mais baixo num secundário. Este tipo de estrangulamento pode ocorrer mesmo que o secundário tenha o mesmo tamanho de computação ou maior do que o primário. Para mais detalhes, incluindo tipos de espera para diferentes tipos de taxa de registo, consulte a [governação da taxa de registo de transações](resource-limits-logical-server.md#transaction-log-rate-governance).

> [!NOTE]
> Azure SQL Database Configurable Backup Storage Despedimento está atualmente disponível apenas na região do Sudeste Asiático Azure. Quando a base de dados de origem é criada com redundância de armazenamento de backup de segurança redundante local ou redundante, a criação de uma base de dados secundária numa região de Azure diferente não é suportada. 

Para obter mais informações sobre os tamanhos de cálculo da Base de Dados [SQL, consulte quais são os Níveis de Serviço de Base de Dados SQL](purchasing-models.md).

## <a name="cross-subscription-geo-replication"></a>Geo-replicação de subscrição cruzada

Para configurar a geo-replicação ativa entre duas bases de dados pertencentes a diferentes subscrições (seja sob o mesmo inquilino ou não), deve seguir o procedimento especial descrito nesta secção.  O procedimento baseia-se em comandos SQL e requer:

- Criar um login privilegiado em ambos os servidores
- Adicionar o endereço IP à lista de admissões do cliente que executa a alteração em ambos os servidores (como o endereço IP do anfitrião que executa o SQL Server Management Studio).

O cliente que executa as alterações precisa de acesso à rede ao servidor primário. Embora o mesmo endereço IP do cliente deva ser adicionado à lista de autorizações no servidor secundário, a conectividade da rede ao servidor secundário não é estritamente necessária.

### <a name="on-the-master-of-the-primary-server"></a>No mestre do servidor primário

1. Adicione o endereço IP à lista de admissões do cliente que executa as alterações (para mais informações ver, [Configure firewall](firewall-configure.md)).
1. Criar um login dedicado à configuração da geo-replicação ativa (e ajustar as credenciais conforme necessário):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Criar um utilizador correspondente e atribuí-lo à função dbmanager:

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Tome nota do SID do novo login utilizando esta consulta:

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>Na base de dados de origem do servidor primário

1. Criar um utilizador para o mesmo login:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Adicione o utilizador à função db_owner:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>No mestre do servidor secundário

1. Adicione o endereço IP do cliente à lista permitida de acordo com as regras de firewall para o servidor secundário. Valide que o mesmo endereço IP do cliente que foi adicionado no servidor primário também foi adicionado ao secundário. Este é um passo necessário a ser feito antes de executar o comando ALTER DATABASE ADD SECONDARY para iniciar a geo-replicação.

1. Crie o mesmo login que no servidor primário, utilizando a mesma palavra-passe do nome de utilizador, e SID:

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Criar um utilizador correspondente e atribuí-lo à função dbmanager:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>No mestre do servidor primário

1. Faça login no mestre do servidor primário usando o novo login.
1. Criar uma réplica secundária da base de dados de origem no servidor secundário (ajustar o nome da base de dados e o nome do servidor, conforme necessário):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Após a configuração inicial, os utilizadores, logins e regras de firewall criados podem ser removidos.

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Manter credenciais e regras de firewall em sincronização

Recomendamos a [utilização de regras de firewall IP de nível de base de dados](firewall-configure.md) para bases de dados geo-replicadas para que estas regras possam ser replicadas com a base de dados para garantir que todas as bases de dados secundárias tenham as mesmas regras de firewall IP que as principais. Esta abordagem elimina a necessidade de os clientes configurarem manualmente e manterem as regras de firewall nos servidores que hospedam as bases de dados primárias e secundárias. Da mesma forma, a utilização de [utilizadores de bases de dados contidos](logins-create-manage.md) para acesso a dados garante que as bases de dados primárias e secundárias têm sempre as mesmas credenciais de utilizador, pelo que, durante uma falha, não há interrupções devido a incompatibilidades com logins e senhas. Com a adição do [Azure Ative Directory,](../../active-directory/fundamentals/active-directory-whatis.md)os clientes podem gerir o acesso dos utilizadores a bases de dados primárias e secundárias e eliminar a necessidade de gerir as credenciais em bases de dados completamente.

## <a name="upgrading-or-downgrading-primary-database"></a>Atualizar ou degradar base de dados primária

Pode atualizar ou desclassificar uma base de dados primária para um tamanho de computação diferente (dentro do mesmo nível de serviço, não entre o Final Geral e a Critical De Negócios) sem desligar quaisquer bases de dados secundárias. Ao atualizar, recomendamos que atualize primeiro a base de dados secundária e, em seguida, atualize a base de dados primária. Se mudar para uma versão anterior, inverta a ordem: mude primeiro a base de dados primária para uma versão anterior e, em seguida, mude a secundária para uma versão anterior. Quando atualizar a base de dados ou mudar para um escalão de serviço superior ou inferior, será aplicada esta recomendação.

> [!NOTE]
> Se tiver criado uma base de dados secundária como parte da configuração do grupo de ativação pós-falha, não será recomendável mudar a base de dados secundária para uma versão anterior. Isto é para garantir que o seu nível de dados tem capacidade suficiente para processar a sua carga de trabalho regular após a ativação da falha.

> [!IMPORTANT]
> A base de dados primária num grupo de ativação pós-falha não pode ser dimensionada para um escalão superior, a menos que a base de dados secundária seja dimensionada primeiro para o escalão superior. Se tentar escalar a base de dados primária antes da escala da base de dados secundária, poderá receber o seguinte erro:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Prevenção da perda de dados críticos

Devido à elevada latência de amplas redes de área, a cópia contínua utiliza um mecanismo de replicação assíncronos. A replicação assíncronia torna a perda de dados inevitável se ocorrer uma falha. No entanto, algumas aplicações podem não requerer perda de dados. Para proteger estas atualizações críticas, um desenvolvedor de aplicações pode ligar para o procedimento do sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a efeção da transação. Ligar **sp_wait_for_database_copy_sync** bloqueia o fio de chamada até que a última transação comprometida tenha sido transmitida para a base de dados secundária. No entanto, não espera que as transações transmitidas sejam repetidas e cometidas no secundário. **sp_wait_for_database_copy_sync** é traçado para uma ligação de cópia contínua específica. Qualquer utilizador com os direitos de ligação à base de dados primária pode ligar para este procedimento.

> [!NOTE]
> **sp_wait_for_database_copy_sync** evita a perda de dados após o failover, mas não garante a sincronização total para o acesso à leitura. O atraso causado por uma chamada de **procedimento sp_wait_for_database_copy_sync** pode ser significativo e depende do tamanho do registo de transações no momento da chamada.

## <a name="monitoring-geo-replication-lag"></a>Atraso de geo-replicação de monitorização

Para monitorizar o atraso no que diz respeito à RPO, utilize *replication_lag_sec* coluna de [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) na base de dados primária. Mostra um atraso em segundos entre as transações cometidas nas primárias e persistiu no secundário. Por exemplo, se o valor do lag for de 1 segundo, significa que se a primária for impactada por uma paragem neste momento e o failover for iniciado, 1 segundo das transições mais recentes não serão salvas.

Para medir o atraso no que diz respeito às alterações na base de dados primária que foram aplicadas no secundário, ou seja, disponível para ler a partir do secundário, compare *last_commit* tempo na base de dados secundária com o mesmo valor na base de dados primária.

> [!NOTE]
> Por *vezes, replication_lag_sec* na base de dados primária tem um valor NU, o que significa que o primário não sabe atualmente até onde está o secundário.   Isto normalmente acontece após o reinício do processo e deve ser uma condição transitória. Considere alertar o pedido se o *replication_lag_sec* devolver NU POR um longo período de tempo. Indica que a base de dados secundária não pode comunicar com a base de dados primária devido a uma falha permanente de conectividade. Existem também condições que podem fazer com que a diferença entre *last_commit* tempo no secundário e na base de dados primária se torne grande. Por exemplo, se um compromisso for feito na primária após um longo período sem alterações, a diferença saltará para um grande valor antes de voltar rapidamente a 0. Considere-o uma condição de erro quando a diferença entre estes dois valores permanece grande por muito tempo.

## <a name="programmatically-managing-active-geo-replication"></a>Gestão programática de geo-replicação ativa

Como discutido anteriormente, a geo-replicação ativa também pode ser gerida programáticamente usando a Azure PowerShell e a API REST. As tabelas seguintes descrevem o conjunto de comandos disponíveis. A geo-replicação ativa inclui um conjunto de APIs do Gestor de Recursos Azure para gestão, incluindo os cmdlets [AZURE SQL Database REST](https://docs.microsoft.com/rest/api/sql/) E [Azure PowerShell](https://docs.microsoft.com/powershell/azure/). Estas APIs requerem a utilização de grupos de recursos e apoiam a segurança baseada em funções (RBAC). Para obter mais informações sobre como implementar funções de acesso, consulte [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Gerir o failover de bases de dados individuais e agmbadas

> [!IMPORTANT]
> Estes comandos Transact-SQL aplicam-se apenas à geo-replicação ativa e não se aplicam a grupos de failover. Como tal, também não se aplicam a casos de SQL Managed Instance, uma vez que apenas suportam grupos de failover.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Use o argumento ADD SECONDARY ON SERVER para criar uma base de dados secundária para uma base de dados existente e inicie a replicação de dados |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Utilize FAILOVER ou FORCE_FAILOVER_ALLOW_DATA_LOSS para mudar uma base de dados secundária para ser primária para iniciar a falha |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Utilize remover SECUNDÁRIO NO SERVIDOR para terminar uma replicação de dados entre uma Base de Dados SQL e a base de dados secundária especificada. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Retorna informações sobre todos os links de replicação existentes para cada base de dados num servidor. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtém o último tempo de replicação, o último atraso de replicação, e outras informações sobre a ligação de replicação para uma determinada base de dados. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Mostra o estado de todas as operações de base de dados, incluindo o estado das ligações de replicação. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |faz com que a aplicação aguarde até que todas as transações comprometidas sejam replicadas e reconhecidas pela base de dados secundária ativa. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Gerir o failover de bases de dados individuais e agmbadas

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

| Cmdlet | Descrição |
| --- | --- |
| [Base de Dados Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
| [Novo AzSqlDatabaseSesecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Cria uma base de dados secundária para uma base de dados existente e começa a replicação de dados. |
| [Set-AzSqlDatabaseSesecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Muda uma base de dados secundária para primária, para iniciar a ativação pós-falha. |
| [Remover-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Termina a replicação de dados entre uma Base de Dados SQL e a base de dados secundária especificada. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Obtém as ligações de geo-replicação entre uma Base de Dados Azure SQL e um grupo de recursos ou servidor lógico SQL. |
|  | |

> [!IMPORTANT]
> Para scripts de amostra, consulte [Configure e falhe numa única base de dados utilizando a geo-replicação ativa](scripts/setup-geodr-and-failover-database-powershell.md) e [a configuração e falhando uma base de dados em conjunto utilizando a geo-replicação ativa](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: Gerir o failover de bases de dados individuais e agmbadas

| API | Descrição |
| --- | --- |
| [Criar ou atualizar base de dados (createMode=Restaurar)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Cria, atualiza ou restaura uma base de dados primária ou secundária. |
| [Obtenha criar ou atualizar o estado da base de dados](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Devolve o estado durante uma operação de criação. |
| [Definir base de dados secundária como principal (Failover planeado)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Define qual base de dados secundária é primária falhando na base de dados primária atual. **Esta opção não é suportada para a SQL Managed Instance.**|
| [Definir base de dados secundária como principal (Falha não planeada)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Define qual base de dados secundária é primária falhando na base de dados primária atual. Esta operação pode resultar em perda de dados. **Esta opção não é suportada para a SQL Managed Instance.**|
| [Obtenha ligação de replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Obtém uma ligação de replicação específica para uma determinada base de dados numa parceria de geo-replicação. Recupera a informação visível na vista do catálogo sys.geo_replication_links. **Esta opção não é suportada para a SQL Managed Instance.**|
| [Links de replicação - Lista por base de dados](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Obtém todos os links de replicação para uma dada base de dados numa parceria de geo-replicação. Recupera a informação visível na vista do catálogo sys.geo_replication_links. |
| [Eliminar ligação de replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Elimina uma ligação de replicação de base de dados. Não é possível fazê-lo durante o fracasso. |
|  | |

## <a name="next-steps"></a>Passos seguintes

- Para os scripts de amostra, consulte:
  - [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Configurar e efetuar a ativação pós-falha de uma base de dados de conjunto através de georreplicação ativa](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- A BASE de Dados SQL também suporta grupos de falha automática. Para obter mais informações, consulte a utilização [de grupos de falha automática](auto-failover-group-overview.md).
- Para uma visão geral de continuidade do negócio e cenários, consulte [a visão geral da continuidade do Negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Para obter mais sobre cópias de segurança automatizadas da Base de Dados Azure SQL, consulte [backups automatizados da BASE de Dados SQL](automated-backups-overview.md).
- Para obter a utilização de cópias de segurança automatizadas para recuperação, consulte [Restaurar uma base de dados a partir das cópias de segurança iniciadas pelo serviço.](recovery-using-backups.md)
- Para obter mais sobre os requisitos de autenticação de um novo servidor primário e base de dados, consulte a [segurança da Base de Dados SQL após a recuperação de desastres](active-geo-replication-security-configure.md).
