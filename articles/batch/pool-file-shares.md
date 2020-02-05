---
title: Compartilhamento de arquivos do Azure para pools do lote do Azure | Microsoft Docs
description: Como montar um compartilhamento de arquivos do Azure de nós de computação em um pool do Linux ou do Windows no lote do Azure.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: 156dad25af5abd1b4d5db32569faf09a23fadfb1
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022516"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Usar um compartilhamento de arquivos do Azure com um pool do lote

Os [arquivos do Azure](../storage/files/storage-files-introduction.md) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio do protocolo SMB (Server Message Block). Este artigo fornece informações e exemplos de código para montar e usar um compartilhamento de arquivos do Azure em nós de computação do pool. Os exemplos de código usam os SDKs .NET e Python do lote, mas você pode executar operações semelhantes usando outros SDKs e ferramentas do lote.

O lote fornece suporte à API nativa para usar os blobs de armazenamento do Azure para ler e gravar dados. No entanto, em alguns casos, talvez você queira acessar um compartilhamento de arquivos do Azure dos nós de computação do pool. Por exemplo, você tem uma carga de trabalho herdada que depende de um compartilhamento de arquivos SMB, ou suas tarefas precisam acessar dados compartilhados ou produzir saída compartilhada. 

## <a name="considerations-for-use-with-batch"></a>Considerações para uso com o lote

* Considere usar um compartilhamento de arquivos do Azure quando houver pools que executam um número relativamente baixo de tarefas paralelas. Examine os [destinos de desempenho e escala](../storage/files/storage-files-scale-targets.md) para determinar se os arquivos do Azure (que usam uma conta de armazenamento do Azure) devem ser usados, considerando o tamanho do pool esperado e o número de arquivos de ativo. 

