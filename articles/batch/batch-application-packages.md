---
title: Implementar pacotes de aplicações para calcular os nosdes
description: Utilize a funcionalidade de pacotes de aplicação do Azure Batch para gerir facilmente várias aplicações e versões para instalação em nós computacional Batch.
ms.topic: how-to
ms.date: 04/26/2019
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8d0ab6227e232728818afbbc58ca6521d9a417df
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922009"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Implementar aplicações para calcular os nóns com pacotes de aplicações batch

A funcionalidade de pacotes de aplicação do Azure Batch proporciona uma gestão fácil das aplicações de tarefas e a sua implantação nos nós de computação na sua piscina. Com pacotes de aplicações, pode carregar e gerir várias versões das aplicações que as suas tarefas executam, incluindo os seus ficheiros de suporte. Em seguida, pode implantar automaticamente uma ou mais destas aplicações nos nós de computação na sua piscina.

Neste artigo, aprende a carregar e gerir pacotes de aplicações no portal Azure. Em seguida, aprende-se a instalá-los nos nós computacionais de uma piscina com a biblioteca [Batch .NET.][api_net]

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações.
>
> As APIs para a criação e gestão de pacotes de aplicações fazem parte da biblioteca [Batch Management .NET.][api_net_mgmt] As APIs para instalar pacotes de aplicações num nó de computação fazem parte da biblioteca [Batch .NET.][api_net] Características comparáveis estão nas APIs do lote disponível para outras línguas. 
>
> A funcionalidade de pacotes de aplicações aqui descrita substitui a funcionalidade De Aplicações de Lote disponível em versões anteriores do serviço.

