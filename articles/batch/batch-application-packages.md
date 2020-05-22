---
title: Implementar pacotes de aplicações para calcular os nódosos
description: Utilize a funcionalidade de pacotes de aplicação do Lote Azure para gerir facilmente várias aplicações e versões para instalação em nódos de computação batch.
ms.topic: how-to
ms.date: 04/26/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd5821a7876cc99be41fbb2c5b095b931653c345
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780318"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Implementar aplicações para calcular os nódosos com pacotes de aplicação do Lote

A funcionalidade de pacotes de aplicação do Lote Azure proporciona uma gestão fácil das aplicações de tarefas e a sua implantação aos nódosos computacionais na sua piscina. Com os pacotes de aplicações, pode fazer o upload e gerir várias versões das aplicações que as suas tarefas executam, incluindo os seus ficheiros de suporte. Em seguida, pode implementar automaticamente uma ou mais destas aplicações para os nós de computação na sua piscina.

Neste artigo, aprende-se a carregar e gerir pacotes de aplicações no portal Azure. Em seguida, aprende a instalá-los nos nódos de cálculo de uma piscina com a biblioteca [Batch .NET.][api_net]

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações.
>
> As APIs para a criação e gestão de pacotes de aplicações fazem parte da biblioteca [Batch Management .NET.][api_net_mgmt] As APIs para instalar pacotes de aplicações num nó de cálculo fazem parte da biblioteca [Batch .NET.][api_net] Características comparáveis estão nas APIs de lote disponíveis para outras línguas. 
>
> A funcionalidade de pacotes de aplicações aqui descrita substitui a funcionalidade Apps de Lote disponível em versões anteriores do serviço.