* Os compartilhamentos de arquivos do Azure são [econômicos](https://azure.microsoft.com/pricing/details/storage/files/) e podem ser configurados com a replicação de dados para outra região, portanto, são globalmente redundantes. 

* Você pode montar um compartilhamento de arquivos do Azure simultaneamente de um computador local.

* Consulte também as considerações gerais de [planejamento](../storage/files/storage-files-planning.md) para compartilhamentos de arquivos do Azure.


## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

[Crie um compartilhamento de arquivos](../storage/files/storage-how-to-create-file-share.md) em uma conta de armazenamento que esteja vinculada à sua conta do lote ou em uma conta de armazenamento separada.

## <a name="mount-a-share-on-a-windows-pool"></a>Montar um compartilhamento em um pool do Windows

Esta seção fornece etapas e exemplos de código para montar e usar um compartilhamento de arquivos do Azure em um pool de nós do Windows. Para obter mais informações, consulte a [documentação](../storage/files/storage-how-to-use-files-windows.md) para montar um compartilhamento de arquivos do Azure no Windows. 

No lote, você precisa montar o compartilhamento cada vez que uma tarefa é executada em um nó do Windows. Atualmente, não é possível manter a conexão de rede entre as tarefas em nós do Windows.

Por exemplo, inclua um comando `net use` para montar o compartilhamento de arquivos como parte de cada linha de comando de tarefa. Para montar o compartilhamento de arquivos, as seguintes credenciais são necessárias:

* **Nome de usuário**: azure\\\<StorageAccountName\>, por exemplo, Azure\\*mystorageaccountname*
* **Senha**: \<StorageAccountKeyWhichEnds em = = >, por exemplo, *XXXXXXXXXXXXXXXXXXXXX = =*

O comando a seguir monta um compartilhamento de arquivos *myfileshare* na conta de armazenamento *mystorageaccountname* como a unidade *S:* :

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Para simplificar, os exemplos aqui passam as credenciais diretamente no texto. Na prática, é altamente recomendável gerenciar as credenciais usando variáveis de ambiente, certificados ou uma solução como Azure Key Vault.

Para simplificar a operação de montagem, mantenha opcionalmente as credenciais nos nós. Em seguida, você pode montar o compartilhamento sem credenciais. Execute as duas etapas a seguir:

1. Execute o utilitário de linha de comando `cmdkey` usando uma tarefa inicial na configuração do pool. Isso persiste as credenciais em cada nó do Windows. A linha de comando de tarefa inicial é semelhante a:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Monte o compartilhamento em cada nó como parte de cada tarefa usando `net use`. Por exemplo, a linha de comando de tarefa a seguir monta o compartilhamento de arquivos como a unidade *S:* . Isso seria seguido por um comando ou script que faz referência ao compartilhamento. As credenciais armazenadas em cache são usadas na chamada para `net use`. Esta etapa pressupõe que você esteja usando a mesma identidade de usuário para as tarefas usadas na tarefa inicial no pool, o que não é apropriado para todos os cenários.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C#exemplo
O exemplo C# a seguir mostra como persistir as credenciais em um pool do Windows usando uma tarefa de início. O nome do serviço de arquivo de armazenamento e as credenciais de armazenamento são passados como constantes definidas. Aqui, a tarefa inicial é executada em uma conta de usuário automático padrão (não administrador) com escopo de pool.

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

Depois de armazenar as credenciais, use as linhas de comando da tarefa para montar o compartilhamento e fazer referência ao compartilhamento em operações de leitura ou gravação. Como um exemplo básico, a linha de comando da tarefa no trecho a seguir usa o comando `dir` para listar arquivos no compartilhamento de arquivos. Certifique-se de executar cada tarefa de trabalho usando a mesma [identidade de usuário](batch-user-accounts.md) usada para executar a tarefa inicial no pool. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Montar um compartilhamento em um pool do Linux

Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o [cliente de kernel CIFS](https://wiki.samba.org/index.php/LinuxCIFS). O exemplo a seguir mostra como montar um compartilhamento de arquivos em um pool de nós de computação do Ubuntu 16, 4 LTS. Se você usar uma distribuição do Linux diferente, as etapas gerais serão semelhantes, mas use o Gerenciador de pacotes apropriado para a distribuição. Para obter detalhes e exemplos adicionais, consulte [usar os arquivos do Azure com o Linux](../storage/files/storage-how-to-use-files-linux.md).

Primeiro, sob uma identidade de usuário administrador, instale o pacote `cifs-utils` e crie o ponto de montagem (por exemplo, */mnt/MyAzureFileShare*) no sistema de arquivos local. Uma pasta para um ponto de montagem pode ser criada em qualquer lugar no sistema de arquivos, mas é uma convenção comum criar isso na pasta `/mnt`. Não se esqueça de criar um ponto de montagem diretamente em `/mnt` (no Ubuntu) ou `/mnt/resource` (em outras distribuições).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Em seguida, execute o comando `mount` para montar o compartilhamento de arquivos, fornecendo estas credenciais:

* **Nome de usuário**: \<StorageAccountName\>, por exemplo, *mystorageaccountname*
* **Senha**: \<StorageAccountKeyWhichEnds em = = >, por exemplo, *XXXXXXXXXXXXXXXXXXXXX = =*

O comando a seguir monta um compartilhamento de arquivos *myfileshare* na conta de armazenamento *mystorageaccountname* em */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Para simplificar, os exemplos aqui passam as credenciais diretamente no texto. Na prática, é altamente recomendável gerenciar as credenciais usando variáveis de ambiente, certificados ou uma solução como Azure Key Vault.

Em um pool do Linux, você pode combinar todas essas etapas em uma única tarefa de inicialização ou executá-las em um script. Execute a tarefa de início como um usuário administrador no pool. Defina a tarefa inicial para aguardar a conclusão com êxito antes de executar tarefas adicionais no pool que faz referência ao compartilhamento.

### <a name="python-example"></a>Exemplo de Python

O exemplo de Python a seguir mostra como configurar um pool do Ubuntu para montar o compartilhamento em uma tarefa inicial. Os pontos de montagem, o ponto de extremidade de compartilhamento de arquivos e as credenciais de armazenamento são passados como constantes definidas. A tarefa inicial é executada em uma conta de usuário automático de administrador com escopo de pool.

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

Depois de montar o compartilhamento e definir um trabalho, use o compartilhamento em suas linhas de comando de tarefa. Por exemplo, o comando básico a seguir usa `ls` para listar arquivos no compartilhamento de arquivos.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Passos seguintes

* Para obter outras opções de leitura e gravação de dados no lote, consulte [visão geral do recurso de lote](batch-api-basics.md) e [manter saída de tarefa e trabalho](batch-task-output.md).

* Consulte também o [batch Shipyard](https://github.com/Azure/batch-shipyard) Toolkit, que inclui as [receitas do Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) para implantar sistemas de arquivos para cargas de trabalho de contêiner do lote.
