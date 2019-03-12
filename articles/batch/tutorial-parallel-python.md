---
title: Executar uma carga de trabalho paralela – Azure Batch Python
description: Tutorial – Processar ficheiros de multimédia em paralelo com o ffmpeg no Azure Batch através da biblioteca de cliente Python para o Batch
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 286bc73cb7226d95c1e46fc51ae5999ea27d44ad
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535688"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>Tutorial: Executar uma carga de trabalho paralela com o Azure Batch com a API Python

Utilize o Azure Batch para executar trabalhos de lote de computação de alto desempenho (HPC) e paralelos em larga escala de forma eficaz no Azure. Este tutorial guia-o por um exemplo Python de execução de uma carga de trabalho paralela com o Batch. Obteve mais informações sobre um fluxo de trabalho de aplicações do Batch comum e como interagir programaticamente com recursos do Batch e de Armazenamento. Saiba como:

> [!div class="checklist"]
> * Autenticar com contas do Batch e de Armazenamento
> * Carregar ficheiros de entrada para o Armazenamento
> * Criar um conjunto de nós de computação para executar uma aplicação
> * Criar um trabalho e tarefas para processar os ficheiros de entrada
> * Monitorizar a execução de tarefas
> * Obter ficheiros de saída

Neste tutorial, vai converter ficheiros de multimédia MP4 em paralelo com o formato MP3 através da ferramenta de código aberto [ffmpeg](https://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Python versão 2.7, 3.3 ou posterior](https://www.python.org/downloads/)

* Gestor de pacotes [pip](https://pip.pypa.io/en/stable/installing/)

* Uma conta do Batch do Azure e uma conta de Armazenamento do Microsoft Azure associada. Para criar estas contas, veja os inícios rápidos do Batch no [portal do Azure](quick-create-portal.md) ou na [CLI do Azure](quick-create-cli.md).

## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>Transferir e executar o exemplo

### <a name="download-the-sample"></a>Transferir o exemplo

[Transfira ou clone a aplicação de exemplo](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial) a partir do GitHub. Para clonar o repositório de aplicações de exemplo com um cliente Git, utilize o seguinte comando:

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

Navegue para o diretório que contém o ficheiro `batch_python_tutorial_ffmpeg.py`.

No ambiente do Python, instale os pacotes necessários através de `pip`.

```bash
pip install -r requirements.txt
```

Abra o ficheiro `config.py`. Atualize as cadeias de credenciais das contas do Batch e de armazenamento com os valores exclusivos das suas contas. Por exemplo:


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>Executar a aplicação

Para executar o script:

```
python batch_python_tutorial_ffmpeg.py
```

Quando executar a aplicação de exemplo, o resultado da consola é semelhante ao seguinte. Durante a execução, ocorre uma pausa em `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` enquanto os nós de computação do conjunto são iniciados. 
   
```
Sample start: 11/28/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 11/28/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Aceda à conta do Batch no portal do Azure para monitorizar o conjunto, os nós de computação, o trabalho e as tarefas. Por exemplo, para ver um mapa érmico dos nós de computação no conjunto, clique em **Conjuntos** > *LinuxFFmpegPool*.

Quando as tarefas estiverem em execução, o mapa térmico é semelhante ao seguinte:

![Mapa térmico do conjunto](./media/tutorial-parallel-python/pool.png)

O tempo de execução normal é de aproximadamente **5 minutos** quando executa a aplicação na configuração predefinida. A criação do conjunto demora mais tempo. 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Rever o código

As secções seguintes dividem a aplicação de exemplo nos passos que executa para processar uma carga de trabalho no serviço Batch. Veja o código Python ao ler o resto deste artigo, uma vez que nem todas as linhas de código no exemplo são abordadas.

### <a name="authenticate-blob-and-batch-clients"></a>Autenticar clientes Blob e Batch

Para interagir com uma conta de armazenamento, a aplicação utiliza o pacote [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) para criar um objeto [BlockBlobService](/python/api/azure.storage.blob.blockblobservice.blockblobservice).

```python
blob_client = azureblob.BlockBlobService(
    account_name=_STORAGE_ACCOUNT_NAME,
    account_key=_STORAGE_ACCOUNT_KEY)
```

A aplicação cria um objeto [BatchServiceClient](/python/api/azure.batch.batchserviceclient) para criar e gerir conjuntos, trabalhos e tarefas no serviço Batch. O cliente Batch no exemplo utiliza a autenticação de chave partilhada. O Batch também suporta a autenticação através do [Azure Active Directory](batch-aad-auth.md), para autenticar utilizadores individuais ou uma aplicação autónoma.

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
    _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>Carregar ficheiros de entrada

A aplicação utiliza a referência `blob_client` para criar um contentor de armazenamento para os ficheiros MP4 de entrada e um contentor para o resultado da tarefa. Em seguida, chama a função `upload_file_to_container` para carregar os ficheiros MP4 no diretório `InputFiles` local para o contentor. Os ficheiros no armazenamento estão definidos como objetos [ResourceFile](/python/api/azure.batch.models.resourcefile) que o Batch pode transferir mais tarde para os nós de computação.

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []
    
for folder, subs, files in os.walk(os.path.join(sys.path[0],'./InputFiles/')):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks. 
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>Criar um conjunto de nós de computação

Em seguida, o exemplo cria um conjunto de nós de computação na conta do Batch, com uma chamada para `create_pool`. Esta função definida utiliza a classe [PoolAddParameter](/python/api/azure.batch.models.pooladdparameter) do Batch para definir o número de nós, o tamanho da VM e uma configuração de conjuntos. Aqui, um [VirtualMachineConfiguration](/python/api/azure.batch.models.virtualmachineconfiguration) objeto Especifica um [ImageReference](/python/api/azure.batch.models.imagereference) a uma imagem de Ubuntu Server 18.04 LTS publicada no Azure Marketplace. O Batch suporta inúmeras imagens da VM no Azure Marketplace, bem como imagens da VM personalizadas.

O número de nós e o tamanho da VM são definidos através de constantes definidas. O Batch suporta nós dedicados e [nós de prioridade baixa](batch-low-pri-vms.md), e pode utilizar um ou ambos nos conjuntos. Os nós dedicados estão reservados para o conjunto. Os nós de prioridade baixa são disponibilizados a um preço reduzido a partir da capacidade excedente da VM no Azure. Os nós de prioridade baixa ficam indisponíveis se o Azure não tiver capacidade suficiente. Por predefinição, o exemplo cria um conjunto com apenas 5 nós de prioridade baixa no tamanho *Standard_A1_v2*. 

Além das propriedades dos nós físicos, esta configuração de conjuntos inclui um objeto [StartTask](/python/api/azure.batch.models.starttask). O StartTask é executado em cada nó à medida que esse nó se associa ao conjunto, e sempre que um nó for reiniciado. Neste exemplo, o StartTask executa comandos da shell Bash para instalar o pacote ffmpeg e as dependências nos nós.

O método [pool.add](/python/api/azure.batch.operations.pooloperations) submete o conjunto para o serviço Batch.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="18.04-LTS",
            version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>Criar uma tarefa

Um trabalho do Batch especifica um conjunto para executar tarefas e definições opcionais, como uma prioridade e agenda para o trabalho. O exemplo cria um trabalho com uma chamada para `create_job`. Esta função definida utiliza a classe [JobAddParameter](/python/api/azure.batch.models.jobaddparameter) para criar um trabalho no conjunto. O método [job.add](/python/api/azure.batch.operations.joboperations) submete o conjunto para o serviço Batch. Inicialmente, o trabalho não tem tarefas.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Criar tarefas

A aplicação cria tarefas no trabalho com uma chamada para `add_tasks`. Esta função definida cria uma lista de objetos de tarefas através da classe [TaskAddParameter](/python/api/azure.batch.models.taskaddparameter). Cada tarefa executa o ffmpeg para processar um objeto `resource_files` de entrada através de um parâmetro `command_line`. O ffmpeg foi instalado anteriormente em cada nó quando o conjunto foi criado. Aqui, a linha de comandos executa o ffmpeg para converter cada ficheiro MP4 (vídeo) de entrada num ficheiro MP3 (áudio).

O exemplo cria um objeto [OutputFile](/python/api/azure.batch.models.outputfile) para o ficheiro MP3 depois de executar a linha de comandos. Os ficheiros de saída de cada tarefa (um, neste caso) são carregados para um contentor na conta de armazenamento associada através da propriedade `output_files` da tarefa.

Em seguida, a aplicação adiciona tarefas ao trabalho com o método [task.add_collection](/python/api/azure.batch.operations.taskoperations), o que as coloca em fila para serem executadas nos nós de computação. 

```python
tasks = list()

for idx, input_file in enumerate(input_files): 
    input_file_path=input_file.file_path
    output_file_path="".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(
            file_pattern=output_file_path,
            destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(
                    container_url=output_container_sas_url)),
            upload_options=batchmodels.OutputFileUploadOptions(
                upload_condition=batchmodels.OutputFileUploadCondition.task_success))]
        )
    )
