---
title: Partilha de ficheiros Azure para piscinas de Lotes Azure
description: Como montar uma partilha de Ficheiros Azure a partir de nódos de computação numa piscina linux ou Windows em Azure Batch.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: 0aa8c8f6dcf1114688e6abaf1a17f2e8af6fa4fe
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723498"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Use uma partilha de ficheiros Azure com uma piscina de lote

[O Azure Files](../storage/files/storage-files-introduction.md) oferece partilhas de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo do Bloco de Mensagens do Servidor (SMB). Este artigo fornece exemplos de informações e códigos para montagem e utilização de uma partilha de ficheiros Azure em nós de computação de piscina. Os exemplos de código utilizam os SDKs Batch .NET e Python, mas pode realizar operações semelhantes utilizando outros SDKs e ferramentas de lote.

O lote fornece suporte nativo da API para usar bolhas de armazenamento Azure para ler e escrever dados. No entanto, em alguns casos, é melhor aceder a uma partilha de ficheiros Azure a partir dos seus nós de computação de piscina. Por exemplo, tem uma carga de trabalho antiga que depende de uma partilha de ficheiros SMB, ou as suas tarefas precisam de aceder a dados partilhados ou produzir saída partilhada. 

## <a name="considerations-for-use-with-batch"></a>Considerações para utilização com Lote

* Considere utilizar uma partilha de ficheiros Azure quando tiver piscinas que executam um número relativamente baixo de tarefas paralelas. Reveja os alvos de [desempenho e escala](../storage/files/storage-files-scale-targets.md) para determinar se os Ficheiros Azure (que utilizam uma conta de Armazenamento Azure) devem ser utilizados, dado o tamanho esperado do pool e o número de ficheiros de ativos. 

