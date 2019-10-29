---
title: Utilizar atividades personalizadas num pipeline do Azure Data Factory
description: Saiba como criar atividades personalizadas e usá-las em um pipeline Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 698ca6736af86358de13f6deae8f1e2dba92f46e
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990655"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilizar atividades personalizadas num pipeline do Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-use-custom-activities.md)
> * [Versão 2 (versão atual)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [atividades personalizadas na v2](../transform-data-using-dotnet-custom-activity.md).

Há dois tipos de atividades que você pode usar em um pipeline de Azure Data Factory.

- [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para mover dados entre [armazenamentos de dados de origem e coletor com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para transformar dados usando serviços de computação, como Azure HDInsight, lote do azure e Azure Machine Learning.

Para mover dados de/para um armazenamento de dados ao qual Data Factory não oferece suporte, crie uma **atividade personalizada** com sua própria lógica de movimentação de dados e use a atividade em um pipeline. Da mesma forma, para transformar/processar dados de uma forma que não seja suportada pelo Data Factory, crie uma atividade personalizada com sua própria lógica de transformação de dados e use a atividade em um pipeline.

Você pode configurar uma atividade personalizada para ser executada em um pool de máquinas virtuais do **lote do Azure** . Ao usar o lote do Azure, você pode usar apenas um pool do lote do Azure existente.

A instrução a seguir fornece instruções passo a passo para criar uma atividade personalizada do .NET e usar a atividade personalizada em um pipeline. A instrução usa um serviço vinculado **do lote do Azure** .

> [!IMPORTANT]
> - Não é possível usar um Gerenciamento de Dados gateway de uma atividade personalizada para acessar fontes de dados locais. Atualmente, [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) dá suporte apenas à atividade de cópia e atividade de procedimento armazenado no data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Walkthrough: criar uma atividade personalizada
### <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2012/2013/2015/2017
* Transferir e instalar o [SDK .NET do Azure](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Pré-requisitos do lote do Azure
No passo a passo, você executa suas atividades personalizadas do .NET usando o lote do Azure como um recurso de computação. O **Azure Batch** é um serviço de plataforma que serve para executar aplicações de computação de alto desempenho (HPC) e paralelas em larga escala de forma eficaz na cloud. O lote do Azure agenda o trabalho de computação intensiva para ser executado em uma coleção gerenciada **de máquinas virtuais**e pode dimensionar automaticamente os recursos de computação para atender às necessidades de seus trabalhos. Consulte o artigo [noções básicas do lote do Azure][batch-technical-overview] para obter uma visão geral detalhada do serviço de lote do Azure.

Para o tutorial, crie uma conta do lote do Azure com um pool de VMs. Eis os passos:

1. Crie uma **conta do lote do Azure** usando o [portal do Azure](https://portal.azure.com). Confira [o artigo criar e gerenciar uma conta do lote do Azure][batch-create-account] para obter instruções.
2. Anote o nome da conta do lote do Azure, a chave da conta, o URI e o nome do pool. Você precisa deles para criar um serviço vinculado do lote do Azure.
    1. Na home page da conta do lote do Azure, você verá uma **URL** no seguinte formato: `https://myaccount.westus.batch.azure.com`. Neste exemplo, **myaccount** é o nome da conta do lote do Azure. O URI usado na definição de serviço vinculado é a URL sem o nome da conta. Por exemplo: `https://<region>.batch.azure.com`.
    2. Clique em **chaves** no menu à esquerda e copie a **chave de acesso primária**.
    3. Para usar um pool existente, clique em **pools** no menu e anote a **ID** do pool. Se você não tiver um pool existente, vá para a próxima etapa.
2. Crie um **pool do lote do Azure**.

   1. Na [portal do Azure](https://portal.azure.com), clique em **procurar** no menu à esquerda e clique em **contas do lote**.
   2. Selecione sua conta do lote do Azure para abrir a folha **conta do lote** .
   3. Clique no bloco **pools** .
   4. Na folha **pools** , clique no botão Adicionar na barra de ferramentas para adicionar um pool.
      1. Insira uma ID para o pool (ID do pool). Anote a **ID do pool**; Você precisará dela ao criar a solução de Data Factory.
      2. Especifique o **Windows Server 2012 R2** para a configuração da família do sistema operacional.
      3. Selecione um **tipo de preço de nó**.
      4. Insira **2** como valor para a configuração **dedicada de destino** .
      5. Insira **2** como valor para a configuração **máximo de tarefas por nó** .
   5. Clique em **OK** para criar o conjunto.
   6. Anote a **ID** do pool.

### <a name="high-level-steps"></a>Etapas de alto nível
Aqui estão as duas etapas de alto nível que você executa como parte deste passo a passos:

1. Crie uma atividade personalizada que contenha uma lógica de processamento/transformação de dados simples.
2. Crie um data factory do Azure com um pipeline que usa a atividade personalizada.

### <a name="create-a-custom-activity"></a>Criar uma atividade personalizada
Para criar uma atividade personalizada do .NET, crie um projeto de **biblioteca de classes do .net** com uma classe que implemente essa interface **IDotNetActivity** . Esta interface tem apenas um método: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) e sua assinatura é:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

O método usa quatro parâmetros:

- **linkservices**. Essa propriedade é uma lista enumerável de serviços vinculados do repositório de dados referenciados por conjuntos de dados de entrada/saída para a atividade.
- **conjuntos**de os. Esta propriedade é uma lista enumerável de conjuntos de dados de entrada/saída para a atividade. Você pode usar esse parâmetro para obter os locais e esquemas definidos pelos conjuntos de dados de entrada e saída.
- **atividade**. Essa propriedade representa a atividade atual. Ele pode ser usado para acessar propriedades estendidas associadas à atividade personalizada. Consulte [acessar propriedades estendidas](#access-extended-properties) para obter detalhes.
- **agente de log**. Esse objeto permite escrever comentários de depuração que são exibidos no log do usuário para o pipeline.

O método retorna um dicionário que pode ser usado para encadear atividades personalizadas em conjunto no futuro. Este recurso ainda não está implementado, portanto, retorne um dicionário vazio do método.

### <a name="procedure"></a>Procedimento
1. Crie um projeto de **biblioteca de classes .net** .
   <ol type="a">
     <li>Inicie o Visual Studio.</li>
     <li>Clique em <b>Ficheiro</b>, aponte para <b>Novo</b> e, em seguida, clique em <b>Projeto</b>.</li>
     <li>Expanda <b>Modelos</b> e selecione <b>Visual C#</b>. Neste tutorial, você usará C#, mas poderá usar qualquer linguagem .net para desenvolver a atividade personalizada.</li>
     <li>Selecione <b>biblioteca de classes</b> na lista de tipos de projeto à direita. No Visual Studio, escolha <b>biblioteca de classes (.NET Framework)</b> </li>
     <li>Digite <b>MyDotNetActivity</b> para o <b>nome</b>.</li>
     <li>Selecione <b>C:\ADFGetStarted</b> para o <b>local</b>.</li>
     <li>Clique em <b>OK</b> para criar o projeto.</li>
   </ol>

2. Clique em **Ferramentas**, aponte para **Gestor de Pacotes NuGet** e clique em **Consola do Gestor de Pacotes**.

3. No console do Gerenciador de pacotes, execute o seguinte comando para importar **Microsoft. Azure. Management. Datafactorings**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importe o pacote NuGet do **armazenamento do Azure** no projeto.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory iniciador de serviço requer a versão 4,3 do WindowsAzure. Storage. Se você adicionar uma referência a uma versão mais recente do assembly de armazenamento do Azure em seu projeto de atividade personalizada, você verá um erro quando a atividade for executada. Para resolver o erro, consulte a seção [isolamento de AppDomain](#appdomain-isolation) .
5. Adicione as seguintes instruções **using** ao arquivo de origem no projeto.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Altere o nome do **namespace** para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Altere o nome da classe para **MyDotNetActivity** e derive-a da interface **IDotNetActivity** , conforme mostrado no trecho de código a seguir:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implemente (adicione) o método **Execute** da interface **IDotNetActivity** para a classe **MyDotNetActivity** e copie o código de exemplo a seguir para o método.

    O exemplo a seguir conta o número de ocorrências do termo de pesquisa ("Microsoft") em cada blob associado a uma fatia de dados.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);

            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```
9. Adicione os seguintes métodos auxiliares:

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>

    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
        return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    O método GetFolderPath retorna o caminho para a pasta que o conjunto de pontos aponta e o método GetFileName Retorna o nome do blob/arquivo para o qual o conjunto de pontos aponta. Se você tiver folderPath define usando variáveis como {Year}, {month}, {Day} etc., o método retornará a cadeia de caracteres como está sem substituí-las por valores de tempo de execução. Consulte a seção [Access Extended Properties](#access-extended-properties) para obter detalhes sobre como acessar SliceStart, SliceEnd, etc.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    O método Calculate calcula o número de instâncias da palavra-chave Microsoft nos arquivos de entrada (BLOBs na pasta). O termo de pesquisa ("Microsoft") é embutido no código.
10. Compile o projeto. Clique em **Compilar** no menu e clique em **Compilar solução**.

    > [!IMPORTANT]
    > Defina a versão 4.5.2 do .NET Framework como a estrutura de destino para seu projeto: clique com o botão direito do mouse no projeto e clique em **Propriedades** para definir a estrutura de destino. Data Factory não oferece suporte a atividades personalizadas compiladas em versões .NET Framework posteriores a 4.5.2.

11. Inicie o **Windows Explorer**e navegue para a pasta **bin\Debug** ou **bin\Release** , dependendo do tipo de compilação.
12. Crie um arquivo zip **MyDotNetActivity. zip** que contenha todos os binários na pasta \<projeto\>pasta \bin\Debug Inclua o arquivo **MyDotNetActivity. pdb** para obter detalhes adicionais, como número de linha no código-fonte que causou o problema se houvesse uma falha.

    > [!IMPORTANT]
    > Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.

    ![Arquivos de saída binários](./media/data-factory-use-custom-activities/Binaries.png)
14. Crie um contêiner de blob chamado **customactivitycontainer** se ele ainda não existir.
15. Carregue MyDotNetActivity. zip como um blob para o customactivitycontainer em um armazenamento de BLOBs do Azure de **uso geral** (não é um armazenamento de BLOBs quente/frio) que é referenciado por AzureStorageLinkedService.

> [!IMPORTANT]
> Se você adicionar esse projeto de atividade do .NET a uma solução no Visual Studio que contém um projeto Data Factory e adicionar uma referência ao projeto de atividade do .NET do projeto de aplicativo Data Factory, não será necessário executar as duas últimas etapas da criação manual do zip arquivo e carregando-o para o armazenamento de BLOBs do Azure de uso geral. Quando você publica entidades de Data Factory usando o Visual Studio, essas etapas são feitas automaticamente pelo processo de publicação. Para obter mais informações, consulte [Data Factory projeto no Visual Studio](#data-factory-project-in-visual-studio) seção.

## <a name="create-a-pipeline-with-custom-activity"></a>Criar um pipeline com atividade personalizada
Você criou uma atividade personalizada e carregou o arquivo zip com binários em um contêiner de BLOB em uma conta de armazenamento do Azure de **uso geral** . Nesta seção, você cria uma data factory do Azure com um pipeline que usa a atividade personalizada.

O conjunto de dados de entrada para a atividade personalizada representa BLOBs (arquivos) na pasta customactivityinput do contêiner adftutorial no armazenamento de BLOBs. O conjunto de resultados de saída para a atividade representa os blobs de saída na pasta customactivityoutput do contêiner adftutorial no armazenamento de BLOBs.

Crie um arquivo **File. txt** com o conteúdo a seguir e carregue-o na pasta **customactivityinput** do contêiner **adftutorial** . Crie o contêiner adftutorial se ele ainda não existir.

```
test custom activity Microsoft test custom activity Microsoft
```

A pasta de entrada corresponde a uma fatia em Azure Data Factory mesmo que a pasta tenha dois ou mais arquivos. Quando cada fatia é processada pelo pipeline, a atividade personalizada é iterada por todos os BLOBs na pasta de entrada para essa fatia.

Você vê um arquivo de saída com na pasta adftutorial\customactivityoutput com uma ou mais linhas (o mesmo que o número de BLOBs na pasta de entrada):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Aqui estão as etapas que você executa nesta seção:

1. Crie um **Data Factory**.
2. Crie **Serviços vinculados** para o pool de VMs do lote do Azure no qual a atividade personalizada é executada e o armazenamento do Azure que contém os blobs de entrada/saída.
3. Crie **conjuntos** de dados de entrada e saída que representem a entrada e a saída da atividade personalizada.
4. Crie um **pipeline** que usa a atividade personalizada.

> [!NOTE]
> Crie o **arquivo. txt** e carregue-o em um contêiner de BLOBs, caso ainda não tenha feito isso. Consulte as instruções na seção anterior.

### <a name="step-1-create-the-data-factory"></a>Etapa 1: criar o data factory
1. Depois de fazer logon no portal do Azure, execute as seguintes etapas:
   1. Clique em **criar um recurso** no menu à esquerda.
   2. Clique em **dados + análise** na folha **novo** .
   3. Clique em **Data Factory** no painel **Análise de dados**.

      ![Menu novo Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Na folha **novo data Factory** , digite **CustomActivityFactory** para o nome. O nome do Azure Data Factory deve ser globalmente exclusivo. Se você receber o erro: o **nome do data Factory "CustomActivityFactory" não está disponível**, altere o nome do data Factory (por exemplo, **yournameCustomActivityFactory**) e tente criar novamente.

    ![Nova folha Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Clique em **nome do grupo de recursos**e selecione um grupo de recursos existente ou crie um grupo de recursos.
4. Verifique se você está usando a **assinatura** e a **região** corretas onde deseja que a data Factory seja criada.
5. Clique em **Criar** no painel **Nova fábrica de dados**.
6. Você verá o data factory que está sendo criado no **painel** do portal do Azure.
7. Depois que o data factory tiver sido criado com êxito, você verá a folha Data Factory, que mostra o conteúdo da data factory.

    ![Painel Data Factory](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Etapa 2: criar serviços vinculados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Nesta etapa, você vincula sua conta de armazenamento do Azure e a conta do lote do Azure ao seu data factory.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
1. Clique no bloco **criar e implantar** na folha **Data Factory** para **CustomActivityFactory**. Será apresentado o Editor do Data Factory.
2. Clique em **novo armazenamento de dados** na barra de comandos e escolha **armazenamento do Azure**. Deverá ver o script JSON para criar um serviço ligado do Storage do Azure no editor.

    ![Novo armazenamento de dados-armazenamento do Azure](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Substitua `<accountname>` pelo nome da sua conta de armazenamento do Azure e `<accountkey>` com a chave de acesso da conta de armazenamento do Azure. Para saber como obter a chave de acesso ao armazenamento, veja [Ver, copiar e regenerar as chaves de acesso ao armazenamento](../../storage/common/storage-account-manage.md#access-keys).

    ![Serviço curtido do armazenamento do Azure](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="create-azure-batch-linked-service"></a>Criar serviço vinculado do lote do Azure
1. No editor de Data Factory, clique em **... Mais** na barra de comandos, clique em **nova computação**e, em seguida, selecione **lote do Azure** no menu.

    ![Novo computação-lote do Azure](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Faça as seguintes alterações no script JSON:

   1. Especifique o nome da conta do lote do Azure para a propriedade **accountName** . A **URL** da **folha da conta do lote do Azure** está no seguinte formato: `http://accountname.region.batch.azure.com`. Para a propriedade **batchUri** no JSON, você precisa remover `accountname.` da URL e usar o `accountname` para a propriedade JSON `accountName`.
   2. Especifique a chave de conta do lote do Azure para a propriedade **accessKey** .
   3. Especifique o nome do pool que você criou como parte dos pré-requisitos para a propriedade **PoolName** . Você também pode especificar a ID do pool em vez do nome do pool.
   4. Especifique o URI do lote do Azure para a propriedade **batchUri** . Exemplo: `https://westus.batch.azure.com`.
   5. Especifique o **AzureStorageLinkedService** para a propriedade **linkedServiceName** .

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       Para a propriedade **PoolName** , você também pode especificar a ID do pool em vez do nome do pool.

### <a name="step-3-create-datasets"></a>Etapa 3: criar conjuntos de os
Nesta etapa, você cria conjuntos de dados para representar a entrada e a saída.

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Editor** do data Factory, clique em...  **Mais** na barra de comandos, clique em **novo conjunto**de informações e selecione **armazenamento de BLOBs do Azure** no menu suspenso.
2. Substitua o JSON no painel direito pelo seguinte trecho JSON:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```

   Você cria um pipeline mais adiante neste guia com a hora de início: 2016-11-16T00:00:00Z e hora de término: 2016-11-16T05:00:00Z. Ele está agendado para gerar dados por hora, portanto, há cinco fatias de entrada/saída (entre **00**: 00:00-> **05**: 00:00).

   A **frequência** e o **intervalo** do conjunto de dados de entrada são definidos como **hora** e **1**, o que significa que a fatia de entrada está disponível por hora. Neste exemplo, ele é o mesmo arquivo (File. txt) no intputfolder.

   Aqui estão as horas de início para cada fatia, que é representada pela variável de sistema SliceStart no trecho de JSON acima.
3. Clique em **implantar** na barra de ferramentas para criar e implantar o **InputDataset**. Confirme que vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.

#### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
1. No **Editor de data Factory**, clique em **... Mais** na barra de comandos, clique em **novo conjunto**de informações e selecione **armazenamento de BLOBs do Azure**.
2. Substitua o script JSON no painel direito pelo seguinte script JSON:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     O local de saída é **adftutorial/customactivityoutput/** e o nome do arquivo de saída é yyyy-mm-dd-hh. txt, em que aaaa-mm-dd-HH é o ano, o mês, a data e a hora da fatia que está sendo produzida. Consulte a [referência do desenvolvedor][adf-developer-reference] para obter detalhes.

    Um blob/arquivo de saída é gerado para cada fatia de entrada. Veja como um arquivo de saída é nomeado para cada fatia. Todos os arquivos de saída são gerados em uma pasta de saída: **adftutorial\customactivityoutput**.

   | Slicer | Hora de início | Ficheiro de saída |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00. txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01. txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16 -02. txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16 -03. txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16 -04. txt |

    Lembre-se de que todos os arquivos em uma pasta de entrada fazem parte de uma fatia com os horários de início mencionados acima. Quando essa fatia é processada, a atividade personalizada examina cada arquivo e produz uma linha no arquivo de saída com o número de ocorrências do termo de pesquisa ("Microsoft"). Se houver três arquivos na pasta de entrada, haverá três linhas no arquivo de saída para cada fatia por hora: 2016-11-16-00. txt, 2016-11-16:01:00:00. txt, etc.
3. Para implantar o **OutputDataset**, clique em **implantar** na barra de comandos.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Criar e executar um pipeline que usa a atividade personalizada
1. No editor de Data Factory, clique em **... Mais**e, em seguida, selecione **novo pipeline** na barra de comandos.
2. Substitua o JSON no painel direito pelo seguinte script JSON:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Tenha em atenção os seguintes pontos:

   * A **simultaneidade** é definida como **2** para que duas fatias sejam processadas em paralelo por 2 VMs no pool do lote do Azure.
   * Há uma atividade na seção atividades e é do tipo: **DotNetActivity**.
   * **AssemblyName** é definido como o nome da dll: **MyDotnetActivity. dll**.
   * **EntryPoint** é definido como **MyDotNetActivityNS. MyDotNetActivity**.
   * **PackageLinkedService** é definido como **AzureStorageLinkedService** que aponta para o armazenamento de BLOBs que contém o arquivo zip da atividade personalizada. Se você estiver usando diferentes contas de armazenamento do Azure para arquivos de entrada/saída e o arquivo zip da atividade personalizada, crie outro serviço vinculado do armazenamento do Azure. Este artigo pressupõe que você esteja usando a mesma conta de armazenamento do Azure.
   * O **pacotefile** está definido como **customactivitycontainer/MyDotNetActivity. zip**. Ele está no formato: containerforthezip/nameofthezip. zip.
   * A atividade personalizada usa **InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade linkedServiceName da atividade personalizada aponta para o **AzureBatchLinkedService**, que informa Azure data Factory que a atividade personalizada precisa ser executada nas VMs do lote do Azure.
   * a propriedade **IsPaused** é definida como **false** por padrão. O pipeline é executado imediatamente neste exemplo porque as fatias começam no passado. Você pode definir essa propriedade como true para pausar o pipeline e defini-lo de volta como false para reiniciar.
   * A hora de **início** e as horas de **término** são separadas por **cinco** horas e as fatias são produzidas por hora, portanto, cinco fatias são produzidas pelo pipeline.
3. Para implantar o pipeline, clique em **implantar** na barra de comandos.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. Na folha Data Factory na portal do Azure, clique em **diagrama**.

    ![Mosaico do diagrama](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. No modo de exibição de diagrama, clique agora em OutputDataset.

    ![Vista de diagrama](./media/data-factory-use-custom-activities/diagram.png)
3. Você deve ver que as cinco fatias de saída estão no estado pronto. Se eles não estiverem no estado pronto, ainda não foram produzidos.

   ![Fatias de saída](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Verifique se os arquivos de saída são gerados no armazenamento de BLOBs no contêiner **adftutorial** .

   ![saída da atividade personalizada][image-data-factory-output-from-custom-activity]
5. Se você abrir o arquivo de saída, deverá ver a saída semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Use os cmdlets [portal do Azure][azure-preview-portal] ou Azure PowerShell para monitorar seus data Factory, pipelines e conjuntos de dados. Você pode ver as mensagens do **ActivityLogger** no código para a atividade personalizada nos logs (especificamente, User-0. log) que você pode baixar no portal ou usando cmdlets.

   ![baixar logs de atividade personalizada][image-data-factory-download-logs-from-custom-activity]

Consulte [monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md) para obter etapas detalhadas para o monitoramento de conjuntos de informações e pipelines.

## <a name="data-factory-project-in-visual-studio"></a>Data Factory projeto no Visual Studio
Você pode criar e publicar Data Factory entidades usando o Visual Studio em vez de usar portal do Azure. Para obter informações detalhadas sobre como criar e publicar Data Factory entidades usando o Visual Studio, consulte [criar seu primeiro pipeline usando o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) e [copiar dados do blob do Azure para artigos sobre SQL do Azure](data-factory-copy-activity-tutorial-using-visual-studio.md) .

Execute as seguintes etapas adicionais se você estiver criando Data Factory projeto no Visual Studio:

1. Adicione o projeto Data Factory à solução do Visual Studio que contém o projeto de atividade personalizada.
2. Adicione uma referência ao projeto de atividade do .NET do projeto Data Factory. Clique com o botão direito do mouse em Data Factory projeto, aponte para **Adicionar**e clique em **referência**.
3. Na caixa de diálogo **Adicionar referência** , selecione o projeto **MyDotNetActivity** e clique em **OK**.
4. Crie e publique a solução.

    > [!IMPORTANT]
    > Quando você publica Data Factory entidades, um arquivo zip é criado automaticamente para você e é carregado para o contêiner de blob: customactivitycontainer. Se o contêiner de BLOB não existir, ele também será criado automaticamente.

## <a name="data-factory-and-batch-integration"></a>Integração de Data Factory e lote
O serviço Data Factory cria um trabalho no lote do Azure com o nome: **ADF-PoolName: trabalho-xxx**. Clique em **trabalhos** no menu à esquerda.

![Trabalhos de Azure Data Factory em lotes](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Uma tarefa é criada para cada execução de atividade de uma fatia. Se houver cinco fatias prontas para serem processadas, cinco tarefas serão criadas nesse trabalho. Se houver vários nós de computação no pool do lote, duas ou mais fatias podem ser executadas em paralelo. Se o máximo de tarefas por nó de computação for definido como > 1, você também poderá ter mais de uma fatia em execução na mesma computação.

![Azure Data Factory-tarefas de trabalho em lotes](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

O diagrama a seguir ilustra a relação entre Azure Data Factory e as tarefas do lote.

![Data Factory & lote](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Solucionar problemas de falhas
A solução de problemas consiste em algumas técnicas básicas:

1. Se você vir o erro a seguir, talvez esteja usando um armazenamento de BLOBs quente/frio em vez de usar um armazenamento de BLOBs do Azure de uso geral. Carregue o arquivo zip em uma **conta de armazenamento do Azure de uso geral**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Se você vir o erro a seguir, confirme se o nome da classe no arquivo CS corresponde ao nome especificado para a propriedade **EntryPoint** no JSON do pipeline. No passo a passo, o nome da classe é: MyDotNetActivity, e o EntryPoint no JSON é: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Se os nomes forem correspondentes, confirme se todos os binários estão na **pasta raiz** do arquivo zip. Ou seja, ao abrir o arquivo zip, você verá todos os arquivos na pasta raiz, e não em nenhuma subpasta.
3. Se a fatia de entrada não estiver definida como **pronto**, confirme se a estrutura da pasta de entrada está correta e se **File. txt** existe nas pastas de entrada.
3. No método **Execute** de sua atividade personalizada, use o objeto **IActivityLogger** para registrar informações que ajudam a solucionar problemas. As mensagens registradas aparecem nos arquivos de log do usuário (um ou mais arquivos chamados: user-0. log, user-1. log, User-2. log, etc.).

   Na folha **OutputDataset** , clique na fatia para ver a folha **fatia de dados** dessa fatia. Você vê as **execuções de atividade** para essa fatia. Você deve ver uma execução de atividade para a fatia. Se você clicar em executar na barra de comandos, poderá iniciar outra execução de atividade para a mesma fatia.

   Ao clicar na execução da atividade, você verá a folha **detalhes da execução da atividade** com uma lista de arquivos de log. Você vê mensagens registradas no arquivo user_0. log. Quando ocorrer um erro, você verá três execuções de atividade porque a contagem de repetição é definida como 3 no JSON de atividade/pipeline. Ao clicar na execução da atividade, você verá os arquivos de log que podem ser examinados para solucionar o erro.

   Na lista de arquivos de log, clique no **User-0. log**. No painel direito estão os resultados do uso do método **IActivityLogger. Write** . Se você não vir todas as mensagens, verifique se você tem mais arquivos de log chamados: user_1. log, user_2. log etc. Caso contrário, o código pode ter falhado após a última mensagem registrada.

   Além disso, verifique **System-0. log** para quaisquer mensagens de erro e exceções do sistema.
4. Inclua o arquivo **PDB** no arquivo zip para que os detalhes do erro tenham informações como **pilha de chamadas** quando ocorrer um erro.
5. Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.
6. Certifique-se de que **AssemblyName** (MyDotNetActivity. dll), **EntryPoint**(MyDotNetActivityNS. MyDotNetActivity), **PackageFile** (customactivitycontainer/MyDotNetActivity. zip) e **packageLinkedService** (deve apontar para para o armazenamento de BLOBs do Azure de **uso geral**que contém o arquivo zip), defina os valores corretos.
7. Se corrigir um erro e pretender processar de novo o setor, clique com o botão direito do rato no setor no painel **OutputDataset** e clique em **Executar**.
8. Se você vir o erro a seguir, você está usando o pacote de armazenamento do Azure da versão > 4.3.0. Data Factory iniciador de serviço requer a versão 4,3 do WindowsAzure. Storage. Consulte a seção [isolamento de AppDomain](#appdomain-isolation) para uma solução alternativa se você precisar usar a versão mais recente do assembly de armazenamento do Azure.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Se você puder usar a versão do 4.3.0 do pacote de armazenamento do Azure, remova a referência existente para o pacote de armazenamento do Azure da versão > 4.3.0. Em seguida, execute o comando a seguir no console do Gerenciador de pacotes NuGet.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Crie o projeto. Exclua o assembly Azure. Storage da versão > 4.3.0 da pasta bin\Debug. Crie um arquivo zip com binários e o arquivo PDB. Substitua o arquivo zip antigo por este no contêiner de BLOB (customactivitycontainer). Execute novamente as fatias que falharam (clique com o botão direito do mouse em fatia e clique em executar).
8. A atividade personalizada não usa o arquivo **app. config** do seu pacote. Portanto, se o código ler as cadeias de conexão do arquivo de configuração, ele não funcionará em tempo de execução. A prática recomendada ao usar o lote do Azure é armazenar segredos em um **cofre de chaves do Azure**, usar uma entidade de serviço baseada em certificado para proteger o **keyvault**e distribuir o certificado para o pool do lote do Azure. A atividade personalizada de .NET pode, então, aceder aos segredos do Cofre de Chaves durante o tempo de execução. Essa solução é uma solução genérica e pode ser dimensionada para qualquer tipo de segredo, não apenas para a cadeia de conexão.

   Há uma solução alternativa mais fácil (mas não é uma prática recomendada): você pode criar um **serviço vinculado do SQL do Azure** com as configurações de cadeia de conexão, criar um conjunto de dados que usa o serviço vinculado e encadear o conjunto de dados como um DataSet de entrada fictício para a atividade personalizada do .net. Em seguida, você pode acessar a cadeia de conexão do serviço vinculado no código de atividade personalizado.

## <a name="update-custom-activity"></a>Atualizar atividade personalizada
Se você atualizar o código para a atividade personalizada, compile-o e carregue o arquivo zip que contém novos binários para o armazenamento de BLOBs.

## <a name="appdomain-isolation"></a>Isolamento de AppDomain
Consulte [exemplo de AppDomain cruzado](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) que mostra como criar uma atividade personalizada que não está restrita às versões de assembly usadas pelo iniciador de data Factory (exemplo: WindowsAzure. Storage v 4.3.0, Newtonsoft. JSON v 6.0. x, etc.).

## <a name="access-extended-properties"></a>Acessar propriedades estendidas
Você pode declarar propriedades estendidas na atividade JSON, conforme mostrado no exemplo a seguir:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

No exemplo, há duas propriedades estendidas: **SliceStart** e **datafactoryname**. O valor de SliceStart é baseado na variável de sistema SliceStart. Consulte [variáveis do sistema](data-factory-functions-variables.md) para obter uma lista de variáveis de sistema com suporte. O valor de datafactoryname é embutido em código como CustomActivityFactory.

Para acessar essas propriedades estendidas no método **Execute** , use um código semelhante ao código a seguir:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Dimensionamento automático do lote do Azure
Você também pode criar um pool do lote do Azure com o recurso de **dimensionamento automático** . Por exemplo, você pode criar um pool do lote do Azure com 0 VMs dedicadas e uma fórmula de dimensionamento automático com base no número de tarefas pendentes.

A fórmula de exemplo obtém o seguinte comportamento: quando o pool é inicialmente criado, ele começa com 1 VM. $PendingTasks métrica define o número de tarefas no estado executando + ativo (na fila).  A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated de acordo. Ele garante que o TargetDedicated nunca vá além de 25 VMs. Assim, à medida que novas tarefas são enviadas, o pool aumenta automaticamente e, à medida que as tarefas são concluídas, as VMs tornam-se gratuitas uma a uma e o dimensionamento automático reduz essas VMs. startingNumberOfVMs e maxNumberofVMs podem ser ajustados para suas necessidades.

Fórmula de autoescala:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Consulte [dimensionar automaticamente nós de computação em um pool do lote do Azure](../../batch/batch-automatic-scaling.md) para obter detalhes.

Se o pool estiver usando o [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)padrão, o serviço de lote poderá levar de 15-30 minutos para preparar a VM antes de executar a atividade personalizada.  Se o pool estiver usando um autoScaleEvaluationInterval diferente, o serviço de lote poderá levar autoScaleEvaluationInterval + 10 minutos.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Criar uma atividade personalizada usando o SDK do .NET
Neste artigo, você cria uma data factory com um pipeline que usa a atividade personalizada usando o portal do Azure. O código a seguir mostra como criar o data factory usando o SDK do .NET em vez disso. Você pode encontrar mais detalhes sobre como usar o SDK para criar pipelines programaticamente no artigo [criar um pipeline com a atividade de cópia usando a API .net](data-factory-copy-activity-tutorial-using-dotnet-api.md) .

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Depurar atividade personalizada no Visual Studio
O exemplo de [ambiente Azure data Factory local](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) no GitHub inclui uma ferramenta que permite que você depure atividades personalizadas do .net no Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Atividades personalizadas de exemplo no GitHub
| Exemplo | Qual atividade personalizada faz |
| --- | --- |
| [Downloader de dados http](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Baixa dados de um ponto de extremidade HTTP para o armazenamento de C# BLOBs do Azure usando atividade personalizada no data Factory. |
| [Exemplo de Análise de Sentimento do Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Invoca um modelo do Azure Machine Learning Studio e análise de sentimentos, pontuação, previsão, etc. |
| [Execute o script do R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Invoca o script R executando RScript. exe em seu cluster HDInsight que já tem o R instalado nele. |
| [Atividade do .NET de AppDomain cruzada](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Usa versões de assembly diferentes daquelas usadas pelo iniciador de Data Factory |
| [Reprocessar um modelo no Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Reprocessa um modelo no Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