batch_service_client.task.add_collection(job_id, tasks)
```    

### <a name="monitor-tasks"></a>Monitorizar tarefas

Quando as tarefas forem adicionadas a um trabalho, o Batch coloca automaticamente em fila e agenda-as para execução nos nós de computação no conjunto associado. Com base nas definições especificadas, o Batch processa todas as tarefas de colocação em fila, agendamento, repetições e outras funções de administração de tarefas. 

Existem várias abordagens à monitorização da execução de tarefas. A função `wait_for_tasks_to_complete` neste exemplo utiliza o objeto [TaskState](/python/api/azure.batch.models.taskstate) para monitorizar as tarefas num determinado estado, neste caso, o estado de concluído, dentro de um limite de tempo.

```python
while datetime.datetime.now() < timeout_expiration:
    print('.', end='')
    sys.stdout.flush()
    tasks = batch_service_client.task.list(job_id)

    incomplete_tasks = [task for task in tasks if
                         task.state != batchmodels.TaskState.completed]
    if not incomplete_tasks:
        print()
        return True
    else:
        time.sleep(1)
...
```

## <a name="clean-up-resources"></a>Limpar recursos

Depois de executar as tarefas, a aplicação elimina automaticamente o contentor de armazenamento de entrada que criou e dá-lhe a opção de eliminar o conjunto e o trabalho do Batch. As classes [JobOperations](/python/api/azure.batch.operations.joboperations) e [PoolOperations](/python/api/azure.batch.operations.pooloperations) do BatchClient têm métodos de eliminação, que são chamados se confirmar a eliminação. Apesar de os próprios trabalhos e tarefas não lhe serem cobrados, os nós de computação são cobrados. Assim, recomendamos que atribua conjuntos apenas conforme necessário. Quando eliminar o conjunto, todos os resultados da tarefa nos nós são eliminados. No entanto, os ficheiros de entrada e saída permanecem na conta de armazenamento.

Quando já não forem necessários, elimine o grupo de recursos, a conta do Batch e a conta de armazenamento. Para tal, no portal do Azure, selecione o grupo de recursos da conta do Batch e clique em **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Autenticar com contas do Batch e de Armazenamento
> * Carregar ficheiros de entrada para o Armazenamento
> * Criar um conjunto de nós de computação para executar uma aplicação
> * Criar um trabalho e tarefas para processar os ficheiros de entrada
> * Monitorizar a execução de tarefas
> * Obter ficheiros de saída

Para obter mais exemplos de utilização da API Python para agendar e processar cargas de trabalho do Batch, veja os exemplos no GitHub.

> [!div class="nextstepaction"]
> [Exemplos de Python para o Batch](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