* As ações de ficheiros Azure são [rentáveis](https://azure.microsoft.com/pricing/details/storage/files/) e podem ser configuradas com replicação de dados para outra região, pelo que são globalmente redundantes. 

* Pode montar uma partilha de ficheiros Azure simultaneamente a partir de um computador no local.

* Consulte também as [considerações gerais](../storage/files/storage-files-planning.md) de planeamento das ações de ficheiros Azure.


## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

[Crie uma parte](../storage/files/storage-how-to-create-file-share.md) de ficheiro numa conta de armazenamento que esteja ligada à sua conta Batch, ou numa conta de armazenamento separada.

## <a name="mount-a-share-on-a-windows-pool"></a>Monte uma parte em uma piscina windows

Esta secção fornece passos e exemplos de código para montar e usar uma partilha de ficheiros Azure numa piscina de nós do Windows. Para obter fundos adicionais, consulte a [documentação](../storage/files/storage-how-to-use-files-windows.md) para montar uma partilha de ficheiros Azure no Windows. 

No Batch, é necessário montar a parte sempre que uma tarefa é executada num nó windows. Atualmente, não é possível persistir a ligação de rede entre tarefas em nós do Windows.

Por exemplo, inclua um `net use` comando para montar a partilha de ficheiros como parte de cada linha de comando de tarefa. Para montar a parte do ficheiro, são necessárias as seguintes credenciais:

* **Nome do utilizador**: nome da conta de armazenamento AZURE, por exemplo, nome de \\ \< conta de \> armazenamento \\ *AZURE*
* **Palavra-passe:** \< StorageAccountKeyWhichEnds in==>, por exemplo, *XXXXXXXXXXXXXXXXXX==*

O seguinte comando monta um ficheiro partilhar *myfileshare* na conta *de armazenamento mystorage accountname* como *s:* drive: drive:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Para a simplicidade, os exemplos aqui passam as credenciais diretamente em texto. Na prática, recomendamos vivamente a gestão das credenciais utilizando variáveis ambientais, certificados ou uma solução como o Azure Key Vault.

Para simplificar o funcionamento do suporte, persistir opcionalmente as credenciais nos nós. Depois, podes montar a parte sem credenciais. Executar os seguintes dois passos:

1. Executar o `cmdkey` utilitário da linha de comando utilizando uma tarefa de início na configuração da piscina. Isto persiste as credenciais em cada nó do Windows. A linha de comando de tarefa inicial é semelhante a:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Monte a parte em cada nó como parte de cada tarefa utilizando `net use` . Por exemplo, a seguinte linha de comando de tarefa saque a parte do ficheiro como o *S:* unidade. Isto seria seguido por um comando ou script que referencia a parte. As credenciais em cache são usadas na chamada para `net use` . Este passo pressupõe que está a usar a mesma identidade de utilizador para as tarefas que utilizou na tarefa inicial na piscina, o que não é apropriado para todos os cenários.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C# exemplo
O exemplo c# seguinte mostra como persistir as credenciais numa piscina do Windows usando uma tarefa inicial. O nome do serviço de arquivo de armazenamento e as credenciais de armazenamento são passados como constantes definidas. Aqui, a tarefa inicial é realizada sob uma conta de utilizador automático padrão (não administrador) com âmbito de piscina.

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

Depois de armazenar as credenciais, utilize as linhas de comando de tarefa seleção para montar a parte e fazer referência à parte nas operações de leitura ou escrita. Como exemplo básico, a linha de comando de tarefas no seguinte corte utiliza o `dir` comando para listar ficheiros na partilha de ficheiros. Certifique-se de executar cada tarefa de trabalho utilizando a mesma identidade de [utilizador](batch-user-accounts.md) que usou para executar a tarefa inicial na piscina. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Monte uma parte em uma piscina linux

As ações de ficheiros Azure podem ser montadas nas distribuições linux utilizando o [cliente kernel CIFS](https://wiki.samba.org/index.php/LinuxCIFS). O exemplo seguinte mostra como montar uma partilha de ficheiros numa piscina de nódos os nomes de cálculo ubuntu 16.04 LTS. Se utilizar uma distribuição linux diferente, os passos gerais são semelhantes, mas use o gestor de pacotes apropriado para a distribuição. Para mais detalhes e exemplos adicionais, consulte [Use Azure Files com Linux](../storage/files/storage-how-to-use-files-linux.md).

Em primeiro lugar, sob uma identidade de utilizador administrador, instale o pacote e crie o ponto de `cifs-utils` montagem (por exemplo, */mnt/MyAzureFileShare)* no sistema de ficheiros local. Uma pasta para um ponto de montagem pode ser criada em qualquer lugar do sistema de ficheiros, mas é uma convenção comum para criar isto sob a `/mnt` pasta. Certifique-se de não criar um ponto de montagem diretamente em `/mnt` Ubuntu ou `/mnt/resource` (em outras distribuições).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Em seguida, executar o `mount` comando para montar a parte do ficheiro, fornecendo estas credenciais:

* **Nome do utilizador**: \< nome da conta de \> armazenamento, por exemplo, nome de conta *mystorage*
* **Palavra-passe:** \< StorageAccountKeyWhichEnds in==>, por exemplo, *XXXXXXXXXXXXXXXXXX==*

O comando seguinte monta um ficheiro partilhar *myfileshare* na conta de armazenamento *mystorageaccount name* at */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Para a simplicidade, os exemplos aqui passam as credenciais diretamente em texto. Na prática, recomendamos vivamente a gestão das credenciais utilizando variáveis ambientais, certificados ou uma solução como o Azure Key Vault.

Numa piscina linux, você pode combinar todos estes passos em uma única tarefa inicial, ou executá-los em um script. Execute a tarefa inicial como um utilizador administrador na piscina. Desloque a sua tarefa inicial para aguardar para completar com sucesso antes de executar mais tarefas na piscina que referenciam a parte.

### <a name="python-example"></a>Exemplo python

O exemplo python seguinte mostra como configurar uma piscina Ubuntu para montar a parte numa tarefa inicial. O ponto de montagem, o ponto final da partilha de ficheiros e as credenciais de armazenamento são passados como constantes definidas. A tarefa inicial é realizada sob uma conta de utilizador automático administrador com âmbito de piscina.

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

Depois de montar a parte e definir um trabalho, use a parte nas linhas de comando de tarefa. Por exemplo, o seguinte comando básico `ls` utiliza-se para listar ficheiros na partilha de ficheiros.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Próximos passos

* Para outras opções para ler e escrever dados em Batch, consulte a visão geral da [funcionalidade do Lote](batch-api-basics.md) e persista a saída de trabalho e [tarefas](batch-task-output.md).

* Consulte também o conjunto de [ferramentas](https://github.com/Azure/batch-shipyard/tree/master/recipes) do Estaleiro de [Lote,](https://github.com/Azure/batch-shipyard) que inclui receitas do Estaleiro para implantar sistemas de ficheiros para cargas de carga de contentores de lote.
