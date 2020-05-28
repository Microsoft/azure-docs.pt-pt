---
title: Trabalhos de base de dados elásticos (pré-visualização)
description: Configure trabalhos de base de dados elásticos (pré-visualização) para executar scripts Transact-SQL (T-SQL) através de um conjunto de uma ou mais bases de dados em Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 995d70419edb374c3761fe2f989ce3a757cc7a47
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050066"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Criar, configurar e gerir trabalhos elásticos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste artigo, você vai aprender a criar, configurar e gerir trabalhos elásticos.

Se não utilizou trabalhos elásticos, saiba mais sobre os conceitos de automação de emprego na Base de [Dados Azure SQL](job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Criar e configurar o agente

1. Crie ou identifique uma S0 vazia ou uma base de dados SQL superior. Esta base de dados será utilizada como base de *dados de trabalho* durante a criação de agente de trabalho elástico.
2. Crie um agente elástico no [portal](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) ou com [powerShell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent).

   ![Criação de agente elástico de trabalho](./media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Criar, executar e gerir tarefas

1. Crie uma credencial para execução de emprego na base de *dados job* utilizando [PowerShell](elastic-jobs-powershell-create.md) ou [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-credential-for-job-execution).
2. Defina o grupo-alvo (as bases de dados contra as quais pretende executar) utilizando [o PowerShell](elastic-jobs-powershell-create.md) ou [o T-SQL](elastic-jobs-tsql-create-manage.md#create-a-target-group-servers).
3. Crie uma credencial do agente de tarefa em cada base de dados onde a tarefa será executada [(adicione o utilizador (ou função) a cada base de dados no grupo)](logins-create-manage.md). Por consultar um exemplo, veja o [tutorial do PowerShell](elastic-jobs-powershell-create.md).
4. Crie um trabalho utilizando [powerShell](elastic-jobs-powershell-create.md) ou [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases).
5. Adicione passos de tarefa com o [PowerShell](elastic-jobs-powershell-create.md) ou o [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases).
6. Faça um trabalho usando [PowerShell](elastic-jobs-powershell-create.md#run-the-job) ou [T-SQL](elastic-jobs-tsql-create-manage.md#begin-unplanned-execution-of-a-job).
7. Monitorize o estado de execução do trabalho utilizando o portal, [PowerShell](elastic-jobs-powershell-create.md#monitor-status-of-job-executions) ou [T-SQL](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status).

   ![Portal](./media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Credenciais para executar tarefas

As tarefas utilizam [credenciais com âmbito de base de dados](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) para ligar às bases de dados especificadas pelo grupo de destino após a execução. Se um grupo de destino contiver servidores ou conjuntos, estas credenciais com âmbito de base de dados são utilizadas para ligar à base de dados mestra para enumerar as bases de dados disponíveis.

A configuração das credenciais corretas para executar uma tarefa pode ser um pouco confusa, por isso, tenha em consideração os seguintes pontos:

- As credenciais com âmbito de base de dados têm de ser criadas na *Base de dados da tarefa*.
- Todas as bases de **dados-alvo devem ter um login com [permissões suficientes](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) para que o trabalho esteja concluído com sucesso** `jobuser` (no diagrama abaixo).
- As credenciais podem ser reutilizadas em todos os trabalhos, e as palavras-passe credenciais são encriptadas e protegidas por utilizadores que tenham acesso apenas a objetos de trabalho.

A imagem seguinte tem como objetivo ajudar a compreender e a configurar as credenciais de tarefa corretas. **Lembre-se de criar o utilizador em todas as bases de dados (todas as *dbs de utilizador de destino*) onde a tarefa tenha de ser executada**.

![Credenciais de Tarefas Elásticas](./media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Melhores práticas de segurança

Algumas considerações sobre melhores práticas para trabalhar com Tarefas Elásticas:

- Limite a utilização das APIs a pessoas de confiança.
- As credenciais devem ter o mínimo de privilégios necessários para executar o passo de tarefa. Para mais informações, consulte [Autorizações e Permissões.](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
- Ao utilizar um membro do grupo de alvos de servidor e/ou pool, é altamente sugerido criar uma credencial separada com direitos na base de dados principal para visualizar/listar bases de dados que são usadas para expandir as listas de bases de dados dos servidores e/ou pool(s) antes da execução do trabalho.

## <a name="agent-performance-capacity-and-limitations"></a>Desempenho, capacidade e limitações do agente

As Tarefas Elásticas utilizam recursos de computação mínimos enquanto aguardam pela conclusão de tarefas de execução longa.

Dependendo do tamanho do grupo de destino de bases de dados e do tempo de execução pretendido para uma tarefa (número de trabalhos em simultâneo), o agente requer diferentes quantidades de computação e desempenho da *Base de dados da tarefa* (quando mais destinos houver e quanto maior for o número de tarefas, maior é a quantidade de computação necessária).

Atualmente, a pré-visualização está limitada a 100 tarefas simultâneas.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Impedir que as tarefas reduzam o desempenho da base de dados de destino

Para garantir que os recursos não são sobrecarregados quando executar tarefas nas bases de dados num conjunto elástico de SQL, as tarefas podem ser configuradas para limitar o número de bases de dados nas quais uma tarefa pode ser executada ao mesmo tempo.

Definir o número de bases de dados simultâneas em que um trabalho funciona, definindo o `sp_add_jobstep` parâmetro do procedimento armazenado em `@max_parallelism` T-SQL ou `Add-AzSqlElasticJobStep -MaxParallelism` no PowerShell.

## <a name="best-practices-for-creating-jobs"></a>Melhores práticas para criar tarefas

### <a name="idempotent-scripts"></a>Scripts Idempotent
Os scripts T-SQL de uma tarefa têm de ser [idempotent](https://en.wikipedia.org/wiki/Idempotence). **Idempotent** significa que, se o script executado com êxito e for executado novamente, ocorre o mesmo resultado. Um script pode falhar devido a problemas de rede transitória. Nesse caso, a tarefa repetirá automaticamente a execução do script um número predefinido de vezes antes de desistir. Um script idempotent tem o mesmo resultado mesmo que seja executado com êxito duas vezes (ou mais).

Uma tática simples consiste em testar a existência de um objeto antes de o criar.


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Da mesma forma, um script tem de poder ser executado com êxito ao testar logicamente e refutar as condições que encontrar.



## <a name="next-steps"></a>Próximos passos

- [Criar e gerir Tarefas Elásticas com o PowerShell](elastic-jobs-powershell-create.md)
- [Criar e gerir Tarefas Elásticas com o Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)
