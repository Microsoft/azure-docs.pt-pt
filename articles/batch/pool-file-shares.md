---
title: Azure partilha de ficheiros para piscinas Azure Batch
description: Como montar uma partilha de Ficheiros Azure a partir de nós de computação num conjunto Linux ou Windows em Azure Batch.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: cb7e6f158e246319e851ee2edd5b21bae33c3723
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83780276"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Use uma partilha de ficheiros Azure com uma piscina de Lote

[O Azure Files](../storage/files/storage-files-introduction.md) oferece ações de ficheiros totalmente geridas na nuvem que estão acessíveis através do protocolo Bloco de Mensagens do Servidor (SMB). Este artigo fornece informações e exemplos de código para montagem e utilização de uma partilha de ficheiros Azure nos nós de cálculo de piscina. Os exemplos de código utilizam os SDKs Batch .NET e Python, mas pode efetuar operações semelhantes utilizando outros SDKs e ferramentas de lote.

O Batch fornece suporte api nativo para usar bolhas de armazenamento Azure para ler e escrever dados. No entanto, em alguns casos, você pode querer aceder a uma partilha de ficheiros Azure a partir dos seus nós de cálculo de piscina. Por exemplo, tem uma carga de trabalho antiga que depende de uma partilha de ficheiros SMB, ou as suas tarefas precisam de aceder a dados partilhados ou produzir uma produção partilhada. 

## <a name="considerations-for-use-with-batch"></a>Considerações a utilizar com Batch

* Considere usar uma partilha de ficheiros Azure quando tiver piscinas que executam um número relativamente baixo de tarefas paralelas. Reveja os alvos de desempenho e escala para determinar se os Ficheiros Azure (que utiliza uma conta de Armazenamento Azure) devem ser [utilizados,](../storage/files/storage-files-scale-targets.md) dado o tamanho esperado da piscina e o número de ficheiros de ativos. 

