---
title: Instalar pacotes de aplicativos em nós de computação – lote do Azure | Microsoft Docs
description: Use o recurso pacotes de aplicativos do lote do Azure para gerenciar facilmente vários aplicativos e versões para instalação em nós de computação do lote.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e8fdafc3e8f83cb529718993ffe9d0f7383c10c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449813"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Implantar aplicativos em nós de computação com pacotes de aplicativos do lote

O recurso de pacotes de aplicativos do lote do Azure fornece gerenciamento fácil de aplicativos de tarefas e sua implantação para os nós de computação em seu pool. Com os pacotes de aplicativos, você pode carregar e gerenciar várias versões dos aplicativos que suas tarefas executam, incluindo seus arquivos de suporte. Em seguida, você pode implantar automaticamente um ou mais desses aplicativos nos nós de computação em seu pool.

Neste artigo, você aprenderá a carregar e gerenciar pacotes de aplicativos no portal do Azure. Em seguida, você aprende como instalá-los em nós de computação de um pool com a biblioteca [.net do lote][api_net] .

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações.
>
> As APIs para criar e gerenciar pacotes de aplicativos fazem parte da biblioteca [.net de gerenciamento do lote][api_net_mgmt] . As APIs para instalar pacotes de aplicativos em um nó de computação fazem parte da biblioteca [.net do lote][api_net] . Os recursos comparáveis estão nas APIs do lote disponíveis para outras linguagens. 
>
> O recurso de pacotes de aplicativos descrito aqui substitui o recurso de aplicativos do lote disponível em versões anteriores do serviço.