## <a name="application-package-requirements"></a>Requisitos de pacote de aplicação
Para utilizar pacotes de aplicações, precisa [de ligar uma conta de Armazenamento Azure](#link-a-storage-account) à sua conta Batch.

## <a name="about-applications-and-application-packages"></a>Sobre aplicações e pacotes de aplicações
Dentro do Azure Batch, uma *aplicação* refere-se a um conjunto de binários versados que podem ser automaticamente descarregados para os nós computacional na sua piscina. Um pacote de *aplicações* refere-se a um *conjunto específico* dessas binários e representa uma determinada *versão* da aplicação.

![Diagrama de alto nível de aplicações e pacotes de aplicações][1]

### <a name="applications"></a>Aplicações
Uma aplicação em Batch contém um ou mais pacotes de aplicação e especifica opções de configuração para a aplicação. Por exemplo, uma aplicação pode especificar a versão de pacote de aplicação padrão para instalar nos nós computacionais e se os seus pacotes podem ser atualizados ou eliminados.

### <a name="application-packages"></a>Pacotes de aplicações
Um pacote de aplicações é um ficheiro .zip que contém os binários de aplicação e ficheiros de suporte necessários para que as suas tarefas executem a aplicação. Cada pacote de aplicações representa uma versão específica da aplicação.

Pode especificar pacotes de aplicações nos níveis de piscina e tarefa. Pode especificar uma ou mais destas embalagens e (opcionalmente) uma versão quando criar uma piscina ou tarefa.

* **Os pacotes de aplicação da** piscina são implantados em *todos os* nós da piscina. As aplicações são implementadas quando um nó une uma piscina, e quando é reiniciado ou reesimagem.
  
    Os pacotes de aplicação da piscina são apropriados quando todos os nós de uma piscina executam as tarefas de um trabalho. Pode especificar um ou mais pacotes de aplicações quando criar uma piscina, e pode adicionar ou atualizar os pacotes de uma piscina existente. Se atualizar os pacotes de aplicação de uma piscina existentes, tem de reiniciar os seus nós para instalar o novo pacote.
* **Os pacotes de aplicação de tarefas são implantados apenas** num nó de computação programado para executar uma tarefa, pouco antes de executar a linha de comando da tarefa. Se o pacote e versão de aplicação especificados já estiver no nó, este não é reenviado e o pacote existente é utilizado.
  
    Os pacotes de aplicação de tarefas são úteis em ambientes de piscina partilhada, onde diferentes empregos são executados em uma piscina, e a piscina não é eliminada quando um trabalho é concluído. Se o trabalho tiver menos tarefas do que nós no conjunto, os pacotes de aplicações de tarefas podem minimizar a transferência de dados, uma vez que a aplicação é implementada apenas nos nós que executam tarefas.
  
    Outros cenários que podem beneficiar de pacotes de aplicação de tarefas são empregos que executam uma grande aplicação, mas apenas para algumas tarefas. Por exemplo, uma fase de pré-processamento ou uma tarefa de fusão, em que a aplicação de pré-processamento ou fusão é pesada, pode beneficiar da utilização de pacotes de aplicação de tarefa.

> [!IMPORTANT]
> Existem restrições ao número de pedidos e pacotes de aplicações dentro de uma conta Batch e no tamanho máximo do pacote de aplicação. Consulte [quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter mais informações sobre estes limites.
> 
> 

### <a name="benefits-of-application-packages"></a>Benefícios dos pacotes de aplicação
Os pacotes de aplicação podem simplificar o código na sua solução Batch e baixar as despesas necessárias para gerir as aplicações que as suas tarefas executam.

Com pacotes de aplicações, a tarefa inicial da sua piscina não tem de especificar uma longa lista de ficheiros de recursos individuais para instalar nos nós. Não tem de gerir manualmente várias versões dos ficheiros da sua aplicação no Azure Storage ou nos seus nós. E não precisa de se preocupar em gerar [URLs SAS](../storage/common/storage-sas-overview.md) para fornecer acesso aos ficheiros da sua conta de Armazenamento. O lote trabalha em segundo plano com o Azure Storage para armazenar pacotes de aplicações e implantá-los para calcular nós.

> [!NOTE] 
> O tamanho total de uma tarefa de início tem de ser inferior ou igual a 32 768 carateres, incluindo ficheiros de recursos e variáveis de ambiente. Se a sua tarefa inicial exceder este limite, então a utilização de pacotes de aplicações é outra opção. Também pode criar um arquivo com fecho de correr contendo os seus ficheiros de recursos, carregá-lo como uma bolha para o Azure Storage e, em seguida, desapertá-lo da linha de comando da sua tarefa inicial. 
>
>

## <a name="upload-and-manage-applications"></a>Carregar e gerir aplicações
Pode utilizar o [portal Azure][portal] ou as APIs de Gestão de Lotes para gerir os pacotes de aplicações na sua conta Batch. Nas próximas secções, primeiro mostramos como ligar uma conta de Armazenamento, depois discutimos adicionar aplicações e pacotes e geri-los com o portal.

### <a name="link-a-storage-account"></a>Ligue uma conta de Armazenamento
Para utilizar pacotes de aplicações, tem primeiro de ligar uma [conta de Armazenamento Azure](accounts.md#azure-storage-accounts) à sua conta Batch. Se ainda não tiver configurado uma conta de Armazenamento, o portal Azure apresenta um aviso na primeira vez que clica em **Aplicações** na sua conta Batch.



![Aviso 'Sem conta de armazenamento configurado' no portal Azure][9]

O serviço Batch utiliza a conta de Armazenamento associada para armazenar os seus pacotes de aplicações. Depois de ter ligado as duas contas, o Batch pode implantar automaticamente os pacotes armazenados na conta de Armazenamento ligada aos seus nós de computação. Para ligar uma conta de Armazenamento à sua conta Batch, clique em **'Armazenamento'** na janela **Aviso** e, em seguida, clique novamente na **Conta de Armazenamento.**

![Escolha a lâmina da conta de armazenamento no portal Azure][10]

Recomendamos que crie uma conta de Armazenamento *especificamente* para utilização com a sua conta Batch e selecione-a aqui. Depois de criar uma conta de Armazenamento, pode ligá-la à sua conta Batch utilizando a janela **da Conta de Armazenamento.**

> [!IMPORTANT] 
> - Atualmente não é possível utilizar pacotes de aplicações com uma conta de Armazenamento Azure que esteja configurada com [regras de firewall](../storage/common/storage-network-security.md).
> - Uma conta de Armazenamento Azure com **espaço hierárquico** definido para **Enabled** não pode ser usada para pacotes de aplicações.

O serviço Batch utiliza o Azure Storage para armazenar os seus pacotes de aplicações como bolhas de bloqueio. Você é [cobrado normalmente][storage_pricing] para os dados do bloco blob, e o tamanho de cada embalagem não pode exceder o tamanho máximo de blob de bloco. Para obter mais informações, consulte [a escalabilidade do armazenamento Azure e os objetivos de desempenho para contas de armazenamento.](../storage/blobs/scalability-targets.md) Certifique-se de considerar o tamanho e o número dos seus pacotes de aplicação e remover periodicamente pacotes prectados para minimizar os custos.

### <a name="view-current-applications"></a>Ver aplicações atuais
Para ver as aplicações na sua conta Batch, clique no item do menu **Aplicações** no menu esquerdo enquanto vê a sua **conta Batch**.

![Azulejo de aplicações][2]

A seleção desta opção de menu abre a janela **Aplicações:**

![Listar aplicações][3]

Esta janela apresenta o ID de cada aplicação na sua conta e as seguintes propriedades:

* **Pacotes**: Número de versões associadas a esta aplicação.
* **Versão predefinida**: A versão de aplicação instalada se não indicar uma versão quando especificar a aplicação para um pool. Esta definição é opcional.
* **Permitir atualizações**: O valor que especifica se são permitidas atualizações, supressões e adições de pacotes. Se isto for definido como **Nº,** as atualizações e supressões de pacotes são desativadas para a aplicação. Apenas novas versões de pacote de aplicações podem ser adicionadas. A predefinição é **Sim**.

Se quiser ver a estrutura de ficheiros do pacote de aplicações no nó de computação, navegue para a sua conta Batch no portal. A partir da sua conta Batch, navegue até **Pools.** Selecione a piscina que contém os nó(s) de computação em que está interessado.

![Nódes na piscina][13]

Depois de selecionar a sua piscina, navegue para o nó de cálculo onde o pacote de aplicação está instalado. A partir daí, os detalhes do pacote de aplicações estão localizados na pasta de **aplicações.** As pastas adicionais no nó de computação contêm outros ficheiros, tais como tarefas de início, ficheiros de saída, saída de erro, etc.

![Ficheiros no nó][14]

### <a name="view-application-details"></a>Ver detalhes da aplicação
Para ver os detalhes de uma aplicação, selecione a aplicação na janela **Aplicações.**

![Detalhes da aplicação][4]

Nos detalhes da aplicação, pode configurar as seguintes definições para a sua aplicação.

* **Permitir atualizações**: Especifique se os seus pacotes de aplicação podem ser atualizados ou eliminados. Consulte "Atualizar ou Eliminar um pacote de aplicações" mais tarde neste artigo.
* **Versão predefinida**: Especifique um pacote de aplicação predefinido para implementar para os nós de computação.
* **Nome do visor**: Especifique um nome amigável que a sua solução Batch possa utilizar quando apresentar informações sobre a aplicação, por exemplo, na UI de um serviço que fornece aos seus clientes através do Batch.

### <a name="add-a-new-application"></a>Adicionar uma nova aplicação
Para criar uma nova aplicação, adicione um pacote de aplicações e especifique um novo ID de aplicação único. O primeiro pacote de aplicações que adiciona com o novo ID da aplicação também cria a nova aplicação.

Clique **em Aplicações**  >  **Adicionar**.

![Nova lâmina de aplicação no portal Azure][5]

A nova janela **de aplicações** fornece os seguintes campos para especificar as definições do seu novo pacote de aplicações e aplicações.

**ID da Aplicação**

Este campo especifica o ID da sua nova aplicação, que está sujeita às regras padrão de validação do Lote Azure. As regras para a disponibilização de um ID de aplicação são as seguintes:

* Nos nós windows, o ID pode conter qualquer combinação de caracteres alfanuméricos, hífens e sublinhados. Nos nós Linux, apenas são permitidos caracteres alfanuméricos e sublinhados.
* Não pode conter mais de 64 caracteres.
* Deve ser único na conta do Lote.
* É preservação de casos e caso-insensível.

**Versão**

Este campo especifica a versão do pacote de aplicações que está a carregar. As cadeias de versão estão sujeitas às seguintes regras de validação:

* Nos nós windows, a cadeia de versão pode conter qualquer combinação de caracteres alfanuméricos, hífens, sublinhados e períodos. Nos nós Linux, a cadeia de versão pode conter apenas caracteres alfanuméricos e sublinhados.
* Não pode conter mais de 64 caracteres.
* Deve ser único dentro da aplicação.
* São preservação de casos e casos insensíveis.

**Pacote de candidaturas**

Este campo especifica o ficheiro .zip que contém os binários de aplicação e ficheiros de suporte necessários para executar a aplicação. Clique na **caixa de ficheiros Select ou** no ícone de pasta para navegar e selecione um ficheiro .zip que contenha os ficheiros da sua aplicação.

Depois de ter selecionado um ficheiro, clique **em OK** para iniciar o upload para O Azure Storage. Quando a operação de upload estiver concluída, o portal apresenta uma notificação. Dependendo do tamanho do ficheiro que está a carregar e da velocidade da sua ligação de rede, esta operação pode demorar algum tempo.

> [!WARNING]
> Não feche a nova janela **de aplicação** antes de a operação de upload estar concluída. Ao fazê-lo, interrompe o processo de upload.
> 
> 

### <a name="add-a-new-application-package"></a>Adicione um novo pacote de aplicações
Para adicionar uma versão pacote de aplicação para uma aplicação existente, selecione uma aplicação nas **janelas aplicações** e clique em **Pacotes**  >  **Adicionar**.

![Adicione a lâmina do pacote de aplicação no portal Azure][8]

Como pode ver, os campos coincidem com os da nova janela de **aplicação,** mas a caixa **de identificação** de aplicação está desativada. Tal como fez para a nova aplicação, especifique a **versão** para o seu novo pacote, navegue no seu **pacote de aplicação** .zip file e, em seguida, clique em **OK** para carregar o pacote.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou eliminar um pacote de aplicações
Para atualizar ou eliminar um pacote de aplicações existente, abra os detalhes da aplicação, clique em **Pacotes,** clique na **elipse** na linha do pacote de aplicações que pretende modificar e selecione a ação que pretende realizar.

![Atualizar ou eliminar pacote no portal Azure][7]

**Atualizar**

Quando clicar em **Atualização,** as janelas **de pacote de atualização** são apresentadas. Esta janela é semelhante à janela do **pacote de novas aplicações,** no entanto apenas o campo de seleção de pacotes está ativado, permitindo especificar um novo ficheiro ZIP para carregar.

![Atualizar a lâmina do pacote no portal Azure][11]

**Eliminar**

Quando clicar em **Eliminar,** é-lhe pedido que confirme a eliminação da versão do pacote e o Batch elimina a embalagem do Azure Storage. Se eliminar a versão predefinida de uma aplicação, a definição **de versão Predefinição** é removida para a aplicação.

![Excluir aplicação ][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicações em nó de computação
Agora que aprendeu a gerir pacotes de aplicações com o portal Azure, podemos discutir como implantá-los para calcular nós e executá-los com tarefas de Batch.

### <a name="install-pool-application-packages"></a>Instalar pacotes de aplicação de piscina
Para instalar um pacote de aplicações em todos os nós computacionais numa piscina, especifique uma ou mais referências de *pacotes* de aplicação para o pool. Os pacotes de aplicação que especifica para uma piscina são instalados em cada nó de computação quando esse nó se junta à piscina, e quando o nó é reiniciado ou remeximedido.

Em Batch .NET, especifique uma ou mais [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] quando cria uma nova piscina, ou para uma piscina existente. A classe [ApplicationPackageReference][net_pkgref] especifica um ID de aplicação e versão para instalar nos nós de cálculo de uma piscina.

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
> Se uma implementação de pacote de aplicação falhar por qualquer motivo, o serviço Batch marca o nó [inutilizável][net_nodestate], e não estão agendadas tarefas para a execução nesse nó. Neste caso, deve **reiniciar** o nó para reiniciar a colocação do pacote. Reiniciar o nó também permite agendar a tarefa novamente no nó.
> 
> 

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicação de tarefas
Semelhante a uma piscina, você especifica *referências* de pacote de aplicação para uma tarefa. Quando uma tarefa está programada para ser executada num nó, o pacote é descarregado e extraído pouco antes da linha de comando da tarefa ser executada. Se um pacote e versão especificados já estiverem instalados no nó, o pacote não é descarregado e o pacote existente é usado.

Para instalar um pacote de aplicações de tarefa, configuure o [CloudTask][net_cloudtask]da tarefa . [Propriedade ApplicationPackageReferences:][net_cloudtask_pkgref]

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

Por exemplo, se definir "2.7" como a versão padrão para *o liquidificador*de aplicações , e as suas tarefas referenciarem a seguinte variável ambiental, então os seus nós Windows executarão a versão 2.7:

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
Se uma piscina já existente já tiver sido configurada com um pacote de aplicações, pode especificar um novo pacote para a piscina. Se especificar uma nova referência de pacote para uma piscina, aplica-se:

* O serviço Batch instala o pacote recentemente especificado em todos os novos nós que se juntam à piscina e em qualquer nó existente que seja reiniciado ou reesimagem.
* Os nós computacionais que já se encontram na piscina quando atualiza as referências do pacote não instalam automaticamente o novo pacote de aplicações. Estes nós computatórios devem ser reiniciados ou remimagemed para receber o novo pacote.
* Quando um novo pacote é implementado, as variáveis ambientais criadas refletem as novas referências do pacote de aplicações.

Neste exemplo, o pool existente tem a versão 2.7 da aplicação *do liquidificador* configurada como uma das suas [CloudPool.][net_cloudpool] [AplicaçõesPackageReferences][net_cloudpool_pkgref]. Para atualizar os nós do pool com a versão 2.76b, especifique uma nova [ApplicationPackageReference][net_pkgref] com a nova versão e comprometa a alteração.

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

Agora que a nova versão foi configurada, o serviço Batch instala a versão 2.76b para qualquer *novo* nó que una a piscina. Para instalar 2.76b nos nós que *já* se encontram na piscina, reinicie-os ou reimagem.. Note que os nós reiniciados retêm os ficheiros de implementações de pacotes anteriores.

## <a name="list-the-applications-in-a-batch-account"></a>Listar as candidaturas numa conta Batch
Pode listar as aplicações e as suas embalagens numa conta Batch utilizando as [Operações de Aplicação.][net_appops] [Método ListApplicationSummaries.][net_appops_listappsummaries]

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

## <a name="wrap-up"></a>Embrulhe
Com pacotes de aplicações, pode ajudar os seus clientes a selecionar as aplicações para os seus trabalhos e especificar a versão exata a utilizar ao processar trabalhos com o seu serviço habilitado para o Lote. Pode também fornecer a capacidade para os seus clientes fazerem o upload e rastrearem as suas próprias aplicações no seu serviço.

## <a name="next-steps"></a>Passos seguintes
* A [API de Lote REST][api_rest] também fornece suporte para trabalhar com pacotes de aplicações. Por exemplo, consulte o elemento [Decomprase][rest_add_pool_with_packages] de aplicação [Adicionar um pool a uma conta][rest_add_pool] para obter informações sobre como especificar as embalagens a instalar utilizando a API REST. Consulte [as Aplicações][rest_applications] para obter informações sobre como obter informações sobre a aplicação utilizando a API do Lote REST.
* Saiba como gerir programáticamente [as contas e quotas do Azure Batch com a Batch Management .NET](batch-management-dotnet.md). A biblioteca [Batch Management .NET][api_net_mgmt] pode permitir funcionalidades de criação e eliminação de conta para a sua aplicação ou serviço batch.

[api_net]: /dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: /rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: /dotnet/api/microsoft.azure.batch.applicationoperations
[net_appops_listappsummaries]: /dotnet/api/microsoft.azure.batch.applicationoperations
[net_cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_cloudpool_pkgref]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_cloudtask_pkgref]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_nodestate]: /dotnet/api/microsoft.azure.batch.computenode
[net_pkgref]: /dotnet/api/microsoft.azure.batch.applicationpackagereference
[portal]: https://portal.azure.com
[rest_applications]: /rest/api/batchservice/application
[rest_add_pool]: /rest/api/batchservice/pool/add
[rest_add_pool_with_packages]: /rest/api/batchservice/pool/add#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Pacotes de aplicação diagrama de alto nível"
[2]: ./media/batch-application-packages/app_pkg_02.png "Azulejos de aplicações no portal Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Lâmina de aplicações no portal Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Lâmina de detalhes da aplicação no portal Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nova lâmina de aplicação no portal Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Atualizar ou apagar pacotes drop-down no portal Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nova lâmina de pacote de aplicação no portal Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nenhum alerta de conta de armazenamento ligado"
[10]: ./media/batch-application-packages/app_pkg_10.png "Escolha a lâmina da conta de armazenamento no portal Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Atualizar a lâmina do pacote no portal Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Apagar o diálogo de confirmação do pacote no portal Azure"
[13]: ./media/batch-application-packages/package-file-structure.png "Informação sobre nó computacional no portal Azure"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Ficheiros no nó computativo apresentados no portal Azure"
