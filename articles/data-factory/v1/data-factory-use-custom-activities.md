---
title: Utilizar atividades personalizadas num pipeline do Azure Data Factory
description: Aprenda a criar atividades personalizadas e use-as num oleoduto Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 54cb06f1c77ab68818d8531b57d6eb936deda8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265730"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Utilizar atividades personalizadas num pipeline do Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-use-custom-activities.md)
> * [Versão 2 (versão atual)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [as atividades personalizadas em V2](../transform-data-using-dotnet-custom-activity.md).

Existem dois tipos de atividades que pode utilizar num oleoduto Azure Data Factory.

- [Atividades](data-factory-data-movement-activities.md) de Movimento de Dados para mover dados entre [fonte suportada e lojas de dados afundadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Atividades](data-factory-data-transformation-activities.md) de transformação de dados para transformar dados utilizando serviços de computação como Azure HDInsight, Azure Batch e Azure Machine Learning.

Para mover dados de/para uma loja de dados que a Data Factory não suporta, crie uma **atividade personalizada** com a sua própria lógica de movimento de dados e utilize a atividade num pipeline. Da mesma forma, para transformar/processar dados de uma forma que não seja suportada pela Data Factory, criar uma atividade personalizada com a sua própria lógica de transformação de dados e usar a atividade num pipeline.

Você pode configurar uma atividade personalizada para executar em uma piscina **de lotes Azure** em máquinas virtuais. Ao utilizar o Lote Azure, só pode utilizar uma piscina azure batch existente.

O seguinte walkthrough fornece instruções passo a passo para criar uma atividade personalizada .NET e usar a atividade personalizada em um pipeline. O walkthrough utiliza um serviço ligado ao **Lote Azure.**

> [!IMPORTANT]
> - Não é possível utilizar um Gateway de Gestão de Dados a partir de uma atividade personalizada para aceder a fontes de dados no local. Atualmente, o Portal de [Gestão](data-factory-data-management-gateway.md) de Dados suporta apenas a atividade de cópia e a atividade de procedimento armazenado na Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Walkthrough: criar uma atividade personalizada
### <a name="prerequisites"></a>Pré-requisitos
* Estúdio Visual 2012/2013/2015/2017
* Transferir e instalar o [SDK .NET do Azure](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Pré-requisitos do Lote Azure
No walkthrough, executa as suas atividades personalizadas .NET usando o Lote Azure como recurso de computação. **O Azure Batch** é um serviço de plataforma para executar aplicações de computação paralela e de alto desempenho (HPC) de forma eficiente na nuvem. O Azure Batch programa um trabalho intensivo para funcionar numa coleção gerida **de máquinas virtuais,** e pode automaticamente dimensionar os recursos computacionais para atender às necessidades dos seus trabalhos. Consulte o artigo básico do [Azure Batch][batch-technical-overview] para uma visão detalhada do serviço De lote de Azure.

Para o tutorial, crie uma conta Azure Batch com um conjunto de VMs. Eis os passos:

1. Crie uma **conta Azure Batch** utilizando o [portal Azure](https://portal.azure.com). Consulte criar e gerir um artigo de [conta Azure Batch][batch-create-account] para obter instruções.
2. Anote o nome da conta Do Lote Azure, chave de conta, URI e nome da piscina. Precisa deles para criar um serviço ligado ao Lote Azure.
    1. Na página inicial da conta Azure **URL** Batch, vê-se `https://myaccount.westus.batch.azure.com`um URL no seguinte formato: . Neste exemplo, **myaccount** é o nome da conta Azure Batch. Uri que utiliza na definição de serviço vinculado é o URL sem o nome da conta. Por exemplo: `https://<region>.batch.azure.com`.
    2. Clique em **Teclas** no menu esquerdo e copie a **CHAVE DE ACESSO PRIMÁRIO**.
    3. Para utilizar uma piscina existente, clique em **Pools** no menu e anote a **identificação** da piscina. Se você não tem uma piscina existente, mude para o próximo passo.
2. Crie uma **piscina azure batch**.

   1. No [portal Azure,](https://portal.azure.com)clique em **Navegar** no menu esquerdo e clique em **Contas de Lote**.
   2. Selecione a sua conta De Lote Azure para abrir a lâmina **da Conta lote.**
   3. Clique em azulejo **pools.**
   4. Na lâmina **Pools,** clique em Adicionar botão na barra de ferramentas para adicionar uma piscina.
      1. Introduza um ID para a piscina (ID da piscina). Note a **identificação da piscina;** precisa dele ao criar a solução Data Factory.
      2. Especifique o **Windows Server 2012 R2** para a definição familiar do sistema operativo.
      3. Selecione um **nível**de preços do nó .
      4. Introduza **2** como valor para a definição **de Destino Dedicado.**
      5. Introduza **2** como valor para as **tarefas Max por** definição de nó.
   5. Clique em **OK** para criar o conjunto.
   6. Anote a **identificação** da piscina.

### <a name="high-level-steps"></a>Passos gerais
Aqui estão os dois passos de alto nível que executa como parte desta passagem:

1. Crie uma atividade personalizada que contenha uma simples tecnologia de transformação/processamento de dados.
2. Crie uma fábrica de dados Azure com um oleoduto que utilize a atividade personalizada.

### <a name="create-a-custom-activity"></a>Criar uma atividade personalizada
Para criar uma atividade personalizada .NET, crie um projeto **.NET Class Library** com uma classe que implemente essa interface **IDotNetActivity.** Esta interface tem apenas um método: [Executar](https://msdn.microsoft.com/library/azure/mt603945.aspx) e a sua assinatura é:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

O método leva quatro parâmetros:

- **linkedServices**. Esta propriedade é uma lista enumerada de serviços ligados à Data Store referenciados por conjuntos de dados de entrada/saída para a atividade.
- **conjuntos de dados.** Esta propriedade é uma lista enumerada de conjuntos de dados de entrada/saída para a atividade. Pode utilizar este parâmetro para obter as localizações e esquemas definidos por conjuntos de dados de entrada e saída.
- **atividade**. Este imóvel representa a atividade atual. Pode ser usado para aceder a propriedades estendidas associadas à atividade personalizada. Consulte [o Access propriedades estendidas](#access-extended-properties) para mais detalhes.
- **madeireiro.** Este objeto permite-lhe escrever comentários de depurados que surgem no registo do utilizador para o gasoduto.

O método devolve um dicionário que pode ser usado para acorrentar atividades personalizadas no futuro. Esta funcionalidade ainda não foi implementada, por isso devolva um dicionário vazio do método.

### <a name="procedure"></a>Procedimento
1. Crie um projeto **.NET Class Library.**
   <ol type="a">
     <li>Inicie o Visual Studio.</li>
     <li>Clique em <b>Ficheiro</b>, aponte para <b>Novo</b> e, em seguida, clique em <b>Projeto</b>.</li>
     <li>Expanda <b>Modelos</b> e selecione <b>Visual C#</b>. Neste walkthrough, você usa C#, mas você pode usar qualquer idioma .NET para desenvolver a atividade personalizada.</li>
     <li>Selecione Biblioteca de <b>Classes</b> a partir da lista de tipos de projeto à direita. Em Estúdio Visual, escolha <b>Biblioteca de Classe (.QUADRO NET)</b> </li>
     <li>Introduza <b>MyDotNetActivity</b> para o <b>nome</b>.</li>
     <li>Selecione <b>C:\ADFGetStarted</b> para a <b>localização</b>.</li>
     <li>Clique em <b>OK</b> para criar o projeto.</li>
   </ol>

2. Clique em **Ferramentas**, aponte para **Gestor de Pacotes NuGet** e clique em **Consola do Gestor de Pacotes**.

3. Na Consola gestora de pacotes, execute o seguinte comando para importar **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importe o pacote NuGet de **Armazenamento Azure** para o projeto.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > O lançador de serviço data Factory requer a versão 4.3 do WindowsAzure.Storage. Se adicionar uma referência a uma versão posterior do conjunto de Armazenamento Azure no seu projeto de atividade personalizada, vê um erro quando a atividade executa. Para resolver o erro, consulte a secção de [isolamento do Appdomain.](#appdomain-isolation)
5. Adicione as seguintes declarações **utilizando** o ficheiro fonte do projeto.

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
6. Altere o nome do espaço de **nome** para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Mude o nome da classe para **MyDotNetActivity** e obtenha-o a partir da interface **IDotNetActivity,** como mostrado no seguinte código:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementar (Adicionar) o método **executar** da interface **IDotNetActivity** para a classe **MyDotNetActivity** e copiar o seguinte código de amostra para o método.

    A amostra seguinte conta o número de ocorrências do termo de pesquisa ("Microsoft") em cada bolha associada a uma fatia de dados.

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
9. Adicione os seguintes métodos de ajuda:

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

    O método GetFolderPath devolve o caminho para a pasta a que o conjunto de dados aponta e o método GetFileName devolve o nome da bolha/ficheiro a que o conjunto de dados aponta. Se tiver pastaO pato define usando variáveis como {Year}, {Month}, {Day} etc., o método devolve a corda tal como está sem as substituir por valores de tempo de execução. Consulte a secção de [propriedades estendidas do Access](#access-extended-properties) para obter mais informações sobre o acesso ao SliceStart, SliceEnd, etc.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    O método Calcular calcula o número de instâncias da palavra-chave Microsoft nos ficheiros de entrada (bolhas na pasta). O termo de pesquisa ("Microsoft") está codificado no código.
10. Compile o projeto. Clique em **Construir** a partir do menu e clique em **Construir Solução**.

    > [!IMPORTANT]
    > Deset 4.5.2 versão de .NET Framework como o quadro-alvo do seu projeto: clique no direito do projeto e clique em **Propriedades** para definir o quadro-alvo. Data Factory não suporta atividades personalizadas compiladas contra versões .NET Framework superiores a 4.5.2.

11. Lance **o Windows Explorer**e navegue para a pasta de lançamento de **bin\debug** ou **bin\,** dependendo do tipo de construção.
12. Crie um ficheiro zip **MyDotNetActivity.zip** que \<contenha\>todos os binários na pasta do projeto \bin\Debug pasta. Inclua o ficheiro **MyDotNetActivity.pdb** para que obtenha detalhes adicionais, como o número de linha no código fonte que causou o problema se houve uma falha.

    > [!IMPORTANT]
    > Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.

    ![Ficheiros de saída binários](./media/data-factory-use-custom-activities/Binaries.png)
14. Crie um recipiente de blob chamado **customactivitycontainer** se ainda não existir.
15. Carregue MyDotNetActivity.zip como uma bolha para o recipiente de atividade sucinta num armazenamento de blob Azure de **uso geral** (não armazenamento blob quente/fresco) que é referido pelo AzureStorageLinkedService.

> [!IMPORTANT]
> Se adicionar este projeto de atividade .NET a uma solução no Estúdio Visual que contém um projeto data Factory, e adicionar uma referência ao projeto de atividade .NET do projeto de aplicação data Factory, não precisa de realizar os dois últimos passos de criação manual do zip arquivar e carregá-lo para o armazenamento de bolhas Azure de propósito geral. Quando publica entidades da Data Factory utilizando o Visual Studio, estes passos são automaticamente feitos pelo processo de publicação. Para mais informações, consulte o projeto Data Factory na secção [Estúdio Visual.](#data-factory-project-in-visual-studio)

## <a name="create-a-pipeline-with-custom-activity"></a>Criar um oleoduto com atividade personalizada
Criou uma atividade personalizada e carregou o ficheiro zip com binários para um recipiente de blob numa Conta de Armazenamento Azure de **propósito geral.** Nesta secção, cria-se uma fábrica de dados Azure com um pipeline que utiliza a atividade personalizada.

O conjunto de dados de entrada para a atividade personalizada representa bolhas (ficheiros) na pasta de entrada personalizada do recipiente adftutorial no armazenamento de blob. O conjunto de dados de saída para a atividade representa bolhas de saída na pasta de saída de atividades personalizadas do recipiente adftutorial no armazenamento de blob.

Crie **ficheiro sintet.txt** com o seguinte conteúdo e carregue-o para a pasta de **entrada** personalizada do recipiente **adftutorial.** Crie o recipiente adftutorial se já não existir.

```
test custom activity Microsoft test custom activity Microsoft
```

A pasta de entrada corresponde a uma fatia na Azure Data Factory mesmo que a pasta tenha dois ou mais ficheiros. Quando cada fatia é processada pelo gasoduto, a atividade personalizada iterates através de todas as bolhas na pasta de entrada para essa fatia.

Você vê um ficheiro de saída com na pasta adftutorial\customactivityoutput com uma ou mais linhas (o mesmo que o número de bolhas na pasta de entrada):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Aqui estão os passos que executa nesta secção:

1. Criar uma fábrica de **dados.**
2. Crie **serviços Linked** para o conjunto de VMs do Lote Azure em que a atividade personalizada funciona e o Armazenamento Azure que detém as bolhas de entrada/saída.
3. Crie **conjuntos** de dados de entrada e saída que representem a entrada e a saída da atividade personalizada.
4. Crie um **oleoduto** que utilize a atividade personalizada.

> [!NOTE]
> Crie o **ficheiro.txt** e carregue-o para um recipiente de bolha se ainda não o tiver feito. Consulte as instruções na secção anterior.

### <a name="step-1-create-the-data-factory"></a>Passo 1: Criar a fábrica de dados
1. Depois de iniciar sessão no portal Azure, faça os seguintes passos:
   1. Clique em **Criar um recurso** no menu esquerdo.
   2. Clique em **Dados + Analytics** na **nova** lâmina.
   3. Clique em **Data Factory** no painel **Análise de dados**.

      ![Novo menu azure data factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Na nova lâmina de fábrica de **dados,** **introduza CustomActivityFactory** para o nome. O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: O nome de **fábrica de dados "CustomActivityFactory" não está disponível,** altere o nome da fábrica de dados (por exemplo, **o seu nomeCustomActivity )** e tente criar novamente.

    ![Nova lâmina azure data factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Clique em NOME DE **GRUPO DE RECURSOS**, e selecione um grupo de recursos existente ou crie um grupo de recursos.
4. Verifique se está a utilizar a **subscrição** e **região corretas** onde pretende que a fábrica de dados seja criada.
5. Clique em **Criar** no painel **Nova fábrica de dados**.
6. Vê-se a fábrica de dados a ser criada no **Dashboard** do portal Azure.
7. Depois de a fábrica de dados ter sido criada com sucesso, vê a lâmina data Factory, que lhe mostra o conteúdo da fábrica de dados.

    ![Painel Data Factory](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Passo 2: Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Neste passo, liga a sua conta de Armazenamento Azure e a conta Azure Batch à sua fábrica de dados.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
1. Clique no **Autor e implante** azulejos na lâmina DATA **FACTORY** para **CustomActivityFactory**. Será apresentado o Editor do Data Factory.
2. Clique em **Nova loja** de dados na barra de comandoe escolha o **armazenamento Azure**. Deverá ver o script JSON para criar um serviço ligado do Storage do Azure no editor.

    ![Nova loja de dados - Armazenamento Azure](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Substitua-o `<accountname>` pelo nome da `<accountkey>` sua conta de armazenamento Azure e pela chave de acesso da conta de armazenamento Azure. Para aprender como obter a sua chave de acesso ao armazenamento, consulte [Gerir as chaves](../../storage/common/storage-account-keys-manage.md)de acesso à conta de armazenamento .

    ![Azure Storage gostava de serviço](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="create-azure-batch-linked-service"></a>Criar serviço ligado ao Lote Azure
1. No Editor da Fábrica de Dados, **clique... Mais** na barra de comando, clique em **Novo compute,** e, em seguida, selecione **Azure Batch** a partir do menu.

    ![Novo cálculo - Lote Azure](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Faça as seguintes alterações no script JSON:

   1. Especifique o nome da conta Do Lote Azure para a propriedade **accountName.** O **URL** da lâmina de conta Do Lote `http://accountname.region.batch.azure.com` **Azure** encontra-se no seguinte formato: . Para a propriedade **batchUri** no JSON, `accountname.` você precisa remover `accountname` do `accountName` URL e usar o para a propriedade JSON.
   2. Especifique a chave da conta Do Lote Azure para a propriedade **accessKey.**
   3. Especifique o nome da piscina que criou como parte dos pré-requisitos para a propriedade **PoolName.** Você também pode especificar a identificação da piscina em vez do nome da piscina.
   4. Especifique o lote azure URI para a propriedade **batchUri.** Exemplo: `https://westus.batch.azure.com`.
   5. Especifique o **AzureStorageLinkedService** para a propriedade **linkedServiceName.**

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

       Para a propriedade **poolName,** você também pode especificar a id da piscina em vez do nome da piscina.

### <a name="step-3-create-datasets"></a>Passo 3: Criar conjuntos de dados
Neste passo, cria conjuntos de dados para representar dados de entrada e saída.

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Editor** da Fábrica de Dados, **clique... Mais** na barra de comando, clique em **Novo conjunto**de dados e, em seguida, selecione o **armazenamento Azure Blob** a partir do menu suspenso.
2. Substitua o JSON no painel direito pelo seguinte corte JSON:

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

   Cria-se um oleoduto mais tarde nesta passagem com a hora de início: 2016-11-16T00:00:00Z e hora final: 2016-11-16T05:00:00Z. Está programado para produzir dados por hora, pelo que existem **00**cinco fatias de entrada/saída (entre 00:00:00 -> 05:00:00). **05**

   A **frequência** e **intervalo** para o conjunto de dados de entrada está definido para **Hora** e **1**, o que significa que a fatia de entrada está disponível de hora em hora. Nesta amostra, é o mesmo ficheiro (file.txt) na intputfolder.

   Aqui estão os tempos de início para cada fatia, que é representado pela variável do sistema SliceStart no corte jSON acima.
3. Clique em **implementar** na barra de ferramentas para criar e implementar o **InputDataset**. Confirme que vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.

#### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
1. No **editor da Data Factory,** **clique... Mais** na barra de comando, clique em **Novo conjunto de dados,** e, em seguida, selecione **armazenamento Azure Blob**.
2. Substitua o script JSON no painel direito com o seguinte script JSON:

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

     A localização da saída é **adftutorial/customactivityoutput/** e o nome do ficheiro de saída é yyyy-MM-dd-HH.txt onde yyy-MM-dd-HH é o ano, mês, data e hora da fatia que está sendo produzida. Consulte a [Referência do Desenvolvedor][adf-developer-reference] para obter mais detalhes.

    Uma bolha/ficheiro de saída é gerada para cada fatia de entrada. Aqui está como um ficheiro de saída é nomeado para cada fatia. Todos os ficheiros de saída são gerados numa pasta de saída: **adftutorial\customactivityoutput**.

   | Fatia | Hora de início | Ficheiro de saída |
   |:--- |:--- |:--- |
   | 1 |2016-11-16t00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16t01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16t02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16t03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16t04:00:00 |2016-11-16-04.txt |

    Lembre-se que todos os ficheiros de uma pasta de entrada fazem parte de uma fatia com os tempos de início acima mencionados. Quando esta fatia é processada, a atividade personalizada digitaliza cada ficheiro e produz uma linha no ficheiro de saída com o número de ocorrências de termo de pesquisa ("Microsoft"). Se houver três ficheiros na pasta de entrada, existem três linhas no ficheiro de saída para cada fatia horária: 2016-11-16-00.txt, 2016-11-16:01:00:txt, etc.
3. Para implementar o **Conjunto de Dados de Saída,** clique em **implementar** na barra de comando.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Crie e gere um oleoduto que use a atividade personalizada
1. No Editor da Fábrica de Dados, **clique... Mais,** e depois selecione **Novo oleoduto** na barra de comando.
2. Substitua o JSON no painel direito com o seguinte script JSON:

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

   * **A moeda está** definida para **2** de modo a que duas fatias sejam processadas paralelamente por 2 VMs na piscina do Lote Azure.
   * Há uma atividade na secção de atividades e é de tipo: **DotNetActivity**.
   * **O nome de montagem** está definido para o nome do DLL: **MyDotnetActivity.dll**.
   * **O EntryPoint** está definido para **MyDotNetActivityNS.MyDotNetActivity**.
   * **O PackageLinkedService** está definido para **o AzureStorageLinkedService** que aponta para o armazenamento blob que contém o ficheiro zip de atividade personalizada. Se estiver a utilizar diferentes contas de Armazenamento Azure para ficheiros de entrada/saída e o ficheiro zip de atividade personalizada, cria outro serviço ligado ao Armazenamento Azure. Este artigo assume que está a usar a mesma conta de Armazenamento Azure.
   * **O PackageFile** é definido para **customactivitycontainer/MyDotNetActivity.zip**. Está no formato: containerforthezip/nameofthezip.zip.zip.
   * A atividade personalizada toma o **InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade linkedServiceName da atividade personalizada aponta para o **AzureBatchLinkedService,** que diz à Azure Data Factory que a atividade personalizada precisa de ser executada em VMs de Lote Azure.
   * **isApausa** propriedade é definido para **falso** por padrão. O gasoduto funciona imediatamente neste exemplo porque as fatias começam no passado. Você pode definir esta propriedade para verdadeira para parar o oleoduto e reduzi-lo para falso para reiniciar.
   * Os tempos de **início** e os prazos **finais** são **de cinco** horas de intervalo e as fatias são produzidas de hora a hora, pelo que cinco fatias são produzidas pelo gasoduto.
3. Para implantar o gasoduto, clique em **colocar** na barra de comando.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. Na lâmina data Factory no portal Azure, clique no **Diagrama**.

    ![Mosaico do diagrama](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Na vista do diagrama, clique agora no Conjunto de Dados de Saída.

    ![Vista de diagrama](./media/data-factory-use-custom-activities/diagram.png)
3. Devias ver que as cinco fatias de saída estão no estado ready. Se não estão no estado ready, ainda não foram produzidos.

   ![Fatias de saída](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Verifique se os ficheiros de saída são gerados no armazenamento de bolhas no recipiente **adftutorial.**

   ![saída de atividade personalizada][image-data-factory-output-from-custom-activity]
5. Se abrir o ficheiro de saída, deverá ver a saída semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Utilize o [portal Azure][azure-preview-portal] ou os cmdlets Azure PowerShell para monitorizar a sua fábrica de dados, oleodutos e conjuntos de dados. Pode ver mensagens do **ActivityLogger** no código para a atividade personalizada nos registos (especificamente user-0.log) que pode descarregar a partir do portal ou utilizando cmdlets.

   ![baixar registos de atividade personalizada][image-data-factory-download-logs-from-custom-activity]

Consulte o Monitor e a Manage Pipelines para obter [medidas detalhadas](data-factory-monitor-manage-pipelines.md) para monitorizar conjuntos de dados e oleodutos.

## <a name="data-factory-project-in-visual-studio"></a>Projeto data factory em Estúdio Visual
Pode criar e publicar entidades data Factory utilizando o Visual Studio em vez de utilizar o portal Azure. Para obter informações detalhadas sobre a criação e publicação de entidades data factory utilizando o Visual Studio, veja [construir o seu primeiro pipeline utilizando](data-factory-build-your-first-pipeline-using-vs.md) dados do Visual Studio e copy de [artigos Azure Blob para Azure SQL.](data-factory-copy-activity-tutorial-using-visual-studio.md)

Faça os seguintes passos adicionais se estiver a criar o projeto Data Factory no Estúdio Visual:

1. Adicione o projeto Data Factory à solução Visual Studio que contém o projeto de atividade personalizada.
2. Adicione uma referência ao projeto de atividade .NET do projeto Data Factory. Clique no projeto Data Factory, aponte para **Adicionar,** e depois clique em **Referência**.
3. Na caixa de diálogo **Add Reference,** selecione o projeto **MyDotNetActivity** e clique em **OK**.
4. Construir e publicar a solução.

    > [!IMPORTANT]
    > Ao publicar entidades data Factory, um ficheiro zip é automaticamente criado para si e é enviado para o recipiente blob: customactivitycontainer. Se o recipiente de bolhas não existir, também é criado automaticamente.

## <a name="data-factory-and-batch-integration"></a>Integração de Fábrica de Dados e Lote
O serviço Data Factory cria um trabalho em Azure Batch com o nome: **adf-poolname: job-xxx**. Clique em **Jobs** a partir do menu esquerdo.

![Azure Data Factory - Trabalhos em lotes](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Uma tarefa é criada para cada execução de atividade de uma fatia. Se houver cinco fatias prontas a serem processadas, são criadas cinco tarefas neste trabalho. Se houver vários nós computacionais na piscina do Lote, duas ou mais fatias podem ser executadas em paralelo. Se as tarefas máximas por nó de cálculo estiverem definidas para > 1, também pode ter mais de uma fatia em execução no mesmo cálculo.

![Azure Data Factory - Tarefas de trabalho em lote](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

O diagrama que se segue ilustra a relação entre as tarefas azure Data Factory e Batch.

![Lote de & da fábrica de dados](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Falhas de resolução de problemas
A resolução de problemas consiste em algumas técnicas básicas:

1. Se vir o seguinte erro, poderá estar a utilizar um armazenamento de bolhas Hot/Cool em vez de utilizar um armazenamento de bolhas Azure de uso geral. Faça upload do ficheiro zip para uma **conta de armazenamento azure de fins gerais**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Se vir o seguinte erro, confirme que o nome da classe no ficheiro CS corresponde ao nome especificado para a propriedade **EntryPoint** no pipeline JSON. No walkthrough, o nome da classe é: MyDotNetActivity, e o EntryPoint no JSON é: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Se os nomes coincidirem, confirme que todos os binários estão na **pasta raiz** do ficheiro zip. Isto é, quando abrir o ficheiro zip, deve ver todos os ficheiros na pasta raiz, e não em quaisquer subpastas.
3. Se a fatia de entrada não estiver definida para **Ready**, confirme se a estrutura da pasta de entrada está correta e **o ficheiro.txt** existe nas pastas de entrada.
3. No método **executar** da sua atividade personalizada, utilize o objeto **IActivityLogger** para registar informações que o ajudem a resolver problemas. As mensagens registadas aparecem nos ficheiros de registo do utilizador (um ou mais ficheiros nomeados: user-0.log, user-1.log, user-2.log, etc.).

   Na lâmina **OutputDataset,** clique na fatia para ver a lâmina **DATA SLICE** para essa fatia. Vês **que a atividade corre** para essa fatia. Deviaver uma atividade correr para a fatia. Se clicar em Executar na barra de comando, pode iniciar outra corrida de atividade para a mesma fatia.

   Ao clicar na execução da atividade, consulte a lâmina **DE DETALHES DE EXECUÇÃO DE ATIVIDADE** com uma lista de ficheiros de registo. Vê mensagens registadas no ficheiro user_0.log. Quando ocorre um erro, vê-se três funcionamentos de atividade porque a contagem de retry está definida para 3 no pipeline/atividade JSON. Ao clicar na execução da atividade, consulte os ficheiros de registo que pode rever para resolver o erro.

   Na lista de ficheiros de registo, clique no **user-0.log**. No painel certo estão os resultados da utilização do método **IActivityLogger.Write.** Se não vir todas as mensagens, verifique se tem mais ficheiros de registo nomeados: user_1.log, user_2.log etc. Caso contrário, o código pode ter falhado após a última mensagem registada.

   Além disso, verifique o **sistema-0.log** para quaisquer mensagens de erro do sistema e exceções.
4. Inclua o ficheiro **PDB** no ficheiro zip para que os detalhes do erro tenham informações como **a pilha de chamadas** quando ocorre um erro.
5. Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.
6. Certifique-se de que o **nome** de montagem (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e **pacoteLinkedService** (deve apontar para o armazenamento de bolhas Azure de **uso geral**que contém o ficheiro zip) estão definidos para corrigir valores.
7. Se corrigir um erro e pretender processar de novo o setor, clique com o botão direito do rato no setor no painel **OutputDataset** e clique em **Executar**.
8. Se vir o seguinte erro, está a utilizar o pacote de armazenamento Azure da versão > 4.3.0. O lançador de serviço data Factory requer a versão 4.3 do WindowsAzure.Storage. Consulte a secção de [isolamento do Appdomain](#appdomain-isolation) para obter um trabalho em torno se tiver de utilizar a versão posterior do conjunto de armazenamento do Azure.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Se puder utilizar a versão 4.3.0 do pacote De armazenamento Azure, remova a referência existente ao pacote de armazenamento azure da versão > 4.3.0. Em seguida, execute o seguinte comando da Consola nuGet Package Manager.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Crie o projeto. Delete Azure.Armazenamento de conjunto de versão > 4.3.0 da pasta bin\Debug. Crie um ficheiro zip com binários e o ficheiro PDB. Substitua o antigo ficheiro zip por este no recipiente de blob (recipiente de atividade saem). Reexecutar as fatias que falharam (fatia de clique direito e clique em Executar).
8. A atividade personalizada não utiliza o ficheiro **app.config** do seu pacote. Portanto, se o seu código ler quaisquer cordas de ligação do ficheiro de configuração, não funciona no tempo de execução. A melhor prática ao utilizar o Lote Azure é guardar quaisquer segredos num **Azure KeyVault,** utilizar um diretor de serviço baseado em certificados para proteger o cofre de **chaves**e distribuir o certificado para a piscina do Lote Azure. A atividade personalizada de .NET pode, então, aceder aos segredos do Cofre de Chaves durante o tempo de execução. Esta solução é uma solução genérica e pode escalar para qualquer tipo de segredo, não apenas corda de ligação.

   Existe uma suverização mais fácil (mas não é uma boa prática): pode criar um serviço ligado ao **Azure SQL com** definições de cordas de ligação, criar um conjunto de dados que utilize o serviço ligado e acorrentar o conjunto de dados como um conjunto de dados de entrada manequim para a atividade personalizada .NET. Em seguida, pode aceder à cadeia de ligação do serviço ligado no código de atividade personalizado.

## <a name="update-custom-activity"></a>Atualizar atividade personalizada
Se atualizar o código para a atividade personalizada, construa-o e carregue o ficheiro zip que contém novos binários para o armazenamento de blob.

## <a name="appdomain-isolation"></a>Isolamento de appdomain
Consulte [a Amostra Cross AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) que lhe mostra como criar uma atividade personalizada que não está limitada a versões de montagem utilizadas pelo lançador data Factory (exemplo: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.).

## <a name="access-extended-properties"></a>Acesso a propriedades estendidas
Pode declarar propriedades estendidas na atividade JSON, como mostra a seguinte amostra:

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

No exemplo, existem duas propriedades estendidas: **SliceStart** e **DataFactoryName**. O valor para SliceStart baseia-se na variável do sistema SliceStart. Consulte [variáveis](data-factory-functions-variables.md) do sistema para obter uma lista de variáveis do sistema suportadas. O valor para DataFactoryName é codificado para CustomActivityFactory.

Para aceder a estas propriedades estendidas no método **Executar,** utilize código semelhante ao seguinte código:

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

## <a name="auto-scaling-of-azure-batch"></a>Auto-escalação do Lote Azure
Também pode criar uma piscina azure batch com recurso de **escala automática.** Por exemplo, você poderia criar um lote azul com 0 VMs dedicados e uma fórmula de escala automática baseada no número de tarefas pendentes.

A fórmula da amostra aqui consegue o seguinte comportamento: Quando a piscina é inicialmente criada, começa com 1 VM. $PendingTasks métrica define o número de tarefas em execução + estado ativo (em fila).  A fórmula encontra o número médio de tarefas pendentes nos últimos 180 segundos e define o TargetDedicated em conformidade. Garante que o TargetDedicated nunca vai além de 25 VMs. Assim, à medida que novas tarefas são submetidas, o pool cresce automaticamente e à medida que as tarefas completam, os VMs tornam-se gratuitos um a um e o autoscalcificante encolhe esses VMs. iniciarNumberOfVMs e maxNumberofVMs podem ser ajustados às suas necessidades.

Fórmula de escala automática:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Consulte [automaticamente os nódosos de cálculo numa piscina de Lote Azure](../../batch/batch-automatic-scaling.md) para obter mais detalhes.

Se a piscina estiver a utilizar o [intervalo de avaliação automática](https://msdn.microsoft.com/library/azure/dn820173.aspx)predefinido, o serviço Batch pode demorar 15-30 minutos a preparar o VM antes de executar a atividade personalizada.  Se a piscina estiver a utilizar um intervalo de avaliação automática diferente, o serviço Batch poderá demorar a avaliação automática + 10 minutos.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Criar uma atividade personalizada usando .NET SDK
No walkthrough neste artigo, cria-se uma fábrica de dados com um pipeline que utiliza a atividade personalizada utilizando o portal Azure. O código que se segue mostra como criar a fábrica de dados utilizando o .NET SDK. Pode encontrar mais detalhes sobre a utilização do SDK para criar sistematrócolas na criação de um pipeline com a atividade de cópia utilizando o artigo [.NET API.](data-factory-copy-activity-tutorial-using-dotnet-api.md)

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

## <a name="debug-custom-activity-in-visual-studio"></a>Atividade personalizada de depuração no Estúdio Visual
A [Azure Data Factory -](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) amostra de ambiente local no GitHub inclui uma ferramenta que permite desbugimar atividades personalizadas .NET dentro do Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Amostra de atividades personalizadas no GitHub
| Sample | O que a atividade personalizada faz |
| --- | --- |
| [HTTP Data Downloader](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Descarrega dados de um HTTP Endpoint para O Armazenamento De Blob Azure usando a atividade personalizada de C# na Fábrica de Dados. |
| [Amostra de análise de sentimento do Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Invoca um modelo de estúdio Azure Machine Learning e faz análise de sentimentos, pontuação, previsão etc. |
| [Executar o Script R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Invoca o script R executando RScript.exe no seu cluster HDInsight que já tem R Instalado nele. |
| [Cross AppDomain .NET Activity](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Utiliza diferentes versões de montagem das utilizadas pelo lançador data Factory |
| [Reprocessar um modelo nos Serviços de Análise Azure](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Reprocessa um modelo em Serviços de Análise Azure. |

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
