---
title: Executar tarefas em contas de utilizador
description: Aprenda os tipos de contas de utilizador e como configurá-las.
ms.topic: how-to
ms.date: 08/20/2020
ms.custom: seodec18
ms.openlocfilehash: cce374e7d7ffb513bed882b048ea54bcbad81b0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719364"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Executar tarefas em contas de utilizadores em Batch

> [!NOTE]
> As contas de utilizador discutidas neste artigo são diferentes das contas de utilizador utilizadas para o Protocolo de Ambiente de Trabalho Remoto (RDP) ou Secure Shell (SSH), por razões de segurança.
>
> Para ligar a um nó que executa a configuração da máquina virtual Linux via SSH, consulte [utilizar o Ambiente de Trabalho Remoto para um Linux VM em Azure](../virtual-machines/linux/use-remote-desktop.md). Para ligar aos nós que executam o Windows via RDP, consulte [Connect a um VM do Servidor do Windows](../virtual-machines/windows/connect-logon.md).<br /><br />
> Para ligar a um nó que executa a configuração do serviço de nuvem através de RDP, consulte [Ativar a Conexão de Ambiente de Trabalho Remoto para uma função nos serviços de cloud Azure](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).

Uma tarefa em Azure Batch é sempre executada numa conta de utilizador. Por predefinição, as tarefas são executadas em contas de utilizador padrão, sem permissões de administrador. Para determinados cenários, poderá querer configurar a conta de utilizador na qual pretende que uma tarefa seja executada. Este artigo discute os tipos de contas de utilizador e como configurá-las para o seu cenário.

## <a name="types-of-user-accounts"></a>Tipos de contas de utilizador

O Azure Batch fornece dois tipos de contas de utilizador para executar tarefas:

- **Contas de utilizador automático.** As contas de utilizador automático são contas de utilizador incorporadas que são criadas automaticamente pelo serviço Batch. Por predefinição, as tarefas são executadas numa conta de utilizador automático. Pode configurar a especificação do utilizador automático para uma tarefa que indique em que conta de utilizador automático uma tarefa deve ser executada. A especificação do utilizador automático permite especificar o nível de elevação e o âmbito da conta do utilizador automático que executará a tarefa.

- **Uma conta de utilizador com nome.** Pode especificar uma ou mais contas de utilizador nomeadas para uma piscina quando criar a piscina. Cada conta de utilizador é criada em cada nó da piscina. Além do nome da conta, especifica a palavra-passe da conta de utilizador, o nível de elevação e, para as piscinas Linux, a chave privada SSH. Quando adicionar uma tarefa, pode especificar a conta de utilizador nomeada sob a qual essa tarefa deve ser executada.

