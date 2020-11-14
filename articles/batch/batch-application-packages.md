---
title: Implementar pacotes de aplicações para calcular os nosdes
description: Utilize a funcionalidade de pacotes de aplicação do Azure Batch para gerir facilmente várias aplicações e versões para instalação em nós computacional Batch.
ms.topic: how-to
ms.date: 09/24/2020
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperfq1
ms.openlocfilehash: 1bacb0c71c05aeb983bfa9ebf71873a22fea39a1
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "91277704"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Implementar aplicações para calcular os nóns com pacotes de aplicações batch

Os pacotes de aplicação podem simplificar o código na sua solução Azure Batch e facilitar a gestão das aplicações que as suas tarefas executam. Com pacotes de aplicações, pode carregar e gerir várias versões de aplicações que as suas tarefas executam, incluindo os seus ficheiros de suporte. Em seguida, pode implantar automaticamente uma ou mais destas aplicações nos nós de computação na sua piscina.

As APIs para a criação e gestão de pacotes de aplicações fazem parte da biblioteca [Batch Management .NET.](/dotnet/api/overview/azure/batch/management) As APIs para instalar pacotes de aplicações num nó de computação fazem parte da biblioteca [Batch .NET.](/dotnet/api/overview/azure/batch/client) Características comparáveis estão nas APIs do lote disponível para outras línguas.

Este artigo explica como carregar e gerir pacotes de aplicações no portal Azure. Também mostra como instalá-los nos nós computacionais de uma piscina com a biblioteca [Batch .NET.](/dotnet/api/overview/azure/batch/client)

## <a name="application-package-requirements"></a>Requisitos de pacote de aplicação

