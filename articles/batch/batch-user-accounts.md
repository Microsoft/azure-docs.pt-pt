---
title: Executar tarefas em contas de utilizador - Azure Batch | Documentos da Microsoft
description: Configurar contas de utilizador para tarefas em execução no Azure Batch
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: bd5c16d755ef9b71f36b3d499838b12e6099ba6d
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595379"
---
> [!NOTE] 
> As contas de utilizador discutidas neste artigo são diferentes dos utilizadores contas utilizadas para o protocolo RDP (Remote Desktop) ou Secure Shell (SSH), por motivos de segurança. 
>
> Para ligar a um nó a executar a configuração de máquina virtual do Linux através de SSH, veja [ambiente de trabalho remoto do uso de uma VM do Linux no Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Para ligar a nós a executar o Windows através de RDP, veja [ligar a uma VM do Windows Server](../virtual-machines/windows/connect-logon.md).<br /><br />
> Para ligar a um nó a executar a configuração do serviço cloud através de RDP, veja [ativar a ligação de ambiente de trabalho remoto para uma função nos serviços Cloud do Azure](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Executar tarefas em contas de utilizador no Batch

Uma tarefa no Azure Batch é sempre executado sob uma conta de utilizador. Por predefinição, as tarefas executadas em contas de usuário padrão, sem permissões de administrador. Estas definições de conta de utilizador predefinidas são normalmente suficientes. No entanto, para determinados cenários, é útil poder configurar a conta de utilizador onde pretende que a execução de uma tarefa. Este artigo aborda os tipos de contas de utilizador e como pode configurá-las para o seu cenário.

## <a name="types-of-user-accounts"></a>Tipos de contas de utilizador

O Azure Batch fornece dois tipos de contas de utilizador para a execução de tarefas:

- **Contas de utilizador automaticamente.** As contas de utilizador automático são contas de utilizador incorporadas que são criadas automaticamente pelo serviço Batch. Por predefinição, as tarefas executadas numa conta de utilizador automaticamente. Pode configurar a especificação de auto-utilizador indicar sob que conta de utilizador automaticamente uma tarefa deve ser executada uma tarefa. A especificação de auto-utilizador permite-lhe especificar o nível de privilégio e o escopo da conta de utilizador automático que será executada a tarefa. 

- **Uma conta de utilizador nomeado.** Pode especificar uma ou mais contas de utilizador nomeado para um conjunto quando cria o conjunto. Cada conta de utilizador é criada em cada nó do conjunto. Além do nome de conta, especifique a senha da conta de utilizador, elevação de nível e, para os conjuntos de Linux, a chave privada SSH. Quando adiciona uma tarefa, pode especificar a conta de utilizador nomeado sob a qual deve executar essa tarefa.

> [!IMPORTANT] 
> A versão de serviço do Batch 2017-01-01.4.0 apresenta uma alteração de última hora que requer que atualizar seu código para chamar essa versão. Se estiver migrando o código de uma versão mais antiga do Batch, tenha em atenção de que o **runElevated** propriedade já não é suportada nas bibliotecas de cliente de REST API ou lote. Utilizar a nova **userIdentity** propriedade de uma tarefa para especificar o nível de elevação. Consulte a secção intitulada [atualizar seu código com a biblioteca de cliente mais recente do Batch](#update-your-code-to-the-latest-batch-client-library) para rápida diretrizes para atualizar seu código do Batch, se estiver a utilizar uma das bibliotecas de cliente.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Conta de utilizador acesso a arquivos e diretórios

Uma conta de utilizador automático e uma conta de utilizador nomeado tem acesso de leitura/escrita ao diretório de trabalho, o diretório partilhado e o diretório de tarefas de várias instâncias da tarefa. Ambos os tipos de contas de tem acesso de leitura para os diretórios de preparação de tarefas e de inicialização.

Se uma tarefa é executada na mesma conta que foi utilizada para executar uma tarefa de início, a tarefa tem acesso de leitura / escrita ao diretório da tarefa de início. Da mesma forma, se uma tarefa é executada na mesma conta que foi utilizada para executar uma tarefa de preparação, a tarefa tem acesso de leitura / escrita para o diretório de tarefas de preparação da tarefa. Se uma tarefa é executada sob uma conta diferente da tarefa de início ou a tarefa de preparação, em seguida, a tarefa tem acesso de leitura para o respetivo diretório.

Para obter mais informações sobre como acessar arquivos e diretórios a partir de uma tarefa, consulte [soluções com o Batch de computação paralelas em grande escala de desenvolver](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Acesso elevado para tarefas 

Nível de elevação da conta de utilizador indica se uma tarefa é executada com acesso elevado. Uma conta de utilizador automático e uma conta de utilizador nomeado podem executar com acesso elevado. As duas opções para o nível de elevação são:

- **NonAdmin:** A tarefa é executada como usuário padrão sem acesso elevado. O nível de elevação de predefinido para uma conta de utilizador do Batch é sempre **NonAdmin**.
- **Administrador:** A tarefa é executado como um utilizador com acesso elevado e funciona com todas as permissões de administrador. 

## <a name="auto-user-accounts"></a>Contas de utilizador automático

Por predefinição, as tarefas executadas em Batch através de uma conta de utilizador automático, como usuário padrão sem acesso elevado e com o âmbito da tarefa. Quando a especificação de auto-utilizador está configurada para o âmbito de tarefa, o serviço Batch cria uma conta de utilizador automaticamente para essa tarefa apenas.

A alternativa ao âmbito da tarefa é o âmbito de agrupamento. Quando a especificação de auto-utilizador para uma tarefa está configurada para o âmbito de agrupamento, a tarefa é executada sob uma conta de utilizador automaticamente que está disponível para qualquer tarefa no conjunto. Para obter mais informações sobre o âmbito de agrupamento, consulte a seção intitulada executar uma tarefa que o utilizador automático com âmbito de agrupamento.   

O âmbito de padrão é diferente em nós do Windows e Linux:

- Em nós do Windows, tarefas são executadas sob o escopo de tarefas por predefinição.
- Nós do Linux ser executado sempre sob o âmbito de agrupamento.

Existem quatro configurações possíveis para a especificação de auto-utilizador, cada uma delas corresponde a uma conta de utilizador exclusivo de automática:

- Acesso não-administrador com âmbito de tarefa (a especificação de auto-utilizador predefinido)
- Acesso de administrador (elevados) com âmbito de tarefa
- Acesso não-administrador com âmbito de conjunto
- Acesso de administrador com âmbito de conjunto

> [!IMPORTANT] 
> Tarefas em execução sob o escopo de tarefas não têm acesso de fato para outras tarefas num nó. No entanto, um utilizador mal intencionado com acesso à conta de poderia contornar esta restrição ao submeter uma tarefa que é executado com privilégios de administrador e aceda aos outros diretórios de tarefa. Um utilizador mal intencionado pode também utilizar RDP ou SSH para ligar a um nó. É importante proteger o acesso para as chaves de conta do Batch para impedir que um cenário como esse. Se suspeitar de que sua conta poderá ter sido comprometida, certifique-se de que regenerar as chaves.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Executar uma tarefa como um utilizador automático com acesso elevado

Pode configurar a especificação de auto-utilizador privilégios de administrador quando tiver de executar uma tarefa com acesso elevado. Por exemplo, uma tarefa de início poderá ter acesso elevado para instalar o software no nó.

> [!NOTE] 
> Em geral, é melhor usar acesso elevado apenas quando necessário. Melhores práticas recomendado conceder o privilégio mínimo necessário para alcançar o resultado desejado. Por exemplo, se uma tarefa de início instala o software para o utilizador atual, em vez de para todos os utilizadores, pode conseguir evitar conceder o acesso elevado para tarefas. Pode configurar a especificação de auto-utilizador para acesso de âmbito e não-administrador de conjunto de todas as tarefas que precisa para ser executado sob a mesma conta, incluindo a tarefa de início. 
>
>

Os fragmentos de código seguintes mostram como configurar a especificação de auto-utilizador. Os exemplos definido para a elevação `Admin` e o escopo para `Task`. Âmbito da tarefa é a configuração padrão, mas é incluído aqui no exemplo.

#### <a name="batch-net"></a>.NET do Batch

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Executar uma tarefa como um utilizador automático com âmbito de conjunto

Quando um nó é aprovisionado, dois em todo o agrupamento automático as contas de usuários são criadas em cada nó no conjunto, uma com acesso elevado e outra sem acesso elevado. Definir o âmbito do utilizador automaticamente ao âmbito de agrupamento para uma determinada tarefa é executada a tarefa em uma destas duas contas de usuário automaticamente em todo o conjunto. 

Quando especificar âmbito de conjunto para o auto-utilizador, todas as tarefas que são executados com acesso de administrador a ser executado sob a mesma conta de usuário automaticamente em todo o conjunto. Da mesma forma, tarefas que são executados sem permissões de administrador também são executadas numa única em todo o agrupamento automático-conta de utilizador. 

> [!NOTE] 
> As duas contas de usuário automaticamente em todo o conjunto são contas separadas. Tarefas em execução sob a conta administrativa de todo o conjunto não podem partilhar dados com tarefas em execução sob a conta padrão e vice-versa. 
>
>

A vantagem à execução sob a mesma conta de utilizador automático é que as tarefas são capazes de partilhar dados com outras tarefas em execução no mesmo nó.

Compartilhamento de segredos entre tarefas é um cenário em que a execução de tarefas em uma das duas contas de usuário automaticamente em todo o agrupamento é útil. Por exemplo, suponha que uma tarefa de início tem de aprovisionar um segredo para o nó que podem utilizar outras tarefas. Poderia usar a API de proteção de dados de Windows (DPAPI), mas ela requer privilégios de administrador. Em vez disso, pode proteger o segredo no nível do usuário. Tarefas em execução na mesma conta de usuário podem acessar o segredo sem acesso elevado.

Outro cenário em que pode querer executar tarefas numa conta de utilizador automático com o âmbito de agrupamento é uma partilha de ficheiros de Message Passing Interface (MPI). Uma partilha de ficheiros MPI é útil quando os nós na tarefa MPI precisam para trabalhar com os mesmos dados de ficheiro. O nó principal cria uma partilha de ficheiros que os nós subordinados podem aceder, se estiverem a executar sob a mesma conta de utilizador automaticamente. 

O seguinte trecho de código define o âmbito do utilizador automaticamente ao âmbito de agrupamento para uma tarefa no .NET do Batch. O nível de elevação for omitido, para a tarefa seja executada sob a conta de todo o agrupamento automático-usuário padrão.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Contas de utilizador nomeado

Pode definir as contas de utilizador nomeado quando cria um conjunto. Uma conta de utilizador nomeado tem um nome e uma palavra-passe que fornecer. Pode especificar o nível de elevação para uma conta de utilizador nomeado. Para nós do Linux, também pode fornecer uma chave privada SSH.

Uma conta de utilizador nomeado existe em todos os nós no conjunto e está disponível para todas as tarefas em execução em nós. Pode definir qualquer número de utilizadores nomeados para um conjunto. Quando adiciona uma tarefa ou a coleção de tarefas, pode especificar que a tarefa é executada em uma das contas de utilizador nomeado definidas no conjunto.

Uma conta de utilizador nomeado é útil quando deseja executar todas as tarefas numa tarefa sob a mesma conta de utilizador, mas isolá-las a partir de tarefas em execução nas outras tarefas em simultâneo. Por exemplo, pode criar um utilizador com o nome de cada tarefa e executar tarefas de cada tarefa nessa conta de utilizador nomeado. Cada tarefa, em seguida, pode partilhar um segredo com suas próprias tarefas, mas não com tarefas em execução nas outras tarefas.

Também pode utilizar uma conta de utilizador nomeado para executar uma tarefa que define as permissões em recursos externos, como partilhas de ficheiros. Com uma conta de utilizador nomeado, controlar a identidade do usuário e pode utilizar essa identidade de utilizador para definir as permissões.  

As contas de utilizador nomeado permitem sem palavra-passe SSH entre os nós do Linux. Pode utilizar uma conta de utilizador nomeado connosco do Linux que precisam executar tarefas de várias instâncias. Cada nó no conjunto pode executar tarefas numa conta de usuário definidos no pool de todo. Para obter mais informações sobre tarefas de várias instâncias, consulte [utilizar várias\-tarefas para executar aplicações MPI de instância](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Criar contas de utilizador nomeado

Para criar contas de utilizador nomeado no Batch, adicione uma coleção de contas de utilizador para o conjunto. Os fragmentos de código seguintes mostram como criar contas de utilizador nomeado no .NET, Java e Python. Estes trechos de código mostram como criar o administrador e não-administrador com o nome contas num agrupamento. Os exemplos criar conjuntos com a configuração do serviço em nuvem, mas utilize a mesma abordagem ao criar um agrupamento do Windows ou Linux utilizar a configuração de máquina virtual.

#### <a name="batch-net-example-windows"></a>Exemplo de .NET do batch (Windows)

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

#### <a name="batch-net-example-linux"></a>Exemplo de .NET do batch (Linux)

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


#### <a name="batch-java-example"></a>Exemplo de Java do batch

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

#### <a name="batch-python-example"></a>Exemplo de Python do batch

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Executar uma tarefa numa conta de utilizador nomeado com acesso elevado

Para executar uma tarefa como um usuário elevado, defina a tarefa **UserIdentity** propriedade para uma conta de utilizador nomeado, que foi criada com seu **ElevationLevel** propriedade definida como `Admin`.

Este fragmento de código especifica que a tarefa deve ser executado sob uma conta de utilizador nomeado. Esta conta de utilizador nomeado foi definida no conjunto, quando o conjunto foi criado. Neste caso, a conta de utilizador nomeado foi criada com permissões de administrador:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Atualizar seu código com a biblioteca de cliente mais recente do Batch

A versão de serviço do Batch 2017-01-01.4.0 introduz uma mudança fundamental, substituindo os **runElevated** propriedade disponível em versões anteriores com o **userIdentity** propriedade. As tabelas seguintes fornecem um mapeamento simple que pode utilizar para atualizar seu código de versões anteriores das bibliotecas de cliente.

### <a name="batch-net"></a>.NET do Batch

| Se utilizar o seu código...                  | Atualizá-la para...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` Não foi especificado | Nenhuma atualização necessária                                                                                               |

### <a name="batch-java"></a>Batch Java

| Se utilizar o seu código...                      | Atualizá-la para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` Não foi especificado | Nenhuma atualização necessária                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Se utilizar o seu código...                      | Atualizá-la para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin))`             |
| `run_elevated` Não foi especificado | Nenhuma atualização necessária                                                                                                                                  |


## <a name="next-steps"></a>Passos Seguintes

* Para uma descrição geral aprofundada do Batch, consulte [soluções com o Batch de computação paralelas em grande escala de desenvolver](batch-api-basics.md).