## <a name="application-package-requirements"></a>Requisitos do pacote de aplicativos
Para usar pacotes de aplicativos, você precisa [vincular uma conta de armazenamento do Azure](#link-a-storage-account) à sua conta do lote.

## <a name="about-applications-and-application-packages"></a>Sobre aplicativos e pacotes de aplicativos
No lote do Azure, um *aplicativo* se refere a um conjunto de binários com versão que pode ser baixado automaticamente para os nós de computação em seu pool. Um *pacote de aplicativos* refere-se a um *conjunto específico* desses binários e representa uma determinada *versão* do aplicativo.

![Diagrama de alto nível de aplicativos e pacotes de aplicativos][1]

### <a name="applications"></a>Aplicações
Um aplicativo no lote contém um ou mais pacotes de aplicativos e especifica opções de configuração para o aplicativo. Por exemplo, um aplicativo pode especificar a versão padrão do pacote de aplicativos para instalar em nós de computação e se seus pacotes podem ser atualizados ou excluídos.

### <a name="application-packages"></a>Pacotes de aplicações
Um pacote de aplicativos é um arquivo. zip que contém os binários do aplicativo e os arquivos de suporte necessários para que as tarefas executem o aplicativo. Cada pacote de aplicativo representa uma versão específica do aplicativo.

Você pode especificar pacotes de aplicativos nos níveis de pool e tarefa. Você pode especificar um ou mais desses pacotes e (opcionalmente) uma versão ao criar um pool ou uma tarefa.

* Os **pacotes de aplicativos de pool** são implantados em *todos* os nós no pool. Os aplicativos são implantados quando um nó ingressa em um pool e quando ele é reinicializado ou a imagem é recriada.
  
    Os pacotes de aplicativos de pool são apropriados quando todos os nós em um pool executam as tarefas de um trabalho. Você pode especificar um ou mais pacotes de aplicativos ao criar um pool e pode adicionar ou atualizar pacotes de um pool existente. Se você atualizar os pacotes de aplicativos de um pool existente, deverá reiniciar seus nós para instalar o novo pacote.
* Os **pacotes de aplicativos de tarefas** são implantados somente em um nó de computação agendado para executar uma tarefa, logo antes de executar a linha de comando da tarefa. Se o pacote de aplicativos e a versão especificados já estiverem no nó, ele não será reimplantado e o pacote existente será usado.
  
    Os pacotes de aplicativos de tarefas são úteis em ambientes de pool compartilhado, em que diferentes trabalhos são executados em um pool e o pool não é excluído quando um trabalho é concluído. Se o trabalho tiver menos tarefas do que nós no conjunto, os pacotes de aplicações de tarefas podem minimizar a transferência de dados, uma vez que a aplicação é implementada apenas nos nós que executam tarefas.
  
    Outros cenários que podem se beneficiar de pacotes de aplicativos de tarefas são trabalhos que executam um aplicativo grande, mas apenas para algumas tarefas. Por exemplo, um estágio de pré-processamento ou uma tarefa de mesclagem, em que o aplicativo de pré-processamento ou de mesclagem é pesado, pode se beneficiar do uso de pacotes de aplicativos de tarefa.

> [!IMPORTANT]
> Há restrições quanto ao número de aplicativos e pacotes de aplicativos em uma conta do lote e no tamanho máximo do pacote de aplicativos. Consulte [cotas e limites para o serviço do lote do Azure](batch-quota-limit.md) para obter detalhes sobre esses limites.
> 
> 

### <a name="benefits-of-application-packages"></a>Benefícios dos pacotes de aplicativos
Os pacotes de aplicativos podem simplificar o código em sua solução do lote e reduzir a sobrecarga necessária para gerenciar os aplicativos executados por suas tarefas.

Com os pacotes de aplicativos, a tarefa inicial do pool não precisa especificar uma longa lista de arquivos de recursos individuais a serem instalados nos nós. Você não precisa gerenciar manualmente várias versões de seus arquivos de aplicativo no armazenamento do Azure ou em seus nós. E você não precisa se preocupar em gerar [URLs SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) para fornecer acesso aos arquivos em sua conta de armazenamento. O lote funciona em segundo plano com o armazenamento do Azure para armazenar pacotes de aplicativos e implantá-los em nós de computação.

> [!NOTE] 
> O tamanho total de uma tarefa de início tem de ser inferior ou igual a 32 768 carateres, incluindo ficheiros de recursos e variáveis de ambiente. Se a tarefa inicial exceder esse limite, o uso de pacotes de aplicativos será outra opção. Você também pode criar um arquivo compactado contendo os arquivos de recurso, carregá-lo como um blob no armazenamento do Azure e descompactá-lo da linha de comando de sua tarefa inicial. 
>
>

## <a name="upload-and-manage-applications"></a>Carregar e gerenciar aplicativos
Você pode usar o [portal do Azure][portal] ou as APIs de gerenciamento do lote para gerenciar os pacotes de aplicativos em sua conta do lote. Nas próximas seções, primeiro mostramos como vincular uma conta de armazenamento e, em seguida, abordamos a adição de aplicativos e pacotes e seu gerenciamento com o Portal.

### <a name="link-a-storage-account"></a>Vincular uma conta de armazenamento
Para usar pacotes de aplicativos, você deve primeiro vincular uma [conta de armazenamento do Azure](batch-api-basics.md#azure-storage-account) à sua conta do lote. Se você ainda não tiver configurado uma conta de armazenamento, o portal do Azure exibirá um aviso na primeira vez em que você clicar em **aplicativos** em sua conta do lote.



![Aviso "nenhum conta de armazenamento configurada" no portal do Azure][9]

O serviço de lote usa a conta de armazenamento associada para armazenar seus pacotes de aplicativos. Depois de vincular as duas contas, o lote pode implantar automaticamente os pacotes armazenados na conta de armazenamento vinculada em seus nós de computação. Para vincular uma conta de armazenamento à sua conta do lote, clique em **conta de armazenamento** na janela de **aviso** e clique em **conta de armazenamento** novamente.

![Escolha a folha conta de armazenamento em portal do Azure][10]

Recomendamos que você crie uma conta de armazenamento *especificamente* para uso com sua conta do lote e selecione-a aqui. Depois de criar uma conta de armazenamento, você poderá vinculá-la à sua conta do lote usando a janela **conta de armazenamento** .

> [!NOTE] 
> No momento, você não pode usar pacotes de aplicativos com uma conta de armazenamento do Azure configurada com [regras de firewall](../storage/common/storage-network-security.md).
> 

O serviço de lote usa o armazenamento do Azure para armazenar seus pacotes de aplicativos como BLOBs de blocos. Você é [cobrado como normal][storage_pricing] para os dados do blob de blocos e o tamanho de cada pacote não pode exceder o tamanho máximo do blob de blocos. Para obter mais informações, consulte [escalabilidade e metas de desempenho do armazenamento do Azure para contas de armazenamento](../storage/blobs/scalability-targets.md). Certifique-se de considerar o tamanho e o número de seus pacotes de aplicativos e remover periodicamente os pacotes preteridos para minimizar os custos.
> 
> 

### <a name="view-current-applications"></a>Exibir aplicativos atuais
Para exibir os aplicativos em sua conta do lote, clique no item de menu **aplicativos** no menu à esquerda ao exibir sua **conta do lote**.

![Bloco aplicativos][2]

A seleção dessa opção de menu abre a janela **aplicativos** :

![Listar aplicações][3]

Esta janela exibe a ID de cada aplicativo em sua conta e as seguintes propriedades:

* **Pacotes**: o número de versões associadas a este aplicativo.
* **Versão padrão**: a versão do aplicativo instalada se você não indicar uma versão ao especificar o aplicativo para um pool. Esta definição é opcional.
* **Permitir atualizações**: o valor que especifica se atualizações de pacote, exclusões e adições são permitidas. Se isso for definido como **não**, as atualizações e exclusões do pacote serão desabilitadas para o aplicativo. Somente novas versões de pacote de aplicativos podem ser adicionadas. A predefinição é **Sim**.

Se você quiser ver a estrutura de arquivos do pacote de aplicativos no nó de computação, navegue até sua conta do lote no Portal. Na sua conta do lote, navegue até **pools**. Selecione o pool que contém os nós de computação nos quais você está interessado.

![Nós no pool][13]

Depois de selecionar o pool, navegue até o nó de computação em que o pacote de aplicativos está instalado. A partir daí, os detalhes do pacote de aplicativos estão localizados na pasta **aplicativos** . Pastas adicionais no nó de computação contêm outros arquivos, como tarefas de inicialização, arquivos de saída, saída de erro, etc.

![Arquivos no nó][14]

### <a name="view-application-details"></a>Exibir detalhes do aplicativo
Para ver os detalhes de um aplicativo, selecione o aplicativo na janela **aplicativos** .

![Detalhes do aplicativo][4]

Nos detalhes do aplicativo, você pode definir as seguintes configurações para seu aplicativo.

* **Permitir atualizações**: especifique se seus pacotes de aplicativos podem ser atualizados ou excluídos. Consulte "atualizar ou excluir um pacote de aplicativos" posteriormente neste artigo.
* **Versão padrão**: especifique um pacote de aplicativos padrão para implantar em nós de computação.
* **Nome de exibição**: especifique um nome amigável que sua solução de lote pode usar ao exibir informações sobre o aplicativo, por exemplo, na interface do usuário de um serviço que você fornece aos clientes por meio do lote.

### <a name="add-a-new-application"></a>Adicionar um novo aplicativo
Para criar um novo aplicativo, adicione um pacote de aplicativos e especifique uma ID de aplicativo nova e exclusiva. O primeiro pacote de aplicativos que você adiciona com a nova ID do aplicativo também cria o novo aplicativo.

Clique em **Aplicações** > **Adicionar**.

![Folha novo aplicativo no portal do Azure][5]

A janela **novo aplicativo** fornece os campos a seguir para especificar as configurações do seu novo aplicativo e pacote de aplicativos.

**ID da Aplicação**

Este campo especifica a ID do novo aplicativo, que está sujeita às regras de validação de ID de lote do Azure padrão. As regras para fornecer uma ID de aplicativo são as seguintes:

* Em nós do Windows, a ID pode conter qualquer combinação de caracteres alfanuméricos, hifens e sublinhados. Em nós do Linux, somente caracteres alfanuméricos e sublinhados são permitidos.
* Não pode conter mais de 64 caracteres.
* Deve ser exclusivo na conta do lote.
* Diferencia maiúsculas de minúsculas e não diferencia maiúsculas de minúsculas.

**Versão**

Este campo especifica a versão do pacote de aplicativos que você está carregando. As cadeias de caracteres de versão estão sujeitas às seguintes regras de validação:

* Em nós do Windows, a cadeia de caracteres de versão pode conter qualquer combinação de caracteres alfanuméricos, hifens, sublinhados e pontos. Em nós do Linux, a cadeia de caracteres de versão pode conter apenas caracteres alfanuméricos e sublinhados.
* Não pode conter mais de 64 caracteres.
* Deve ser exclusivo dentro do aplicativo.
* Diferencia maiúsculas de minúsculas e não diferencia maiúsculas de minúsculas.

**Pacote de aplicativos**

Esse campo especifica o arquivo. zip que contém os binários do aplicativo e os arquivos de suporte necessários para executar o aplicativo. Clique na caixa **selecionar um arquivo** ou no ícone de pasta para procurar e selecionar um arquivo. zip que contém os arquivos do aplicativo.

Depois de selecionar um arquivo, clique em **OK** para iniciar o carregamento no armazenamento do Azure. Quando a operação de upload for concluída, o portal exibirá uma notificação. Dependendo do tamanho do arquivo que você está carregando e da velocidade da conexão de rede, essa operação pode levar algum tempo.

> [!WARNING]
> Não feche a janela **novo aplicativo** antes que a operação de carregamento seja concluída. Isso interrompe o processo de carregamento.
> 
> 

### <a name="add-a-new-application-package"></a>Adicionar um novo pacote de aplicativos
Para adicionar uma versão do pacote de aplicativos a um aplicativo existente, selecione um aplicativo nas janelas de **aplicativos** e clique em **pacotes** > **Adicionar**.

![Folha adicionar pacote de aplicativos no portal do Azure][8]

Como você pode ver, os campos correspondem aos da janela **novo aplicativo** , mas a caixa **ID do aplicativo** está desabilitada. Como você fez para o novo aplicativo, especifique a **versão** do novo pacote, navegue até o arquivo. zip do **pacote do aplicativo** e clique em **OK** para carregar o pacote.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou excluir um pacote de aplicativos
Para atualizar ou excluir um pacote de aplicativos existente, abra os detalhes do aplicativo, clique em **pacotes**, clique nas **reticências** na linha do pacote de aplicativos que você deseja modificar e selecione a ação que deseja executar.

![Atualizar ou excluir pacote no portal do Azure][7]

**Atualização**

Quando você clica em **Atualizar**, a janela **Atualizar pacote** é exibida. Essa janela é semelhante à janela **novo pacote de aplicativos** . no entanto, somente o campo de seleção de pacote é habilitado, permitindo que você especifique um novo arquivo zip a ser carregado.

![Folha atualizar pacote no portal do Azure][11]

**Eliminar**

Ao clicar em **excluir**, você será solicitado a confirmar a exclusão da versão do pacote e o lote excluirá o pacote do armazenamento do Azure. Se você excluir a versão padrão de um aplicativo, a configuração de **versão padrão** será removida para o aplicativo.

![Excluir aplicativo][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicativos em nós de computação
Agora que você aprendeu como gerenciar pacotes de aplicativos com o portal do Azure, podemos discutir como implantá-los em nós de computação e executá-los com tarefas em lote.

### <a name="install-pool-application-packages"></a>Instalar pacotes de aplicativos de pool
Para instalar um pacote de aplicativos em todos os nós de computação em um pool, especifique uma ou mais *referências* de pacote de aplicativos para o pool. Os pacotes de aplicativos que você especificar para um pool são instalados em cada nó de computação quando esse nó ingressa no pool e quando o nó é reinicializado ou a imagem é recriada.

Em .NET do lote, especifique um ou mais [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] quando você cria um novo pool ou para um pool existente. A classe [ApplicationPackageReference][net_pkgref] especifica uma ID de aplicativo e uma versão para instalar nos nós de computação de um pool.

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
> Se uma implantação de pacote de aplicativos falhar por algum motivo, o serviço de lote marcará o nó como [inutilizável][net_nodestate]e nenhuma tarefa será agendada para execução nesse nó. Nesse caso, você deve reiniciar o nó para **reinicializar** a implantação do pacote. Reiniciar o nó também habilita o agendamento de tarefas novamente no nó.
> 
> 

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicativos de tarefa
Semelhante a um pool, você especifica *referências* de pacote de aplicativo para uma tarefa. Quando uma tarefa é agendada para ser executada em um nó, o pacote é baixado e extraído logo antes da linha de comando da tarefa ser executada. Se um pacote e uma versão especificados já estiverem instalados no nó, o pacote não será baixado e o pacote existente será usado.

Para instalar um pacote de aplicativo de tarefa, configure o [CloudTask][net_cloudtask]da tarefa. Propriedade [ApplicationPackageReferences][net_cloudtask_pkgref] :

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

## <a name="execute-the-installed-applications"></a>Executar os aplicativos instalados
Os pacotes que você especificou para um pool ou tarefa são baixados e extraídos para um diretório nomeado dentro do `AZ_BATCH_ROOT_DIR` do nó. O lote também cria uma variável de ambiente que contém o caminho para o diretório nomeado. As linhas de comando de tarefa usam essa variável de ambiente ao referenciar o aplicativo no nó. 

Em nós do Windows, a variável está no seguinte formato:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Em nós do Linux, o formato é ligeiramente diferente. Os pontos (.), hifens (-) e sinais numéricos (#) são achatados para sublinhados na variável de ambiente. Além disso, observe que o caso da ID do aplicativo é preservado. Por exemplo:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` e `version` são valores que correspondem ao aplicativo e à versão do pacote que você especificou para a implantação. Por exemplo, se você especificou que a versão 2,7 do Application *Blender* deve ser instalada em nós do Windows, as linhas de comando da tarefa usarão essa variável de ambiente para acessar seus arquivos:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Em nós do Linux, especifique a variável de ambiente neste formato. Achatar os pontos (.), hifens (-) e sinais numéricos (#) para sublinhados e preservar o caso da ID do aplicativo:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Ao carregar um pacote de aplicativos, você pode especificar uma versão padrão a ser implantada em seus nós de computação. Se você tiver especificado uma versão padrão para um aplicativo, poderá omitir o sufixo de versão ao fazer referência ao aplicativo. Você pode especificar a versão padrão do aplicativo no portal do Azure, na janela **aplicativos** , conforme mostrado em [carregar e gerenciar aplicativos](#upload-and-manage-applications).

Por exemplo, se você definir "2,7" como a versão padrão para o aplicativo *Blender*e suas tarefas fizerem referência à variável de ambiente a seguir, os nós do Windows executarão a versão 2,7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O trecho de código a seguir mostra uma linha de comando de tarefa de exemplo que inicia a versão padrão do aplicativo do *Blender* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Consulte [configurações de ambiente para tarefas](batch-api-basics.md#environment-settings-for-tasks) na [visão geral do recurso de lote](batch-api-basics.md) para obter mais informações sobre as configurações de ambiente do nó de computação.
> 
> 

## <a name="update-a-pools-application-packages"></a>Atualizar os pacotes de aplicações de um conjunto
Se um pool existente já tiver sido configurado com um pacote de aplicativos, você poderá especificar um novo pacote para o pool. Se você especificar uma nova referência de pacote para um pool, o seguinte será aplicável:

* O serviço de lote instala o pacote recentemente especificado em todos os novos nós que ingressam no pool e em qualquer nó existente que seja reinicializado ou com a imagem recriada.
* Os nós de computação que já estão no pool quando você atualiza as referências do pacote não instalam automaticamente o novo pacote de aplicativos. Esses nós de computação devem ser reinicializados ou ter a imagem recriada para receber o novo pacote.
* Quando um novo pacote é implantado, as variáveis de ambiente criadas refletem as novas referências do pacote de aplicativos.

Neste exemplo, o pool existente tem a versão 2,7 do aplicativo de *fusão* configurado como um de seus [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref]. Para atualizar os nós do pool com a versão 2.76 b, especifique um novo [ApplicationPackageReference][net_pkgref] com a nova versão e confirme a alteração.

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

Agora que a nova versão foi configurada, o serviço de lote instala a versão 2.76 b em qualquer nó *novo* que ingresse no pool. Para instalar o 2.76 b nos nós que *já* estão no pool, reinicialize ou refazer a imagem deles. Observe que os nós reinicializados retêm os arquivos de implantações de pacotes anteriores.

## <a name="list-the-applications-in-a-batch-account"></a>Listar os aplicativos em uma conta do lote
Você pode listar os aplicativos e seus pacotes em uma conta do lote usando o [ApplicationOperations][net_appops]. Método [ListApplicationSummaries][net_appops_listappsummaries] .

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
Com os pacotes de aplicativos, você pode ajudar seus clientes a selecionar os aplicativos para seus trabalhos e especificar a versão exata a ser usada ao processar trabalhos com seu serviço habilitado para lote. Você também pode fornecer a capacidade para que seus clientes carreguem e acompanhem seus próprios aplicativos em seu serviço.

## <a name="next-steps"></a>Passos seguintes
* A [API REST do lote][api_rest] também fornece suporte para trabalhar com pacotes de aplicativos. Por exemplo, consulte o elemento [applicationPackageReferences][rest_add_pool_with_packages] em [Adicionar um pool a uma conta][rest_add_pool] para obter informações sobre como especificar os pacotes a serem instalados usando a API REST. Consulte [aplicativos][rest_applications] para obter detalhes sobre como obter informações do aplicativo usando a API REST do lote.
* Saiba como gerenciar programaticamente [contas e cotas do lote do Azure com o .net de gerenciamento do lote](batch-management-dotnet.md). A biblioteca [.net de gerenciamento do lote][api_net_mgmt] pode habilitar recursos de criação e exclusão de conta para seu aplicativo ou serviço do lote.

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

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nível dos pacotes de aplicativos"
[2]: ./media/batch-application-packages/app_pkg_02.png "Bloco de aplicativos no portal do Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Folha de aplicativos no portal do Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Folha detalhes do aplicativo no portal do Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Folha novo aplicativo no portal do Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Lista suspensa atualizar ou excluir pacotes no portal do Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Folha novo pacote de aplicativos no portal do Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Nenhum alerta de conta de armazenamento vinculado"
[10]: ./media/batch-application-packages/app_pkg_10.png "Escolha a folha conta de armazenamento em portal do Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Folha atualizar pacote no portal do Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Caixa de diálogo de confirmação excluir pacote no portal do Azure"
[13]: ./media/batch-application-packages/package-file-structure.png "Informações do nó de computação no portal do Azure"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Arquivos no nó de computação exibido no portal do Azure"
