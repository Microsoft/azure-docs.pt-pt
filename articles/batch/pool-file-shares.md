---
title: Partilha de ficheiros do Azure para conjuntos do Azure Batch | Documentos da Microsoft
description: Como montar uma partilha de ficheiros do Azure a partir de nós de computação de um conjunto do Linux ou Windows no Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 1e9d039769e7fbcb9c2b7285aa727acd7322bcdf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103337"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Utilizar uma partilha de ficheiros do Azure com um conjunto do Batch

[Os ficheiros do Azure](../storage/files/storage-files-introduction.md) oferece totalmente geridos partilhas de ficheiros na cloud que estão acessíveis através do protocolo de bloco de mensagem de servidor (SMB). Este artigo fornece informações e exemplos de código para montar e a utilizar uma partilha de ficheiros do Azure em nós de computação do conjunto. Os exemplos de código utilizam o .NET do Batch e SDKs de Python, mas pode efetuar operações semelhantes com outras ferramentas e SDKs do Batch.

O batch fornece suporte de API nativa para usar os blobs de armazenamento do Azure para ler e escrever dados. No entanto, em alguns casos, que pode querer aceder a uma partilha de ficheiros do Azure do seu conjunto de nós de computação. Por exemplo, tem uma carga de trabalho de legado que depende de uma partilha de ficheiros SMB ou as suas tarefas tiverem acessar dados compartilhados ou produzir saída partilhados. 

## <a name="considerations-for-use-with-batch"></a>Considerações para utilização com o Batch

* Considere utilizar uma partilha de ficheiros do Azure quando tiver agrupamentos que executam um número relativamente baixo de tarefas paralelas. Reveja os [metas de desempenho e dimensionamento](../storage/files/storage-files-scale-targets.md) para determinar se os ficheiros do Azure (que utiliza uma conta de armazenamento do Azure) deve ser utilizados, dado o tamanho do conjunto esperado e o número de ficheiros de elemento. 

