---
title: Visão geral da automação de emprego com empregos elásticos
description: Utilize trabalhos elásticos para automação de emprego para executar scripts Transact-SQL (T-SQL) através de um conjunto de uma ou mais bases de dados
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, contperf-fy21q3
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: conceptual
author: williamdassafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/1/2021
ms.openlocfilehash: 295889cf64d27761021dd09549a3366ea142516e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752036"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Automatizar tarefas de gestão utilizando trabalhos elásticos (pré-visualização)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Você pode criar e agendar trabalhos elásticos que podem ser executados periodicamente contra uma ou muitas bases de dados Azure SQL para executar consultas Transact-SQL (T-SQL) e executar tarefas de manutenção. 

Pode definir uma base de dados ou grupos de bases de dados de destino onde a tarefa será executada e, além disso, definir horários para executar uma tarefa.
Uma tarefa processa a tarefa de início de sessão na base de dados de destino. Também pode definir, manter e persistir os scripts Transact-SQL para serem executados num grupo de bases de dados.

Cada tarefa regista o estado de execução e, além disso, repete as operações automaticamente caso ocorra alguma falha.

## <a name="when-to-use-elastic-jobs"></a>Quando usar trabalhos elásticos

Existem vários cenários em que se pode utilizar a automatização elástica do trabalho:

- Automatizar tarefas de gestão e agendar-lhes para correr todos os dias da semana, fora de horas, etc.
  - Implemente alterações de esquema, gestão de credenciais, recolha de dados de desempenho ou recolha de telemetria do inquilino (cliente).
  - Atualizar dados de referência (informações comuns em todas as bases de dados), carregar dados do armazenamento de Azure Blob.
- Configure tarefas para serem executadas numa coleção de bases de dados recorrentemente, como, por exemplo, fora das horas de ponta.
  - Recolha os resultados da consulta a partir de um conjunto de bases de dados numa tabela central de forma contínua. As consultas de desempenho podem ser continuamente executadas e configuradas para acionar tarefas adicionais para serem executadas.
- Recolher dados para relatórios
  - Dados agregados de uma recolha de bases de dados numa única tabela de destino.
  - Execute consultas de processamento de dados de execução mais longa num grande conjunto de bases de dados, por exemplo, a coleção de telemetria de cliente. Os resultados são recolhidos para uma tabela de destino única para análise adicional.
- Movimentos de dados 

### <a name="automation-on-other-platforms"></a>Automação em outras plataformas

Considere as seguintes tecnologias de agendamento de empregos em diferentes plataformas:

- **Os Trabalhos Elásticos** são serviços de Agendamento de Emprego que executam trabalhos personalizados em uma ou muitas bases de dados na Base de Dados Azure SQL.
- **Sql Agent Jobs** são executados pelo serviço SQL Agent que continua a ser usado para automatização de tarefas no SQL Server e também está incluído com Azure SQL Managed Instances. Sql Agent Jobs não estão disponíveis na Base de Dados Azure SQL.

As Bases de Dados Elásticas sql podem visar [as bases de dados Azure SQL,](sql-database-paas-overview.md) [as piscinas elásticas Azure SQL Database](elastic-pool-overview.md)e as Bases de Dados Azure SQL em [mapas de fragmentos.](elastic-scale-shard-map-management.md)

Para a automatização de trabalho de script T-SQL no SQL Server e na Azure SQL Managed Instance, considere [o Agente SQL](job-automation-managed-instances.md). 

Para a automatização de trabalho de script T-SQL em Azure Synapse Analytics, considere [os oleodutos com gatilhos recorrentes](../../synapse-analytics/data-integration/concepts-data-factory-differences.md), que são [baseados na Azure Data Factory](../../synapse-analytics/data-integration/concepts-data-factory-differences.md).

Vale a pena notar diferenças entre o Agente SQL (disponível no SQL Server e como parte do SQL Managed Instance), e o agente de trabalho elástico da base de dados (que pode executar T-SQL em Bases de Dados SQL ou bases de dados em SQL Server e Azure SQL Managed Instance, Azure Synapse Analytics).

