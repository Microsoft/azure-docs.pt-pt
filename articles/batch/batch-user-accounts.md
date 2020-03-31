---
title: Executar tarefas sob contas de utilizador - Lote Azure
description: É útil ser capaz de configurar a conta de utilizador sob a qual pretende que uma tarefa seja executada. Aprenda os tipos de contas de utilizador e como configurá-las.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/18/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: fee3dc764d2052185160a4ba6b3d70854c54eeac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252275"
---
> [!NOTE] 
> As contas de utilizador discutidas neste artigo são diferentes das contas dos utilizadores utilizadas para o Protocolo de Ambiente de Trabalho Remoto (RDP) ou Secure Shell (SSH), por razões de segurança. 
>
> Para ligar a um nó que executa a configuração da máquina virtual Linux via SSH, consulte [Use Remote Desktop a um Linux VM em Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Para ligar aos nós que executam o Windows via RDP, consulte [Connect to a Windows Server VM](../virtual-machines/windows/connect-logon.md).<br /><br />
> Para ligar a um nó que executa a configuração do serviço em nuvem via RDP, consulte [enable Remote Desktop Connection for a Role in Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Executar tarefas sob contas de utilizador em Lote

Uma tarefa no Lote Azure é sempre realizada sob uma conta de utilizador. Por predefinição, as tarefas são executadas sob as contas de utilizador padrão, sem permissões de administrador. Estas definições de conta de utilizador predefinidas são normalmente suficientes. Para certos cenários, no entanto, é útil ser capaz de configurar a conta de utilizador sob a qual pretende que uma tarefa seja executada. Este artigo discute os tipos de contas de utilizador e como as pode configurar para o seu cenário.

## <a name="types-of-user-accounts"></a>Tipos de contas de utilizador

O Azure Batch fornece dois tipos de contas de utilizador para tarefas de execução:

- **Contas de utilizador automático.** As contas de utilizador automático são contas de utilizador incorporadas que são criadas automaticamente pelo serviço Batch. Por predefinição, as tarefas são executadas sob uma conta de utilizador automático. Pode configurar a especificação do utilizador automático para uma tarefa que indique em que conta de utilizador automático uma tarefa deve ser executada. A especificação de utilizador automático permite especificar o nível de elevação e o âmbito da conta de utilizador automático que executará a tarefa. 

- **Uma conta de utilizador nomeada.** Pode especificar uma ou mais contas de utilizador nomeadas para uma piscina quando criar a piscina. Cada conta de utilizador é criada em cada nó da piscina. Além do nome da conta, especifica a palavra-passe da conta de utilizador, o nível de elevação e, para as piscinas Linux, a chave privada SSH. Quando adicionar uma tarefa, pode especificar a conta de utilizador indicada sob a qual essa tarefa deve ser executada.

> [!IMPORTANT] 
> A versão de serviço do Batch 2017-01-01.4.0 introduz uma alteração de rutura que requer que atualize o seu código para ligar para essa versão. Se estiver a migrar código de uma versão mais antiga do Batch, note que a propriedade **runElevated** já não é suportada nas bibliotecas de clientes REST API ou Batch. Utilize a nova propriedade **userIdentity** de uma tarefa para especificar o nível de elevação. Consulte a secção intitulada [Update your code to the latest Batch client library](#update-your-code-to-the-latest-batch-client-library) para obter diretrizes rápidas para atualizar o seu código de lote se estiver a utilizar uma das bibliotecas do cliente.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Acesso à conta de utilizador a ficheiros e diretórios

Tanto uma conta de utilizador automático como uma conta de utilizador nomeada têm acesso a leitura/escrita ao diretório de trabalho da tarefa, ao diretório partilhado e ao diretório de tarefas de várias instâncias. Ambos os tipos de contas leram acesso às direções de arranque e preparação de emprego.

Se uma tarefa for submetida à mesma conta que foi utilizada para executar uma tarefa inicial, a tarefa tem acesso de leitura-escrita ao diretório de tarefas inpartidas. Da mesma forma, se uma tarefa for submetida à mesma conta que foi utilizada para executar uma tarefa de preparação de emprego, a tarefa tem acesso de leitura-escrita ao diretório de tarefas de preparação de emprego. Se uma tarefa for submetida a uma conta diferente da tarefa inicial ou da tarefa de preparação do trabalho, a tarefa só leu o acesso ao respetivo diretório.

Para obter mais informações sobre o acesso a ficheiros e diretórios a partir de uma tarefa, consulte Desenvolver soluções de [computação paralela em larga escala com o Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Acesso elevado para tarefas 

O nível de elevação da conta de utilizador indica se uma tarefa funciona com acesso elevado. Tanto uma conta de utilizador automático como uma conta de utilizador nomeada podem ser executadas com acesso elevado. As duas opções para o nível de elevação são:

- **Não Admin:** A tarefa funciona como um utilizador padrão sem acesso elevado. O nível de elevação padrão para uma conta de utilizador do Lote é sempre **NonAdmin**.
- **Administrador:** A tarefa funciona como um utilizador com acesso elevado e opera com permissões completas do Administrador. 

## <a name="auto-user-accounts"></a>Contas de utilizador automático

Por predefinição, as tarefas são executadas no Batch sob uma conta de utilizador automático, como um utilizador padrão sem acesso elevado, e com âmbito de tarefa. Quando a especificação do utilizador automático estiver configurada para o âmbito de tarefas, o serviço Batch cria uma conta de utilizador automático apenas para essa tarefa.

A alternativa ao âmbito de tarefa é o âmbito da piscina. Quando a especificação de utilizador automático para uma tarefa é configurada para o âmbito do pool, a tarefa é executado sob uma conta de utilizador automático que está disponível para qualquer tarefa na piscina. Para obter mais informações sobre o âmbito do pool, consulte a secção intitulada Executar uma tarefa como o utilizador automático com âmbito de piscina.   

O âmbito padrão é diferente nos nódosos Windows e Linux:

- Nos nós do Windows, as tarefas são executadas sob âmbito de tarefa por padrão.
- Os nódoslinos linux sempre correm sob o âmbito da piscina.

Existem quatro configurações possíveis para a especificação de utilizador automático, cada uma das quais corresponde a uma conta única de utilizador automático:

- Acesso não administrativo com âmbito de tarefa (especificação predefinida do utilizador automático)
- Acesso administrativo (elevado) com âmbito de tarefa
- Acesso não administrativo com âmbito de piscina
- Acesso à administração com âmbito de piscina

> [!IMPORTANT] 
> As tarefas que executam o âmbito de tarefa não têm acesso de facto a outras tarefas num nó. No entanto, um utilizador malicioso com acesso à conta poderia contornar esta restrição, apresentando uma tarefa que corre com privilégios de administrador e acede a outros diretórios de tarefas. Um utilizador malicioso também poderia usar RDP ou SSH para ligar a um nó. É importante proteger o acesso às chaves da sua conta Batch para evitar tal cenário. Se suspeitar que a sua conta pode ter sido comprometida, certifique-se de regenerar as chaves.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Executar uma tarefa como auto-utilizador com acesso elevado

Pode configurar a especificação de utilizador automático para privilégios de administrador quando necessitar de executar uma tarefa com acesso elevado. Por exemplo, uma tarefa inicial pode necessitar de acesso elevado para instalar software no nó.

> [!NOTE] 
> Em geral, o melhor é usar acesso elevado apenas quando necessário. As melhores práticas recomendam a concessão do privilégio mínimo necessário para alcançar o resultado desejado. Por exemplo, se uma tarefa inicial instalar software para o utilizador atual, em vez de para todos os utilizadores, poderá evitar conceder acesso elevado a tarefas. Pode configurar a especificação de utilizador automático para o âmbito do pool e acesso não administrativo para todas as tarefas que precisam de ser executadas sob a mesma conta, incluindo a tarefa de início. 
>
>

Os seguintes fragmentos de código mostram como configurar a especificação de utilizador automático. Os exemplos estabelecem `Admin` o nível `Task`de elevação e o alcance para . O âmbito de tarefa é a definição padrão, mas está incluído aqui por uma questão de exemplo.

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Executar uma tarefa como um utilizador automático com âmbito de piscina

Quando um nó é provisionado, duas contas de auto-utilizador em toda a piscina são criadas em cada nó na piscina, uma com acesso elevado e outra sem acesso elevado. A definição do âmbito do utilizador automático para a margem de manobra para uma determinada tarefa executa a tarefa sob uma destas duas contas de utilizador automático em toda a piscina. 

Quando especifica o âmbito do pool para o utilizador automático, todas as tarefas que funcionam com o acesso do administrador são executadas na mesma conta de utilizador automático em toda a piscina. Da mesma forma, as tarefas que funcionam sem permissões de administrador também são executadas sob uma única conta de utilizador automático em toda a piscina. 

> [!NOTE] 
> As duas contas de utilizador automático em toda a piscina são contas separadas. As tarefas que executam sob a conta administrativa em toda a piscina não podem partilhar dados com tarefas em execução sob a conta padrão, e vice-versa. 
>
>

A vantagem de executar sob a mesma conta de utilizador automático é que as tarefas são capazes de partilhar dados com outras tarefas que executam no mesmo nó.

Partilhar segredos entre tarefas é um cenário em que executar tarefas sob uma das duas contas de auto-utilizador em toda a piscina é útil. Por exemplo, suponha que uma tarefa inicial precisa fornecer um segredo sobre o nó que outras tarefas podem usar. Pode utilizar a API de Proteção de Dados do Windows (DPAPI), mas requer privilégios de administrador. Em vez disso, pode proteger o segredo ao nível do utilizador. As tarefas que executam na mesma conta de utilizador podem aceder ao segredo sem acesso elevado.

Outro cenário em que poderá querer executar tarefas sob uma conta de utilizador automático com âmbito de piscina é uma partilha de ficheiros de Interface de Passagem de Mensagem (MPI). Uma partilha de ficheiros MPI é útil quando os nódosos na tarefa MPI precisam de trabalhar nos mesmos dados de ficheiros. O nó da cabeça cria uma partilha de ficheiros a que os nódosos da criança podem aceder se estiverem a funcionar sob a mesma conta de utilizador automático. 

O seguinte código de corte define o âmbito do utilizador automático para a margem de manobra de piscina para uma tarefa no Lote .NET. O nível de elevação é omitido, pelo que a tarefa é executado sob a conta de utilizador automático padrão em todo o pool.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Contas de utilizador nomeadas

Pode definir contas de utilizador nomeadas quando criar uma piscina. Uma conta de utilizador nomeada tem um nome e senha que fornece. Pode especificar o nível de elevação de uma conta de utilizador nomeada. Para nós Linux, você também pode fornecer uma chave privada SSH.

Uma conta de utilizador nomeada existe em todos os nós da piscina e está disponível para todas as tarefas em execução nesses nós. Você pode definir qualquer número de utilizadores nomeados para uma piscina. Ao adicionar uma tarefa ou recolha de tarefas, pode especificar que a tarefa é submetida a uma das contas de utilizador nomeadas definidas na piscina.

Uma conta de utilizador nomeada é útil quando pretende executar todas as tarefas num trabalho sob a mesma conta de utilizador, mas isolá-las de tarefas que executam outros postos de trabalho ao mesmo tempo. Por exemplo, pode criar um utilizador nomeado para cada trabalho e executar as tarefas de cada trabalho sob a conta de utilizador nomeada. Cada trabalho pode então partilhar um segredo com as suas próprias tarefas, mas não com tarefas a funcionar noutros postos de trabalho.

Também pode utilizar uma conta de utilizador nomeada para executar uma tarefa que define permissões em recursos externos, tais como partilhas de ficheiros. Com uma conta de utilizador nomeada, controla a identidade do utilizador e pode utilizar essa identidade de utilizador para definir permissões.  

As contas de utilizador nomeadas permitem sSH sem palavra-passe entre nós Linux. Pode utilizar uma conta de utilizador com nódosos Linux que precisam de executar tarefas de várias instâncias. Cada nó na piscina pode executar tarefas sob uma conta de utilizador definida em toda a piscina. Para obter mais informações sobre tarefas de várias instâncias, consulte [Utilize tarefas de várias\-instâncias para executar aplicações de MPI](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Criar contas de utilizador nomeadas

Para criar contas de utilizador nomeadas no Batch, adicione uma coleção de contas de utilizador à piscina. Os seguintes códigos mostram como criar contas de utilizador nomeadas em .NET, Java e Python. Estes fragmentos de código mostram como criar contas nomeadas por administração e não administradores numa piscina. Os exemplos criam piscinas utilizando a configuração do serviço em nuvem, mas você usa a mesma abordagem ao criar uma piscina Windows ou Linux usando a configuração da máquina virtual.

#### <a name="batch-net-example-windows"></a>Exemplo de lote .NET (Windows)

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

#### <a name="batch-net-example-linux"></a>Lote .NET exemplo (Linux)

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


#### <a name="batch-java-example"></a>Exemplo de Java lote

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

#### <a name="batch-python-example"></a>Exemplo de Python de lote

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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Executar uma tarefa sob uma conta de utilizador nomeada com acesso elevado

Para executar uma tarefa como utilizador elevado, detete a propriedade **UserIdentity** da tarefa para uma `Admin`conta de utilizador nomeada que foi criada com a sua propriedade **ElevationLevel** definida para .

Este código de corte especifica que a tarefa deve ser executada sob uma conta de utilizador nomeada. Esta conta de utilizador nomeada foi definida na piscina quando a piscina foi criada. Neste caso, a conta de utilizador nomeada foi criada com permissões de administração:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Atualize o seu código para a mais recente biblioteca de clientes do Batch

A versão de serviço do Batch 2017-01-01.4.0 introduz uma alteração de rutura, substituindo a propriedade **runElevated** disponível em versões anteriores pela propriedade **userIdentity.** As tabelas seguintes fornecem um simples mapeamento que pode utilizar para atualizar o seu código a partir de versões anteriores das bibliotecas de clientes.

### <a name="batch-net"></a>.NET do Batch

| Se o seu código for...                  | Atualize-o para...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`não especificado | Nenhuma atualização necessária                                                                                               |

### <a name="batch-java"></a>Batch Java

| Se o seu código for...                      | Atualize-o para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`não especificado | Nenhuma atualização necessária                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Se o seu código for...                      | Atualize-o para...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, onde <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated`não especificado | Nenhuma atualização necessária                                                                                                                                  |


## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral aprofundada do Lote, consulte Desenvolver soluções de [computação paralela em larga escala com lote](batch-api-basics.md).