Para utilizar pacotes de aplicações, precisa [de ligar uma conta de Armazenamento Azure](#link-a-storage-account) à sua conta Batch.

Existem restrições ao número de pedidos e pacotes de aplicações dentro de uma conta Batch e no tamanho máximo do pacote de aplicação. Para mais informações, consulte [quotas e limites para o serviço Azure Batch](batch-quota-limit.md).

> [!NOTE]
> Os lotes criados antes de 5 de julho de 2017 não suportam pacotes de aplicações (a menos que tenham sido criados após 10 de março de 2016 usando a Configuração de Serviços cloud). A funcionalidade de pacotes de aplicações aqui descrita substitui a funcionalidade De Aplicações de Lote disponível em versões anteriores do serviço.

## <a name="understand-applications-and-application-packages"></a>Compreender aplicações e pacotes de aplicações

Dentro do Azure Batch, uma *aplicação* refere-se a um conjunto de binários versados que podem ser automaticamente descarregados para os nós computacional na sua piscina. Uma aplicação contém um ou mais pacotes de *aplicações* , que representam diferentes versões da aplicação.

Cada *pacote de aplicações* é um ficheiro .zip que contém os binários de aplicação e quaisquer ficheiros de suporte. Apenas o formato .zip é suportado.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagrama que mostra uma visão de alto nível das aplicações e pacotes de aplicações.":::

Pode especificar pacotes de aplicações no pool ou nível de tarefa.

- **Os pacotes de aplicação da** piscina são implantados em todos os nós da piscina. As aplicações são implementadas quando um nó une uma piscina, e quando é reiniciado ou reesimagem.
  
    Os pacotes de aplicação de piscina são apropriados quando todos os nós de uma piscina executarão as tarefas de um trabalho. Pode especificar um ou mais pacotes de aplicação para implementar quando criar uma piscina. Também pode adicionar ou atualizar os pacotes de uma piscina existente. Para instalar um novo pacote numa piscina existente, tem de reiniciar os seus nós.

- **Os pacotes de aplicação de tarefas são implantados apenas** num nó de computação programado para executar uma tarefa, pouco antes de executar a linha de comando da tarefa. Se o pacote e versão de aplicação especificados já estiver no nó, este não é reenviado e o pacote existente é utilizado.
  
    Os pacotes de aplicação de tarefas são úteis em ambientes de piscina partilhada, onde diferentes empregos funcionam numa piscina, e a piscina não é apagada quando um trabalho termina. Se o seu trabalho tiver menos tarefas do que nós no pool, os pacotes de aplicação de tarefas podem minimizar a transferência de dados, uma vez que a sua aplicação é implementada apenas para os nós que executam tarefas.
  
    Outros cenários que podem beneficiar de pacotes de aplicação de tarefas são empregos que executam uma grande aplicação, mas apenas para algumas tarefas. Por exemplo, as aplicações de tarefa podem ser úteis para uma fase de pré-processamento de pesos pesados ou uma tarefa de fusão.

Com pacotes de aplicações, a tarefa inicial da sua piscina não tem de especificar uma longa lista de ficheiros de recursos individuais para instalar nos nós. Não tem de gerir manualmente várias versões dos ficheiros da sua aplicação no Azure Storage ou nos seus nós. E não precisa de se preocupar em gerar [URLs SAS](../storage/common/storage-sas-overview.md) para fornecer acesso aos ficheiros da sua conta de Armazenamento. O lote trabalha em segundo plano com o Azure Storage para armazenar pacotes de aplicações e implantá-los para calcular nós.

> [!NOTE]
> O tamanho total de uma tarefa de início tem de ser inferior ou igual a 32 768 carateres, incluindo ficheiros de recursos e variáveis de ambiente. Se a sua tarefa inicial exceder este limite, a utilização de pacotes de aplicações é outra opção. Também pode criar um ficheiro .zip que contenha os seus ficheiros de recursos, carregá-lo como uma bolha para o Azure Storage e, em seguida, desapertá-lo da linha de comando da sua tarefa inicial.

## <a name="upload-and-manage-applications"></a>Carregar e gerir aplicações

Pode utilizar o [portal Azure](https://portal.azure.com) ou as APIs de Gestão de Lotes para gerir os pacotes de aplicações na sua conta Batch. As secções seguintes explicam como ligar uma conta de armazenamento e como adicionar e gerir aplicações e pacotes de aplicações no portal Azure.

### <a name="link-a-storage-account"></a>Ligue uma conta de armazenamento

Para utilizar pacotes de aplicações, tem de ligar uma [conta de Armazenamento Azure](accounts.md#azure-storage-accounts) à sua conta Batch. O serviço Batch utilizará a conta de armazenamento associada para armazenar os seus pacotes de aplicações. Recomendamos que crie uma conta de armazenamento especificamente para utilização com a sua conta Batch.

Se ainda não tiver configurado uma conta de armazenamento, o portal Azure apresenta um aviso na primeira vez que seleciona **Aplicações** na sua conta Batch. Para ligar uma conta de armazenamento à sua conta Batch, selecione **a conta de Armazenamento** na janela **Aviso** e, em seguida, selecione a Conta **de Armazenamento** novamente.

Depois de ter ligado as duas contas, o Batch pode implantar automaticamente os pacotes armazenados na conta de Armazenamento ligada aos seus nós de computação.

> [!IMPORTANT]
> Não é possível utilizar pacotes de aplicações com contas de Armazenamento Azure configuradas com [regras de firewall](../storage/common/storage-network-security.md), ou com **espaço de nome hierárquico** definido para **Ativado**.

O serviço Batch utiliza o Azure Storage para armazenar os seus pacotes de aplicações como bolhas de bloqueio. Você é [cobrado normalmente](https://azure.microsoft.com/pricing/details/storage/) para os dados do bloco blob, e o tamanho de cada embalagem não pode exceder o tamanho máximo de blob de bloco. Para obter mais informações, consulte [a escalabilidade do armazenamento Azure e os objetivos de desempenho para contas de armazenamento.](../storage/blobs/scalability-targets.md) Para minimizar os custos, certifique-se de considerar o tamanho e o número dos seus pacotes de aplicação e remover periodicamente pacotes prectados.

### <a name="view-current-applications"></a>Ver aplicações atuais

Para ver as aplicações na sua conta Batch, selecione **Aplicações** no menu de navegação à esquerda.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Screenshot do item do menu aplicações no portal Azure.":::

A seleção desta opção de menu abre a janela **Aplicações.** Esta janela apresenta o ID de cada aplicação na sua conta e as seguintes propriedades:

- **Pacotes** : Número de versões associadas a esta aplicação.
- **Versão predefinida** : Se aplicável, a versão de aplicação que será instalada se nenhuma versão for especificada ao implementar a aplicação.
- **Permitir atualizações** : Especifica se são permitidas atualizações e supressões de pacotes.

Para ver a estrutura de [ficheiros](files-and-directories.md) do pacote de aplicações num nó de computação, navegue para a sua conta Batch no portal Azure. Selecione **Piscinas**. em seguida, selecione a piscina que contém o nó de computação. Selecione o nó de cálculo no qual o pacote de aplicações está instalado e abra a pasta **de aplicações.**

### <a name="view-application-details"></a>Ver detalhes da aplicação

Para ver os detalhes de uma aplicação, selecione-o na janela **Aplicações.** Pode configurar as seguintes definições para a sua aplicação.

- **Permitir atualizações** : Indica se os pacotes de aplicações podem ser [atualizados ou eliminados](#update-or-delete-an-application-package). A predefinição é **Sim**. Se definidos para **Nº** , os pacotes de aplicações existentes não podem ser atualizados ou eliminados, mas ainda podem ser adicionadas novas versões de pacotes de aplicações.
- **Versão predefinida** : O pacote de aplicação predefinido a utilizar quando a aplicação for implementada, se nenhuma versão for especificada.
- **Nome do visor** : Um nome amigável que a sua solução Batch pode usar quando apresenta informações sobre a aplicação. Por exemplo, este nome pode ser usado na UI de um serviço que fornece aos seus clientes através do Batch.

### <a name="add-a-new-application"></a>Adicionar uma nova aplicação

Para criar uma nova aplicação, adicione um pacote de aplicações e especifique um ID de aplicação único.

Na sua conta Batch, selecione **Aplicações** e, em seguida, **selecione Adicionar**.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Screenshot do novo processo de criação de aplicações no portal Azure.":::

Introduza as seguintes informações:

- **ID de aplicação** : O ID da sua nova aplicação.
- **Versão** ": A versão para o pacote de aplicações que está a carregar.
- **Pacote de aplicações** : O ficheiro .zip que contém os binários de aplicação e ficheiros de suporte necessários para executar o pedido.

O ID de **aplicação** e **a versão** que inseri devem seguir estes requisitos:

- Nos nós windows, o ID pode conter qualquer combinação de caracteres alfanuméricos, hífens e sublinhados. Nos nós Linux, apenas são permitidos caracteres alfanuméricos e sublinhados.
- Não pode conter mais de 64 caracteres.
- Deve ser único na conta do Lote.
- As identificações são de preservação de casos e insensíveis a casos.

Quando estiver pronto, **selecione Submeter.** Depois de o ficheiro .zip ter sido enviado para a sua conta de Armazenamento Azure, o portal apresenta uma notificação. Dependendo do tamanho do ficheiro que está a carregar e da velocidade da sua ligação de rede, isto pode demorar algum tempo.

### <a name="add-a-new-application-package"></a>Adicione um novo pacote de aplicações

Para adicionar uma versão pacote de aplicação para uma aplicação existente, selecione a aplicação na secção **aplicações** da sua conta Batch e, em seguida, selecione **Adicionar**.

Tal como fez para a nova aplicação, especifique a **versão** para o seu novo pacote, faça o upload do ficheiro .zip no campo **pacote de aplicação** e, em seguida, selecione **Enviar por isso**.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou eliminar um pacote de aplicações

Para atualizar ou eliminar um pacote de aplicações existente, selecione a aplicação na secção **Aplicações** da sua conta Batch. Selecione a elipse na linha do pacote de aplicações que pretende modificar e, em seguida, selecione a ação que pretende realizar.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Screenshot mostrando a atualização e eliminando opções para pacotes de aplicações no portal Azure.":::

Se selecionar **Update,** poderá carregar um novo ficheiro .zip. Isto substituirá o ficheiro de .zip anterior que fez o upload para essa versão.

Se selecionar **Eliminar,** será solicitado que confirme a eliminação dessa versão. Assim que selecionar **OK,** o Batch eliminará o ficheiro .zip da sua conta de Armazenamento Azure. Se eliminar a versão predefinida de uma aplicação, a definição de **versão Predefinição** é removida para essa aplicação.

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicações em nó de computação

Agora que aprendeu a gerir pacotes de aplicações no portal Azure, podemos discutir como implantá-los para calcular nós e executá-los com tarefas de Batch.

### <a name="install-pool-application-packages"></a>Instalar pacotes de aplicação de piscina

Para instalar um pacote de aplicações em todos os nós computacionais numa piscina, especifique uma ou mais referências de pacotes de aplicação para o pool. Os pacotes de aplicação que especifica para uma piscina são instalados em cada nó de cálculo que une a piscina, e em qualquer nó que seja reiniciado ou reescamado.

No Lote .NET, especifique uma ou mais [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) quando criar uma nova piscina, ou para uma piscina existente. A classe [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) especifica um ID de aplicação e versão para instalar nos nós de cálculo de uma piscina.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Se uma implementação de pacote de aplicação falhar, o serviço Batch marca o nó [inutilizável](/dotnet/api/microsoft.azure.batch.computenode.state), e não estão agendadas tarefas para a execução nesse nó. Se isto acontecer, reinicie o nó para reiniciar a colocação do pacote. Reiniciar o nó também permite agendar a tarefa novamente no nó.

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicação de tarefas

Semelhante a uma piscina, você especifica referências de pacote de aplicação para uma tarefa. Quando uma tarefa está programada para ser executada num nó, o pacote é descarregado e extraído pouco antes da linha de comando da tarefa ser executada. Se um pacote e versão especificados já estiverem instalados no nó, o pacote não é descarregado e o pacote existente é usado.

Para instalar um pacote de aplicações de tarefa, configuure a propriedade [CloudTask.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) da tarefa:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Executar as aplicações instaladas

Os pacotes que especificou para uma piscina ou tarefa são descarregados e extraídos para um diretório nomeado dentro `AZ_BATCH_ROOT_DIR` do nó. O lote também cria uma variável ambiental que contém o caminho para o diretório nomeado. As linhas de comando da tarefa utilizam esta variável ambiente quando referenciar a aplicação no nó.

Nos nóns windows, a variável está no seguinte formato:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Nos nós Linux, o formato é ligeiramente diferente. Os períodos (.), os hífens (-) e os sinais de número (#) são achatados para sublinhar a variável ambiente. Além disso, note que o caso do ID do pedido está preservado. Por exemplo:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` e `version` são valores que correspondem à versão de aplicação e pacote que especificou para implementação. Por exemplo, se especificasse que a versão 2.7 do *liquidificador* de aplicações deveria ser instalada nos nós windows, as linhas de comando da tarefa utilizariam esta variável ambiente para aceder aos seus ficheiros:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Nos nós Linux, especifique a variável ambiental neste formato. Achate os períodos (.), hífens (-) e sinais de número (#) para sublinhar, e preservar o caso do ID da aplicação:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

Ao carregar um pacote de aplicações, pode especificar uma versão predefinida para implementar nos seus nós de computação. Se tiver especificado uma versão predefinida para uma aplicação, pode omitir o sufixo da versão quando referenciar a aplicação. Pode especificar a versão de aplicação predefinida no portal Azure, na janela **Aplicações,** como mostrado no [Upload e gerir aplicações](#upload-and-manage-applications).

Por exemplo, se definir "2.7" como a versão padrão para *o liquidificador* de aplicações , e as suas tarefas referenciarem a seguinte variável ambiental, então os seus nós Windows executarão a versão 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O seguinte corte de código mostra uma linha de comando de tarefa exemplo que lança a versão padrão da aplicação do *liquidificador:*

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Para obter mais informações sobre as definições do ambiente do nó de computação, consulte [as definições do Ambiente para tarefas](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="update-a-pools-application-packages"></a>Atualizar os pacotes de aplicações de um conjunto

Se uma piscina já existente já tiver sido configurada com um pacote de aplicações, pode especificar um novo pacote para a piscina. Isto significa:

- O serviço Batch instala o pacote recentemente especificado em todos os novos nós que se juntam à piscina e em qualquer nó existente que seja reiniciado ou reesimagem.
- Os nós computacionais que já se encontram na piscina quando atualiza as referências do pacote não instalam automaticamente o novo pacote de aplicações. Estes nós computatórios devem ser reiniciados ou remimagemed para receber o novo pacote.
- Quando um novo pacote é implementado, as variáveis ambientais criadas refletem as novas referências do pacote de aplicações.

Neste exemplo, o pool existente tem a versão 2.7 da aplicação do *liquidificador* configurada como uma das suas [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences). Para atualizar os nós do pool com a versão 2.76b, especifique uma nova [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) com a nova versão e comprometa a alteração.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Agora que a nova versão foi configurada, o serviço Batch instala a versão 2.76b para qualquer novo nó que una a piscina. Para instalar 2.76b nos nós que já se encontram na piscina, reinicie-os ou reimagem.. Note que os nós reiniciados retêm ficheiros de implementações de pacotes anteriores.

## <a name="list-the-applications-in-a-batch-account"></a>Listar as candidaturas numa conta Batch

Pode listar as aplicações e os seus pacotes numa conta Batch utilizando o método [ApplicationOperations.ListApplicationS.](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries)

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- A [API de Lote REST](/rest/api/batchservice) também fornece suporte para trabalhar com pacotes de aplicações. Por exemplo, consulte o elemento [Decompras de aplicaçãoPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) para saber como especificar pacotes para instalar e [Aplicações](/rest/api/batchservice/application) para obter informações sobre a aplicação.
- Saiba como gerir programáticamente [as contas e quotas do Azure Batch com a Batch Management .NET](batch-management-dotnet.md). A biblioteca [Batch Management .NET](/dotnet/api/overview/azure/batch/management) pode permitir funcionalidades de criação e eliminação de conta para a sua aplicação ou serviço batch.
