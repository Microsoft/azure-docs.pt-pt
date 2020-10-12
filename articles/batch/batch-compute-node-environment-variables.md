---
title: Variáveis de ambiente de runtime de tarefas
description: Orientação variável do ambiente de tempo de execução de tarefas e referência para Azure Batch Analytics.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 6b8ade312146802ede6e12181a082a8fcd3842fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85960916"
---
# <a name="azure-batch-runtime-environment-variables"></a>Variáveis do ambiente de runtime do Azure Batch

O [serviço Azure Batch](https://azure.microsoft.com/services/batch/) define as seguintes variáveis ambientais nos nós computacional. Pode referenciar estas variáveis ambientais nas linhas de comando de tarefas e nos programas e scripts executados pelas linhas de comando.

Para obter mais informações sobre a utilização de variáveis ambientais com o Batch, consulte [as definições de Ambiente para tarefas](./jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Visibilidade variável do ambiente

Estas variáveis ambientais são visíveis apenas no contexto do utilizador da **tarefa**, a conta do utilizador no nó ao abrigo do qual uma tarefa é executada. *Não* as verá se [ligar remotamente](./error-handling.md#connect-to-compute-nodes) a um nó de computação através do protocolo DRP (Remote Desktop Protocol) ou de Secure Shell (SSH) e as apresentar. Isto deve-se ao facto de a conta de utilizador utilizada na ligação remota não ser a mesma conta utilizada pela tarefa.

Para obter o valor atual de uma variável ambiental, lance `cmd.exe` num nó de computação Windows ou num `/bin/sh` nó Linux:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Expansão da linha de comando das variáveis ambientais

As linhas de comando executadas por tarefas nos nós de computação não funcionam sob uma concha. Portanto, estas linhas de comando não podem tirar partido de características de concha, tais como a expansão variável do ambiente (isto inclui o `PATH` ). Para tirar partido de tais características, deve **invocar a concha** na linha de comando. Por exemplo, lançar `cmd.exe` nos nóns computacional do Windows ou `/bin/sh` nos nóns Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Variáveis de ambiente

| Nome da variável                     | Descrição                                                              | Disponibilidade | Exemplo |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | O nome da conta Batch a que a tarefa pertence.                  | Todas as tarefas.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | A URL da conta Batch. | Todas as tarefas. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Um prefixo de todas as variáveis do ambiente do pacote de aplicações. Por exemplo, se a versão "FOO" da aplicação "FOO" for instalada numa piscina, a variável ambiente é AZ_BATCH_APP_PACKAGE_FOO_1 (no Linux) ou AZ_BATCH_APP_PACKAGE_FOO#1 (no Windows). AZ_BATCH_APP_PACKAGE_FOO_1 aponta para o local onde o pacote foi descarregado (uma pasta). Ao utilizar a versão padrão do pacote de aplicações, utilize a variável ambiente AZ_BATCH_APP_PACKAGE sem os números da versão. Se no Linux, e o nome do pacote de aplicações for "Agent-linux-x64" e a versão for "1.1.46.0, o nome do ambiente é na verdade: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, utilizando sublinhados e minúsculas. Aceda [aqui](./batch-application-packages.md#execute-the-installed-applications) para obter mais detalhes. | Qualquer tarefa com um pacote de aplicações associado. Também disponível para todas as tarefas se o próprio nó tiver pacotes de aplicação. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) ou AZ_BATCH_APP_PACKAGE_FOO#1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Um símbolo de autenticação que dá acesso a um conjunto limitado de operações de serviço batch. Esta variável ambiente só está presente se os [conjuntos de autenticaçãoTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) forem definidos quando a [tarefa for adicionada](/rest/api/batchservice/task/add#request-body). O valor simbólico é utilizado nas APIs do lote como credenciais para criar um cliente Batch, como no [BatchClient.Open() .NET API](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Todas as tarefas. | Ficha de acesso OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Um diretório no quadro [de trabalho][files_dirs] da tarefa em que os certificados são armazenados para os nós de computação Linux. Esta variável de ambiente não se aplica aos nós de computação do Windows.                                                  | Todas as tarefas.   |  /mnt/lote/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | A lista de nós que são atribuídos a uma [tarefa multi-instância][multi_instance] no formato `nodeIP,nodeIP` . | Várias instâncias primárias e subtascos. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Especifica se o nó atual é o nó principal para uma [tarefa de vários instâncias][multi_instance]. Os valores possíveis são `true` `false` e.| Várias instâncias primárias e subtascos. | `true` |
| AZ_BATCH_JOB_ID                 | O ID do trabalho ao qual a tarefa pertence. | Todas as tarefas, exceto iniciar a tarefa. | lotejob001 |
| AZ_BATCH_JOB_PREP_DIR           | O caminho completo do diretório de [tarefas de][files_dirs] preparação de trabalho no nó. | Todas as tarefas, exceto iniciar tarefa e tarefa de preparação de trabalho. Só disponível se o trabalho estiver configurado com uma tarefa de preparação de emprego. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | O caminho completo da tarefa de preparação do trabalho [no][files_dirs] nó. | Todas as tarefas, exceto iniciar tarefa e tarefa de preparação de trabalho. Só disponível se o trabalho estiver configurado com uma tarefa de preparação de emprego. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | O endereço IP e a porta do nó de computação em que funciona a tarefa principal de uma [tarefa de vários instâncias.][multi_instance] Não utilize a porta aqui especificada para comunicação MPI ou NCCL - está reservada para o serviço Azure Batch. Utilize a variável MASTER_PORT em vez disso, quer definindo-a com um valor passado através do argumento da linha de comando (a porta 6105 é uma boa escolha predefinitivo), quer utilizando o valor que a AML define se o fizer. | Várias instâncias primárias e subtascos. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | A identificação do nó a que a tarefa está atribuída. | Todas as tarefas. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Se, `true` o nó atual é um nó dedicado. Se, `false` é um nó de baixa [prioridade.](batch-low-pri-vms.md) | Todas as tarefas. | `true` |
| AZ_BATCH_NODE_LIST              | A lista de nós que são atribuídos a uma [tarefa multi-instância][multi_instance] no formato `nodeIP;nodeIP` . | Várias instâncias primárias e subtascos. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | O caminho completo do sistema de suporte do sistema de [ficheiros](virtual-file-mount.md) de nível de nó onde residem todos os diretórios de montagem. As partilhas de ficheiros windows utilizam uma letra de unidade, por isso, para o Windows, a unidade de montagem faz parte dos dispositivos e das unidades.  |  Todas as tarefas, incluindo a tarefa inicial, têm acesso ao utilizador, dado que o utilizador está ciente das permissões de montagem para o diretório montado. | Em Ubuntu, por exemplo, a localização é: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | O caminho completo da raiz de todos os [diretórios do Lote][files_dirs] no nó. | Todas as tarefas. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | O caminho completo do [diretório compartilhado][files_dirs] no nó. Todas as tarefas que executam num nó têm acesso a este diretório. As tarefas que executam noutros nos dois não têm acesso remoto a este diretório (não é um diretório de rede "partilhado"). | Todas as tarefas. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | O caminho completo do diretório de [tarefas ini][files_dirs] por parte do nó. | Todas as tarefas. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | O ID do conjunto no qual a tarefa é executada. | Todas as tarefas. | batchpool001 |
| AZ_BATCH_TASK_DIR               | O caminho completo do diretório de [tarefas][files_dirs] no nó. Este diretório contém o `stdout.txt` e `stderr.txt` para a tarefa, e o AZ_BATCH_TASK_WORKING_DIR. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | O ID da tarefa atual. | Todas as tarefas, exceto iniciar a tarefa. | tarefa001 |
| AZ_BATCH_TASK_SHARED_DIR | Um percurso de diretório idêntico para a tarefa primária e cada subtasco de uma [tarefa multi-instância][multi_instance]. O caminho existe em cada nó em que a tarefa de vários instâncias é executado, e é lido/escrito acessível aos comandos de tarefa em execução nesse nó (tanto o comando de [coordenação][coord_cmd] como o comando de [aplicação).][app_cmd] Subtarefas ou uma tarefa primária que execute em outros nosdes não têm acesso remoto a este diretório (não é um diretório de rede "compartilhado"). | Várias instâncias primárias e subtascos. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | O caminho completo do [diretório de trabalho][files_dirs] da tarefa no nó. A tarefa atualmente em execução tem acesso de leitura/escrita a este diretório. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | A lista de nós e o número de núcleos por nó que são atribuídos a uma [tarefa multi-instância][multi_instance]. Os nódes e os núcleos estão listados no formato `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, quando o número de nós é seguido por um ou mais endereços IP de nó e o número de núcleos para cada um. |  Várias instâncias primárias e subtascos. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: ./files-and-directories.md
[multi_instance]: ./batch-mpi.md
[coord_cmd]: ./batch-mpi.md#coordination-command
[app_cmd]: ./batch-mpi.md#application-command
