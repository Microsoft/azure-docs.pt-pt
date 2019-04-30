---
title: Instalar pacotes de aplicações em nós de computação - Azure Batch | Documentos da Microsoft
description: Utilize a funcionalidade de pacotes de aplicação do Azure Batch para gerir facilmente várias aplicações e versões para instalação em Batch nós de computação.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/05/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee54d37050991763e60a6feb96c75d80384a42ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60722225"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Implementar aplicações em nós de computação com pacotes de aplicações do Batch

A funcionalidade de pacotes de aplicação do Azure Batch fornece a facilitar a gestão de aplicações de tarefas e a respetiva implementação em nós de computação do conjunto. Com pacotes de aplicações, pode carregar e gerir facilmente várias versões das aplicações que suas tarefas vão executar, incluindo os respetivos ficheiros de suporte. Pode, em seguida, implementar automaticamente uma ou mais destas aplicações em nós de computação do conjunto.

Neste artigo, saiba como carregar e gerir pacotes de aplicações no portal do Azure. Em seguida, saiba como instalá-los em nós de computação de um conjunto com o [.NET do Batch] [ api_net] biblioteca.

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações.
>
> As APIs para criar e gerir pacotes de aplicações são parte do [Batch Management .NET] [ api_net_mgmt] biblioteca. As APIs para a instalação de pacotes de aplicações num nó de computação são parte do [.NET do Batch] [ api_net] biblioteca. Funcionalidades comparáveis estão em APIs do Batch disponíveis para outros idiomas. 
>
> A funcionalidade de pacotes de aplicação descrita aqui substitui a funcionalidade de aplicações do Batch disponível em versões anteriores do serviço.

