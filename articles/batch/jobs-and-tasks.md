---
title: Empregos e tarefas em Azure Batch
description: Conheça os empregos e tarefas e como são usados num fluxo de trabalho do Azure Batch do ponto de vista do desenvolvimento.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 5120b76f34e81c2ceeba88767a656b5ee0d40c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85955374"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Empregos e tarefas em Azure Batch

Em Azure Batch, uma *tarefa* representa uma unidade de cálculo. Um *trabalho* é uma coleção destas tarefas. Mais sobre empregos e tarefas, e como são usados num fluxo de trabalho do Azure Batch, é descrito abaixo.

## <a name="jobs"></a>Tarefas

Um trabalho é uma coleção de tarefas. Gere de que forma é que a computação é realizada pelas respetivas tarefas nos nós de computação de um conjunto.

Um trabalho especifica a [piscina](nodes-and-pools.md#pools) em que o trabalho deve ser executado. Pode criar um novo conjunto para cada trabalho ou utilizar um conjunto para muitos trabalhos. Pode criar um conjunto para cada trabalho associado a uma agenda de trabalho ou para todos os trabalhos associados a uma agenda de trabalho.

### <a name="job-priority"></a>Prioridade dos trabalhos

Pode atribuir uma prioridade de trabalho opcional aos empregos que cria. O serviço Batch utiliza o valor de prioridade do trabalho para determinar a ordem de agendamento dos trabalhos numa conta (não deve ser confundido com [trabalhos agendados](#scheduled-jobs)). Os valores de prioridade variam entre -1000 a 1000, sendo -1000 a prioridade mais baixa e 1000 a prioridade mais alta. Para atualizar a prioridade de uma tarefa, realize a operação [Atualizar as propriedades de uma tarefa](/rest/api/batchservice/job/update) (REST do Batch) ou ao modifique a propriedade [CloudJob.Priority](/dotnet/api/microsoft.azure.batch.cloudjob) (NET do Batch).

Dentro da mesma conta, os trabalhos de prioridade mais alta têm precedência de agendamento sobre os de prioridade mais baixa. Um trabalho com um valor de prioridade superior numa conta não tem precedência de agendamento sobre outro trabalho com um valor de prioridade inferior numa conta diferente. As tarefas de trabalhos de prioridade inferior que já estão em execução não serão substituídas.

O agendamento de tarefas no âmbito dos conjuntos é independente. Entre conjuntos diferentes, não é garantido que um trabalho de prioridade superior seja agendado primeiro se o respetivo conjunto associado tiver poucos nós inativos. No mesmo conjunto, as tarefas com o mesmo nível de prioridade têm as mesmas hipóteses de serem agendadas.

### <a name="job-constraints"></a>Restrições de emprego

Pode utilizar restrições de trabalhos para especificar determinados limites para os trabalhos:

- Pode definir um **tempo máximo cronometrado**, para que, se um trabalho for executado durante mais tempo do que o tempo máximo cronometrado especificado, o trabalho e as respetivas tarefas são terminados.
- Pode especificar o **número máximo de tarefas** como restrição, incluindo se uma tarefa é sempre novamente julgada ou nunca novamente. Re-julgar uma tarefa significa que, se a tarefa falhar, será requeada para ser executada novamente.

### <a name="job-manager-tasks-and-automatic-termination"></a>Tarefas de gestor de emprego e rescisão automática

A aplicação cliente pode adicionar tarefas a um trabalho ou pode especificar uma [tarefa de gestão de trabalhos](#job-manager-task). As tarefas de gestão de trabalhos contêm as informações necessárias para criar as tarefas necessárias para um trabalho, sendo a tarefa de gestão de trabalhos executada num dos nós de computação do conjunto. A tarefa de gestor de emprego é tratada especificamente pela Batch; é feito em fila assim que o trabalho é criado e é reiniciado se falhar. É necessária uma tarefa de gestor de emprego para os postos de trabalho criados por um [horário de trabalho](#scheduled-jobs), porque é a única forma de definir as tarefas antes de o trabalho ser instantâneo.

Por predefinição, os trabalhos permanecem no estado ativo quando todas as tarefas dentro do trabalho estiverem concluídas. Pode alterar este comportamento para que o trabalho seja automaticamente terminado quando todas as tarefas no trabalho estiverem concluídas. Defina a propriedade **onAllTasksComplete** da tarefa ([OnAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob) no Batch .NET) para *terminatejob*, para terminar automaticamente o trabalho quando todas as respetivas tarefas estiverem no estado de conclusão.

O serviço Batch considera um trabalho *sem* tarefas para ter todas as suas tarefas concluídas. Por conseguinte, esta opção é frequentemente utilizada com uma [tarefa de gestor de trabalhos](#job-manager-task). Se pretender utilizar a terminação automática do trabalho sem um gestor de trabalhos, deve definir inicialmente uma nova propriedade **onAllTasksComplete** do trabalho como *noaction* e, em seguida, configurá-la para *terminatejob* (terminar o trabalho) apenas depois de terminar de adicionar tarefas ao trabalho.

### <a name="scheduled-jobs"></a>Tarefas agendadas

[Agendas de tarefas](/rest/api/batchservice/jobschedule) permitem-lhe criar tarefas recorrentes no âmbito do serviço Batch. Uma agenda de tarefas especifica quando executar tarefas e inclui as especificações das tarefas a executar. Pode especificar a duração do horário (quanto tempo e quando o horário está em vigor) e a frequência com que os empregos são criados durante o período programado.

## <a name="tasks"></a>Tarefas

As tarefas são uma unidade de computação que estão associadas a um trabalho. São executadas num nó. As tarefas são atribuídas a um nó para execução ou são colocadas na fila até que um nó fique livre. Resumindo, as tarefas executam um ou mais programas ou scripts num nó de computação para fazer os trabalhos necessários.

Quando cria uma tarefa, pode especificar:

- A **linha de comandos** da tarefa. Esta é a linha de comandos que executa a aplicação ou script no nó de computação.

    É importante notar que a linha de comando não funciona sob uma concha. Por conseguinte,-não é possível tirar partido nativamente das funcionalidades de shell, como a expansão da [variável de ambiente](#environment-settings-for-tasks) expansão (inclui `PATH`). Para tirar partido de tais funcionalidades, deve invocar a concha na linha de comando, como por exemplo, lançando `cmd.exe` nos nós windows ou no `/bin/sh` Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Se as suas tarefas tiverem de executar uma aplicação ou script que não esteja no `PATH` ou de referenciar variáveis de ambiente, invoque a shell explicitamente na linha de comandos das tarefas.
- Os **ficheiros de recursos** que contêm os dados a serem processados. Estes ficheiros são copiados automaticamente para o nó a partir do Armazenamento de blobs numa conta de Armazenamento do Azure antes de a linha de comando da tarefa ser executada. Para obter mais informações, consulte [Iniciar a tarefa](#start-task) e [ficheiros e diretórios](files-and-directories.md).
- As **variáveis de ambiente** de que a aplicação precisa. Para obter mais informações, consulte [as definições do Ambiente para tarefas](#environment-settings-for-tasks).
- As **restrições** sob as quais a computação deve ocorrer. Por exemplo, as restrições incluem o tempo máximo dentro do qual a tarefa pode ser executada, o número máximo de vezes que uma tarefa falhada deve ser repetida e o tempo máximo durante o qual os ficheiros no diretório de trabalho da tarefa são retidos.
- **Pacotes de aplicações** para implementar o nó de computação no qual a tarefa está agendada para ser executada. Os [pacotes de aplicações](batch-application-packages.md) fornecem uma implementação simplificada e o controlo de versões das aplicações que as suas tarefas executam. Os pacotes de aplicações ao nível das tarefas são particularmente úteis em ambientes de conjunto partilhado, em que as diferentes tarefas são executadas num conjunto, e o conjunto não é eliminado quando um trabalho estiver concluído. Se o trabalho tiver menos tarefas do que nós no conjunto, os pacotes de aplicações de tarefas podem minimizar a transferência de dados, uma vez que a aplicação é implementada apenas nos nós que executam tarefas.
- Referência da **imagem de contentor** no Docker Hub ou num registo privado e as definições adicionais para criar um contentor do Docker no qual a tarefa seja executada no nó. Apenas pode especificar estas informações se o agrupamento tiver uma configuração de contentor.

> [!NOTE]
> O máximo de vida útil de uma tarefa, a partir do momento em que é adicionado ao trabalho até ao momento em que termina, é de 180 dias. As tarefas concluídas persistem durante 7 dias; os dados relativos a tarefas não concluídas no prazo máximo de vida não são acessíveis.

Além das tarefas que define para executar a computação num nó, várias tarefas especiais também são fornecidas pelo serviço Batch:

- [Tarefa de início](#start-task)
- [Tarefa do gestor de tarefas](#job-manager-task)
- [Tarefas de preparação e de lançamento da tarefa](#job-preparation-and-release-tasks)
- [Tarefas de várias instâncias](#multi-instance-task)
- [Dependências de tarefas](#task-dependencies)

### <a name="start-task"></a>Tarefa de início

Ao associar uma tarefa de início a um conjunto, pode preparar o ambiente de funcionamento dos respetivos nós. Por exemplo, pode realizar ações como instalar as aplicações que as suas tarefas vão executar ou começar processos em segundo plano. A tarefa inicial é sempre que um nó começa, enquanto permanecer na piscina. Isto inclui quando o nó é adicionado pela primeira vez à piscina e quando é reiniciado ou reesimageado.

Uma das principais vantagens da tarefa de início é o facto de poder conter todas as informações necessárias para configurar um nó de computação e instalar as aplicações necessárias para a execução de tarefas. Por conseguinte, aumentar o número de nós num conjunto é tão simples como especificar a nova contagem de nó de destino. A tarefa inicial fornece as informações necessárias para que o serviço Batch configuure os novos nós e os prepare para aceitar tarefas.

À semelhança de qualquer tarefa do Batch, pode especificar uma lista de ficheiros de recursos no [Armazenamento do Azure](../storage/index.yml), além de uma linha de comandos para ser executada. O serviço do Batch começa por copiar os ficheiros de recursos para o nó a partir do Armazenamento do Azure e, depois, executa a linha de comandos. Numa tarefa de início de conjunto, a lista de ficheiros contém, geralmente, a aplicação de tarefa e as respetivas dependências.

Contudo, a tarefa de início também pode incluir dados de referência que todas as tarefas em execução no nó de computação podem utilizar. Por exemplo, uma linha de comando de uma tarefa inicial poderia executar uma `robocopy` operação para copiar ficheiros de aplicações (que foram especificados como ficheiros de recursos e descarregados para o nó) do [diretório](files-and-directories.md) de trabalho da tarefa inicial para a pasta **partilhada** e, em seguida, executar um MSI ou `setup.exe` .

Normalmente, é aconselhável que o serviço Batch aguarde pela conclusão da tarefa de início antes de considerar o nó como estando pronto para receber aplicações, mas esta definição pode ser configurada.

Se uma tarefa inicial falhar num nó de computação, o estado do nó é atualizado para refletir a falha e não são atribuídas tarefas ao nó. Uma tarefa de início pode falhar se houver um problema ao copiar os respetivos ficheiros de recursos do armazenamento ou se o processo executado pela respetiva linha de comandos devolver um código de saída diferente de zero.

Se adicionar ou atualizar a tarefa de início para um agrupamento existente, tem de reiniciar os nós de computação para que a tarefa de início seja aplicada aos mesmos.

>[!NOTE]
> O Batch limita o tamanho total de uma tarefa de início, o que inclui os ficheiros de recurso e as variáveis de ambiente. Se precisar de reduzir o tamanho de uma tarefa de início, pode usar uma de duas abordagens:
>
> 1. Pode utilizar pacotes de aplicações para distribuir aplicações ou dados para todos os nós do agrupamento do Batch. Para obter mais informações sobre os pacotes de aplicações, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).
> 2. Pode criar manualmente um arquivo zipado que contenha os seus ficheiros de aplicação. Carregue o arquivo zipado para o Armazenamento do Azure como um blob. Especifique este arquivo como um ficheiro de recursos para a tarefa de início. Antes de executar a linha de comandos da tarefa de início, descomprima o arquivo a partir da linha de comandos. 
>
>    Para descomprimir o arquivo, pode utilizar uma ferramenta de arquivos à sua escolha. Tem de incluir a ferramenta utilizada para descomprimir o arquivo como um ficheiro de recursos para a tarefa de início.

### <a name="job-manager-task"></a>Tarefa do gestor de tarefas

Normalmente, usa uma tarefa de gestor de emprego para controlar e/ou monitorizar a execução de emprego. Por exemplo, as tarefas de gestor de emprego são muitas vezes usadas para criar e submeter as tarefas para um trabalho, determinar tarefas adicionais a executar e determinar quando o trabalho está concluído.

No entanto, as tarefas de gestor de trabalhos não estão confinadas a estas atividades. É uma tarefa completa que pode executar quaisquer ações que sejam necessárias para o trabalho. Por exemplo, uma tarefa de gestor de trabalhos pode transferir um ficheiro especificado como um parâmetro, analisar o conteúdo desse ficheiro e submeter mais tarefas com base nesse conteúdo.

A tarefa de gestor de trabalhos é iniciada antes de todas as outras tarefas. Proporciona as seguintes funcionalidades:

- É submetida automaticamente como uma tarefa pelo serviço de Batch quando a tarefa é criada.
- É agendada para execução antes das outras tarefas numa tarefa.
- O seu nó associado é o último a ser removido de um conjunto quando este está a ser reduzido.
- A sua terminação pode ser vinculada à terminação de todas as tarefas na tarefa.
- A tarefa do gestor de trabalhos recebe a prioridade mais elevada quando tem de ser reiniciada. Se um nó inativo não estiver disponível, o serviço Batch pode terminar uma das outras tarefas em execução no conjunto, de modo a criar espaço para a tarefa do gestor de trabalhos ser executada.
- Uma tarefa do gestor de tarefas numa tarefa não tem prioridade sobre as tarefas de outras tarefas. No âmbito das tarefas, apenas as prioridades ao nível da tarefa são respeitadas.

### <a name="job-preparation-and-release-tasks"></a>Tarefas de preparação e de lançamento da tarefa

O Batch fornece tarefas de preparação de emprego para a configuração da execução pré-job e tarefas de libertação de emprego para manutenção ou limpeza pós-trabalho.

Uma tarefa de preparação de emprego funciona em todos os nós de computação que estão programados para executar tarefas, antes de qualquer outra tarefa de trabalho ser executada. Por exemplo, pode utilizar uma tarefa de preparação de emprego para copiar dados que são partilhados por todas as tarefas, mas que são exclusivos do trabalho.

Quando um trabalho estiver concluído, uma tarefa de libertação de emprego funciona em cada nó na piscina que executou pelo menos uma tarefa. Por exemplo, uma tarefa de libertação de emprego pode eliminar dados que foram copiados pela tarefa de preparação do trabalho, ou pode comprimir e carregar dados de registo de diagnóstico.

Quer a tarefa de preparação de trabalhos, quer a de libertação de trabalhos, permitem-lhe especificar uma linha de comandos a ser executada quando a tarefa é invocada. Oferecem funcionalidades como, por exemplo, transferência de ficheiros, execução elevada, variáveis de ambiente personalizadas, duração de execução máxima e tempo de retenção de ficheiros.

Para obter mais informações sobre as tarefas de lançamento e de preparação da tarefa, consulte [Executar tarefas de preparação e de conclusão da tarefa em nós de computação do Azure Batch](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tarefa de várias instâncias

Um [tarefa de várias instâncias](batch-mpi.md) é uma tarefa que está configurada para ser executada simultaneamente em mais do que um nó de computação. Com tarefas multi-instâncias, pode ativar cenários de computação de alto desempenho que requerem um grupo de nós de computação que são alocados em conjunto para processar uma única carga de trabalho, como interface de passagem de mensagem (MPI).

Para ver um debate detalhado sobre a execução de trabalhos de MPI no Batch com a biblioteca .NET do Batch, veja [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch (Utilizar tarefas de várias instâncias para executar aplicações de Interface de Passagem de Mensagens (MPI) no Azure Batch)](batch-mpi.md).

### <a name="task-dependencies"></a>Dependências de tarefas

[As dependências de tarefas](batch-task-dependencies.md), como o nome indica, permitem especificar que uma tarefa depende da conclusão de outras tarefas antes da sua execução. Esta funcionalidade fornece suporte para situações em que uma tarefa “a jusante” consome o resultado de uma tarefa “a montante” ou quando uma tarefa a montante efetua alguma inicialização que seja necessária para uma tarefa a jusante.

Para utilizar esta função, tem primeiro de [ativar as dependências de tarefas](batch-task-dependencies.md#enable-task-dependencies
) no seu trabalho em Batch. Em seguida, para cada tarefa que dependa de outra (ou de muitas outras), especifique as tarefas das quais essa tarefa depende.

Com as dependências de tarefas, pode configurar cenários como os seguintes:

- *taskB* depende da *taskA* *(a tarefaB* não iniciará a execução até *que a tarefa A* esteja concluída).
- *taskC* depende tanto *da tarefa A* como da *taskB*.
- *taskD* depende de uma série de tarefas, tais como tarefas *1* a *10,* antes de executar.

Para obter mais detalhes, consulte [as dependências de tarefas em Azure Batch](batch-task-dependencies.md) e a amostra de código [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) no repositório de [amostras de lote de](https://github.com/Azure-Samples/azure-batch-samples) azul GitHub.

### <a name="environment-settings-for-tasks"></a>Definições de ambiente para tarefas

Cada tarefa executada pelo serviço Batch tem acesso às variáveis de ambiente que define nos nós de computação. Isto inclui variáveis ambientais definidas pelo serviço Batch (variáveis de ambiente[definidas pelo serviço](./batch-compute-node-environment-variables.md) e personalizadas que pode definir para as suas tarefas. As aplicações e os scripts que as tarefas executam têm acesso a estas variáveis de ambiente durante a execução.

Pode definir variáveis de ambiente personalizadas ao nível da tarefa ou do trabalho ao preencher a propriedade *definições de ambiente* dessas entidades. Para obter mais detalhes, consulte a [operação Add a job](/rest/api/batchservice/task/add?)[ Batch REST API), ou o [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) e [CloudJob.CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) propriedades em Batch .NET.

O serviço ou aplicação cliente pode obter as variáveis de ambiente de uma tarefa, quer definidas pelo serviço, quer personalizadas, através da operação [Get information about a task](/rest/api/batchservice/task/get) (REST do Batch) (Obter informações sobre uma tarefa) ou ao aceder à propriedade [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) (.NET do Batch). Os processos em execução num nó de computação podem aceder a estas e outras variáveis de ambiente no nó, por exemplo, com a sintaxe familiar `%VARIABLE_NAME%` (Windows) ou `$VARIABLE_NAME` (Linux).

Pode obter uma lista completa de todas as variáveis de ambiente definidas pelo serviço em [Compute node environment variables (Variáveis de ambiente de nó de computação)](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais [sobre ficheiros e diretórios.](files-and-directories.md)
