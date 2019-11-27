---
title: Tarefas de Base de Dados Elástica (pré-visualização)
description: Configurar trabalhos de banco de dados elástico (versão prévia) para executar scripts Transact-SQL (T-SQL) em um conjunto de um ou mais bancos de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 283b4004f34372104eb083496400772884f5965e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420372"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Criar, configurar e gerenciar trabalhos elásticos

Neste artigo, você aprenderá a criar, configurar e gerenciar trabalhos elásticos.

Se você não usou trabalhos elásticos, [saiba mais sobre os conceitos de automação de trabalho no banco de dados SQL do Azure](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Criar e configurar o agente

1. Crie ou identifique uma S0 vazia ou uma base de dados SQL superior. Esse banco de dados será usado como o *banco de dados de trabalho* durante a criação do agente de trabalho elástico.
2. Crie um agente de trabalho elástico no [portal](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) ou com o [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).

   ![Criando um agente de trabalho elástico](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Criar, executar e gerir tarefas

1. Crie uma credencial para a execução do trabalho no *banco de dados de trabalho* usando o [PowerShell](elastic-jobs-powershell.md) ou o [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution).
2. Defina o grupo de destino (os bancos de dados nos quais você deseja executar o trabalho) usando o [PowerShell](elastic-jobs-powershell.md) ou o [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers).
3. Crie uma credencial do agente de tarefa em cada base de dados onde a tarefa será executada [(adicione o utilizador (ou função) a cada base de dados no grupo)](sql-database-control-access.md). Por consultar um exemplo, veja o [tutorial do PowerShell](elastic-jobs-powershell.md).
4. Crie um trabalho usando o [PowerShell](elastic-jobs-powershell.md) ou o [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
5. Adicione passos de tarefa com o [PowerShell](elastic-jobs-powershell.md) ou o [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases).
6. Execute um trabalho usando o [PowerShell](elastic-jobs-powershell.md#run-the-job) ou o [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job).
7. Monitore o status de execução do trabalho usando o portal, o [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) ou o [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status).

   ![Portal](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Credenciais para executar tarefas

As tarefas utilizam [credenciais com âmbito de base de dados](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) para ligar às bases de dados especificadas pelo grupo de destino após a execução. Se um grupo de destino contiver servidores ou conjuntos, estas credenciais com âmbito de base de dados são utilizadas para ligar à base de dados mestra para enumerar as bases de dados disponíveis.

A configuração das credenciais corretas para executar uma tarefa pode ser um pouco confusa, por isso, tenha em consideração os seguintes pontos:

- As credenciais com âmbito de base de dados têm de ser criadas na *Base de dados da tarefa*.
- **Todos os bancos de dados de destino devem ter um logon com [permissões suficientes](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) para que o trabalho seja concluído com êxito** (`jobuser` no diagrama abaixo).
- As credenciais podem ser reutilizadas entre trabalhos e as senhas de credenciais são criptografadas e protegidas de usuários que têm acesso somente leitura aos objetos de trabalho.

A imagem seguinte tem como objetivo ajudar a compreender e a configurar as credenciais de tarefa corretas. **Lembre-se de criar o utilizador em todas as bases de dados (todas as *dbs de utilizador de destino*) onde a tarefa tenha de ser executada**.

![Credenciais de Tarefas Elásticas](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Melhores práticas de segurança

Algumas considerações sobre melhores práticas para trabalhar com Tarefas Elásticas:

- Limite a utilização das APIs a pessoas de confiança.
- As credenciais devem ter o mínimo de privilégios necessários para executar o passo de tarefa. Para obter mais informações, consulte [autorização e permissões SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- Ao usar um membro do grupo de destino do servidor e/ou do pool, é altamente recomendável criar uma credencial separada com direitos no banco de dados mestre para exibir/listar bancos de dados que são usados para expandir as listas de Database do (s) servidor (es) e/ou pool (s) antes da execução do trabalho.

## <a name="agent-performance-capacity-and-limitations"></a>Desempenho, capacidade e limitações do agente

As Tarefas Elásticas utilizam recursos de computação mínimos enquanto aguardam pela conclusão de tarefas de execução longa.

Dependendo do tamanho do grupo de destino de bases de dados e do tempo de execução pretendido para uma tarefa (número de trabalhos em simultâneo), o agente requer diferentes quantidades de computação e desempenho da *Base de dados da tarefa* (quando mais destinos houver e quanto maior for o número de tarefas, maior é a quantidade de computação necessária).

Atualmente, a pré-visualização está limitada a 100 tarefas simultâneas.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Impedir que as tarefas reduzam o desempenho da base de dados de destino

Para garantir que os recursos não são sobrecarregados quando executar tarefas nas bases de dados num conjunto elástico de SQL, as tarefas podem ser configuradas para limitar o número de bases de dados nas quais uma tarefa pode ser executada ao mesmo tempo.

Defina o número de bancos de dados simultâneos em que um trabalho é executado definindo o parâmetro `@max_parallelism` do procedimento armazenado `sp_add_jobstep` no T-SQL ou `Add-AzSqlElasticJobStep -MaxParallelism` no PowerShell.

## <a name="best-practices-for-creating-jobs"></a>Melhores práticas para criar tarefas

### <a name="idempotent-scripts"></a>Scripts Idempotent
Os scripts T-SQL de uma tarefa têm de ser [idempotent](https://en.wikipedia.org/wiki/Idempotence). **Idempotent** significa que, se o script executado com êxito e for executado novamente, ocorre o mesmo resultado. Um script pode falhar devido a problemas de rede transitória. Nesse caso, a tarefa repetirá automaticamente a execução do script um número predefinido de vezes antes de desistir. Um script idempotent tem o mesmo resultado mesmo que seja executado com êxito duas vezes (ou mais).

Uma tática simples consiste em testar a existência de um objeto antes de o criar.


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Da mesma forma, um script tem de poder ser executado com êxito ao testar logicamente e refutar as condições que encontrar.



## <a name="next-steps"></a>Passos seguintes

- [Criar e gerir Tarefas Elásticas com o PowerShell](elastic-jobs-powershell.md)
- [Criar e gerir Tarefas Elásticas com o Transact-SQL (T-SQL)](elastic-jobs-tsql.md)