> [!IMPORTANT]
> A versão de serviço Batch 2017-01-01.4.0 introduz uma alteração de rutura que requer que atualize o seu código para ligar para essa versão. Se estiver a migrar código a partir de uma versão mais antiga do Batch, note que a propriedade **runElevated** já não é suportada nas bibliotecas de clientes REST API ou Batch. Utilize a nova propriedade **da entidade userDin** de uma tarefa para especificar o nível de elevação. Consulte [o seu código para a mais recente biblioteca de clientes do Batch](#update-your-code-to-the-latest-batch-client-library) para obter orientações rápidas para atualizar o seu código Batch se estiver a utilizar uma das bibliotecas do cliente.

## <a name="user-account-access-to-files-and-directories"></a>Acesso da conta de utilizador a ficheiros e diretórios

Tanto uma conta de utilizador automático como uma conta de utilizador nomeada têm acesso a leitura/escrita ao diretório de trabalho da tarefa, diretório partilhado e diretório de tarefas multi-instâncias. Ambos os tipos de contas leram o acesso aos diretórios de arranque e preparação de emprego.

Se uma tarefa for submetida à mesma conta que foi utilizada para executar uma tarefa inicial, a tarefa tem acesso de leitura-escrita ao diretório de tarefas inicial. Da mesma forma, se uma tarefa funcionar na mesma conta que foi utilizada para executar uma tarefa de preparação de emprego, a tarefa tem acesso de leitura-escrita ao diretório de tarefas de preparação de emprego. Se uma tarefa for submetida a uma conta diferente da tarefa inicial ou da tarefa de preparação do trabalho, então a tarefa apenas leu o acesso ao respetivo diretório.

Para obter mais informações sobre o acesso a ficheiros e diretórios a partir de uma tarefa, consulte [Ficheiros e diretórios](files-and-directories.md).

## <a name="elevated-access-for-tasks"></a>Acesso elevado a tarefas

O nível de elevação da conta de utilizador indica se uma tarefa funciona com acesso elevado. Tanto uma conta de utilizador automático como uma conta de utilizador nomeada podem ser executadas com acesso elevado. As duas opções para o nível de elevação são:

- **NonAdmin:** A tarefa funciona como um utilizador padrão sem acesso elevado. O nível de elevação predefinido para uma conta de utilizador do Lote é sempre **NonAdmin**.
- **Administrador:** A tarefa funciona como um utilizador com acesso elevado e funciona com permissões completas do Administrador.

## <a name="auto-user-accounts"></a>Contas de utilizador automático

Por predefinição, as tarefas são executadas em Batch sob uma conta de utilizador automático, como utilizador padrão sem acesso elevado, e com âmbito de piscina. O âmbito do pool significa que a tarefa é executado sob uma conta de utilizador automático que está disponível para qualquer tarefa na piscina. Para obter mais informações sobre o âmbito do pool, consulte [executar uma tarefa como utilizador automático com âmbito de piscina](#run-a-task-as-an-auto-user-with-pool-scope).

A alternativa ao âmbito da piscina é o âmbito da tarefa. Quando a especificação do utilizador automático está configurada para o âmbito de tarefa, o serviço Batch cria uma conta de utilizador automático apenas para essa tarefa.

Existem quatro configurações possíveis para a especificação do utilizador automático, cada uma das quais corresponde a uma conta única de utilizador automático:

- Acesso não administrativo com âmbito de tarefa
- Acesso de administração (elevado) com âmbito de tarefa
- Acesso não administrativo com âmbito de piscina
- Acesso a administrador com âmbito de piscina

> [!IMPORTANT]
> As tarefas que executam no âmbito da tarefa não têm acesso de facto a outras tarefas num nó. No entanto, um utilizador mal-intencionado com acesso à conta poderia contornar esta restrição, apresentando uma tarefa que executa com privilégios de administrador e acede a outros diretórios de tarefas. Um utilizador malicioso também pode usar RDP ou SSH para ligar a um nó. É importante proteger o acesso às chaves da sua conta Batch para evitar tal cenário. Se suspeita que a sua conta pode ter sido comprometida, certifique-se de regenerar as suas chaves.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Executar uma tarefa como utilizador automático com acesso elevado

Pode configurar a especificação do utilizador automático para privilégios de administrador quando precisa executar uma tarefa com acesso elevado. Por exemplo, uma tarefa inicial pode necessitar de acesso elevado para instalar software no nó.

> [!NOTE]
> Utilize acesso elevado apenas quando necessário. As melhores práticas recomendam a concessão do privilégio mínimo necessário para alcançar o resultado desejado. Por exemplo, se uma tarefa inicial instalar software para o utilizador atual, em vez de para todos os utilizadores, poderá evitar conceder um acesso elevado às tarefas. Pode configurar a especificação do utilizador automático para o âmbito do pool e acesso não administrativo para todas as tarefas que precisam de ser executadas na mesma conta, incluindo a tarefa inicial.

Os seguintes fragmentos de código mostram como configurar a especificação do utilizador automático. Os exemplos definem o nível de elevação `Admin` para e o âmbito para `Task` .

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Executar uma tarefa como utilizador automático com âmbito de piscina

Quando um nó é a provisionado, duas contas de utilizador automático em toda a piscina são criadas em cada nó na piscina, uma com acesso elevado e outra sem acesso elevado. Definir o âmbito do utilizador automático para o âmbito de pool para uma determinada tarefa executa a tarefa sob uma destas duas contas de utilizador automático em toda a piscina.

Quando especifica o alcance do pool para o utilizador automático, todas as tarefas que executam com acesso ao administrador são executadas na mesma conta de utilizador automático em toda a piscina. Da mesma forma, as tarefas que funcionam sem permissões de administrador também são executadas numa única conta de utilizador automático em toda a piscina.

> [!NOTE]
> As duas contas de utilizadores automáticos em toda a piscina são contas separadas. As tarefas que executam sob a conta administrativa em toda a piscina não podem partilhar dados com tarefas a decorrer sob a conta padrão, e vice-versa.

A vantagem de executar sob a mesma conta de utilizador automático é que as tarefas são capazes de partilhar dados com outras tarefas que executam no mesmo nó.

Partilhar segredos entre tarefas é um cenário em que executar tarefas sob uma das duas contas de utilizadores automáticos em toda a piscina é útil. Por exemplo, suponha que uma tarefa inicial precisa de providenciar um segredo sobre o nó que outras tarefas podem usar. Pode utilizar a API de Proteção de Dados do Windows (DPAPI), mas requer privilégios de administrador. Em vez disso, pode proteger o segredo ao nível do utilizador. As tarefas que executam na mesma conta de utilizador podem aceder ao segredo sem acesso elevado.

Outro cenário em que poderá querer executar tarefas numa conta de utilizador automático com âmbito de piscina é uma partilha de ficheiros da Interface de Passagem de Mensagens (MPI). Uma partilha de ficheiros MPI é útil quando os nós na tarefa MPI precisam de funcionar nos mesmos dados de ficheiro. O nó da cabeça cria uma partilha de ficheiro a que os nós da criança podem aceder se estiverem a funcionar na mesma conta de utilizador automático.

O seguinte corte de código define o âmbito do utilizador automático para o âmbito de pool para uma tarefa em Batch .NET. O nível de elevação é omitido, pelo que a tarefa é executado sob a conta padrão de utilizador automático em toda a piscina.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Contas de utilizador nomeadas

Pode definir contas de utilizador com nome quando criar uma piscina. Uma conta de utilizador com nome tem um nome e senha que fornece. Pode especificar o nível de elevação de uma conta de utilizador nomeada. Para os nós Linux, também pode fornecer uma chave privada SSH.

Existe uma conta de utilizador nomeada em todos os nós da piscina e está disponível para todas as tarefas que executam nesses nós. Pode definir qualquer número de utilizadores nomeados para uma piscina. Quando adicionar uma tarefa ou uma recolha de tarefas, pode especificar que a tarefa é executado numa das contas de utilizador indicada definidas no pool.

Uma conta de utilizador com nome é útil quando pretende executar todas as tarefas num trabalho na mesma conta de utilizador, mas isolá-las de tarefas executadas noutros trabalhos ao mesmo tempo. Por exemplo, pode criar um utilizador nomeado para cada trabalho e executar as tarefas de cada trabalho na conta de utilizador com o nome. Cada trabalho pode então partilhar um segredo com as suas próprias tarefas, mas não com tarefas em execução noutros trabalhos.

Também pode utilizar uma conta de utilizador nomeada para executar uma tarefa que define permissões em recursos externos, tais como ações de ficheiros. Com uma conta de utilizador nomeada, controla a identidade do utilizador e pode usar essa identidade do utilizador para definir permissões.  

As contas de utilizador com nome permitem sSH sem palavra-passe entre os nós Linux. Pode utilizar uma conta de utilizador com nódoas Linux que necessite de executar tarefas de várias instâncias. Cada nó na piscina pode executar tarefas sob uma conta de utilizador definida em toda a piscina. Para obter mais informações sobre tarefas de vários casos, consulte [utilizar tarefas de \- vários casos para executar aplicações de MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Criar contas de utilizador nomeadas

Para criar contas de utilizador nomeadas em Batch, adicione uma coleção de contas de utilizador à piscina. Os seguintes snippets de código mostram como criar contas de utilizador nomeadas em .NET, Java e Python. Estes códigos mostram como criar contas nomeadas de administrador e não administrador numa piscina. Os exemplos criam piscinas utilizando a configuração do serviço de nuvem, mas você usa a mesma abordagem ao criar uma piscina Windows ou Linux usando a configuração da máquina virtual.

#### <a name="batch-net-example-windows"></a>Exemplo lote .NET (Windows)

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

#### <a name="batch-net-example-linux"></a>Exemplo do lote .NET (Linux)

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

#### <a name="batch-java-example"></a>Exemplo de Lote Java

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

#### <a name="batch-python-example"></a>Exemplo de Lote Python

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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Executar uma tarefa sob uma conta de utilizador com acesso elevado

Para executar uma tarefa como utilizador elevado, desenrânceu a propriedade da **UserIdentity** da tarefa a uma conta de utilizador nomeada que foi criada com a sua propriedade **ElevationLevel** definida para `Admin` .

Este código de corte especifica que a tarefa deve ser executada numa conta de utilizador nomeada. Esta conta de utilizador com nome foi definida na piscina quando a piscina foi criada. Neste caso, a conta de utilizador nomeada foi criada com permissões de administração:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Atualize o seu código para a mais recente biblioteca de clientes do Batch

A versão de serviço Batch 2017-01-01.4.0 introduz uma mudança de rutura, substituindo a propriedade **runElevated** disponível em versões anteriores com a propriedade **userIdentity.** As tabelas que se seguem fornecem um simples mapeamento que pode utilizar para atualizar o seu código a partir de versões anteriores das bibliotecas do cliente.

### <a name="batch-net"></a>.NET do Batch

| Se o seu código usar...                  | Atualize-o para.                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` não especificado | Nenhuma atualização necessária                                                                                               |

### <a name="batch-java"></a>Batch Java

| Se o seu código usar...                      | Atualize-o para.                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` não especificado | Nenhuma atualização necessária                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Se o seu código usar...                      | Atualize-o para.                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` não especificado | Nenhuma atualização necessária                                                                                                                                  |

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Saiba mais [sobre ficheiros e diretórios](files-and-directories.md) em Azure Batch.