| |Tarefas Elásticas |Agente SQL |
|---------|---------|---------|
|**Âmbito** | Qualquer número de bases de dados na Base de Dados Azure SQL e/ou armazéns de dados na mesma nuvem Azure que o agente de trabalho. Os alvos podem estar em diferentes servidores, subscrições e/ou regiões. <br><br>Os grupos-alvo podem ser compostos por bases de dados individuais ou armazéns de dados, ou todas as bases de dados num mapa de servidores, piscinas ou fragmentos (enumerados dinamicamente no tempo de execução do trabalho). | Qualquer base de dados individual no mesmo caso que o agente SQL. A funcionalidade de Administração multi-servidor do SqL Server Agent permite que as instâncias master/target coordene a execução de trabalho, embora esta funcionalidade não esteja disponível em instância gerida pelo SQL. |
|**APIs e Ferramentas suportadas** | Portal, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Alvos de trabalho elásticos

**Os Trabalhos Elásticos** proporcionam a capacidade de executar um ou mais scripts T-SQL em paralelo, através de um grande número de bases de dados, em um horário ou a pedido.

Pode executar trabalhos programados contra qualquer combinação de bases de dados: uma ou mais bases de dados individuais, todas as bases de dados num servidor, todas as bases de dados num pool elástico ou mapa de fragmentos, com a flexibilidade adicional para incluir ou excluir qualquer base de dados específica. As tarefas podem ser executadas em vários servidores, vários conjuntos e podem até ser executadas em bases de dados em subscrições diferentes. Os servidores e os conjuntos são dinamicamente enumerados em runtime, para que as tarefas sejam executadas em todas as bases de dados que existem no grupo de destino no momento da execução.

A imagem seguinte mostra um agente de tarefa a executar tarefas nos diferentes tipos de grupos de destino:

![Modelo conceptual de agente de Tarefa Elástica](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Componentes de trabalho elásticos

|Componente | Descrição (abaixo da tabela encontram-se detalhes adicionais) |
|---------|---------|
|[**Agente de Tarefa Elástica**](#elastic-job-agent) | O recurso do Azure que cria para executar e gerir tarefas. |
|[**Base de dados da tarefa**](#elastic-job-database) | Uma base de dados na Base de Dados Azure SQL que o agente de trabalho utiliza para armazenar dados relacionados com o trabalho, definições de emprego, etc. |
|[**Grupo de destino**](#target-group) | O conjunto de servidores, conjuntos, bases de dados e mapas de shard no qual executar uma tarefa. |
|[**Tarefa**](#elastic-jobs-and-job-steps) | Um trabalho é uma unidade de trabalho que é composta por uma ou mais etapas de trabalho. Os passos de tarefa especificam o script T-SQL a executar, bem como outros detalhes necessários para executar o script. |

#### <a name="elastic-job-agent"></a>Agente de trabalho elástico

Um agente de Tarefa Elástica é o recurso do Azure para criar, executar e gerir tarefas. O agente de Tarefa Elástica é um recurso do Azure que o utilizador cria no portal (o [PowerShell](elastic-jobs-powershell-create.md) e o REST também são suportados).

A criação de um **agente de trabalho elástico** requer uma base de dados existente na Base de Dados Azure SQL. O agente configura esta base de dados Azure SQL existente como base [*de dados job*](#elastic-job-database).

O agente de Tarefa Elástica é gratuito. A base de dados de emprego é faturada ao mesmo ritmo que qualquer base de dados na Base de Dados Azure SQL.

#### <a name="elastic-job-database"></a>Base de dados de trabalho elástica

A *Base de dados da tarefa* serve para definir tarefas e controlar o estado e o histórico de execuções de tarefas. A *base de dados Job* também é usada para armazenar metadados de agentes, registos, resultados, definições de emprego, e também contém muitos procedimentos armazenados úteis e outros objetos de base de dados para criar, executar e gerir empregos usando T-SQL.

Para a pré-visualização atual, é necessária uma base de dados existente na Base de Dados Azure SQL (S0 ou superior) para criar um agente De serviço elástico.

A *base de dados Job* deve ser uma base de dados de serviço limpa, vazia, S0 ou mais alta. O objetivo de serviço recomendado da base de *dados job* é S1 ou superior, mas a escolha ideal depende das necessidades de desempenho do seu(s): o número de passos de emprego, o número de alvos de emprego e a frequência com que os empregos são executados. 

Se as operações contra a base de dados de trabalho forem mais lentas do que o esperado, [monitorize](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) o desempenho da base de dados e a utilização do recurso na base de dados de trabalho durante períodos de lentidão utilizando o portal Azure ou o [DMV sys.dm_db_resource_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) Se a utilização de um recurso, como CPU, Data IO ou Log Write se aproximar a 100% e se correlacionar com períodos de lentidão, considere aumentar gradualmente a base de dados para objetivos de serviço mais elevados (quer no [modelo DTU,](service-tiers-dtu.md) quer no [modelo vCore)](service-tiers-vcore.md)até que o desempenho da base de dados de emprego seja suficientemente melhorado.

##### <a name="elastic-job-database-permissions"></a>Permissões elásticas de base de dados de trabalho

Durante a criação do agente de tarefa, um esquema, tabelas e uma função chamada *jobs_reader* são criados na *Base de dados da tarefa*. A função é criada com as seguintes permissões e tem como objetivo dar aos administradores um melhor controlo de acesso para a monitorização de tarefas:

|Nome da função |permissões de esquema "jobs" |permissões de esquema "jobs_internal" |
|---------|---------|---------|
|**jobs_reader** | SELECIONAR | Nenhum |

> [!IMPORTANT]
> Considere as implicações de segurança antes de conceder acesso à *Base de dados da tarefa* como um administrador da base de dados. Um utilizador malicioso com permissões para criar ou editar empregos poderia criar ou editar um trabalho que utilizasse uma credencial armazenada para se ligar a uma base de dados sob o controlo do utilizador malicioso, o que poderia permitir ao utilizador malicioso determinar a palavra-passe da credencial.

#### <a name="target-group"></a>Grupo de destino

Um *grupo de destino* define o conjunto de bases de dados onde será executado um passo de tarefa. Um grupo de destino pode conter qualquer número e combinação dos seguintes elementos:

- **Servidor lógico DO SQL** - se um servidor for especificado, todas as bases de dados existentes no servidor no momento da execução do trabalho fazem parte do grupo. A credencial da base de dados mestra tem de ser fornecida para que o grupo possa ser enumerado e atualizado antes da execução da tarefa. Para obter mais informações sobre servidores lógicos, consulte [o que é um servidor na Base de Dados Azure SQL e na Azure Synapse Analytics?](logical-servers.md)
- **Conjunto elástico** - se for especificado um conjunto elástico, todas as bases de dados que estão no conjunto elástico no momento da execução da tarefa fazem parte do grupo. Tal como acontece para um servidor, a credencial da base de dados mestra tem de ser fornecida para que o grupo possa ser atualizado antes da execução da tarefa.
- **Base de dados individual** - especifique uma ou mais bases de dados individuais para fazerem parte do grupo.
- **Mapa de fragmentos** - bases de dados de um mapa de fragmentos.

> [!TIP]
> No momento da execução da tarefa, a *enumeração dinâmica* reavalia o conjunto de bases de dados nos grupos de destino que incluem servidores ou conjuntos. A enumeração dinâmica garante que as **tarefas são executadas em todas as bases de dados que existem no servidor ou conjunto no momento da execução da tarefa**. A reavaliação da lista de bases de dados em runtime é especificamente útil para cenários em que a associação do conjunto ou do servidor muda com frequência.

Os conjuntos e as bases de dados individuais podem ser especificados como estando incluídos ou excluídos do grupo. Isto permite criar um grupo de destino com qualquer combinação de bases de dados. Por exemplo, pode adicionar um servidor a um grupo de destino, mas excluir bases de dados específicas num conjunto elástico (ou excluir um conjunto completo).

Um grupo de destino pode incluir bases de dados em várias subscrições e em várias regiões. Lembre-se que as execuções em várias regiões têm uma latência superior do que as execuções na mesma região.

Os seguintes exemplos mostram como as diferentes definições de grupo de destino são enumeradas dinamicamente no momento da execução do trabalho, de modo a determinar as bases de dados que o trabalho vai executar:

![Exemplos de grupos de destino](./media/job-automation-overview/targetgroup-examples1.png)

O **Exemplo 1** mostra um grupo de destino que consiste numa lista de bases de dados individuais. Quando é executado um passo do trabalho com este grupo de destino, a ação desse passo será executada em cada uma dessas bases de dados.<br>
**O exemplo 2** mostra um grupo alvo que contém um servidor como alvo. Quando é executado um passo do trabalho com este grupo de destino, o servidor é enumerado dinamicamente para determinar a lista de bases de dados que estão, atualmente, no servidor. A ação desse passo será executada em cada uma dessas bases de dados.<br>
O **Exemplo 3** mostra um grupo de destino semelhante ao do *Exemplo 2*, mas é excluída especificamente uma base de dados individual. A ação do passo deste trabalho *não* será executada na base de dados excluída.<br>
O **Exemplo 4** mostra um grupo de destino que contém um conjunto elástico como o destino. Tal como no *Exemplo 2*, o conjunto será enumerado dinamicamente no momento de execução do trabalho para determinar a lista de bases de dados contidas no mesmo.
<br><br>

![Exemplos adicionais do grupo alvo](./media/job-automation-overview/targetgroup-examples2.png)

**Os exemplos 5** e **exemplo 6** mostram cenários avançados onde servidores, piscinas elásticas e bases de dados podem ser combinados usando incluir e excluir regras.<br>
O **Exemplo 7** mostra que as partições num mapa de partições também podem ser avaliadas no momento de execução do trabalho.

> [!NOTE]
> A base de dados job em si pode ser o alvo de um trabalho. Neste cenário, a base de dados Job é tratada como qualquer outra base de dados-alvo. O utilizador de emprego deve ser criado e concedido permissões suficientes na base de dados Job, e a credencial de pesquisa da base de dados para o utilizador de emprego também deve existir na base de dados job, tal como acontece com qualquer outra base de dados-alvo.

#### <a name="elastic-jobs-and-job-steps"></a>Trabalhos elásticos e passos de trabalho

Uma *tarefa* é uma unidade de trabalho que é executada com base num agendamento ou como uma tarefa ocasional. Uma tarefa é composta por um ou mais *passos de tarefa*.

Cada passo de tarefa especifica um script T-SQL a executar, um ou mais grupos de destino onde executar o script T-SQL e as credenciais de que o agente de tarefa precisa para ligar à base de dados de destino. Cada passo de tarefa tem políticas de repetição e de tempo limite personalizáveis e, opcionalmente, pode especificar parâmetros de saída.

#### <a name="job-output"></a>Saída da tarefa

O resultado dos passos de uma tarefa em cada base de dados de destino é registado pormenorizadamente e a saída do script pode ser capturada numa tabela especificada. Pode especificar uma base de dados para guardar os dados devolvidos por uma tarefa.

#### <a name="job-history"></a>Histórico de tarefas

Ver o histórico de execução de trabalho elástico na *base de dados job* [consultando a tabela jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). Uma tarefa de limpeza do sistema remove o histórico de execuções com mais de 45 dias. Para remover o histórico com menos de 45 dias, ligue para o **procedimento sp_purge_jobhistory** armazenado na base *de dados job*.

#### <a name="job-status"></a>Estatuto do trabalho

Pode monitorizar execuções elásticas na *base de dados job* [consultando a tabela jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). 

### <a name="agent-performance-capacity-and-limitations"></a>Desempenho, capacidade e limitações do agente

As Tarefas Elásticas utilizam recursos de computação mínimos enquanto aguardam pela conclusão de tarefas de execução longa.

Dependendo do tamanho do grupo de destino de bases de dados e do tempo de execução pretendido para uma tarefa (número de trabalhos em simultâneo), o agente requer diferentes quantidades de computação e desempenho da *Base de dados da tarefa* (quando mais destinos houver e quanto maior for o número de tarefas, maior é a quantidade de computação necessária).

Atualmente, o limite é de 100 postos de trabalho simultâneos.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Impedir que as tarefas reduzam o desempenho da base de dados de destino

Para garantir que os recursos não são sobrecarregados quando executar tarefas nas bases de dados num conjunto elástico de SQL, as tarefas podem ser configuradas para limitar o número de bases de dados nas quais uma tarefa pode ser executada ao mesmo tempo.

## <a name="next-steps"></a>Passos seguintes

- [Como criar e gerir trabalhos elásticos](elastic-jobs-overview.md)
- [Criar e gerir Tarefas Elásticas com o PowerShell](elastic-jobs-powershell-create.md)
- [Criar e gerir Tarefas Elásticas com o Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)
