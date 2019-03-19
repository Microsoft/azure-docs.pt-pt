---
title: Utilizar atividades personalizadas num pipeline do Azure Data Factory
description: Saiba como criar atividades personalizadas e utilizá-los num pipeline do Azure Data Factory.
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
ms.openlocfilehash: 1341a8fce95dff0499a698fcddf8d048f3628dba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108244"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilizar atividades personalizadas num pipeline do Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-use-custom-activities.md)
> * [Versão 2 (versão atual)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [atividades personalizadas no V2](../transform-data-using-dotnet-custom-activity.md).

Existem dois tipos de atividades que podem ser usados num pipeline do Azure Data Factory.

- [Atividades de movimento de dados](data-factory-data-movement-activities.md) para mover dados entre [arquivos de dados de origem e sink suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para transformar dados através de serviços de computação como o Azure HDInsight, Azure Batch e do Azure Machine Learning.

Para mover dados de/para o arquivo de dados que não suporta a fábrica de dados, crie uma **atividade personalizada** com sua própria lógica de movimento de dados e utilize a atividade num pipeline. Da mesma forma, para transformar/processar dados de uma forma que não é suportada pela fábrica de dados, criar uma atividade personalizada com a sua própria lógica de transformação de dados e utilize a atividade num pipeline.

Pode configurar uma atividade personalizada para executar numa **do Azure Batch** conjunto de máquinas virtuais. Ao utilizar o Azure Batch, pode utilizar apenas um conjunto existente do Azure Batch.

A instrução a seguir fornece instruções passo a passo para criar uma atividade .NET personalizada e utilizar a atividade personalizada num pipeline. Passo a passo usa um **do Azure Batch** serviço ligado.

> [!IMPORTANT]
> - Não é possível utilizar um Gateway de gestão de dados de uma atividade personalizada para aceder a origens de dados no local. Atualmente, [Data Management Gateway](data-factory-data-management-gateway.md) suporta apenas a atividade de cópia e a atividade de procedimento armazenado no Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Instruções: criar uma atividade personalizada
### <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2012/2013/2015
* Transferir e instalar o [SDK .NET do Azure](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Pré-requisitos do Azure Batch
Passo a passo, irá executar as suas atividades .NET personalizadas com o Azure Batch como um recurso de computação. **O Azure Batch** é uma plataforma de serviço para a execução de paralelas em grande escala e de alto desempenho aplicações de computação (HPC) com eficiência na cloud. O Azure Batch agenda trabalho de computação intensiva para executar numa gerida **coleção de máquinas virtuais**, e pode automaticamente Dimensionar recursos de computação para satisfazer as necessidades das suas tarefas. Ver [Noções básicas do Azure Batch] [ batch-technical-overview] artigo para uma visão geral detalhada do serviço Azure Batch.

Para este tutorial, crie uma conta do Azure Batch com um conjunto de VMs. Eis os passos:

1. Criar uma **conta do Azure Batch** utilizando o [portal do Azure](https://portal.azure.com). Ver [criar e gerir uma conta do Azure Batch] [ batch-create-account] artigo para obter instruções.
2. Aponte o nome da conta do Azure Batch, a chave da conta, URI e nome do conjunto. Precisa para criar um serviço ligado do Azure Batch.
    1. Na home page para a conta do Azure Batch, verá uma **URL** no seguinte formato: `https://myaccount.westus.batch.azure.com`. Neste exemplo, **myaccount** é o nome da conta do Azure Batch. URI que utilizar na definição do serviço ligado é o URL sem o nome da conta. Por exemplo: `https://<region>.batch.azure.com`.
    2. Clique em **chaves** no menu à esquerda e copie a **chave de acesso primária**.
    3. Para utilizar um conjunto existente, clique em **agrupamentos** no menu e tome nota do **ID** do conjunto. Se não tiver um conjunto existente, mova para o passo seguinte.
2. Criar uma **conjunto do Azure Batch**.

   1. Na [portal do Azure](https://portal.azure.com), clique em **procurar** no menu à esquerda e clique em **contas do Batch**.
   2. Selecione a sua conta do Azure Batch para abrir o **conta do Batch** painel.
   3. Clique em **conjuntos** mosaico.
   4. Na **conjuntos** painel, clique no botão Add na barra de ferramentas para adicionar um conjunto.
      1. Introduza um ID para o conjunto (ID do conjunto). Tenha em atenção a **ID do conjunto**; irá precisar dele quando criar a solução de fábrica de dados.
      2. Especifique **Windows Server 2012 R2** para a definição de família do sistema operativo.
      3. Selecione um **escalão de preço de nó**.
      4. Introduza **2** como o valor para o **dedicados de destino** definição.
      5. Introduza **2** como o valor para o **máx. tarefas por nó** definição.
   5. Clique em **OK** para criar o conjunto.
   6. Tome nota da **ID** do conjunto.

### <a name="high-level-steps"></a>Passos de alto nível
Seguem-se os dois passos de alto nível que deve executar como parte destas instruções:

1. Crie uma atividade personalizada que contém a lógica de transformação/processamento de dados simples.
2. Crie uma fábrica de dados do Azure com um pipeline que utiliza a atividade personalizada.

### <a name="create-a-custom-activity"></a>Criar uma atividade personalizada
Para criar uma atividade personalizada .NET, crie uma **biblioteca de classes .NET** projeto com uma classe que implementa que **IDotNetActivity** interface. Essa interface possui apenas um método: [Executar](https://msdn.microsoft.com/library/azure/mt603945.aspx) e sua assinatura é:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

O método assume quatro parâmetros:

- **linkedServices**. Esta propriedade é uma lista enumerável de serviços de Store de dados ligado referenciado por conjuntos de dados de entrada/saída para a atividade.
- **conjuntos de dados**. Esta propriedade é uma lista enumerável de conjuntos de dados de entrada/saída para a atividade. Pode utilizar este parâmetro para obter as localizações e os esquemas definidos por conjuntos de dados de entrada e saídos.
- **atividade**. Esta propriedade representa a atividade atual. Ele pode ser utilizado para aceder às propriedades expandidas associadas com a atividade personalizada. Ver [acesso a Propriedades estendidas](#access-extended-properties) para obter detalhes.
- **logger**. Este objeto permite-lhe escrever essa superfície de comentários de depuração no registo de utilizador para o pipeline.

O método retorna um dicionário que pode ser utilizado para encadear atividades personalizadas em conjunto no futuro. Esta funcionalidade ainda não está implementada, então, retornar um dicionário vazio do método.

### <a name="procedure"></a>Procedimento
1. Criar uma **biblioteca de classes .NET** projeto.
   <ol type="a">
     <li>Inicie <b>Visual Studio 2017</b> ou <b>Visual Studio 2015</b> ou <b>Visual Studio 2013</b> ou <b>o Visual Studio 2012</b>.</li>
     <li>Clique em <b>Ficheiro</b>, aponte para <b>Novo</b> e, em seguida, clique em <b>Projeto</b>.</li>
     <li>Expanda <b>Modelos</b> e selecione <b>Visual C#</b>. Nestas instruções, utiliza c#, mas pode usar qualquer linguagem .NET para desenvolver a atividade personalizada.</li>
     <li>Selecione <b>biblioteca de classes</b> na lista de tipos de projeto à direita. No VS 2017, escolha <b>biblioteca de classes (.NET Framework)</b> </li>
     <li>Introduza <b>MyDotNetActivity</b> para o <b>nome</b>.</li>
     <li>Selecione <b>C:\ADFGetStarted</b> para o <b>localização</b>.</li>
     <li>Clique em <b>OK</b> para criar o projeto.</li>
   </ol>

2. Clique em **Ferramentas**, aponte para **Gestor de Pacotes NuGet** e clique em **Consola do Gestor de Pacotes**.

3. Na consola do Gestor de pacotes, execute o seguinte comando para importar **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importar os **armazenamento do Azure** pacote NuGet ao projeto.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Iniciador de serviço de fábrica de dados requer a versão 4.3 do windowsazure. Storage. Se adicionar uma referência para uma versão posterior do assembly de armazenamento do Azure no seu projeto de atividade personalizada, verá um erro quando a atividade é executada. Para resolver o erro, consulte [isolamento de Appdomain](#appdomain-isolation) secção.
5. Adicione as seguintes **usando** instruções para o ficheiro de origem no projeto.

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
6. Alterar o nome do **espaço de nomes** ao **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Altere o nome da classe para **MyDotNetActivity** e derivá-lo a partir do **IDotNetActivity** interface conforme mostrado no seguinte fragmento de código:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementar (adicionar) a **Execute** método o **IDotNetActivity** de interface para o **MyDotNetActivity** de classe e copie o seguinte código de exemplo para o método.

    O exemplo a seguir conta o número de ocorrências do termo de pesquisa ("Microsoft") em cada blob associado a um setor de dados.

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
9. Adicione os seguintes métodos de programa auxiliar:

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

    O método GetFolderPath retorna o caminho para a pasta que o conjunto de dados aponta para e o método GetFileName devolve o nome do que o conjunto de dados aponta para ficheiro/blob. Se tiver folderPath define a utilização de variáveis, como {Year}, {Month}, devolve {Day} etc., o método a cadeia de caracteres como ele é sem substituí-los com os valores de tempo de execução. Ver [acesso a Propriedades estendidas](#access-extended-properties) secção para obter detalhes sobre como acessar SliceStart, SliceEnd, etc.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    O método de Calculate calcula o número de instâncias de palavra-chave Microsoft nos ficheiros de entrada (blobs na pasta). O termo de pesquisa ("Microsoft") está embutido no código.
10. Compile o projeto. Clique em **crie** no menu e clique em **compilar solução**.

    > [!IMPORTANT]
    > Versão do conjunto 4.5.2 do .NET Framework, como o framework de destino para o seu projeto: com o botão direito no projeto e clique em **propriedades** para definir a estrutura de destino. Fábrica de dados não suporta a atividades personalizadas compiladas em relação a versões do .NET Framework superior à versão 4.5.2.

11. Inicie **Windows Explorer**e navegue até à **bin\debug** ou **bin\release.** pasta dependendo do tipo de compilação.
12. Criar um ficheiro zip **MyDotNetActivity.zip** que contém todos os binários no \<pasta do projeto\>pasta \bin\Debug. Incluir o **MyDotNetActivity.pdb** para que obtenham detalhes adicionais, como o número de linha no código-fonte que causou o problema se Ocorreu uma falha de ficheiros.

    > [!IMPORTANT]
    > Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.

    ![Ficheiros de saída binário](./media/data-factory-use-custom-activities/Binaries.png)
14. Criar um contentor de BLOBs denominado **customactivitycontainer** caso ainda não exista.
15. Carregar MyDotNetActivity.zip como um blob para o customactivitycontainer num **para fins gerais** armazenamento de Blobs do Azure (armazenamento de BLOBs não acesso frequente/esporádico) que está referido por AzureStorageLinkedService.

> [!IMPORTANT]
> Se adicionar este projeto de atividade do .NET para uma solução no Visual Studio que contém um projeto do Data Factory e adicionar uma referência ao projeto de atividade de .NET do projeto de aplicação de fábrica de dados, não é necessário executar os dois últimos passos de criação manual de zip o ficheiro e carregá-lo para o armazenamento de Blobs do Azure para fins gerais. Ao publicar entidades do Data Factory com o Visual Studio, essas etapas são feitas automaticamente pelo processo de publicação. Para obter mais informações, consulte [projeto do Data Factory no Visual Studio](#data-factory-project-in-visual-studio) secção.

## <a name="create-a-pipeline-with-custom-activity"></a>Criar um pipeline com atividade personalizada
Criou uma atividade personalizada e carregado o ficheiro zip com binários para um contentor de BLOBs numa **para fins gerais** conta de armazenamento do Azure. Nesta secção, vai criar uma fábrica de dados do Azure com um pipeline que utiliza a atividade personalizada.

O conjunto de dados de entrada para a atividade personalizada representa blobs (ficheiros) na pasta customactivityinput do contentor adftutorial no armazenamento de Blobs. O conjunto de dados de saída para a atividade representa os blobs de saída na pasta customactivityoutput do contentor adftutorial no armazenamento de Blobs.

Crie **file.txt** com o seguinte conteúdo de ficheiros e carregue-o para **customactivityinput** pasta do **adftutorial** contentor. Crie o contentor adftutorial, caso ainda não exista.

```
test custom activity Microsoft test custom activity Microsoft
```

Pasta de entrada corresponde a um setor no Azure Data Factory, mesmo que a pasta tem dois ou mais arquivos. Quando cada setor é processado pelo pipeline, a atividade personalizada itera em todos os blobs na pasta de entrada para essas fatias.

Verá um ficheiro com o na pasta adftutorial\customactivityoutput de saída com um ou mais linhas (mesmo que o número de blobs da pasta de entrada):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Eis os passos que efetuar nesta secção:

1. Criar uma **fábrica de dados**.
2. Crie **serviços ligados** para o conjunto do Azure Batch de VMs em que a atividade personalizada é executado e o armazenamento do Azure que contém os blobs de entrada/saída.
3. Criar a entrada e saída **conjuntos de dados** que representam a entrada e saída da atividade personalizada.
4. Criar uma **pipeline** que utiliza a atividade personalizada.

> [!NOTE]
> Criar a **file.txt** e carregá-lo para um contentor de BLOBs, se ainda não o tiver feito. Veja as instruções na secção anterior.

### <a name="step-1-create-the-data-factory"></a>Passo 1: Criar a fábrica de dados
1. Depois de iniciar sessão portal do Azure, siga os passos abaixo:
   1. Clique em **criar um recurso** no menu da esquerda.
   2. Clique em **dados + análise** no **New** painel.
   3. Clique em **Data Factory** no painel **Análise de dados**.

      ![Menu novo do Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Na **nova fábrica de dados** painel, introduza **CustomActivityFactory** para o nome. O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: **Nome da fábrica de dados "CustomActivityFactory" não está disponível**, altere o nome da fábrica de dados (por exemplo, **yournameCustomActivityFactory**) e tente criar novamente.

    ![Novo painel do Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Clique em **nome do grupo de recursos**e selecione um grupo de recursos existente ou crie um grupo de recursos.
4. Certifique-se de que está a utilizar o correto **subscrição** e **região** onde pretende que a fábrica de dados a ser criada.
5. Clique em **Criar** no painel **Nova fábrica de dados**.
6. Verá a fábrica de dados que está a ser criada no **Dashboard** do portal do Azure.
7. Depois da fábrica de dados tiver sido criada com êxito, verá o painel do Data Factory, que mostra o conteúdo da fábrica de dados.

    ![Painel Data Factory](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Passo 2: Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Neste passo, vai ligar a sua conta de armazenamento do Azure e a conta do Batch do Azure à fábrica de dados.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
1. Clique no **autor e implementar** mosaico a **DATA FACTORY** painel **CustomActivityFactory**. Será apresentado o Editor do Data Factory.
2. Clique em **novo arquivo de dados** sobre o comando da barra e escolha **o armazenamento do Azure**. Deverá ver o script JSON para criar um serviço ligado do Storage do Azure no editor.

    ![Novo arquivo de dados - armazenamento do Azure](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Substitua `<accountname>` com o nome da sua conta de armazenamento do Azure e `<accountkey>` pela chave de acesso da conta do storage do Azure. Para saber como obter a chave de acesso ao armazenamento, veja [Ver, copiar e regenerar as chaves de acesso ao armazenamento](../../storage/common/storage-account-manage.md#access-keys).

    ![Serviço de gostos de armazenamento do Azure](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="create-azure-batch-linked-service"></a>Criar serviço ligado do Azure Batch
1. No Editor do Data Factory, clique em **... Obter mais** na barra de comandos, clique em **nova computação**e, em seguida, selecione **do Azure Batch** no menu.

    ![Nova computação - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Efetue as seguintes alterações para o script JSON:

   1. Especifique o nome de conta do Azure Batch para o **accountName** propriedade. O **URL** partir a **painel conta do Azure Batch** está no seguinte formato: `http://accountname.region.batch.azure.com`. Para o **batchUri** propriedade no JSON, terá de remover `accountname.` da URL e utilize o `accountname` para o `accountName` propriedade JSON.
   2. Especifique a chave de conta do Azure Batch para o **accessKey** propriedade.
   3. Especifique o nome do conjunto que criou como parte da pré-requisitos para o **poolName** propriedade. Também pode especificar o ID do conjunto em vez do nome do conjunto.
   4. Especifique o URI do Azure Batch para o **batchUri** propriedade. Exemplo: `https://westus.batch.azure.com`.
   5. Especifique a **AzureStorageLinkedService** para o **linkedServiceName** propriedade.

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

       Para o **poolName** propriedade, também pode especificar o ID do conjunto em vez do nome do conjunto.

### <a name="step-3-create-datasets"></a>Passo 3: Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de entrada e saídos.

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Editor** do Data Factory, clique em **... Obter mais** na barra de comandos, clique em **novo conjunto de dados**e, em seguida, selecione **armazenamento de Blobs do Azure** no menu pendente.
2. Substitua o JSON no painel direito pelo seguinte fragmento JSON:

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

   Irá criar um pipeline mais tarde nestas instruções com a hora de início: 2016-11-hora 16T00:00:00Z e de fim: 2016-11-16T05:00:00Z. Está agendado para produzir os dados por hora, pelo que existem cinco setores de entrada/saída (entre **00**: 00:00 -> **05**: 00:00).

   O **frequência** e **intervalo** o conjunto de dados de entrada está definida como **hora** e **1**, que significa que o setor de entrada está disponível uma vez por hora. Neste exemplo, é o mesmo arquivo (file.txt) o intputfolder.

   Aqui estão as horas de início de cada fatia, que é representado pela variável de sistema do SliceStart no fragmento JSON acima.
3. Clique em **Deploy** na barra de ferramentas para criar e implementar a **InputDataset**. Confirme que vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.

#### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
1. Na **editor do Data Factory**, clique em **... Obter mais** na barra de comandos, clique em **novo conjunto de dados**e, em seguida, selecione **armazenamento de Blobs do Azure**.
2. Substitua o script JSON no painel da direita com o seguinte script JSON:

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

     Localização de saída é **adftutorial/customactivityoutput/** e nome de ficheiro de saída é aaaa-MM-dd-HH.txt onde AAAA-MM-dd-HH é o ano, mês, data e hora do setor a ser produzido. Ver [referência para programadores] [ adf-developer-reference] para obter detalhes.

    Um ficheiro/blob de saída é gerado para cada setor de entrada. Eis como um ficheiro de saída com o nome de cada setor. Todos os ficheiros de saída são gerados numa pasta de saída: **adftutorial\customactivityoutput**.

   | Setor | Hora de início | Ficheiro de saída |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Lembre-se de que todos os arquivos de uma pasta de entrada fazem parte de um setor com as horas de início mencionadas acima. Quando esse setor é processado, a atividade personalizada examina cada arquivo e produz uma linha no arquivo de saída com o número de ocorrências do termo de pesquisa ("Microsoft"). Se existirem três arquivos no inputfolder, existem três linhas no arquivo de saída para cada setor por hora: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt, etc.
3. Para implementar o **OutputDataset**, clique em **implementar** na barra de comandos.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Criar e executar um pipeline que utiliza a atividade personalizada
1. No Editor do Data Factory, clique em **... Obter mais**e, em seguida, selecione **novo pipeline** na barra de comandos.
2. Substitua o JSON no painel da direita com o seguinte script JSON:

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

   * **Simultaneidade** está definido como **2** para que dois setores são processados em paralelo por 2 VMs num conjunto do Batch do Azure.
   * Existe uma atividade na secção Atividades, e é do tipo: **DotNetActivity**.
   * **AssemblyName** está definido como o nome da DLL: **MyDotnetActivity.dll**.
   * **Ponto de entrada** está definido como **mydotnetactivityns. Mydotnetactivity**.
   * **PackageLinkedService** está definido como **AzureStorageLinkedService** que aponta para o armazenamento de BLOBs que contém o ficheiro de zip da atividade personalizada. Se estiver a utilizar diferentes contas de armazenamento do Azure para ficheiros de entrada/saída e o ficheiro de zip da atividade personalizada, criar outro serviço ligado do armazenamento do Azure. Este artigo pressupõe que está a utilizar a mesma conta de armazenamento do Azure.
   * **PackageFile** está definido como **customactivitycontainer**. Ele está no formato: containerforthezip/nameofthezip.zip.
   * A atividade personalizada obtém **InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade linkedServiceName da atividade personalizada aponta para o **AzureBatchLinkedService**, que informa o Azure Data Factory, que a atividade personalizada precisa ser executado em VMs do Azure Batch.
   * **isPaused** estiver definida como **false** por predefinição. O pipeline executa imediatamente neste exemplo, uma vez que os setores de início no passado. Pode definir esta propriedade como true para colocar em pausa o pipeline e defina-o novamente para false para reiniciar.
   * O **começar** tempo e **final** tempos são **cinco** horas afastadas e setores são produzidos por hora, para que os setores de cinco são produzidos pelo pipeline.
3. Para implementar o pipeline, clique em **Deploy** na barra de comandos.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. No painel do Data Factory no portal do Azure, clique em **diagrama**.

    ![Mosaico do diagrama](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Na vista de diagrama, clique agora OutputDataset.

    ![Vista de diagrama](./media/data-factory-use-custom-activities/diagram.png)
3. Deverá ver que os setores de cinco saída estão no estado pronto. Se não estiverem no estado pronto, ainda não foram produzidos ainda.

   ![Setores de saída](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Certifique-se de que os ficheiros de saída são gerados no armazenamento de BLOBs no **adftutorial** contentor.

   ![saída da atividade personalizada][image-data-factory-output-from-custom-activity]
5. Se abrir o ficheiro de saída, deverá ver uma saída semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Utilize o [portal do Azure] [ azure-preview-portal] ou cmdlets do Azure PowerShell para monitorizar a sua fábrica de dados, pipelines e conjuntos de dados. Pode ver as mensagens a partir da **ActivityLogger** no código para a atividade personalizada nos registos (especificamente user-0.log) que pode transferir a partir do portal ou através dos cmdlets.

   ![transferir registos de atividade personalizada][image-data-factory-download-logs-from-custom-activity]

Ver [monitorizar e gerir Pipelines](data-factory-monitor-manage-pipelines.md) para obter passos detalhados para a monitorização de pipelines e conjuntos de dados.

## <a name="data-factory-project-in-visual-studio"></a>Projeto de fábrica de dados no Visual Studio
Pode criar e publicar entidades do Data Factory com o Visual Studio em vez de utilizar o portal do Azure. Para obter informações detalhadas sobre como criar e publicar entidades do Data Factory com o Visual Studio, veja [criar seu primeiro pipeline com o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) e [copiar dados de Blobs do Azure para o Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) artigos.

Se estiver a criar o projeto do Data Factory no Visual Studio, efetue os seguintes passos adicionais:

1. Adicione o projeto do Data Factory para a solução do Visual Studio que contém o projeto de atividade personalizada.
2. Adicione uma referência ao projeto de atividade .NET a partir do projeto do Data Factory. Projeto do Data Factory com o botão direito, aponte para **Add**e, em seguida, clique em **referência**.
3. Na **Adicionar referência** caixa de diálogo, selecione a **MyDotNetActivity** do projeto e clique em **OK**.
4. Criar e publicar a solução.

    > [!IMPORTANT]
    > Ao publicar entidades do Data Factory, um arquivo zip é criado automaticamente para e que é carregado para o contentor de BLOBs: customactivitycontainer. Se o contentor de BLOBs não existir, será automaticamente criada demasiado.

## <a name="data-factory-and-batch-integration"></a>Integração do Data Factory e o Batch
O serviço Data Factory cria um trabalho no Azure Batch com o nome: **adf poolname: tarefa-xxx**. Clique em **tarefas** no menu à esquerda.

![O Azure Data Factory - tarefas de lote](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Uma tarefa é criada para cada execução de atividade de um setor. Se existirem cinco setores prontos a ser processado, cinco tarefas são criadas nesta tarefa. Se existirem vários nós de computação no conjunto do Batch, podem executar duas ou mais setores em paralelo. Se as tarefas máximas por nó de computação é definido como > 1, também pode ter mais do que um setor em execução na mesma computação.

![O Azure Data Factory - tarefas de trabalho do Batch](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

O diagrama seguinte ilustra a relação entre as tarefas de lote e o Azure Data Factory.

![Fábrica de dados e de lote](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Resolver problemas de falhas
Resolução de problemas consiste em algumas técnicas básicas:

1. Se vir o seguinte erro, pode estar a utilizar um armazenamento de BLOBs de acesso frequente/esporádico em vez de usar um armazenamento de Blobs do Azure para fins gerais. Carregue o ficheiro zip para um **conta de armazenamento do Azure para fins gerais**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Se vir o seguinte erro, certifique-se que o nome da classe no arquivo CS corresponde ao nome que especificou para o **EntryPoint** propriedade no JSON do pipeline. No passo a passo, o nome da classe é: MyDotNetActivity e o ponto de entrada no JSON é: MyDotNetActivityNS.**MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Se os nomes forem iguais, confirme que todos os binários no **pasta raiz** do ficheiro zip. Ou seja, quando abrir o ficheiro zip, deverá ver todos os ficheiros na pasta raiz e não na quaisquer subpastas.
3. Se o setor de entrada não estiver definido como **pronto**, confirme que a estrutura de pastas de entrada está correta e **file.txt** existe nas pastas de entrada.
3. Na **Execute** método de sua atividade personalizada, utilize o **IActivityLogger** objeto para registar informações que o ajuda a resolver problemas. As mensagens anteriormente registadas aparecem nos ficheiros de registo de utilizador (um ou mais ficheiros com o nome: user-0.log, user-1.log, user-2.log, etc.).

   Na **OutputDataset** painel, clique no setor para ver a **SETOR de dados** painel para essas fatias. Verá **execuções de atividades** para essas fatias. Verá uma execução para o setor de atividade. Se clicar em executar na barra de comandos, pode começar a executar o setor mesmo de outra atividade.

   Quando clica a execução da atividade, consulte a **detalhes da execução da ATIVIDADE** painel com uma lista de ficheiros de registo. Consulte as mensagens anteriormente registadas no ficheiro user_0.log. Quando ocorre um erro, verá três execuções de atividades porque a contagem de repetições está definida como 3 no pipeline/JSON de atividade. Quando clica a execução de atividade, verá os ficheiros de registo que poderá consultar para resolver o erro.

   Na lista de ficheiros de registo, clique a **user-0.log**. No painel à direita estão os resultados de utilizar o **IActivityLogger.Write** método. Se não ver todas as mensagens, verifique se tiver mais ficheiros de registo com o nome: user_1.log, user_2.log etc. Caso contrário, o código pode ter falhado após a última sessão iniciada a mensagem.

   Além disso, verifique **sistema-0.log** para quaisquer mensagens de erro do sistema e exceções.
4. Incluir o **PDB** no ficheiro zip do ficheiro para que os detalhes do erro tem informações tal como **pilha de chamadas** quando ocorre um erro.
5. Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.
6. Certifique-se de que o **assemblyName** (mydotnetactivity. dll), **entryPoint**(mydotnetactivityns. Mydotnetactivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), e **packageLinkedService** (devem apontar para o **para fins gerais**armazenamento de Blobs do Azure que contém o ficheiro zip) estão definidos para valores corretos.
7. Se corrigir um erro e pretender processar de novo o setor, clique com o botão direito do rato no setor no painel **OutputDataset** e clique em **Executar**.
8. Se vir o erro seguinte, está a utilizar o pacote de armazenamento do Azure de versão > versão 4.3.0. Iniciador de serviço de fábrica de dados requer a versão 4.3 do windowsazure. Storage. Ver [isolamento de Appdomain](#appdomain-isolation) secção para uma solução alternativa, se tiver de utilizar a versão posterior do assembly de armazenamento do Azure.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Se puder usar a versão 4.3.0 versão de pacote de armazenamento do Azure, remova a referência existente ao pacote de armazenamento do Azure de versão > versão 4.3.0. Em seguida, execute o seguinte comando do NuGet Package Manager Console.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Crie o projeto. Elimine assemblagem de Azure.Storage de versão > versão 4.3.0 da pasta bin\Debug. Crie um ficheiro zip com binários e o arquivo PDB. Substitua o ficheiro zip do antigo por no contentor de BLOBs (customactivitycontainer). Volte a executar os setores que falhou (setor com o botão direito e clique em executar).
8. A atividade personalizada não utiliza a **App. config** ficheiro a partir do seu pacote. Por conseguinte, se seu código ler quaisquer cadeias de ligação do arquivo de configuração, ele não funciona em tempo de execução. A melhor prática, quando utilizar o Azure Batch é guardar segredos numa **Azure KeyVault**, utilize um principal de serviço baseada em certificados para proteger a **keyvault**e distribuir o certificado para o Azure Batch conjunto. A atividade personalizada de .NET pode, então, aceder aos segredos do Cofre de Chaves durante o tempo de execução. Esta solução é uma solução genérica e pode ser dimensionado para qualquer tipo de segredo, não apenas a cadeia de ligação.

   Existe uma solução mais fácil (mas não recomendado): pode criar uma **serviço ligado SQL do Azure** com definições de cadeia de ligação, criar um conjunto de dados que utiliza o serviço ligado e o conjunto de dados da cadeia como um conjunto de dados de entrada fictício para o atividade .NET personalizada. Em seguida, pode aceder a cadeia de ligação do serviço ligado no código de atividade personalizada.

## <a name="update-custom-activity"></a>Atualizar atividade personalizada
Se atualizar o código para a atividade personalizada, criar e carregar o ficheiro zip que contém os novos binários para o armazenamento de Blobs.

## <a name="appdomain-isolation"></a>Isolamento de AppDomain
Ver [exemplo de AppDomain Cross](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) que mostra como criar uma atividade personalizada que não está restrito à versões do assembly utilizadas pelo iniciador do Data Factory (exemplo: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.).

## <a name="access-extended-properties"></a>Acesso a Propriedades estendidas
Pode declarar propriedades expandidas da atividade JSON, conforme mostrado no exemplo a seguir:

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

No exemplo, existem duas propriedades expandidas: **SliceStart** e **DataFactoryName**. O valor para SliceStart baseia-se a variável de sistema do SliceStart. Ver [variáveis do sistema](data-factory-functions-variables.md) para obter uma lista de variáveis de sistema suportado. O valor para o DataFactoryName é hard-coded para CustomActivityFactory.

Para aceder a estes propriedades no estendidas a **Execute** método, usar código semelhante ao seguinte código:

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

## <a name="auto-scaling-of-azure-batch"></a>Dimensionamento automático do Azure Batch
Também pode criar um conjunto do Azure Batch com **dimensionamento automático** funcionalidade. Por exemplo, pode criar um conjunto do batch do azure com VMs dedicadas 0 e uma fórmula de dimensionamento automático com base no número de tarefas pendentes.

A fórmula de exemplo aqui alcança o seguinte comportamento: Quando o conjunto for criado inicialmente, ele começa com 1 VM. Métrica de $PendingTasks define o número de tarefas em execução + Active Directory (em fila) Estado.  A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated em conformidade. Ele garante que TargetDedicated nunca vai além de 25 VMs. Então, como novas tarefas submetidas, agrupamento automaticamente cresce e como tarefas são concluídas, as VMs se tornar um gratuito por um e o dimensionamento automático diminui essas VMs. startingNumberOfVMs e maxNumberofVMs pode ser ajustado às suas necessidades.

Fórmula de dimensionamento automático:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Ver [nós num conjunto do Azure Batch de computação de dimensionamento de automaticamente](../../batch/batch-automatic-scaling.md) para obter detalhes.

Se o conjunto está a utilizar a predefinição [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), o serviço Batch pode demorar 15 a 30 minutos para preparar a VM antes de executar a atividade personalizada.  Se o conjunto está a utilizar um autoScaleEvaluationInterval diferente, o serviço Batch pode demorar autoScaleEvaluationInterval + 10 minutos.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Criar uma atividade personalizada com o .NET SDK
Passo a passo neste artigo, vai criar uma fábrica de dados com um pipeline que utiliza a atividade personalizada com o portal do Azure. O código seguinte mostra como criar a fábrica de dados com o .NET SDK em vez disso. Pode encontrar mais detalhes sobre como utilizar o SDK para criar programaticamente pipelines no [criar um pipeline com atividade de cópia com a .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) artigo.

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

## <a name="debug-custom-activity-in-visual-studio"></a>Depurar a atividade personalizada no Visual Studio
O [do Azure Data Factory - ambiente local](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) exemplo no GitHub inclui uma ferramenta que lhe permite depurar atividades .NET personalizadas no Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Atividades personalizadas de exemplo no GitHub
| Sample | Que atividade personalizada faz |
| --- | --- |
| [Dispositivo de transferência da dados HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Transfere dados a partir de um ponto final HTTP ao armazenamento de Blobs do Azure com a atividade personalizada em C# no Data Factory. |
| [Exemplo de análise de sentimentos do twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Invoca um modelo do Azure Machine Learning studio e fazer a análise de sentimentos, classificação, previsão, etc. |
| [Executar R Script](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Invoca o R script, executando RScript.exe no seu cluster do HDInsight que já tenha o R instalado no mesmo. |
| [Em várias atividades de .NET de AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Utiliza as versões do assembly diferente de aplicações utilizada pelo iniciador do Data Factory |
| [Reprocessar um modelo no Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Processa novamente um modelo no Azure Analysis Services. |

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