* As ações de ficheiros Azure são [rentáveis](https://azure.microsoft.com/pricing/details/storage/files/) e podem ser configuradas com a replicação de dados para outra região, pelo que são globalmente redundantes. 

* Pode montar uma partilha de ficheiros Azure simultaneamente a partir de um computador no local.

* Consulte também as [considerações gerais de planeamento](../storage/files/storage-files-planning.md) das ações de ficheiros Azure.


## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

[Crie uma parte de ficheiro](../storage/files/storage-how-to-create-file-share.md) numa conta de armazenamento que esteja ligada à sua conta Batch ou numa conta de armazenamento separada.

## <a name="mount-a-share-on-a-windows-pool"></a>Monte uma parte em uma piscina do Windows

Esta secção fornece passos e exemplos de código para montar e utilizar uma partilha de ficheiros Azure num conjunto de nós Windows. Para obter informações adicionais, consulte a [documentação](../storage/files/storage-how-to-use-files-windows.md) para a montagem de uma partilha de ficheiros Azure no Windows. 

Em Batch, é necessário montar a partilha sempre que uma tarefa for executada num nó do Windows. Atualmente, não é possível persistir a ligação de rede entre tarefas nos nós windows.

Por exemplo, inclua um `net use` comando para montar a partilha de ficheiros como parte de cada linha de comando de tarefa. Para montar a partilha de ficheiros, são necessárias as seguintes credenciais:

* **Nome do utilizador**: AZURE \\ \<storageaccountname\> , por exemplo, \\ *AZURE mystorageaccountname*
* **Palavra-passe**: \<StorageAccountKeyWhichEnds in==> , por exemplo, *XXXXXXXXXXXXXXXXXXXxxxx==*

O seguinte comando monta um ficheiro partilha *myfileshare* na conta de armazenamento *mystoragecountname* como o *S:* drive:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Para simplificar, os exemplos aqui passam as credenciais diretamente em texto. Na prática, recomendamos vivamente a gestão das credenciais utilizando variáveis ambientais, certificados ou uma solução como o Azure Key Vault.

Para simplificar a operação de montagem, persifique opcionalmente as credenciais nos nós. Depois, podes montar a parte sem credenciais. Executar os dois passos seguintes:

1. Executar o `cmdkey` utilitário da linha de comando utilizando uma tarefa inicial na configuração da piscina. Isto persiste nas credenciais de cada nó do Windows. A linha de comando de tarefa inicial é semelhante a:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Monte a partilha em cada nó como parte de cada tarefa utilizando `net use` . Por exemplo, a seguinte linha de comando de tarefa monta a partilha de ficheiros como *o S:* unidade. Isto seria seguido por um comando ou script que faz referência à partilha. As credenciais em cache são utilizadas na chamada para `net use` . Este passo pressupõe que está a usar a mesma identidade de utilizador para as tarefas que utilizou na tarefa inicial na piscina, o que não é apropriado para todos os cenários.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Exemplo C#
O exemplo C# a seguir mostra como persistir as credenciais num pool do Windows utilizando uma tarefa inicial. O nome do serviço de armazenamento e as credenciais de armazenamento são passados como constantes definidas. Aqui, a tarefa inicial é de acordo com uma conta de utilizador automático padrão (não administrador) com âmbito de piscina.

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

Depois de armazenar as credenciais, utilize as linhas de comando de tarefa para montar a partilha e fazer referência à partilha em operações de leitura ou de escrita. Como exemplo básico, a linha de comando de tarefa no seguinte corte utiliza o `dir` comando para listar ficheiros na partilha de ficheiros. Certifique-se de executar cada tarefa de trabalho usando a mesma [identidade de utilizador](batch-user-accounts.md) que usou para executar a tarefa inicial na piscina. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Monte uma parte em uma piscina Linux

As ações de ficheiros Azure podem ser montadas nas distribuições do Linux utilizando o [cliente kernel CIFS](https://wiki.samba.org/index.php/LinuxCIFS). O exemplo a seguir mostra como montar uma partilha de ficheiros num pool de nós de computação Ubuntu 16.04 LTS. Se utilizar uma distribuição Linux diferente, os passos gerais são semelhantes, mas use o gestor de pacotes apropriado para a distribuição. Para mais detalhes e exemplos adicionais, consulte [Use Azure Files com Linux](../storage/files/storage-how-to-use-files-linux.md).

Em primeiro lugar, sob a identidade do utilizador do administrador, instale o `cifs-utils` pacote e crie o ponto de montagem (por exemplo, */mnt/MyAzureFileShare)* no sistema de ficheiros local. Uma pasta para um ponto de montagem pode ser criada em qualquer lugar do sistema de ficheiros, mas é comum criar isto sob a `/mnt` pasta. Certifique-se de que não cria um ponto de montagem diretamente em `/mnt` Ubuntu ou `/mnt/resource` (noutras distribuições).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Em seguida, executar o `mount` comando para montar a partilha de ficheiros, fornecendo estas credenciais:

* **Nome do utilizador**: \<storageaccountname\> , por exemplo, *mystorageaccountname*
* **Palavra-passe**: \<StorageAccountKeyWhichEnds in==> , por exemplo, *XXXXXXXXXXXXXXXXXXXxxxx==*

O seguinte comando monta um ficheiro *partilha myfileshare* na conta de armazenamento *mystorageaccountname* em */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Para simplificar, os exemplos aqui passam as credenciais diretamente em texto. Na prática, recomendamos vivamente a gestão das credenciais utilizando variáveis ambientais, certificados ou uma solução como o Azure Key Vault.

Numa piscina Linux, você pode combinar todos estes passos numa única tarefa inicial, ou executá-los em um script. Executar a tarefa inicial como utilizador de administrador na piscina. Desacorda a sua tarefa inicial para aguardar para completar com sucesso antes de executar mais tarefas no pool que referenciam a partilha.

### <a name="python-example"></a>Exemplo python

O exemplo python que se segue mostra como configurar uma piscina Ubuntu para montar a partilha numa tarefa inicial. O ponto de montagem, o ponto final da partilha de ficheiros e as credenciais de armazenamento são passados como constantes definidas. A tarefa inicial é de produção sob uma conta de utilizador automático do administrador com âmbito de piscina.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Depois de montar a partilha e definir um trabalho, utilize a parte das linhas de comando da tarefa. Por exemplo, o seguinte comando básico utiliza `ls` para listar ficheiros na partilha de ficheiros.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Próximos passos

* Para outras opções para ler e escrever dados em Batch, consulte [a saída de trabalho e tarefa de Persist](batch-task-output.md).
* Consulte também o conjunto de [ferramentas](https://github.com/Azure/batch-shipyard/tree/master/recipes) [do Estaleiro batch,](https://github.com/Azure/batch-shipyard) que inclui receitas de estaleiro para implantar sistemas de ficheiros para cargas de carga de contentores de lotes.
