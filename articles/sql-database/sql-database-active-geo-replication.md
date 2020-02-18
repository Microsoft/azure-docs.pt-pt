---
title: Georreplicação ativa
description: Utilize geo-replicação ativa para criar bases de dados secundárias legíveis de bases de dados individuais no mesmo ou diferente centro de dados (região).
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/17/2020
ms.openlocfilehash: fe006cebe9aab30a6aaa0bdf2bf3362a494f64d7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426280"
---
# <a name="creating-and-using-active-geo-replication"></a>Criação e utilização de geo-replicação ativa

A geo-replicação ativa é uma funcionalidade de Base de Dados Azure SQL que permite criar bases de dados secundárias legíveis de bases de dados individuais num servidor de base de dados SQL na mesma ou diferente data center (região).

> [!NOTE]
> A geo-replicação ativa não é suportada por instância gerida. Para a falha geográfica de instâncias geridas, utilize [grupos de falha automática](sql-database-auto-failover-group.md).

A geo-replicação ativa é projetada como uma solução de continuidade do negócio que permite que a aplicação realize uma rápida recuperação de desastres de bases de dados individuais em caso de desastre regional ou paragem em larga escala. Se a geo-replicação estiver ativada, a aplicação pode iniciar uma falha numa base de dados secundária numa região de Azure diferente. Até quatro secundários são apoiados nas mesmas regiões ou diferentes, e os secundários também podem ser utilizados para consultas de acesso apenas à leitura. A falha deve ser iniciada manualmente pela aplicação ou pelo utilizador. Após a falha, a nova primária tem um ponto final de ligação diferente. O diagrama seguinte ilustra uma configuração típica de uma aplicação de nuvem geo-redundante usando a geo-replicação ativa.