* Partilhas de ficheiros do Azure estão [económico](https://azure.microsoft.com/pricing/details/storage/files/) e pode ser configurado com dados de replicação para outra região são então globalmente redundante. 

* Pode montar uma partilha de ficheiros do Azure em simultâneo a partir de um computador no local.

* Consulte também gerais [considerações sobre planeamento](../storage/files/storage-files-planning.md) partilhas de ficheiros para o Azure.


## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

[Criar uma partilha de ficheiros](../storage/files/storage-how-to-create-file-share.md) numa conta de armazenamento que está ligada à sua conta do Batch, ou numa conta de armazenamento separada.

## <a name="mount-a-share-on-a-windows-pool"></a>Montar uma partilha de um agrupamento do Windows

Esta secção fornece os passos e exemplos de código para montar e utilizar um ficheiro do Azure partilham num conjunto de nós do Windows. Para obter informações adicionais, veja a [documentação](../storage/files/storage-how-to-use-files-windows.md) para montar um Azure partilha de ficheiros no Windows. 

No lote, precisa montar a partilha de cada vez que uma tarefa é executada num nó do Windows. Atualmente, não é possível manter a ligação de rede entre as tarefas em nós do Windows.

Por exemplo, incluir um `net use` comandos para montar a partilha de ficheiros como parte de cada linha de comandos da tarefa. Para montar a partilha de ficheiros, são necessárias as seguintes credenciais:

* **Nome de utilizador**: AZURE\\\<storageaccountname\>, por exemplo, AZURE\\*mystorageaccountname*
* **Palavra-passe**: < StorageAccountKeyWhichEnds no = = >, por exemplo, *XXXXXXXXXXXXXXXXXXXXX = =*

O seguinte comando monta uma partilha de ficheiros *myfileshare* na conta de armazenamento *mystorageaccountname* como o *s:* unidade:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Para simplificar, os exemplos aqui passam as credenciais diretamente no texto. Na prática, recomendamos vivamente gerenciamento das credenciais com variáveis de ambiente, certificados ou uma solução, tais como o Azure Key Vault.

Para simplificar a operação de montagem, manter, opcionalmente, as credenciais em nós. Em seguida, pode montar a partilha sem credenciais. Execute os seguintes dois passos:

1. Execute o `cmdkey` utilitário de linha de comando usando uma tarefa de início na configuração do conjunto. Isto persiste as credenciais em cada nó do Windows. A linha de comandos da tarefa de início é semelhante a:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Montar a partilha em cada nó como parte de cada tarefa utilizando `net use`. Por exemplo, a seguinte linha de comandos de tarefas monta a partilha de ficheiros como o *s:* unidade. Isso poderia ser seguido de um comando ou script que faça referência a partilha. Credenciais em cache são usadas na chamada para `net use`. Este passo pressupõe que está a utilizar a mesma identidade de utilizador para as tarefas que utilizou na tarefa de início no conjunto, que não é adequado para todos os cenários.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Exemplo do c#
O seguinte C# exemplo mostra como manter as credenciais num agrupamento de Windows através de uma tarefa de início. O nome do serviço de ficheiro de armazenamento e as credenciais de armazenamento são transmitidas como constantes definidas. Aqui, a tarefa de início é executado sob uma conta de auto-utilizador (não-administrador) padrão com âmbito de agrupamento.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Depois de armazenar as credenciais, utilize as linhas de comandos de tarefas para montar a partilha e a partilha de referência na leitura ou operações de escrita. Como um exemplo básico, a linha de comandos de tarefas no fragmento seguinte utiliza o `dir` comando para listar ficheiros na partilha de ficheiros. Certifique-se executar cada tarefa de tarefa com o mesmo [identidade do usuário](batch-user-accounts.md) utilizou para executar a tarefa de início do conjunto. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Montar uma partilha de um conjunto do Linux

Partilhas de ficheiros do Azure podem ser montadas em distribuições do Linux com o [cliente de kernel do CIFS](https://wiki.samba.org/index.php/LinuxCIFS). O exemplo seguinte mostra como montar uma partilha de ficheiros num conjunto de nós de computação do Ubuntu 16.04 LTS. Se utilizar uma distribuição Linux diferente, os passos gerais são semelhantes, mas utilizam o Gestor de pacotes adequado de distribuição. Para obter detalhes e exemplos adicionais, consulte [ficheiros do Azure de utilização com o Linux](../storage/files/storage-how-to-use-files-linux.md).

Em primeiro lugar, sob uma identidade de utilizador de administrador, instalar o `cifs-utils` empacotamento e a criar o ponto de montagem (por exemplo, */mnt/MyAzureFileShare*) no sistema de ficheiros local. Uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é Convenção comuns para criar este sob o `/mnt` pasta. Certifique-se de que não criar um ponto de montagem diretamente num `/mnt` (no Ubuntu) ou `/mnt/resource` (em outras distribuições).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Em seguida, execute o `mount` comandos para montar a partilha de ficheiros, estas credenciais:

* **Nome de utilizador**: \<storageaccountname\>, por exemplo, *mystorageaccountname*
* **Palavra-passe**: < StorageAccountKeyWhichEnds no = = >, por exemplo, *XXXXXXXXXXXXXXXXXXXXX = =*

O seguinte comando monta uma partilha de ficheiros *myfileshare* na conta de armazenamento *mystorageaccountname* na */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Para simplificar, os exemplos aqui passam as credenciais diretamente no texto. Na prática, recomendamos vivamente gerenciamento das credenciais com variáveis de ambiente, certificados ou uma solução, tais como o Azure Key Vault.

Num conjunto do Linux, pode combinar todas essas etapas numa tarefa de início único ou executá-los num script. Execute a tarefa de início como um utilizador de administrador no conjunto. Defina a tarefa de início de espera para concluir com êxito antes de executar tarefas adicionais no conjunto que fazem referência a partilha.

### <a name="python-example"></a>Exemplo de Python

O exemplo de Python seguinte mostra como configurar um conjunto de Ubuntu para montar a partilha de uma tarefa de início. O ponto de montagem, ponto final de partilha de ficheiros e as credenciais de armazenamento são transmitidas como constantes definidas. A tarefa de início é executado sob uma conta de utilizador automático de administrador com âmbito de agrupamento.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration = batchmodels.VirtualMachineConfiguration(
        image_reference = batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id = "batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(_COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Depois de montar a partilha e definir uma tarefa, utilize a partilha em suas linhas de comandos de tarefas. Por exemplo, utiliza o seguinte comando básico `ls` para listar ficheiros na partilha de ficheiros.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Passos Seguintes

* Para outras opções ler e escrever dados no Batch, consulte a [descrição geral da funcionalidade do Batch](batch-api-basics.md) e [manter a saída de trabalhos e tarefas](batch-task-output.md).

* Consulte também os [Batch Shipyard](https://github.com/Azure/batch-shipyard) toolkit, que inclui [receitas Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) para implementar sistemas de ficheiros para cargas de trabalho do Batch contentor.