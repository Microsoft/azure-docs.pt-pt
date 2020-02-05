---
title: Variáveis de ambiente de tempo de execução de tarefa – lote do Azure | Microsoft Docs
description: Orientação e referência da variável de ambiente do tempo de execução da tarefa para análise do lote do Azure.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/12/2019
ms.author: labrenne
ms.openlocfilehash: b6a9e21157884c86577b498671e4cfd0bc6068cd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020204"
---
# <a name="azure-batch-runtime-environment-variables"></a>Variáveis de ambiente de tempo de execução do lote do Azure

O [serviço de lote do Azure](https://azure.microsoft.com/services/batch/) define as seguintes variáveis de ambiente em nós de computação. Você pode fazer referência a essas variáveis de ambiente nas linhas de comando da tarefa e nos programas e scripts executados pelas linhas de comando.

Para obter mais informações sobre como usar variáveis de ambiente com o lote, consulte [configurações de ambiente para tarefas](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Visibilidade da variável de ambiente

Essas variáveis de ambiente são visíveis apenas no contexto do **usuário da tarefa**, a conta do usuário no nó sob o qual uma tarefa é executada. *Não* as verá se [ligar remotamente](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a um nó de computação através do protocolo DRP (Remote Desktop Protocol) ou de Secure Shell (SSH) e as apresentar. Isto deve-se ao facto de a conta de utilizador utilizada na ligação remota não ser a mesma conta utilizada pela tarefa.

Para obter o valor atual de uma variável de ambiente, inicie `cmd.exe` em um nó de computação do Windows ou `/bin/sh` em um nó do Linux:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Expansão de linha de comando de variáveis de ambiente

As linhas de comando executadas por tarefas em nós de computação não são executadas em um shell. Portanto, essas linhas de comando não podem aproveitar nativamente os recursos do Shell, como a expansão de variável de ambiente (isso inclui o `PATH`). Para aproveitar esses recursos, você deve **invocar o Shell** na linha de comando. Por exemplo, inicie `cmd.exe` em nós de computação do Windows ou `/bin/sh` em nós do Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Variáveis de ambiente

| Nome da variável                     | Descrição                                                              | Disponibilidade | Exemplo |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | O nome da conta do lote à qual a tarefa pertence.                  | Todas as tarefas.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | A URL da conta do lote. | Todas as tarefas. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Um prefixo de todas as variáveis de ambiente do pacote de aplicativo. Por exemplo, se a versão "FOO" da aplicação "FOO" for instalada numa piscina, a variável ambiental é AZ_BATCH_APP_PACKAGE_FOO_1. AZ_BATCH_APP_PACKAGE_FOO_1 aponta para a localização que o pacote foi descarregado (uma pasta). Ao utilizar a versão padrão do pacote de aplicações, utilize a variável ambiente AZ_BATCH_APP_PACKAGE sem os números da versão. | Qualquer tarefa com um pacote de aplicativo associado. Também estará disponível para todas as tarefas se o próprio nó tiver pacotes de aplicativos. | AZ_BATCH_APP_PACKAGE_FOO_1 |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Um token de autenticação que concede acesso a um conjunto limitado de operações de serviço de lote. Essa variável de ambiente só estará presente se [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) for definido quando a [tarefa for adicionada](/rest/api/batchservice/task/add#request-body). O valor do token é usado nas APIs do lote como credenciais para criar um cliente do lote, como na [API do .net BatchClient. Open ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Todas as tarefas. | Token de acesso do OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Um diretório dentro do [diretório de trabalho de tarefa][files_dirs] no qual os certificados são armazenados para nós de computação do Linux. Essa variável de ambiente não se aplica a nós de computação do Windows.                                                  | Todas as tarefas.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | A lista de nós alocados para uma [tarefa de várias instâncias][multi_instance] no formato `nodeIP,nodeIP`. | Subtarefas e primárias de várias instâncias. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Especifica se o nó atual é o nó mestre para uma [tarefa de várias instâncias][multi_instance]. Os valores possíveis são `true` e `false`.| Subtarefas e primárias de várias instâncias. | `true` |
| AZ_BATCH_JOB_ID                 | O ID do trabalho ao qual a tarefa pertence. | Todas as tarefas, exceto a tarefa de início. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | O caminho completo do [diretório da tarefa][files_dirs] de preparação do trabalho no nó. | Todas as tarefas, exceto tarefas de início e tarefa de preparação de trabalho. Disponível somente se o trabalho estiver configurado com uma tarefa de preparação de trabalho. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | O caminho completo do [diretório de trabalho da tarefa][files_dirs] de preparação do trabalho no nó. | Todas as tarefas, exceto tarefas de início e tarefa de preparação de trabalho. Disponível somente se o trabalho estiver configurado com uma tarefa de preparação de trabalho. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | O endereço IP e a porta do nó de computação no qual a tarefa principal de uma [tarefa de várias instâncias][multi_instance] é executada. | Subtarefas e primárias de várias instâncias. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | A ID do nó ao qual a tarefa é atribuída. | Todas as tarefas. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Se `true`, o nó atual será um nó dedicado. Se `false`, será um [nó de baixa prioridade](batch-low-pri-vms.md). | Todas as tarefas. | `true` |
| AZ_BATCH_NODE_LIST              | A lista de nós alocados para uma [tarefa de várias instâncias][multi_instance] no formato `nodeIP;nodeIP`. | Subtarefas e primárias de várias instâncias. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | O caminho completo do local de [montagem do sistema de arquivos](virtual-file-mount.md) em nível de nó onde residem todos os diretórios de montagem. Os compartilhamentos de arquivos do Windows usam uma letra de unidade, portanto, para o Windows, a unidade de montagem faz parte de dispositivos e unidades.  |  Todas as tarefas, incluindo a tarefa inicial, têm acesso ao usuário, dado que o usuário está ciente das permissões de montagem para o diretório montado. | No Ubuntu, por exemplo, o local é: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | O caminho completo da raiz de todos os [diretórios de lote][files_dirs] no nó. | Todas as tarefas. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | O caminho completo do [diretório compartilhado][files_dirs] no nó. Todas as tarefas que são executadas em um nó têm acesso de leitura/gravação a esse diretório. As tarefas executadas em outros nós não têm acesso remoto a esse diretório (não é um diretório de rede "compartilhado"). | Todas as tarefas. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | O caminho completo do [diretório da tarefa inicial][files_dirs] no nó. | Todas as tarefas. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | O ID do conjunto no qual a tarefa é executada. | Todas as tarefas. | batchpool001 |
| AZ_BATCH_TASK_DIR               | O caminho completo do [diretório da tarefa][files_dirs] no nó. Este diretório contém a `stdout.txt` e `stderr.txt` para a tarefa, e o AZ_BATCH_TASK_WORKING_DIR. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | O ID da tarefa atual. | Todas as tarefas, exceto a tarefa de início. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Um caminho de diretório que é idêntico à tarefa principal e a todas as subtarefas de uma [tarefa de várias instâncias][multi_instance]. O caminho existe em cada nó em que a tarefa de várias instâncias é executada e é acessível para leitura/gravação para os comandos de tarefa em execução nesse nó (o [comando de coordenação][coord_cmd] e o comando do [aplicativo][app_cmd]). Subtarefas ou uma tarefa primária executada em outros nós não têm acesso remoto a esse diretório (não é um diretório de rede "compartilhado"). | Subtarefas e primárias de várias instâncias. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | O caminho completo do [diretório de trabalho de tarefa][files_dirs] no nó. A tarefa em execução no momento tem acesso de leitura/gravação a esse diretório. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | A lista de nós e o número de núcleos por nó que são alocados para uma [tarefa de várias instâncias][multi_instance]. Os nós e os núcleos são listados no formato `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, em que o número de nós é seguido por um ou mais endereços IP de nó e o número de núcleos para cada um. |  Subtarefas e primárias de várias instâncias. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
