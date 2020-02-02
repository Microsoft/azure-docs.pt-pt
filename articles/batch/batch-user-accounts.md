---
title: Executar tarefas sob contas de utilizador - Lote Azure
description: É útil ser capaz de configurar a conta de utilizador sob a qual pretende que uma tarefa seja executada. Aprenda os tipos de contas de utilizador e como configurá-las.
services: batch
author: ju-shim
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/18/2019
ms.author: jushiman
ms.custom: seodec18
ms.openlocfilehash: 01342c8fcd0d2d3c603762b06350a9fcf7c12d03
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937967"
---
> [!NOTE] 
> As contas de usuário discutidas neste artigo são diferentes das contas de usuários usadas para protocolo RDP (RDP) ou Secure Shell (SSH), por motivos de segurança. 
>
> Para se conectar a um nó que executa a configuração de máquina virtual do Linux via SSH, consulte [usar o área de trabalho remota para uma VM do Linux no Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Para se conectar a nós que executam o Windows via RDP, consulte [conectar-se a uma VM do Windows Server](../virtual-machines/windows/connect-logon.md).<br /><br />
> Para se conectar a um nó que executa a configuração do serviço de nuvem via RDP, consulte [habilitar conexão de área de trabalho remota para uma função nos serviços de nuvem do Azure](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Executar tarefas em contas de usuário no lote

Uma tarefa no lote do Azure sempre é executada em uma conta de usuário. Por padrão, as tarefas são executadas em contas de usuário padrão, sem permissões de administrador. Essas configurações de conta de usuário padrão geralmente são suficientes. Para determinados cenários, no entanto, é útil ser capaz de configurar a conta de usuário sob a qual você deseja que uma tarefa seja executada. Este artigo aborda os tipos de contas de usuário e como você pode configurá-las para seu cenário.

## <a name="types-of-user-accounts"></a>Tipos de contas de usuário

O lote do Azure fornece dois tipos de contas de usuário para executar tarefas:

- **Contas de usuário automático.** As contas de usuário automático são contas de usuário internas que são criadas automaticamente pelo serviço de lote. Por padrão, as tarefas são executadas em uma conta de usuário automático. Você pode configurar a especificação de usuário automático para uma tarefa a fim de indicar sob qual conta de usuário automático uma tarefa deve ser executada. A especificação de usuário automático permite que você especifique o nível de elevação e o escopo da conta de usuário automático que executará a tarefa. 

- **Uma conta de usuário nomeada.** Você pode especificar uma ou mais contas de usuário nomeadas para um pool ao criar o pool. Cada conta de usuário é criada em cada nó do pool. Além do nome da conta, você especifica a senha da conta do usuário, o nível de elevação e, para pools do Linux, a chave privada SSH. Ao adicionar uma tarefa, você pode especificar a conta de usuário nomeada na qual essa tarefa deve ser executada.

> [!IMPORTANT] 
> O serviço de lote versão 2017-01-01.4.0 introduz uma alteração significativa que exige que você atualize seu código para chamar essa versão. Se você estiver migrando o código de uma versão mais antiga do lote, observe que a propriedade **runElevated** não tem mais suporte na API REST ou nas bibliotecas de cliente do lote. Use a nova propriedade **UserIdentity** de uma tarefa para especificar o nível de elevação. Consulte a seção intitulada [atualizar seu código para a biblioteca de cliente do lote mais recente](#update-your-code-to-the-latest-batch-client-library) para obter diretrizes rápidas para atualizar o código do lote se você estiver usando uma das bibliotecas de cliente.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Acesso de conta de usuário a arquivos e diretórios

Uma conta de usuário automático e uma conta de usuário nomeado têm acesso de leitura/gravação ao diretório de trabalho da tarefa, diretório compartilhado e diretório de tarefas de várias instâncias. Ambos os tipos de contas têm acesso de leitura aos diretórios de inicialização e de preparação de trabalho.

Se uma tarefa for executada na mesma conta que foi usada para executar uma tarefa de inicialização, a tarefa terá acesso de leitura/gravação ao diretório de tarefa inicial. Da mesma forma, se uma tarefa for executada na mesma conta que foi usada para executar uma tarefa de preparação de trabalho, a tarefa terá acesso de leitura/gravação ao diretório da tarefa de preparação do trabalho. Se uma tarefa for executada em uma conta diferente da tarefa inicial ou da tarefa de preparação do trabalho, a tarefa terá apenas acesso de leitura ao respectivo diretório.

Para obter mais informações sobre como acessar arquivos e diretórios de uma tarefa, consulte [desenvolver soluções de computação paralela em larga escala com o lote](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Acesso elevado para tarefas 

O nível de elevação da conta do usuário indica se uma tarefa é executada com acesso elevado. Uma conta de usuário automático e uma conta de usuário nomeado podem ser executadas com acesso elevado. As duas opções para o nível de elevação são:

- Não **administrador:** A tarefa é executada como usuário padrão sem acesso elevado. O nível de elevação padrão para uma conta de usuário do lote é sempre não **administrador**.
- **Administrador:** A tarefa é executada como um usuário com acesso elevado e opera com permissões de administrador completo. 

## <a name="auto-user-accounts"></a>Contas de usuário automático

Por padrão, as tarefas são executadas no lote em uma conta de usuário automático, como um usuário padrão sem acesso elevado e com o escopo da tarefa. Quando a especificação de usuário automático é configurada para o escopo da tarefa, o serviço de lote cria uma conta de usuário automático somente para essa tarefa.

A alternativa ao escopo da tarefa é o escopo do pool. Quando a especificação de usuário automático para uma tarefa é configurada para o escopo do pool, a tarefa é executada em uma conta de usuário automático que está disponível para qualquer tarefa no pool. Para obter mais informações sobre o escopo do pool, consulte a seção executar uma tarefa como o usuário automático com escopo de pool.   

O escopo padrão é diferente em nós do Windows e Linux:

- Em nós do Windows, as tarefas são executadas em escopo da tarefa por padrão.
- Os nós do Linux sempre são executados sob o escopo do pool.

Há quatro configurações possíveis para a especificação de usuário automático, cada uma correspondendo a uma conta de usuário automático exclusiva:

- Acesso não administrador com escopo de tarefa (a especificação de usuário automático padrão)
- Acesso de administrador (elevado) com escopo de tarefa
- Acesso não administrador com escopo de pool
- Acesso de administrador com escopo de pool

> [!IMPORTANT] 
> As tarefas em execução no escopo da tarefa não têm acesso de fato a outras tarefas em um nó. No entanto, um usuário mal-intencionado com acesso à conta pode contornar essa restrição enviando uma tarefa que é executada com privilégios de administrador e acessa outros diretórios de tarefas. Um usuário mal-intencionado também pode usar RDP ou SSH para se conectar a um nó. É importante proteger o acesso às chaves de conta do lote para evitar esse cenário. Se você suspeitar que sua conta pode ter sido comprometida, certifique-se de regenerar suas chaves.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Executar uma tarefa como um usuário automático com acesso elevado

Você pode configurar a especificação de usuário automático para privilégios de administrador quando precisar executar uma tarefa com acesso elevado. Por exemplo, uma tarefa de início pode precisar de acesso elevado para instalar o software no nó.

> [!NOTE] 
> Em geral, é melhor usar acesso elevado somente quando necessário. As práticas recomendadas recomendam conceder o privilégio mínimo necessário para atingir o resultado desejado. Por exemplo, se uma tarefa inicial instalar o software para o usuário atual, em vez de para todos os usuários, você poderá evitar a concessão de acesso elevado a tarefas. Você pode configurar a especificação de usuário automático para o escopo do pool e o acesso não administrador para todas as tarefas que precisam ser executadas na mesma conta, incluindo a tarefa inicial. 
>
>

Os trechos de código a seguir mostram como configurar a especificação de usuário automático. Os exemplos definem o nível de elevação como `Admin` e o escopo como `Task`. O escopo da tarefa é a configuração padrão, mas está incluído aqui para fins de exemplo.

#### <a name="batch-net"></a>.NET do Batch

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Java do lote

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Executar uma tarefa como um usuário automático com escopo de pool

Quando um nó é provisionado, duas contas de usuário automático em todo o pool são criadas em cada nó no pool, uma com acesso elevado e outra sem acesso elevado. Definir o escopo do usuário automático para o escopo de pool para uma determinada tarefa executa a tarefa em uma dessas duas contas de usuário automático em todo o pool. 

Quando você especifica o escopo do pool para o usuário automático, todas as tarefas executadas com acesso de administrador são executadas sob a mesma conta de usuário automático em todo o pool. Da mesma forma, as tarefas executadas sem permissões de administrador também são executadas em uma única conta de usuário automático em todo o pool. 

> [!NOTE] 
> As duas contas de usuário automático em todo o pool são contas separadas. As tarefas em execução na conta administrativa em todo o pool não podem compartilhar dados com tarefas em execução na conta padrão e vice-versa. 
>
>

A vantagem de ser executada na mesma conta de usuário automático é que as tarefas podem compartilhar dados com outras tarefas em execução no mesmo nó.

O compartilhamento de segredos entre tarefas é um cenário em que a execução de tarefas em uma das duas contas de usuário automático em todo o pool é útil. Por exemplo, suponha que uma tarefa inicial precise provisionar um segredo no nó que outras tarefas podem usar. Você pode usar a API de proteção de dados do Windows (DPAPI), mas ela requer privilégios de administrador. Em vez disso, você pode proteger o segredo no nível do usuário. As tarefas em execução na mesma conta de usuário podem acessar o segredo sem acesso elevado.

Outro cenário em que você talvez queira executar tarefas em uma conta de usuário automático com escopo de pool é um compartilhamento de arquivos MPI (interface de transmissão de mensagens). Um compartilhamento de arquivos MPI é útil quando os nós na tarefa MPI precisam trabalhar nos mesmos dados de arquivo. O nó de cabeçalho cria um compartilhamento de arquivos que os nós filho podem acessar se estiverem em execução na mesma conta de usuário automático. 

O trecho de código a seguir define o escopo do usuário automático para o escopo de pool para uma tarefa no .NET do lote. O nível de elevação é omitido, portanto, a tarefa é executada na conta de usuário automático de todo o pool padrão.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Contas de usuário nomeado

Você pode definir contas de usuário nomeadas ao criar um pool. Uma conta de usuário nomeado tem um nome e uma senha que você fornece. Você pode especificar o nível de elevação para uma conta de usuário nomeado. Para nós do Linux, você também pode fornecer uma chave privada SSH.

Existe uma conta de usuário nomeada em todos os nós no pool e está disponível para todas as tarefas em execução nesses nós. Você pode definir qualquer número de usuários nomeados para um pool. Ao adicionar uma tarefa ou coleção de tarefas, você pode especificar que a tarefa seja executada em uma das contas de usuário nomeadas definidas no pool.

Uma conta de usuário nomeada é útil quando você deseja executar todas as tarefas em um trabalho na mesma conta de usuário, mas isolá-las de tarefas em execução em outros trabalhos ao mesmo tempo. Por exemplo, você pode criar um usuário nomeado para cada trabalho e executar as tarefas de cada trabalho sob essa conta de usuário nomeada. Cada trabalho pode compartilhar um segredo com suas próprias tarefas, mas não com tarefas em execução em outros trabalhos.

Você também pode usar uma conta de usuário nomeada para executar uma tarefa que define permissões em recursos externos, como compartilhamentos de arquivos. Com uma conta de usuário nomeada, você controla a identidade do usuário e pode usar essa identidade de usuário para definir permissões.  

As contas de usuário nomeados permitem o SSH sem senha entre os nós do Linux. Você pode usar uma conta de usuário nomeado com nós do Linux que precisam executar tarefas de várias instâncias. Cada nó no pool pode executar tarefas em uma conta de usuário definida no pool inteiro. Para obter mais informações sobre tarefas de várias instâncias, consulte [usar tarefas de instância de várias\-para executar aplicativos MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Criar contas de usuário nomeadas

Para criar contas de usuário nomeadas no lote, adicione uma coleção de contas de usuário ao pool. Os trechos de código a seguir mostram como criar contas de usuário nomeados em .NET, Java e Python. Esses trechos de código mostram como criar contas nomeadas de administrador e não administrador em um pool. Os exemplos criam pools usando a configuração do serviço de nuvem, mas você usa a mesma abordagem ao criar um pool do Windows ou Linux usando a configuração da máquina virtual.

#### <a name="batch-net-example-windows"></a>Exemplo de .NET do lote (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Exemplo de .NET do lote (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Exemplo de Java do lote

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Exemplo de Python do lote

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Executar uma tarefa em uma conta de usuário nomeado com acesso elevado

Para executar uma tarefa como um usuário elevado, defina a propriedade **UserIdentity** da tarefa como uma conta de usuário nomeada que foi criada com sua propriedade **ElevationLevel** definida como `Admin`.

Este trecho de código especifica que a tarefa deve ser executada em uma conta de usuário nomeada. Essa conta de usuário nomeada foi definida no pool quando o pool foi criado. Nesse caso, a conta de usuário nomeado foi criada com permissões de administrador:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Atualizar seu código para a biblioteca de cliente do lote mais recente

O serviço de lote versão 2017-01-01.4.0 introduz uma alteração significativa, substituindo a propriedade **runElevated** disponível em versões anteriores pela propriedade **UserIdentity** . As tabelas a seguir fornecem um mapeamento simples que você pode usar para atualizar seu código de versões anteriores das bibliotecas de cliente.

### <a name="batch-net"></a>.NET do Batch

| Se seu código usar...                  | Atualize-o para...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` não especificado | Nenhuma atualização necessária                                                                                               |

### <a name="batch-java"></a>Java do lote

| Se seu código usar...                      | Atualize-o para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` não especificado | Nenhuma atualização necessária                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Se seu código usar...                      | Atualize-o para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` não especificados | Nenhuma atualização necessária                                                                                                                                  |


## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral detalhada do lote, consulte [desenvolver soluções de computação paralela em larga escala com o lote](batch-api-basics.md).