![geo-replicação ativa](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> A Base de Dados SQL também suporta grupos de falha automática. Para mais informações, consulte a utilização [de grupos de falha automática](sql-database-auto-failover-group.md). Além disso, a geo-replicação ativa não é suportada para bases de dados criadas dentro de uma Instância Gerida. Considere usar [grupos de failover](sql-database-auto-failover-group.md) com Instâncias Geridas.

Se, por alguma razão, a sua base de dados primária falhar, ou simplesmente necessitar de ser desligada, pode iniciar a falha em qualquer uma das suas bases de dados secundárias. Quando a falha é ativada numa das bases de dados secundárias, todos os outros secundários estão automaticamente ligados às novas primárias.

Pode gerir a replicação e a falha de uma base de dados individual ou de um conjunto de bases de dados num servidor ou numa piscina elástica utilizando geo-replicação ativa. Pode fazê-lo usando:

- O [portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell: Base de dados única](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Piscina elástica](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: Base de dados única ou piscina elástica](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Base de dados única](https://docs.microsoft.com/rest/api/sql/replicationlinks)


A geo-replicação ativa aproveita a tecnologia [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) do SQL Server para replicar assincronicamente transações comprometidas na base de dados primária para uma base de dados secundária utilizando o isolamento instantâneo. Os grupos de falha automática fornecem a semântica do grupo em cima da geo-replicação ativa, mas o mesmo mecanismo de replicação assíncrono é usado. Embora em qualquer ponto, a base de dados secundária possa estar ligeiramente por detrás da base de dados primária, os dados secundários são garantidos para nunca terem transações parciais. O despedimento transversal permite que as aplicações recuperem rapidamente de uma perda permanente de todo um datacenter ou partes de um datacenter causado por desastres naturais, erros humanos catastróficos ou atos maliciosos. Os dados de RPO específicos podem ser encontrados na [visão geral da continuidade do negócio](sql-database-business-continuity.md).

> [!NOTE]
> Se houver uma falha de rede entre duas regiões, retentamos a cada 10 segundos para restabelecer as ligações.
> [!IMPORTANT]
> Para garantir que uma mudança crítica na base de dados primária é replicada para um secundário antes de falhar, pode forçar a sincronização para garantir a replicação de alterações críticas (por exemplo, atualizações de passwords). A sincronização forçada impacta o desempenho porque bloqueia o fio de chamada até que todas as transações comprometidas sejam replicadas. Para mais detalhes, consulte [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Para monitorizar o desfasamento de replicação entre a base de dados primária e o geo-secundário, consulte [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

O número seguinte mostra um exemplo de geo-replicação ativa configurada com uma primária na região centro-norte dos EUA e secundária na região centro-sul dos EUA.

![relação geo-replicação](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Uma vez que as bases de dados secundárias são legíveis, podem ser utilizadas para descarregar cargas de trabalho apenas de leitura, como os trabalhos de reporte. Se estiver a utilizar a geo-replicação ativa, é possível criar a base de dados secundária na mesma região com as primárias, mas não aumenta a resiliência da aplicação a falhas catastróficas. Se estiver a utilizar grupos de falha automática, a sua base de dados secundária é sempre criada numa região diferente.

Além da geo-replicação ativa de recuperação de desastres pode ser usada nos seguintes cenários:

- **Migração de bases**de dados: Pode utilizar a geo-replicação ativa para migrar uma base de dados de um servidor para outro online com o tempo mínimo de inatividade.
- **Atualizações de aplicações:** Pode criar uma cópia secundária extra como cópia de falha durante as atualizações da aplicação.

Para alcançar a continuidade real do negócio, adicionar redundância na base de dados entre datacenters é apenas uma parte da solução. Recuperar uma aplicação (serviço) de ponta a ponta após uma falha catastrófica requer a recuperação de todos os componentes que constituam o serviço e quaisquer serviços dependentes. Exemplos destes componentes incluem o software cliente (por exemplo, um navegador com um JavaScript personalizado), extremidades frontais web, armazenamento e DNS. É fundamental que todos os componentes sejam resilientes às mesmas falhas e estejam disponíveis dentro do objetivo de tempo de recuperação (RTO) da sua aplicação. Por isso, é necessário identificar todos os serviços dependentes e compreender as garantias e capacidades que eles fornecem. Em seguida, deve tomar as medidas adequadas para garantir que o seu serviço funciona durante a falha dos serviços de que depende. Para obter mais informações sobre a conceção de soluções para a recuperação de desastres, consulte [O Desenho de Soluções cloud para recuperação de desastres Utilizando a geo-replicação ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Terminologia e capacidades de geo-replicação ativa

- **Replicação assíncrona automática**

  Pode criar apenas uma base de dados secundária adicionando uma base de dados existente. O secundário pode ser criado em qualquer servidor de base de dados Azure SQL. Uma vez criada, a base de dados secundária é povoada com os dados copiados da base de dados primária. Este processo é conhecido como semente. Após a criação e sedenta de base de dados secundárias, as atualizações da base de dados primária são assíncronamente replicadas para a base de dados secundária automaticamente. A replicação assíncrona significa que as transações são cometidas na base de dados primária antes de serem replicadas na base de dados secundária.

- **Bases de dados secundárias legíveis**

  Uma aplicação pode aceder a uma base de dados secundária para operações apenas de leitura utilizando os mesmos ou diferentes diretores de segurança utilizados para aceder à base de dados primária. As bases de dados secundárias funcionam no modo de isolamento instantâneo para garantir que a replicação das atualizações do primário (repetição de registo) não seja adiada por consultas executadas no secundário.

> [!NOTE]
> A repetição do registo é adiada na base de dados secundária se houver atualizações de esquemas na Primary. Este último requer um bloqueio de esquemas na base de dados secundária.
> [!IMPORTANT]
> Pode usar a geo-replicação para criar uma base de dados secundária na mesma região que a primária. Você pode usar este secundário para carregar uma carga de trabalho apenas de leitura na mesma região. No entanto, uma base de dados secundária na mesma região não fornece resiliência adicional de falhas, pelo que não é um objetivo adequado para a recuperação de catástrofes. Também não garantirá o isolamento da zona de disponibilidade. Utilize o nível de serviço crítico ou Premium do Negócio com [configuração redundante](sql-database-high-availability.md#zone-redundant-configuration) de zona para alcançar o isolamento da zona de disponibilidade.   
>

- **Falha planeada**

  A falha planeada muda as funções das bases de dados primárias e secundárias após a completa sincronização. É uma operação online que não resulta em perda de dados. O tempo da operação depende da dimensão do registo de transações na primária que precisa de ser sincronizada. A failover planeada destina-se a seguir cenários: (a) realizar exercícios DE DR em produção quando a perda de dados não for aceitável; b Transferir a base de dados para outra região; e (c) devolver a base de dados à região primária após a interrupção ter sido atenuada (recuo).

- **Falha não planeada**

  Falha não planeada ou forçada muda imediatamente o secundário para o papel primário sem qualquer sincronização com o primário. Quaisquer transações cometidas com o primário, mas não replicadas para o secundário, perder-se-ão. Esta operação foi concebida como um método de recuperação durante as interrupções quando o primário não está acessível, mas a disponibilidade da base de dados deve ser rapidamente restaurada. Quando a primária original estiver novamente on-line, reconectar-se-á automaticamente e tornar-se-á um novo secundário. Todas as transações não sincronizadas antes da falha serão preservadas no ficheiro de cópia de segurança, mas não serão sincronizadas com as novas primárias para evitar conflitos. Estas transações terão de ser misturadas manualmente com a versão mais recente da base de dados primária.
 
- **Múltiplos secundários legíveis**

  Podem ser criadas até 4 bases de dados secundárias para cada primária. Se houver apenas uma base de dados secundária, e falhar, a aplicação é exposta a um risco mais elevado até que seja criada uma nova base de dados secundária. Se existirem várias bases de dados secundárias, a aplicação permanece protegida mesmo que uma das bases de dados secundárias falhe. Os secundários adicionais também podem ser usados para escalar as cargas de trabalho apenas para leitura

  > [!NOTE]
  > Se estiver a usar a geo-replicação ativa para construir uma aplicação distribuída globalmente e precisar de fornecer acesso apenas a leitura sondar dados em mais de quatro regiões, pode criar um secundário de um secundário (um processo conhecido como cadeia). Desta forma, pode alcançar uma escala praticamente ilimitada de replicação de bases de dados. Além disso, a corrente reduz a sobrecarga da replicação a partir da base de dados primária. A compensação é o aumento do desfasamento de replicação nas bases de dados mais secundárias.

- **Geo-replicação de bases de dados em uma piscina elástica**

  Cada base de dados secundária pode participar separadamente numa piscina elástica ou não estar em qualquer piscina elástica. A escolha do pool para cada base de dados secundária é separada e não depende da configuração de qualquer outra base de dados secundária (primária ou secundária). Cada piscina elástica está contida numa única região, pelo que várias bases de dados secundárias na mesma topologia nunca podem partilhar uma piscina elástica.


- **Falha controlada pelo utilizador e failback**

  Uma base de dados secundária pode ser explicitamente alterada para o papel principal a qualquer momento pela aplicação ou pelo utilizador. Durante uma paragem real, deve ser utilizada a opção "não planeada", que promove imediatamente um secundário para ser o principal. Quando a primária falhada recupera e está novamente disponível, o sistema marca automaticamente a primária recuperada como secundária e o torna atualizado com as novas primárias. Devido à natureza assíncrona da replicação, uma pequena quantidade de dados pode ser perdida durante falhas não planeadas se uma primária falhar antes de replicar as alterações mais recentes para o secundário. Quando uma primária com vários secundários falha, o sistema reconfigura automaticamente as relações de replicação e liga os restantes secundários às primárias recém-promovidas sem necessitar de qualquer intervenção do utilizador. Após a interrupção que causou a falha, pode ser desejável devolver a aplicação à região primária. Para isso, o comando de failover deve ser invocado com a opção "planeada".

## <a name="preparing-secondary-database-for-failover"></a>Preparação de base de dados secundária para falhas

Para garantir que a sua aplicação pode aceder imediatamente ao novo primário após a falha, certifique-se de que os requisitos de autenticação para o seu servidor secundário e base de dados estão corretamente configurados. Para mais detalhes, consulte a segurança da Base de [Dados SQL após a recuperação](sql-database-geo-replication-security-config.md)de desastres . Para garantir o cumprimento após a falha, certifique-se de que a política de retenção de cópias de segurança na base de dados secundária corresponde à das primárias. Estas definições não fazem parte da base de dados e não são replicadas. Por predefinição, o secundário será configurado com um período de retenção PITR padrão de sete dias. Para mais detalhes, consulte cópias de segurança automatizadas da Base de [Dados SQL](sql-database-automated-backups.md).

> [!IMPORTANT]
> Se a sua base de dados for membro de um grupo failover, não pode iniciar a sua falha utilizando o comando de faiover de geo-replicação. Considere usar o comando de failover para o grupo. Se necessitar de falhar numa base de dados individual, deve removê-la primeiro do grupo failover. Consulte os [grupos failover](sql-database-auto-failover-group.md) para obter detalhes. 


## <a name="configuring-secondary-database"></a>Configuração da base de dados secundária

Tanto as bases de dados primárias como secundárias são obrigadas a ter o mesmo nível de serviço. Recomenda-se também fortemente que a base de dados secundária seja criada com o mesmo tamanho de computação (DTUs ou vCores) que o primário. Se a base de dados primária estiver a passar por uma carga de trabalho de escrita pesada, um secundário com menor tamanho de computação pode não ser capaz de acompanhá-la. Causará o atraso no atraso secundário e potencial indisponibilidade. Uma base de dados secundária que está atrasada atrás das primárias também arrisca uma grande perda de dados caso seja necessária uma falha forçada. Para mitigar estes riscos, uma geo-replicação ativa eficaz irá acelerar a taxa de registo primária para permitir que os seus secundários o apanhem. A outra consequência de uma configuração secundária desequilibrada é que após a falha do desempenho da aplicação sofrerá devido à capacidade calculista insuficiente das novas primárias. Será necessário atualizar para um cálculo mais elevado para o nível necessário, o que não será possível até que a paralisação seja atenuada. 


> [!IMPORTANT]
> O RPO publicado = 5 seg não pode ser garantido a menos que a base de dados secundária esteja configurada com o mesmo tamanho de computação que o primário. 


Se decidir criar o secundário com menor tamanho de computação, o gráfico percentual de log IO no portal Azure fornece uma boa maneira de estimar o tamanho mínimo da computação do secundário que é necessário para sustentar a carga de replicação. Por exemplo, se a sua base de dados primária for P6 (1000 DTU) e o seu log IO por cento for de 50% o secundário precisa de ser pelo menos P4 (500 DTU). Também pode recuperar os dados do log IO utilizando vistas de base de dados [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) ou [sys.dm_db_resource_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)  A aceleração é relatada como um estado de espera HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO nas vistas de base de dados [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_os_wait_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 

Para obter mais informações sobre os tamanhos de computação da Base de Dados SQL, consulte o que são os níveis de serviço da Base de [Dados SQL](sql-database-purchase-models.md).

## <a name="cross-subscription-geo-replication"></a>Geo-replicação de assinaturacruzada

Para configurar a geo-replicação ativa entre duas bases de dados pertencentes a diferentes subscrições (sob o mesmo inquilino ou não), deve seguir o procedimento especial descrito nesta secção.  O procedimento baseia-se em comandos SQL e requer: 

- Criar um login privilegiado em ambos os servidores
- Adicionar o endereço IP à lista de licenças do cliente que executa a alteração em ambos os servidores (como o endereço IP do estúdio de gestão de servidores SQL). 

O cliente que executa as alterações precisa de acesso à rede ao servidor primário. Embora o mesmo endereço IP do cliente tenha de ser adicionado à lista de licenças no servidor secundário, a conectividade da rede com o servidor secundário não é estritamente necessária. 

### <a name="on-the-master-of-the-primary-server"></a>No mestre do servidor primário

1. Adicione o endereço IP à lista de espera do cliente que executa as alterações (para mais informações ver, [Configurar](sql-database-firewall-configure.md)firewall ). 
1. Criar um login dedicado à configuração da geo-replicação ativa (e ajustar as credenciais conforme necessário):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Criar um utilizador correspondente e atribuí-lo ao papel de dbmanager: 

   ```sql
   create user geodrsetup for login geodrsetup
   alter role geodrsetup dbmanager add member geodrsetup
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

1. Adicione o utilizador ao papel db_owner:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>No mestre do servidor secundário 

1. Adicione o endereço IP à lista de admissões do cliente que executa as alterações. Deve ter o mesmo endereço IP exato do servidor primário. 
1. Crie o mesmo login que no servidor principal, utilizando a mesma palavra-passe de nome de utilizador, e SID: 

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Criar um utilizador correspondente e atribuí-lo ao papel de dbmanager:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>No mestre do servidor primário

1. Inicie sessão com o mestre do servidor principal utilizando o novo login. 
1. Criar uma réplica secundária da base de dados de origem no servidor secundário (ajustar o nome da base de dados e o nome do servidor conforme necessário):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Após a configuração inicial, os utilizadores, logins e regras de firewall criadas podem ser removidos. 


## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Manter as credenciais e as regras de firewall em sincronização

Recomendamos a utilização de [regras de firewall IP de nível de base de dados](sql-database-firewall-configure.md) para bases de dados geo-replicadas para que estas regras possam ser replicadas com a base de dados para garantir que todas as bases de dados secundárias tenham as mesmas regras de firewall IP que as primárias. Esta abordagem elimina a necessidade de os clientes configurarem manualmente e manterem as regras de firewall nos servidores que acolhem as bases de dados primárias e secundárias. Da mesma forma, a utilização de utilizadores de [bases de dados contidos](sql-database-manage-logins.md) para acesso a dados garante que as bases de dados primárias e secundárias têm sempre as mesmas credenciais de utilizador, pelo que, durante uma falha, não existem interrupções devido a incompatibilidades com logins e senhas. Com a adição do [Azure Ative Directory,](../active-directory/fundamentals/active-directory-whatis.md)os clientes podem gerir o acesso dos utilizadores a bases de dados primárias e secundárias e eliminar completamente a necessidade de gerir credenciais em bases de dados.

## <a name="upgrading-or-downgrading-primary-database"></a>Atualizar ou reduzir base de dados primária

Pode atualizar ou desvalorizar uma base de dados primária para um tamanho de computação diferente (dentro do mesmo nível de serviço, não entre o Propósito Geral e o Business Critical) sem desligar quaisquer bases de dados secundárias. Ao atualizar, recomendamos que atualize primeiro a base de dados secundária e, em seguida, atualize a principal. Ao degradar, inverta a ordem: desagrade primeiro a primária e, em seguida, desagrade o secundário. Quando atualiza ou desvaloriza a base de dados para um nível de serviço diferente, esta recomendação é aplicada.

> [!NOTE]
> Se criou uma base de dados secundária como parte da configuração do grupo failover, não é aconselhável desvalorizar a base de dados secundária. Isto é para garantir que o seu nível de dados tem capacidade suficiente para processar a sua carga de trabalho regular após a ativação da falha.

> [!IMPORTANT]
> A base de dados primária de um grupo de failover não pode escalar para um nível mais alto a menos que a base de dados secundária seja primeiramente dimensionada para o nível mais alto. Se tentar escalar a base de dados primária antes de a base de dados secundária ser dimensionada, poderá receber o seguinte erro:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Prevenção da perda de dados críticos

Devido à elevada latência de redes de área ampla, a cópia contínua utiliza um mecanismo de replicação assíncrono. A replicação assíncrona torna alguma perda de dados inevitável se ocorrer uma falha. No entanto, algumas aplicações podem não necessitar de perda de dados. Para proteger estas atualizações críticas, um desenvolvedor de aplicações pode ligar para o procedimento do sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a emissão. A chamada **sp_wait_for_database_copy_sync** bloqueia o fio de chamada até que a última transação cometida tenha sido transmitida para a base de dados secundária. No entanto, não aguarda que as transações transmitidas sejam reproduzidas e cometidas no secundário. **sp_wait_for_database_copy_sync** é viapara uma ligação de cópia contínua específica. Qualquer utilizador com os direitos de ligação à base de dados primária pode chamar este procedimento.

> [!NOTE]
> **sp_wait_for_database_copy_sync** evita a perda de dados após a falha, mas não garante a sincronização total para o acesso à leitura. O atraso causado por uma chamada **de procedimento sp_wait_for_database_copy_sync** pode ser significativo e depende da dimensão do registo de transações no momento da chamada.

## <a name="monitoring-geo-replication-lag"></a>Monitorização do lag de geo-replicação

Para monitorizar o lag em relação ao RPO, utilize *replication_lag_sec* coluna de [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) na base de dados primária. Mostra um atraso em segundos entre as transações cometidas no primário e persistiu no secundário. Por exemplo, se o valor do atraso for de 1 segundo, significa que se o primário for afetado por uma paragem neste momento e a failover for iniciada, 1 segundo das transições mais recentes não serão salvos. 

Para medir o atraso no que diz respeito às alterações na base de dados primária seletiva seletiva seletiva, ou seja, disponível para ler a partir do secundário, comparar *last_commit* tempo na base de dados secundária com o mesmo valor na base de dados primária.

> [!NOTE]
> Por vezes, *replication_lag_sec* na base de dados primária tem um valor NULO, o que significa que o primário não sabe atualmente até onde está o secundário.   Isto normalmente acontece após o reinício do processo e deve ser uma condição transitória. Considere alertar o pedido se o *replication_lag_sec* devolver NULO por um longo período de tempo. Indica que a base de dados secundária não pode comunicar com a principal devido a uma falha de conectividade permanente. Existem também condições que podem fazer com que a diferença entre *last_commit* tempo no secundário e na base de dados primária se torne grande. Por exemplo, se um compromisso for feito nas primárias após um longo período sem alterações, a diferença saltará para um grande valor antes de voltar rapidamente a 0. Considere-o uma condição de erro quando a diferença entre estes dois valores permanece grande por muito tempo.


## <a name="programmatically-managing-active-geo-replication"></a>Gestão programática da geo-replicação ativa

Como discutido anteriormente, a geo-replicação ativa também pode ser gerida programáticamente usando o Azure PowerShell e a REST API. As tabelas seguintes descrevem o conjunto de comandos disponíveis. A geo-replicação ativa inclui um conjunto de APIs do Gestor de Recursos Azure para gestão, incluindo a Base de [Dados Azure SQL REST API](https://docs.microsoft.com/rest/api/sql/) e [os cmdlets Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Estas APIs exigem a utilização de grupos de recursos e apoiam a segurança baseada em funções (RBAC). Para obter mais informações sobre como implementar funções de acesso, consulte [o Controlo de Acesso baseado em Funções azure](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Gerir falhas de bases de dados individuais e reunidas

> [!IMPORTANT]
> Estes comandos Transact-SQL aplicam-se apenas à geo-replicação ativa e não se aplicam a grupos de failover. Como tal, também não se aplicam às Instâncias Geridas, uma vez que apenas apoiam grupos de failover.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Utilize o argumento secundário do ADD SECONDY ON SERVER para criar uma base de dados secundária para uma base de dados existente e inicie a replicação de dados |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Utilize failover ou FORCE_FAILOVER_ALLOW_DATA_LOSS para mudar uma base de dados secundária para ser primária para iniciar falha |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Utilize remover o SERVIDOR SECUNDÁRIO PARA terminar uma replicação de dados entre uma base de dados SQL e a base de dados secundária especificada. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Devolve informações sobre todos os links de replicação existentes para cada base de dados no servidor de base de dados Azure SQL. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtém o último tempo de replicação, o último lag de replicação, e outras informações sobre o link de replicação para uma determinada base de dados SQL. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Mostra o estado de todas as operações de base de dados, incluindo o estado das ligações de replicação. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |faz com que a aplicação aguarde até que todas as transações cometidas sejam replicadas e reconhecidas pela base de dados secundária ativa. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Gerir falhas de bases de dados únicas e reunidas

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

| Cmdlet | Descrição |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Cria uma base de dados secundária para uma base de dados existente e começa a replicação de dados. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Muda uma base de dados secundária para primária, para iniciar a ativação pós-falha. |
| [Remover-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Termina a replicação de dados entre uma Base de Dados SQL e a base de dados secundária especificada. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Obtém as ligações de georreplicação entre uma Base de Dados SQL do Azure e um grupo de recursos ou o SQL Server. |
|  | |

> [!IMPORTANT]
> Para scripts de amostra, consulte [Configure e failover uma única base de dados utilizando geo-replicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) e [Configurar e failover uma base de dados pooled usando geo-replicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: Gerir falhas de bases de dados individuais e reunidas

| API | Descrição |
| --- | --- |
| [Criar ou atualizar base de dados (criar Modo=Restaurar)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Cria, atualiza ou restaura uma base de dados primária ou secundária. |
| [Obter criar ou atualizar estado da base de dados](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Devolve o estado durante uma operação de criação. |
| [Definir base de dados secundária como primária (Falha planeada)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Define qual base de dados secundária é primária falhando na base de dados primária atual. **Esta opção não é suportada para A Instância Gerida.**|
| [Definir base de dados secundária como primária (Failover não planeada)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Define qual base de dados secundária é primária falhando na base de dados primária atual. Esta operação pode resultar em perda de dados. **Esta opção não é suportada para A Instância Gerida.**|
| [Obter Link de Replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Obtém uma ligação de replicação específica para uma determinada base de dados SQL numa parceria de geo-replicação. Recupera a informação visível na vista do catálogo sys.geo_replication_links. **Esta opção não é suportada para A Instância Gerida.**|
| [Links de replicação - Lista por Base de Dados](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Obtém todos os links de replicação para uma determinada base de dados SQL numa parceria de geo-replicação. Recupera a informação visível na vista do catálogo sys.geo_replication_links. |
| [Eliminar link de replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Elimina uma ligação de replicação de base de dados. Não pode ser feito durante a falha. |
|  | |

## <a name="next-steps"></a>Passos seguintes

- Para scripts de amostra, consulte:
  - [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Configurar e efetuar a ativação pós-falha de uma base de dados de conjunto através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- A Base de Dados SQL também suporta grupos de falha automática. Para mais informações, consulte a utilização [de grupos de falha automática](sql-database-auto-failover-group.md).
- Para uma visão geral da continuidade do negócio e cenários, consulte a [visão geral da continuidade do Negócio](sql-database-business-continuity.md)
- Para saber sobre cópias de segurança automatizadas da Base de Dados Azure SQL, consulte cópias de segurança automatizadas da Base de [Dados SQL](sql-database-automated-backups.md).
- Para aprender a utilizar cópias de segurança automatizadas para recuperação, consulte [Restaurar uma base de dados das cópias de segurança iniciadas](sql-database-recovery-using-backups.md)pelo serviço .
- Para conhecer os requisitos de autenticação de um novo servidor primário e base de dados, consulte a segurança da Base de [Dados SQL após](sql-database-geo-replication-security-config.md)a recuperação de desastres .
