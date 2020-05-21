---
title: Variáveis de ambiente de runtime de tarefas
description: Orientação variável de ambiente de execução de tarefas e referência para Azure Batch Analytics.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 2027716283ca4910f45ae3e32111896ef0045ce8
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726762"
---
# <a name="azure-batch-runtime-environment-variables"></a>Variáveis ambientais de tempo de execução do Lote Azure

O [serviço Azure Batch](https://azure.microsoft.com/services/batch/) define as seguintes variáveis ambientais nos nódosos de cálculo. Pode fazer referência a estas variáveis ambientais nas linhas de comando de tarefas, e nos programas e scripts executados pelas linhas de comando.

Para obter mais informações sobre a utilização de variáveis ambientais com o Batch, consulte [as definições do Ambiente para tarefas](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Visibilidade variável ambiental

Estas variáveis ambientais são visíveis apenas no contexto do utilizador da **tarefa,** a conta de utilizador no nó sob o qual uma tarefa é executada. *Não* as verá se [ligar remotamente](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) a um nó de computação através do protocolo DRP (Remote Desktop Protocol) ou de Secure Shell (SSH) e as apresentar. Isto deve-se ao facto de a conta de utilizador utilizada na ligação remota não ser a mesma conta utilizada pela tarefa.

Para obter o valor atual de uma variável ambiental, lance num nó de `cmd.exe` computação Windows ou `/bin/sh` num nó Linux:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Expansão da linha de comando de variáveis ambientais

As linhas de comando executadas por tarefas em nódeos computacionais não são executadas sob uma concha. Portanto, estas linhas de comando não podem aproveitar de forma nativa características de concha, tais como a expansão variável ambiental (isto inclui o `PATH` ). Para tirar partido de tais características, deve **invocar a concha** na linha de comando. Por exemplo, lançamento `cmd.exe` em nós de computação Windows ou em `/bin/sh` nós Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Variáveis de ambiente

| Nome da variável                     | Descrição                                                              | Disponibilidade | Exemplo |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | O nome da conta Batch a que a tarefa pertence.                  | Todas as tarefas.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | O URL da conta Batch. | Todas as tarefas. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Um prefixo de todas as variáveis ambientais de pacotes de aplicativos. Por exemplo, se a versão "FOO" da aplicação "FOO" for instalada numa piscina, a variável ambiental é AZ_BATCH_APP_PACKAGE_FOO_1 (no Linux) ou AZ_BATCH_APP_PACKAGE_FOO#1 (no Windows). AZ_BATCH_APP_PACKAGE_FOO_1 aponta para a localização que o pacote foi descarregado (uma pasta). Ao utilizar a versão padrão do pacote de aplicações, utilize a variável ambiente AZ_BATCH_APP_PACKAGE sem os números da versão. Se em Linux, e o nome do pacote de aplicação for "Agent-linux-x64" e a versão for "1.1.46.0, o nome do ambiente é na verdade: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, usando sublinhados e minúsculas. Aceda [aqui](https://docs.microsoft.com/azure/batch/batch-application-packages#execute-the-installed-applications) para obter mais detalhes. | Qualquer tarefa com um pacote de aplicativos associado. Também disponível para todas as tarefas se o nó em si tiver pacotes de aplicação. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) ou AZ_BATCH_APP_PACKAGE_FOO#1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Um símbolo de autenticação que dá acesso a um conjunto limitado de operações de serviço de Lote. Esta variável ambiental só está presente se as [definições de autenticaçãoTokenDefine](/rest/api/batchservice/task/add#authenticationtokensettings) forem definidas quando a [tarefa for adicionada](/rest/api/batchservice/task/add#request-body). O valor simbólico é usado nas APIs do Lote como credenciais para criar um cliente Batch, como no [BatchClient.Open() .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Todas as tarefas. | Ficha de acesso OAuth2 |
| AZ_BATCH_CERTIFICATES_DIR       | Um diretório no âmbito do diretório de trabalho de [tarefas][files_dirs] em que os certificados são armazenados para os nódos de computação Linux. Esta variável ambiental não se aplica aos nós de cálculo do Windows.                                                  | Todas as tarefas.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | A lista de nódosos que são atribuídos a uma [tarefa em várias instâncias][multi_instance] no formato `nodeIP,nodeIP` . | Tarefas primárias e subtarefas em várias instâncias. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Especifica se o nó atual é o nó principal para uma [tarefa em várias instâncias][multi_instance]. Os valores possíveis são `true` `false` e.| Tarefas primárias e subtarefas em várias instâncias. | `true` |
| AZ_BATCH_JOB_ID                 | O ID do trabalho ao qual a tarefa pertence. | Todas as tarefas exceto começar a tarefa. | lotejob001 |
| AZ_BATCH_JOB_PREP_DIR           | O percurso completo do diretório de [tarefas][files_dirs] de preparação de emprego no nó. | Todas as tarefas exceto iniciar tarefas e tarefas de preparação de emprego. Só disponível se o trabalho estiver configurado com uma tarefa de preparação de emprego. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Todo o percurso do [diretório][files_dirs] de trabalho de preparação de trabalho no nó. | Todas as tarefas exceto iniciar tarefas e tarefas de preparação de emprego. Só disponível se o trabalho estiver configurado com uma tarefa de preparação de emprego. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | O endereço IP e o porto do nó computacional em que se executa a principal tarefa de uma [tarefa em várias instâncias.][multi_instance] | Tarefas primárias e subtarefas em várias instâncias. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | A identificação do nó a que a tarefa é atribuída. | Todas as tarefas. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Sim, `true` o nó atual é um nó dedicado. Se, `false` é um nó de baixa [prioridade.](batch-low-pri-vms.md) | Todas as tarefas. | `true` |
| AZ_BATCH_NODE_LIST              | A lista de nódosos que são atribuídos a uma [tarefa em várias instâncias][multi_instance] no formato `nodeIP;nodeIP` . | Tarefas primárias e subtarefas em várias instâncias. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | O caminho completo do sistema de montagem do sistema de [ficheiros](virtual-file-mount.md) nível do nó local onde todos os diretórios de montagem residem. As partilhas de ficheiros do Windows utilizam uma letra de unidade, pelo que, para o Windows, a unidade de montagem faz parte de dispositivos e unidades.  |  Todas as tarefas, incluindo a tarefa inicial, têm acesso ao utilizador, dado que o utilizador está ciente das permissões de montagem para o diretório montado. | Em Ubuntu, por exemplo, a localização é:`/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | O caminho completo da raiz de todos os [diretórios][files_dirs] do Lote no nó. | Todas as tarefas. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | O caminho completo do [diretório partilhado][files_dirs] no nó. Todas as tarefas que executam num nó têm acesso a este diretório. As tarefas que executam noutros nós não têm acesso remoto a este diretório (não é um diretório de rede "partilhado"). | Todas as tarefas. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | O caminho completo do diretório de [tarefas instituantes][files_dirs] no nó. | Todas as tarefas. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | O ID do conjunto no qual a tarefa é executada. | Todas as tarefas. | lote001 |
| AZ_BATCH_TASK_DIR               | O caminho completo do diretório de [tarefas][files_dirs] no nó. Este diretório contém o `stdout.txt` e `stderr.txt` para a tarefa, e o AZ_BATCH_TASK_WORKING_DIR. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | O ID da tarefa atual. | Todas as tarefas exceto começar a tarefa. | tarefa001 |
| AZ_BATCH_TASK_SHARED_DIR | Um percurso direcional idêntico para a tarefa principal e cada subtarefa de uma [tarefa em várias instâncias.][multi_instance] O caminho existe em cada nó em que a tarefa de várias instâncias corre, e é lido/escrito acessível aos comandos de tarefa sintetizados nesse nó (tanto o comando de [coordenação][coord_cmd] como o comando de [aplicação).][app_cmd] As subtarefas ou uma tarefa primária que executa noutros nós não têm acesso remoto a este diretório (não é um diretório de rede "partilhado"). | Tarefas primárias e subtarefas em várias instâncias. | C:\user\tasks\workitems\multiinstânciasamplejob\job-1\multiinstânciasampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Todo o percurso do diretório de trabalho de [tarefas][files_dirs] no nó. A tarefa atualmente em curso tem acesso de leitura/escrita a este diretório. | Todas as tarefas. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | A lista de nós e o número de núcleos por nó que são atribuídos a uma [tarefa em várias instâncias.][multi_instance] Nóeos e núcleos estão listados no formato`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, quando o número de nós é seguido por um ou mais endereços IP do nó e o número de núcleos para cada um. |  Tarefas primárias e subtarefas em várias instâncias. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