## <a name="application-package-requirements"></a>Requisitos do pacote de aplicação
Para utilizar pacotes de aplicações, terá [associar uma conta de armazenamento do Azure](#link-a-storage-account) à conta do Batch.

## <a name="about-applications-and-application-packages"></a>Sobre as aplicações e pacotes de aplicações
Dentro do Azure Batch, um *aplicativo* refere-se a um conjunto de binários com a versão que pode ser baixado automaticamente em nós de computação do conjunto. Uma *pacote de aplicação* refere-se a um *conjunto específico* desses binários e representa um determinado *versão* do aplicativo.

![Diagrama de alto nível de aplicativos e pacotes de aplicações][1]

### <a name="applications"></a>Aplicações
Uma aplicação no Batch contém uma ou mais aplicações, pacotes e especifica as opções de configuração para a aplicação. Por exemplo, um aplicativo pode especificar a versão do pacote de aplicativo padrão para instalar em nós de computação e se os seus pacotes podem ser atualizados ou excluídos.

### <a name="application-packages"></a>Pacotes de aplicações
Um pacote de aplicação é um ficheiro. zip que contém os binários de aplicativo e ficheiros de suporte que são necessários para as suas tarefas executar a aplicação. Cada pacote de aplicação representa uma versão específica do aplicativo.

Pode especificar pacotes de aplicações nos níveis de agrupamento e tarefas. Pode especificar um ou mais desses pacotes e (opcionalmente) uma versão quando cria um conjunto ou tarefas.

* **Agrupamento de pacotes de aplicações** são implementadas *cada* nó no conjunto. Os aplicativos são implantados quando um nó se associa um conjunto e quando ele é reiniciado ou recriado.
  
    Pacotes de aplicações do conjunto são adequados quando todos os nós num conjunto executam tarefas de um trabalho. Quando cria um conjunto, e pode adicionar ou atualizar os pacotes de um conjunto existente, pode especificar um ou mais pacotes de aplicações. Se atualizar pacotes de aplicações de um conjunto existente, tem de reiniciar os nós para instalar o novo pacote.
* **Pacotes de aplicações de tarefas** são implementadas apenas para um nó de computação agendado para ser executada uma tarefa, antes de executar linha de comandos da tarefa. Se o pacote de aplicação especificado e a versão já está no nó, não está a ser reimplementada e o pacote existente é usado.
  
    Pacotes de aplicações de tarefas são úteis em ambientes de conjunto partilhado, em que as diferentes tarefas são executadas num conjunto, e o conjunto não é eliminado quando uma tarefa estiver concluída. Se o trabalho tiver menos tarefas do que nós no conjunto, os pacotes de aplicações de tarefas podem minimizar a transferência de dados, uma vez que a aplicação é implementada apenas nos nós que executam tarefas.
  
    Outros cenários que podem beneficiar de pacotes de aplicações de tarefas são tarefas que executem um aplicativo grande, mas para apenas algumas tarefas. Por exemplo, uma fase de pré-processamento ou uma tarefa de intercalação, onde o aplicativo de intercalação ou pré-processamento for pesadas, pode beneficiar da utilização de pacotes de aplicações de tarefas.

> [!IMPORTANT]
> Existem restrições no número de aplicativos e pacotes de aplicativos dentro de uma conta do Batch e o tamanho do pacote de aplicação máximo. Ver [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter detalhes sobre estes limites.
> 
> 

### <a name="benefits-of-application-packages"></a>Benefícios de pacotes de aplicações
Pacotes de aplicações podem simplificar o código na sua solução do Batch e reduzir a sobrecarga necessária para gerir as aplicações que as tarefas executam.

Com pacotes de aplicações, a tarefa de início de seu conjunto não tem que especificar uma longa lista de ficheiros de recurso individual para instalar em nós. Não precisa de gerir manualmente várias versões de arquivos do aplicativo no armazenamento do Azure ou nos seus nós. E, não precisa se preocupar sobre como gerar [SAS URLs](../storage/common/storage-dotnet-shared-access-signature-part-1.md) para fornecer acesso aos ficheiros na sua conta de armazenamento. O batch funciona em segundo plano com o armazenamento do Azure para armazenar pacotes de aplicações e implementá-las em nós de computação.

> [!NOTE] 
> O tamanho total de uma tarefa de início tem de ser inferior ou igual a 32 768 carateres, incluindo ficheiros de recursos e variáveis de ambiente. Se a tarefa de início excede este limite, em seguida, utilizar pacotes de aplicações é outra opção. Pode também criar um arquivo morto compactado que contém os ficheiros de recursos, carregue-o como um blob no armazenamento do Azure e, em seguida, deszipe-a partir da linha de comando da sua tarefa de início. 
>
>

## <a name="upload-and-manage-applications"></a>Carregar e gerir aplicações
Pode utilizar o [portal do Azure] [ portal] ou as APIs de gestão do Batch para gerir os pacotes de aplicações na sua conta do Batch. As secções seguintes, vamos primeiro Mostrar como ligar uma conta de armazenamento, em seguida, discutir a adição de aplicações e pacotes e gerenciá-los com o portal.

### <a name="link-a-storage-account"></a>Ligar uma conta de armazenamento
Para utilizar pacotes de aplicações, tem de associar primeiro uma [conta de armazenamento do Azure](batch-api-basics.md#azure-storage-account) à conta do Batch. Se ainda não tiver configurado uma conta de armazenamento, o portal do Azure apresenta um aviso na primeira vez que clicar **aplicativos** na sua conta do Batch.



![Aviso de "Nenhuma conta de armazenamento configurada" no portal do Azure][9]

O serviço Batch utiliza a conta de armazenamento associada para armazenar pacotes de aplicativos. Depois de ligar as duas contas, o Batch pode implementar automaticamente os pacotes armazenados na conta de armazenamento ligada para os nós de computação. Para ligar uma conta de armazenamento à sua conta do Batch, clique em **conta de armazenamento** sobre o **aviso** janela e clique em **conta de armazenamento** novamente.

![Selecione o painel de conta de armazenamento no portal do Azure][10]

Recomendamos que crie uma conta de armazenamento *especificamente* para utilização com a sua conta do Batch e selecione-o aqui. Depois de criar uma conta de armazenamento, pode, em seguida, ligá-la à conta do Batch ao utilizar o **conta de armazenamento** janela.

> [!NOTE] 
> Atualmente não é possível utilizar pacotes de aplicações com uma conta de armazenamento do Azure que está configurada com [regras de firewall](../storage/common/storage-network-security.md).
> 

O serviço Batch utiliza o armazenamento do Azure para armazenar pacotes de aplicativos como blobs de blocos. Estiver [cobrado como habitualmente] [ storage_pricing] para o blob de blocos de dados e o tamanho de cada pacote não podem exceder os [tamanho do blob de bloco máximo](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets). Certifique-se de que considerar o tamanho e número de pacotes de aplicativos e periodicamente remover pacotes preteridos para minimizar os custos.
> 
> 

### <a name="view-current-applications"></a>Ver aplicações atuais
Para ver as aplicações na sua conta do Batch, clique nas **aplicativos** item de menu no menu à esquerda enquanto a visualização de seu **conta do Batch**.

![Mosaico de aplicativos][2]

Selecionar esta opção de menu abre o **aplicativos** janela:

![Listar aplicações][3]

Esta janela exibe o ID de cada aplicação na sua conta e as seguintes propriedades:

* **Pacotes**: O número de versões associadas esta aplicação.
* **Versão predefinida**: A versão do aplicativo instalada se não indicam uma versão quando especificar a aplicação para um conjunto. Esta definição é opcional.
* **Permitir atualizações**: O valor que especifica se o pacote de atualizações, eliminações e adições são permitidas. Se isto estiver definido como **não**, as atualizações de pacotes e eliminações, estão desativadas para a aplicação. Podem ser adicionadas apenas versões de pacote de aplicação nova. A predefinição é **Sim**.

Se gostaria de ver a estrutura de ficheiros do pacote de aplicação no seu nó de computação, navegue para a sua conta do Batch no portal. A partir da sua conta do Batch, navegue para **conjuntos**. Selecione o conjunto que contém os nós de computação que está interessado.

![Nós no conjunto][13]

Depois de selecionar o conjunto, navegue até o nó de computação que o pacote de aplicação está instalado no. A partir daí, os detalhes do pacote de aplicação estão localizados no **aplicativos** pasta. Pastas adicionais no nó de computação contenham outros arquivos, como tarefas de início, ficheiros de saída, saída de erro, etc.

![Ficheiros no nó][14]

### <a name="view-application-details"></a>Ver detalhes da aplicação
Para ver os detalhes para uma aplicação, selecione a aplicação no **aplicativos** janela.

![Detalhes da aplicação][4]

Nos detalhes da aplicação, pode configurar as seguintes definições para a sua aplicação.

* **Permitir atualizações**: Especifique se os seus pacotes de aplicações podem ser atualizados ou excluídos. Consulte "Atualizar ou eliminar um pacote de aplicação", mais adiante neste artigo.
* **Versão predefinida**: Especifique um pacote de aplicações predefinido a implementar em nós de computação.
* **Nome a apresentar**: Especifique um nome amigável que sua solução do Batch pode utilizar quando esta apresenta informações sobre a aplicação, por exemplo, a interface do Usuário de um serviço que fornece aos seus clientes através do Batch.

### <a name="add-a-new-application"></a>Adicionar uma nova aplicação
Para criar uma nova aplicação, adicione um pacote de aplicação e especifique um ID de aplicação novo e exclusivo. O primeiro pacote de aplicação que adicionar com o novo ID de aplicação também cria a nova aplicação.

Clique em **Aplicações** > **Adicionar**.

![Novo painel de aplicações no portal do Azure][5]

O **nova aplicação** janela fornece os seguintes campos para especificar as definições da sua nova aplicação e o pacote de aplicação.

**Id da aplicação**

Este campo especifica o ID da sua nova aplicação, o que está sujeita a regras de validação de ID de lote do Azure standard. As regras para fornecer um ID de aplicação são os seguintes:

* Em nós do Windows, o ID pode conter qualquer combinação de carateres alfanuméricos, hífenes e carateres de sublinhado. Em nós do Linux, são permitidos apenas carateres alfanuméricos e carateres de sublinhado.
* Não pode conter mais de 64 carateres.
* Tem de ser exclusivo dentro da conta do Batch.
* É a preservação de caso e maiúsculas de minúsculas.

**Versão**

Este campo especifica a versão do pacote de aplicação que está a carregar. Cadeias de caracteres de versão estão sujeitas às seguintes regras de validação:

* Em nós do Windows, a cadeia de versão pode conter qualquer combinação de carateres alfanuméricos, hífenes, carateres de sublinhado e períodos. Em nós do Linux, a cadeia de versão pode conter apenas carateres alfanuméricos e carateres de sublinhado.
* Não pode conter mais de 64 carateres.
* Tem de ser exclusivo dentro do aplicativo.
* São a preservação de caso e maiúsculas de minúsculas.

**Pacote de aplicação**

Este campo especifica o ficheiro. zip que contém os binários de aplicativo e os ficheiros de suporte que são necessárias para executar a aplicação. Clique nas **selecionar um ficheiro** caixa ou o ícone de pasta, procurar e selecione um ficheiro. zip que contém ficheiros de seu aplicativo.

Depois de selecionar um ficheiro, clique em **OK** para iniciar o carregamento para o armazenamento do Azure. Quando a operação de carregamento estiver concluída, o portal apresenta uma notificação. Consoante o tamanho do ficheiro que está a carregar e a velocidade da sua ligação de rede, esta operação pode demorar algum tempo.

> [!WARNING]
> Não feche o **nova aplicação** janela antes da conclusão da operação de carregamento. Se o fizer, interromperá o processo de carregamento.
> 
> 

### <a name="add-a-new-application-package"></a>Adicionar um novo pacote de aplicação
Para adicionar uma versão de pacote de aplicação para um aplicativo existente, selecione uma aplicação no **aplicativos** windows e clique em **pacotes** > **adicionar**.

![Adicionar o painel de pacote de aplicações no portal do Azure][8]

Como pode ver, os campos corresponderem do **nova aplicação** janela, mas o **id de aplicação** caixa está desativada. Como fez para a nova aplicação, especifique a **versão** para o novo pacote, navegue até sua **pacote de aplicação** . zip de ficheiros, em seguida, clique em **OK** para carregar o pacote.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou eliminar um pacote de aplicação
Para atualizar ou eliminar um pacote de aplicação existente, abra os detalhes para a aplicação, clique em **pacotes**, clique nas **reticências** na linha do pacote de aplicação que pretende modificar e, selecione a ação que pretende executar.

![Atualizar ou eliminar o pacote no portal do Azure][7]

**Atualização**

Quando clica em **atualização**, o **pacote de atualização** for apresentada a janela. Essa janela é semelhante para o **novo pacote de aplicações** janela, no entanto, apenas o campo de seleção de pacote estiver ativado, permitindo que especifique um novo ficheiro ZIP para carregar.

![Painel de pacote de atualização no portal do Azure][11]

**Eliminar**

Quando clica em **eliminar**, é-lhe perguntado para confirmar a eliminação da versão do pacote e Batch elimina o pacote do armazenamento do Azure. Se eliminar a versão predefinida de uma aplicação, o **versão predefinida** configuração seja removida da aplicação.

![Eliminar aplicação ][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicações em nós de computação
Agora que aprendeu como gerir pacotes de aplicações com o portal do Azure, podemos analisar como implementá-las em nós de computação e execute-os com tarefas do Batch.

### <a name="install-pool-application-packages"></a>Instalar pacotes de aplicações de conjunto
Para instalar um pacote de aplicação em todos os nós de computação num conjunto, especifique o pacote de aplicações de um ou mais *referências* para o conjunto. Os pacotes de aplicação que especificou para um conjunto são instalados em cada nó de computação quando esse nó se associa ao conjunto e quando o nó é reiniciado ou recriado.

No .NET do Batch, especificar um ou mais [CloudPool][net_cloudpool].[ ApplicationPackageReferences] [ net_cloudpool_pkgref] quando criar um novo conjunto, ou para um conjunto existente. O [ApplicationPackageReference] [ net_pkgref] classe especifica um ID de aplicação e a versão a instalar num conjunto de nós de computação.

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
> Se uma implementação de pacote de aplicação falhar por algum motivo, o serviço Batch marca o nó [inutilizável][net_nodestate], e não existem tarefas são agendadas para execução nesse nó. Neste caso, deve **reiniciar** o nó para reinicie o processo de implantação do pacote. Reiniciar o nó também permite agendamento de tarefas novamente no nó.
> 
> 

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicações de tarefas
Semelhante a um conjunto, especifica o pacote de aplicação *referências* para uma tarefa. Quando uma tarefa estiver agendada para ser executada num nó, o pacote é transferido e extraiu imediatamente antes de linha de comandos da tarefa é executada. Se um pacote especificado e a versão já está instalado no nó, o pacote não é transferido e o pacote existente é usado.

Para instalar um pacote de aplicação de tarefa, configurar a tarefa [CloudTask][net_cloudtask].[ ApplicationPackageReferences] [ net_cloudtask_pkgref] propriedade:

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
Os pacotes que tiver especificado para um conjunto ou tarefas são transferidos e extraídos para um diretório com nome dentro do `AZ_BATCH_ROOT_DIR` do nó. O batch também cria uma variável de ambiente que contém o caminho para o diretório com nome. Linhas de comando de tarefas Utilize esta variável de ambiente ao referenciar a aplicação no nó. 

Em nós do Windows, a variável é o seguinte formato:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Em nós do Linux, o formato é ligeiramente diferente. Pontos (.), hífenes (-) e sinais de cardinal (#) são simplificados para a variável de ambiente de carateres de sublinhado. Além disso, tenha em atenção que o caso do ID da aplicação é preservado. Por exemplo:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` e `version` são valores que correspondem para a versão de pacotes e aplicações que especificou para a implementação. Por exemplo, se tiver especificado que a versão 2.7 do aplicativo *blender* devem ser instalados em nós do Windows, o suas linhas de comandos de tarefas utilizaria esta variável de ambiente para acessar seus arquivos:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Em nós do Linux, especifique a variável de ambiente neste formato. Nivelamento de pontos (.), hífenes (-) e o número de sinais (#) para carateres de sublinhado e preservar o caso do ID da aplicação:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Ao carregar um pacote de aplicação, pode especificar uma versão predefinida para implementar os nós de computação. Se especificar uma versão predefinida para uma aplicação, pode omitir o sufixo de versão quando referencia o aplicativo. Pode especificar a versão da aplicação predefinida no portal do Azure, o **aplicativos** janela, conforme mostrado na [carregar e gerir aplicações](#upload-and-manage-applications).

Por exemplo, se definir "2,7" como a versão predefinida para a aplicação *blender*, suas tarefas fazem referência a seguinte variável de ambiente, e os nós do Windows serão executado versão 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O fragmento de código seguinte mostra uma linha de comandos de tarefas de exemplo que inicia a versão predefinida dos *blender* aplicação:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Ver [definições de ambiente para tarefas](batch-api-basics.md#environment-settings-for-tasks) no [descrição geral da funcionalidade do Batch](batch-api-basics.md) para obter mais informações sobre definições de ambiente de nó de computação.
> 
> 

## <a name="update-a-pools-application-packages"></a>Atualizar os pacotes de aplicações de um conjunto
Se já tiver sido configurado um conjunto existente com um pacote de aplicação, pode especificar um novo pacote para o conjunto. Se especificar uma nova referência de pacote para um conjunto, situações seguintes:

* O serviço Batch instala o pacote especificado recentemente em todos os novos nós se associam ao conjunto e em qualquer nó existente que é reiniciado ou recriado.
* Computação de nós que já estão no conjunto quando atualizar o pacote referenciará não instalam automaticamente o novo pacote de aplicação. Estes de computação de nós tem de ser reiniciados ou recriar a imagem para receber o novo pacote.
* Quando um novo pacote é implementado, as variáveis de ambiente criado refletem as novas referências de pacote de aplicação.

Neste exemplo, o conjunto existente tem a versão 2.7 do *blender* aplicação configurada como um dos respetivos [CloudPool][net_cloudpool].[ ApplicationPackageReferences][net_cloudpool_pkgref]. Para atualizar os nós do conjunto com a versão 2.76b, especifique um novo [ApplicationPackageReference] [ net_pkgref] com a nova versão e confirmar a alteração.

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

Agora que a nova versão tiver sido configurada, o serviço Batch instala a versão 2.76b a qualquer *novo* nó se associa ao conjunto. Para instalar 2.76b em nós que estão *já* no agrupamento, reiniciar ou recriar imagem-los. Tenha em atenção de que nós reinicializada mantém os ficheiros a partir de implementações de pacote anterior.

## <a name="list-the-applications-in-a-batch-account"></a>Lista os aplicativos numa conta do Batch
Pode listar as aplicações e os pacotes numa conta do Batch ao utilizar o [ApplicationOperations][net_appops].[ ListApplicationSummaries] [ net_appops_listappsummaries] método.

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

## <a name="wrap-up"></a>Conclusão
Com pacotes de aplicações, pode ajudar os seus clientes, selecione as aplicações aos seus trabalhos e especificar a versão exata a utilizar durante o processamento de tarefas com o seu serviço habilitados no Batch. Também pode fornecer a capacidade dos clientes podem carregar e controlar as suas aplicações no seu serviço.

## <a name="next-steps"></a>Passos Seguintes
* O [API REST do Batch] [ api_rest] também fornece suporte para trabalhar com pacotes de aplicações. Por exemplo, consulte a [applicationPackageReferences] [ rest_add_pool_with_packages] elemento [adicionar um conjunto a uma conta] [ rest_add_pool] para obter informações sobre como especificar pacotes a serem instalados com a API de REST. Ver [aplicativos] [ rest_applications] para obter detalhes sobre como obter informações sobre a aplicação ao utilizar a API de REST do Batch.
* Saiba como programaticamente [gerir contas do Azure Batch e quotas with Batch Management .NET](batch-management-dotnet.md). O [gestão de lotes .NET] [ api_net_mgmt] biblioteca pode ativar funcionalidades de criação e eliminação de conta para o serviço ou aplicação do Batch.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nível de pacotes de aplicação"
[2]: ./media/batch-application-packages/app_pkg_02.png "Mosaico de aplicações no portal do Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Painel de aplicações no portal do Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Painel de detalhes da aplicação no portal do Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Novo painel de aplicações no portal do Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Atualizar ou eliminar pacotes pendente no portal do Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Novo painel de pacote de aplicação no portal do Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nenhum alerta de conta de armazenamento ligada"
[10]: ./media/batch-application-packages/app_pkg_10.png "Selecione o painel de conta de armazenamento no portal do Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Painel de pacote de atualização no portal do Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Eliminar a caixa de diálogo de confirmação de pacote no portal do Azure"
[13]: ./media/batch-application-packages/package-file-structure.png "Computação de informações do nó no portal do Azure"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Ficheiros no nó de computação apresentado no portal do Azure"