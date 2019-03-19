---
title: Variáveis de ambiente de nó - Azure Batch de computação | Documentos da Microsoft
description: Referência de variável de ambiente de nó de computação do Azure Batch Analytics.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/07/2019
ms.author: lahugh
ms.openlocfilehash: 9902f38ddfd3035adcce697c2eb5b77bdc1d8c9c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57874766"
---
# <a name="azure-batch-compute-node-environment-variables"></a>Variáveis do ambiente de nó de computação do Azure Batch

O [serviço Azure Batch](https://azure.microsoft.com/services/batch/) define as seguintes variáveis de ambiente em nós de computação. Pode referenciar estas variáveis de ambiente nas linhas de comandos de tarefas e, os programas e scripts executam pelas linhas de comando.

Para obter mais informações sobre como utilizar variáveis de ambiente com o Batch, consulte [definições de ambiente para tarefas](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Visibilidade de variável de ambiente

Estas variáveis de ambiente são visíveis apenas no contexto do **utilizador da tarefa**, a conta de utilizador no nó em que é executada uma tarefa. *Não* as verá se [ligar remotamente](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a um nó de computação através do protocolo DRP (Remote Desktop Protocol) ou de Secure Shell (SSH) e as apresentar. Isto deve-se ao facto de a conta de utilizador utilizada na ligação remota não ser a mesma conta utilizada pela tarefa.

## <a name="command-line-expansion-of-environment-variables"></a>Expansão de variáveis de ambiente da linha de comandos

As linhas de comando executadas pelas tarefas na computação fazer de nós não executada sob uma shell. Por conseguinte, estas linhas de comando não é possível tirar partido nativamente das funcionalidades de shell, tais como a expansão de variáveis de ambiente (Isto inclui o `PATH`). Para tirar partido destas funcionalidades, deve **invocar a shell** na linha de comandos. Por exemplo, inicie `cmd.exe` nós de computação no Windows ou `/bin/sh` em nós do Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Variáveis de ambiente

| Nome da variável                     | Descrição                                                              | Disponibilidade | Exemplo |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | O nome da conta do Batch que a tarefa pertence.                  | Todas as tarefas.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | O URL da conta do Batch. | Todas as tarefas. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Um prefixo de todas as aplicações pacote variáveis de ambiente. Por exemplo, se o aplicativo "Foo" versão "1" está instalado num conjunto, a variável de ambiente é AZ_BATCH_APP_PACKAGE_FOO_1. AZ_BATCH_APP_PACKAGE_FOO_1 pontos para a localização que o pacote foi transferido (uma pasta). | Qualquer tarefa com um pacote de aplicação associada. Também disponível para todas as tarefas se o nó em si tem pacotes de aplicações. | AZ_BATCH_APP_PACKAGE_FOO_1 |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Um token de autenticação que concede acesso a um conjunto limitado de operações de serviço do Batch. Esta variável de ambiente está presente apenas se o [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) estão definidas quando o [é adicionada a tarefa](/rest/api/batchservice/task/add#request-body). O valor do token é utilizado como credenciais nas APIs de Batch para criar um cliente do Batch, tal como nos [BatchClient.Open() .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Todas as tarefas. | Token de acesso de OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Um diretório dentro do [diretório de trabalho] [ files_dirs] em quais certificados são armazenados para Linux de nós de computação. Tenha em atenção que esta variável de ambiente não é aplicável ao Windows nós de computação.                                                  | Todas as tarefas.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | A lista de nós que estão atribuídas a uma [tarefas de várias instâncias] [ multi_instance] no formato `nodeIP,nodeIP`. | Principal de várias instâncias e as subtarefas. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Especifica se o nó atual é o nó principal para um [tarefas de várias instâncias][multi_instance]. Os valores possíveis são `true` e `false`.| Principal de várias instâncias e as subtarefas. | `true` |
| AZ_BATCH_JOB_ID                 | O ID do trabalho ao qual a tarefa pertence. | Todas as tarefas, exceto pelo fato de uma tarefa de início. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | O caminho completo do que a tarefa de preparação [diretório de tarefas] [ files_dirs] no nó. | Todas as tarefas, exceto a tarefa de início e a tarefa de preparação. Disponível apenas se a tarefa estiver configurada com uma tarefa de preparação. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | O caminho completo do que a tarefa de preparação [diretório de trabalho] [ files_dirs] no nó. | Todas as tarefas, exceto a tarefa de início e a tarefa de preparação. Disponível apenas se a tarefa estiver configurada com uma tarefa de preparação. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | O endereço IP e porta do nó de computação em que a tarefa principal de um [tarefas de várias instâncias] [ multi_instance] é executado. | Principal de várias instâncias e as subtarefas. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | O ID do nó que a tarefa está atribuída. | Todas as tarefas. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Se `true`, o nó atual é um nó dedicado. Se `false`, é um [nó de baixa prioridade](batch-low-pri-vms.md). | Todas as tarefas. | `true` |
| AZ_BATCH_NODE_LIST              | A lista de nós que estão atribuídas a uma [tarefas de várias instâncias] [ multi_instance] no formato `nodeIP;nodeIP`. | Principal de várias instâncias e as subtarefas. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_ROOT_DIR          | O caminho completo de raiz de todos os [diretórios do Batch] [ files_dirs] no nó. | Todas as tarefas. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | O caminho completo dos [diretório partilhado] [ files_dirs] no nó. Todas as tarefas executadas num nó de ter acesso de leitura/escrita para este diretório. Tarefas que são executadas nos outros nós não tem acesso remoto para este diretório (não é um diretório de rede "partilhado"). | Todas as tarefas. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | O caminho completo dos [iniciar o diretório de tarefas] [ files_dirs] no nó. | Todas as tarefas. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | O ID do conjunto no qual a tarefa é executada. | Todas as tarefas. | batchpool001 |
| AZ_BATCH_TASK_DIR               | O caminho completo dos [diretório de tarefas] [ files_dirs] no nó. Esse diretório contém a `stdout.txt` e `stderr.txt` para a tarefa e o AZ_BATCH_TASK_WORKING_DIR. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | O ID da tarefa atual. | Todas as tarefas, exceto pelo fato de uma tarefa de início. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Um caminho de diretório é idêntico para a principal tarefa e cada subtarefa de um [tarefas de várias instâncias][multi_instance]. O caminho existe em cada nó em que várias instâncias execuções de tarefas e é de leitura/escrita acessível para os comandos de tarefas em execução nesse nó (tanto a [comandos de coordenação] [ coord_cmd] e o [ comando de aplicativo][app_cmd]). Subtarefas ou uma tarefa principal, que são executadas nos outros nós não tem acesso remoto para este diretório (não é um diretório de rede "partilhado"). | Principal de várias instâncias e as subtarefas. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | O caminho completo dos [diretório de trabalho] [ files_dirs] no nó. A tarefa em execução tem acesso de leitura/escrita para este diretório. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | A lista de nós e o número de núcleos por nó que são alocados para uma [tarefas de várias instâncias][multi_instance]. Nós e núcleos estão listados no formato `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, onde o número de nós é seguido por um ou mais endereços IP do nó e o número de núcleos para cada um. |  Principal de várias instâncias e as subtarefas. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