## <a name="application-package-requirements"></a>Requisitos do pacote de aplicação
Para utilizar pacotes de aplicação, precisa [de ligar uma conta de Armazenamento Azure](#link-a-storage-account) à sua conta Batch.

## <a name="about-applications-and-application-packages"></a>Sobre aplicações e pacotes de aplicações
Dentro do Lote Azure, uma *aplicação* refere-se a um conjunto de binários versonizados que podem ser automaticamente descarregados para os nós de computação na sua piscina. Um pacote de *aplicação* refere-se a um *conjunto específico* desses binários e representa uma determinada *versão* da aplicação.

![Diagrama de alto nível de aplicações e pacotes de aplicações][1]

### <a name="applications"></a>Aplicações
Uma aplicação no Batch contém um ou mais pacotes de aplicação e especifica opções de configuração para a aplicação. Por exemplo, uma aplicação pode especificar a versão do pacote de aplicação predefinido para instalar em nós de cálculo e se os seus pacotes podem ser atualizados ou eliminados.

### <a name="application-packages"></a>Pacotes de aplicações
Um pacote de aplicação é um ficheiro .zip que contém os binários de aplicação e ficheiros de suporte que são necessários para as suas tarefas executar a aplicação. Cada pacote de aplicação representa uma versão específica da aplicação.

Pode especificar pacotes de aplicação na piscina e nos níveis de tarefa. Pode especificar um ou mais destes pacotes e (opcionalmente) uma versão quando criar uma piscina ou tarefa.

* **Os pacotes** de aplicação da piscina são implantados em todos os nódosos da piscina. *every* As aplicações são implementadas quando um nó se junta a uma piscina, e quando é reiniciada ou reimagem.
  
    Os pacotes de aplicação de piscina são apropriados quando todos os nós de uma piscina executam as tarefas de um trabalho. Pode especificar um ou mais pacotes de aplicação quando criar uma piscina, e pode adicionar ou atualizar os pacotes de uma piscina existente. Se atualizar os pacotes de aplicação de um pool existentes, tem de reiniciar os seus nós para instalar o novo pacote.
* **Os pacotes** de aplicação de tarefas são implantados apenas para um nó de cálculo programado para executar uma tarefa, pouco antes de executar a linha de comando da tarefa. Se o pacote e a versão especificados já estiverem no nó, não é redistribuído e o pacote existente é utilizado.
  
    Os pacotes de aplicações de tarefas são úteis em ambientes de piscina partilhada, onde diferentes trabalhos são executados numa piscina, e a piscina não é eliminada quando um trabalho é concluído. Se o trabalho tiver menos tarefas do que nós no conjunto, os pacotes de aplicações de tarefas podem minimizar a transferência de dados, uma vez que a aplicação é implementada apenas nos nós que executam tarefas.
  
    Outros cenários que podem beneficiar de pacotes de aplicação de tarefas são empregos que executam uma grande aplicação, mas para apenas algumas tarefas. Por exemplo, uma fase de pré-processamento ou uma tarefa de fusão, quando a aplicação de pré-processamento ou fusão for pesada, pode beneficiar da utilização de pacotes de aplicação de tarefas.

> [!IMPORTANT]
> Existem restrições ao número de pedidos e pacotes de aplicações dentro de uma conta Batch e no tamanho máximo do pacote de aplicação. Consulte [quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para mais detalhes sobre estes limites.
> 
> 

### <a name="benefits-of-application-packages"></a>Benefícios dos pacotes de candidaturas
Os pacotes de aplicação podem simplificar o código na sua solução Batch e baixar as despesas necessárias para gerir as aplicações que as suas tarefas executam.

Com os pacotes de aplicação, a tarefa inicial do seu pool não tem de especificar uma longa lista de ficheiros de recursos individuais para instalar nos nós. Não tem de gerir manualmente várias versões dos seus ficheiros de aplicação no Armazenamento Azure ou nos seus nós. E não precisa de se preocupar em gerar [URLs SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) para fornecer acesso aos ficheiros na sua conta de Armazenamento. O lote trabalha em segundo plano com o Azure Storage para armazenar pacotes de aplicações e implantá-los para calcular os nódosos.

> [!NOTE] 
> O tamanho total de uma tarefa de início tem de ser inferior ou igual a 32 768 carateres, incluindo ficheiros de recursos e variáveis de ambiente. Se a sua tarefa inicial exceder este limite, então usar pacotes de aplicação é outra opção. Também pode criar um arquivo com fecho de dados contendo os seus ficheiros de recursos, carregá-lo como uma bolha para o Armazenamento Azure e, em seguida, desapertá-lo da linha de comando da sua tarefa inicial. 
>
>

## <a name="upload-and-manage-applications"></a>Carregar e gerir aplicações
Pode utilizar o [portal Azure][portal] ou as APIs de Gestão de Lotes para gerir os pacotes de aplicação na sua conta Batch. Nas próximas secções, primeiro mostramos como ligar uma conta de Armazenamento, depois discutir a adição de aplicações e pacotes e geri-las com o portal.

### <a name="link-a-storage-account"></a>Link uma conta de Armazenamento
Para utilizar pacotes de aplicação, tem primeiro de ligar uma [conta de Armazenamento Azure](accounts.md#azure-storage-accounts) à sua conta Batch. Se ainda não configurar uma conta de Armazenamento, o portal Azure apresenta um aviso na primeira vez que clica em **Aplicações** na sua conta 'Lote'.



![Aviso "Sem conta de armazenamento configurada" no portal Azure][9]

O serviço Batch utiliza a conta de Armazenamento associada para armazenar os seus pacotes de aplicações. Depois de ter ligado as duas contas, o Batch pode implementar automaticamente os pacotes armazenados na conta de Armazenamento ligada aos seus nós de cálculo. Para ligar uma conta de Armazenamento à sua conta 'Lote', clique na **conta de Armazenamento** na janela **'Aviso'** e, em seguida, clique novamente na **Conta de Armazenamento.**

![Escolha a lâmina da conta de armazenamento no portal Azure][10]

Recomendamos que crie uma conta de Armazenamento *especificamente* para utilização com a sua conta Batch, e selecione-a aqui. Depois de criar uma conta de Armazenamento, pode ligá-la à sua conta 'Lote', utilizando a janela da Conta de **Armazenamento.**

> [!IMPORTANT] 
> - Atualmente não é possível utilizar pacotes de aplicação com uma conta de Armazenamento Azure que está configurada com regras de [firewall](../storage/common/storage-network-security.md).
> - Uma conta de Armazenamento Azure com espaço de **nome hierárquico** definido para **Ativado** não pode ser usada para pacotes de aplicação.

O serviço Batch utiliza o Armazenamento Azure para armazenar os seus pacotes de aplicação como bolhas de bloco. É [cobrado normalmente][storage_pricing] pelos dados blob do bloco, e o tamanho de cada embalagem não pode exceder o tamanho máximo da bolha do bloco. Para mais informações, consulte a [escalabilidade do Armazenamento Azure e os alvos](../storage/blobs/scalability-targets.md)de desempenho para contas de armazenamento . Certifique-se de que considera o tamanho e o número dos seus pacotes de aplicação e remova periodicamente pacotes depreciados para minimizar os custos.

### <a name="view-current-applications"></a>Ver aplicações atuais
Para ver as aplicações na sua conta 'Lote', clique no item do menu **Aplicações** no menu esquerdo enquanto visualiza a sua **conta 'Lote'.**

![Telha de aplicações][2]

A seleção desta opção de menu abre a janela **Aplicações:**

![Listar aplicações][3]

Esta janela exibe a identificação de cada aplicação na sua conta e as seguintes propriedades:

* **Pacotes**: O número de versões associadas a esta aplicação.
* **Versão predefinida**: A versão de aplicação instalada se não indicar uma versão quando especifica a aplicação para um pool. Esta definição é opcional.
* **Permitir atualizações**: O valor que especifica se são permitidas atualizações, supressões e adições do pacote. Se isto estiver definido para **Nº ,** as atualizações e eliminações do pacote são desativadas para a aplicação. Só podem ser adicionadas novas versões de pacotes de aplicações. A predefinição é **Sim**.

Se quiser ver a estrutura de ficheiros do pacote de aplicação no seu nó de cálculo, navegue para a sua conta De lote no portal. A partir da sua conta Batch, navegue para **Pools**. Selecione a piscina que contém o nó de computação que lhe interessa.

![Nódosos na piscina][13]

Depois de selecionar a sua piscina, navegue até ao nó computacional em que o pacote de aplicações está instalado. A partir daí, os detalhes do pacote de aplicações estão localizados na pasta de **aplicações.** Pastas adicionais no nó computacional contêm outros ficheiros, tais como tarefas de início, ficheiros de saída, saída de erro, etc.

![Arquivos no nó][14]

### <a name="view-application-details"></a>Ver detalhes da aplicação
Para ver os detalhes de uma aplicação, selecione a aplicação na janela **Aplicações.**

![Detalhes da aplicação][4]

Nos detalhes da aplicação, pode configurar as seguintes definições para a sua aplicação.

* **Permitir atualizações**: Especifique se os seus pacotes de aplicação podem ser atualizados ou eliminados. Consulte "Atualizar ou Apagar um pacote de aplicações" mais tarde neste artigo.
* **Versão predefinida**: Especifique um pacote de aplicação predefinido para implantar para calcular os nódosos.
* **Nome**do ecrã : Especifique um nome amigável que a sua solução Batch pode usar quando apresentar informações sobre a aplicação, por exemplo, na UI de um serviço que fornece aos seus clientes através do Batch.

### <a name="add-a-new-application"></a>Adicione uma nova aplicação
Para criar uma nova aplicação, adicione um pacote de aplicação e especifique um novo ID de aplicação único. O primeiro pacote de aplicações que adiciona com o novo ID de aplicação também cria a nova aplicação.

Clique em **Aplicações**  >  **Adicionar**.

![Nova lâmina de aplicação no portal Azure][5]

A nova janela de **aplicações** fornece os seguintes campos para especificar as definições do seu novo pacote de aplicação e aplicação.

**ID da Aplicação**

Este campo especifica a identificação da sua nova aplicação, que está sujeita às regras padrão de validação do ID do Lote Azure. As regras para a disponibilização de um ID de aplicação são as seguintes:

* Nos nós do Windows, o ID pode conter qualquer combinação de caracteres alfanuméricos, hífenes e sublinhados. Nos nós linux, apenas são permitidos caracteres alfanuméricos e sublinhados.
* Não pode conter mais de 64 caracteres.
* Deve ser único dentro da conta Batch.
* É a preservação de casos e a insensibilidade dos casos.

**Versão**

Este campo especifica a versão do pacote de aplicações que está a carregar. As cordas de versão estão sujeitas às seguintes regras de validação:

* Nos nós do Windows, a cadeia de versão pode conter qualquer combinação de caracteres alfanuméricos, hífenes, sublinhados e períodos. Nos nós linux, a cadeia de versão pode conter apenas caracteres alfanuméricos e sublinha.
* Não pode conter mais de 64 caracteres.
* Deve ser único dentro da aplicação.
* São preservação de casos e insensíveis a casos.

**Pacote de candidatura**

Este campo especifica o ficheiro .zip que contém os binários de aplicação e ficheiros de suporte que são necessários para executar a aplicação. Clique na **lista de ficheiros Do Select ou** no ícone da pasta para procurar e selecionar um ficheiro .zip que contenha os ficheiros da sua aplicação.

Depois de selecionar um ficheiro, clique em **OK** para iniciar o upload para o Armazenamento Azure. Quando a operação de upload estiver concluída, o portal apresenta uma notificação. Dependendo do tamanho do ficheiro que está a carregar e da velocidade da sua ligação de rede, esta operação pode demorar algum tempo.

> [!WARNING]
> Não feche a nova janela de **aplicação** antes de o funcionamento de upload estar concluído. Ao fazê-lo, interrompe o processo de upload.
> 
> 

### <a name="add-a-new-application-package"></a>Adicione um novo pacote de aplicações
Para adicionar uma versão de pacote de aplicação para uma aplicação existente, selecione uma aplicação nas **janelas aplicações** e clique em **Pacotes**  >  **Adicionar**.

![Adicione a lâmina do pacote de aplicação no portal Azure][8]

Como pode ver, os campos coincidem com os da nova janela de **aplicação,** mas a caixa de ID de **aplicação** está desativada. Tal como fez para a nova aplicação, especifique a **Versão** para o seu novo pacote, navegue para o seu **pacote de aplicação** .zip file e, em seguida, clique em **OK** para carregar o pacote.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou eliminar um pacote de aplicações
Para atualizar ou eliminar um pacote de aplicação existente, abra os detalhes para a aplicação, clique em **Pacotes,** clique na **elipse** na linha do pacote de aplicação que pretende modificar e selecione a ação que pretende executar.

![Atualizar ou eliminar pacote no portal Azure][7]

**Atualizar**

Quando clicar em **Atualizar,** o **pacote 'Actualizar'** é apresentado. Esta janela é semelhante à janela **do pacote de aplicações,** no entanto apenas o campo de seleção do pacote está ativado, permitindo especificar um novo ficheiro ZIP para carregar.

![Atualizar lâmina de pacote no portal Azure][11]

**Eliminar**

Quando clicar em **Delete,** é-lhe pedido que confirme a eliminação da versão do pacote e o Batch elimina o pacote do Armazenamento Azure. Se eliminar a versão predefinida de uma aplicação, a definição da **versão Predefinida** é removida para a aplicação.

![Excluir aplicação][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicações em nódeos computacionais
Agora que aprendeu a gerir pacotes de aplicações com o portal Azure, podemos discutir como implementá-los para calcular nós e executá-los com tarefas do Batch.

### <a name="install-pool-application-packages"></a>Instalar pacotes de aplicação de piscina
Para instalar um pacote de aplicação em todos os nós de computação numa piscina, especifique uma ou mais *referências* de pacote de aplicação para a piscina. Os pacotes de aplicação que especifica para uma piscina são instalados em cada nó de computação quando esse nó se junta à piscina, e quando o nó é reiniciado ou reimagemdo.

No Lote .NET, especifique um ou mais [CloudPool][net_cloudpool]. [AplicaçõesPacoteReferências][net_cloudpool_pkgref] quando cria uma piscina nova, ou para uma piscina existente. A classe [ApplicationPackageReference][net_pkgref] especifica um ID de aplicação e versão para instalar nos nódosos de cálculo de uma piscina.

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
> Se uma implementação de pacote de aplicação falhar por qualquer motivo, o serviço Batch marca o nó [inutilizável][net_nodestate], e não estão agendadas tarefas para a execução nesse nó. Neste caso, deve **reiniciar** o nó para reiniciar a implementação do pacote. Reiniciar o nó também permite o agendamento de tarefas novamente no nó.
> 
> 

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicação de tarefas
Semelhante a uma piscina, especifica *referências* de pacote de aplicação para uma tarefa. Quando uma tarefa está programada para ser executada num nó, o pacote é descarregado e extraído pouco antes da execução da linha de comando da tarefa. Se um pacote e versão especificados já estiverem instalados no nó, o pacote não é descarregado e o pacote existente é utilizado.

Para instalar um pacote de aplicação de tarefas, configure a [CloudTask][net_cloudtask]da tarefa . [AplicaçõesPacoteReferências][net_cloudtask_pkgref] propriedade:

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
Os pacotes que especificou para uma piscina ou tarefa são descarregados e extraídos para um diretório nomeado dentro `AZ_BATCH_ROOT_DIR` do nó. O lote também cria uma variável ambiental que contém o caminho para o diretório nomeado. As linhas de comando de tarefa utilizam esta variável ambiental ao fazer referência à aplicação no nó. 

Nos nós do Windows, a variável encontra-se no seguinte formato:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Nos nós linux, o formato é ligeiramente diferente. Os períodos (.), os hífenes (-) e os sinais de número (#) são achatados para sublinhados na variável ambiental. Note ainda que o caso do ID da aplicação é preservado. Por exemplo:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`e `version` são valores que correspondem à aplicação e versão pacote que especificou para implementação. Por exemplo, se especificasse que a versão 2.7 do *liquidificador* de aplicações deveria ser instalada nos nós do Windows, as linhas de comando de tarefas usariam esta variável ambiental para aceder aos seus ficheiros:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Nos nós linux, especifique a variável ambiental neste formato. Achate os períodos (.), hífenes (-) e sinais de número (#) para sublinhar, e preservar o caso do ID da aplicação:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Ao fazer o upload de um pacote de aplicações, pode especificar uma versão predefinida para ser implementada nos seus nós de cálculo. Se tiver especificado uma versão predefinida para uma aplicação, pode omitir o sufixo da versão quando se refere a aplicação. Pode especificar a versão de aplicação predefinida no portal Azure, na janela **Aplicações,** como mostrado no [Upload e gerir aplicações](#upload-and-manage-applications).

Por exemplo, se definir "2.7" como a versão padrão para *liquidificador*de aplicações , e as suas tarefas referenciarem a seguinte variável ambiental, então os seus nós windows executarão a versão 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O seguinte código snippet mostra uma linha de comando de tarefa sonante que lança a versão padrão da aplicação *liquidificadora:*

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Para obter mais informações sobre as definições ambientais do nó computacional, consulte as definições do [Ambiente para tarefas](jobs-and-tasks.md#environment-settings-for-tasks). 

## <a name="update-a-pools-application-packages"></a>Atualizar os pacotes de aplicações de um conjunto
Se uma piscina existente já tiver sido configurada com um pacote de aplicação, pode especificar um novo pacote para a piscina. Se especificar uma nova referência de pacote para uma piscina, aplicam-se:

* O serviço Batch instala o pacote recém-especificado em todos os novos nós que se juntam à piscina e em qualquer nó existente que seja reiniciado ou reimagedo.
* Os nódosos computacionais que já se encontram na piscina quando atualiza as referências do pacote não instalam automaticamente o novo pacote de aplicações. Estes nódosos de computação devem ser reiniciados ou re-imagemdos para receber o novo pacote.
* Quando um novo pacote é implementado, as variáveis ambientais criadas refletem as referências do novo pacote de aplicação.

Neste exemplo, o pool existente tem a versão 2.7 da aplicação *liquidificadora* configurada como uma das suas [CloudPool][net_cloudpool]. [AplicaçõesPacoteReferências][net_cloudpool_pkgref]. Para atualizar os nós do pool com a versão 2.76b, especifique um novo [ApplicationPackageReference][net_pkgref] com a nova versão e comprometa a alteração.

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

Agora que a nova versão foi configurada, o serviço Batch instala a versão 2.76b para qualquer *novo* nó que se junte à piscina. Para instalar 2.76b nos nós que *já* estão na piscina, reinicie ou redissolhe-os. Note que os nódosos reiniciados retêm os ficheiros de implementações anteriores do pacote.

## <a name="list-the-applications-in-a-batch-account"></a>Enumerar os pedidos numa conta de Lote
Pode listar as aplicações e os seus pacotes numa conta de Lote utilizando as Operações de [Aplicação][net_appops]. [Método ListApplicationResumos.][net_appops_listappsummaries]

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

## <a name="wrap-up"></a>Embrulhar
Com pacotes de aplicações, pode ajudar os seus clientes a selecionar as aplicações para os seus trabalhos e especificar a versão exata a utilizar ao processar trabalhos com o seu serviço ativado pelo Batch. Também pode fornecer a capacidade de os seus clientes fazerem upload e rastrearem as suas próprias aplicações no seu serviço.

## <a name="next-steps"></a>Passos seguintes
* A [API do Lote REST][api_rest] também fornece suporte para trabalhar com pacotes de aplicação. Por exemplo, consulte o elemento [aplicaçãoPackageReferences][rest_add_pool_with_packages] em [Adicionar um pool a uma conta][rest_add_pool] para obter informações sobre como especificar pacotes a instalar utilizando a API REST. Consulte [as Aplicações][rest_applications] para obter informações sobre como obter informações sobre a aplicação utilizando a API do Lote REST.
* Saiba gerir programáticamente as contas e quotas do [Lote Azure com a Batch Management .NET](batch-management-dotnet.md). A biblioteca [Batch Management .NET][api_net_mgmt] pode ativar funcionalidades de criação e eliminação de conta para a sua aplicação ou serviço de lote.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Pacotes de aplicação diagrama de alto nível"
[2]: ./media/batch-application-packages/app_pkg_02.png "Telha de aplicações no portal Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Lâmina de aplicações no portal Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Detalhes da aplicação lâmina no portal Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nova lâmina de aplicação no portal Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Atualizar ou eliminar pacotes drop-down no portal Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nova lâmina de pacote de aplicação no portal Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nenhum alerta de conta de armazenamento ligado"
[10]: ./media/batch-application-packages/app_pkg_10.png "Escolha a lâmina da conta de armazenamento no portal Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Atualizar lâmina de pacote no portal Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Eliminar o diálogo de confirmação do pacote no portal Azure"
[13]: ./media/batch-application-packages/package-file-structure.png "Informação do nó computacional no portal Azure"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Ficheiros no nó computacional exibidos no portal Azure"
