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
ms.custom: devx-track-csharp
manager: anandsub
robots: noindex
ms.openlocfilehash: b3391727b19e9e8e88646f72667545f1df7fe5a7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012872"
---
# <a name="use-custom-activities-in-an-azure-data-factory-version-1-pipeline"></a>Utilize atividades personalizadas num pipeline da versão 1 da Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-use-custom-activities.md)
> * [Versão 2 (versão atual)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [as atividades personalizadas em V2](../transform-data-using-dotnet-custom-activity.md).

Existem dois tipos de atividades que pode utilizar num oleoduto Azure Data Factory.

- [Atividades de Movimento de Dados](data-factory-data-movement-activities.md) para mover dados entre [fontes suportadas e lojas de dados de sumidouros](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para transformar dados usando serviços de computação como Azure HDInsight, Azure Batch e Azure Machine Learning Studio (clássico).

Para mover dados de/para uma loja de dados que a Data Factory não suporta, crie uma **atividade personalizada** com a sua própria lógica de movimento de dados e utilize a atividade num pipeline. Da mesma forma, para transformar/processar dados de uma forma que não seja suportada pela Data Factory, crie uma atividade personalizada com a sua própria lógica de transformação de dados e use a atividade num oleoduto.

Você pode configurar uma atividade personalizada para executar em uma piscina **Azure Batch** de máquinas virtuais. Ao utilizar o Azure Batch, só pode utilizar uma piscina Azure Batch existente.

O seguinte walkthrough fornece instruções passo a passo para criar uma atividade personalizada .NET e usar a atividade personalizada em um oleoduto. O walkthrough utiliza um serviço ligado a **Azure Batch.**

> [!IMPORTANT]
> - Não é possível utilizar um Gateway de Gestão de Dados a partir de uma atividade personalizada para aceder a fontes de dados no local. Atualmente, [a Data Management Gateway](data-factory-data-management-gateway.md) suporta apenas a atividade de cópia e atividade de procedimento armazenado na Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Walkthrough: criar uma atividade personalizada
### <a name="prerequisites"></a>Pré-requisitos
* Estúdio Visual 2012/2013/2015/2017
* Transferir e instalar o [SDK .NET do Azure](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Pré-requisitos do Azure Batch
Na passagem, executou as suas atividades personalizadas .NET usando o Azure Batch como recurso de computação. **Azure Batch** é um serviço de plataforma para executar aplicações de computação paralela e de alto desempenho (HPC) de grande escala na nuvem. Azure Batch programa um trabalho intensivo para executar numa coleção gerida **de máquinas virtuais,** e pode escalar automaticamente recursos de computação para atender às necessidades dos seus trabalhos. Consulte o artigo [básico do Azure Batch][batch-technical-overview] para obter uma visão geral detalhada do serviço Azure Batch.

Para o tutorial, crie uma conta Azure Batch com um conjunto de VMs. Eis os passos:

1. Criar uma **conta Azure Batch** utilizando o [portal Azure](https://portal.azure.com). Consulte Criar e gerir um artigo [de conta Azure Batch][batch-create-account] para obter instruções.
2. Note o nome da conta Azure Batch, chave de conta, URI e nome da piscina. Precisa deles para criar um serviço ligado a Azure Batch.
    1. Na página inicial da conta Azure Batch, vê um **URL** no seguinte formato: `https://myaccount.westus.batch.azure.com` . Neste exemplo, **a minha contagem** é o nome da conta Azure Batch. URI que utiliza na definição de serviço ligada é o URL sem o nome da conta. Por exemplo: `https://<region>.batch.azure.com`.
    2. Clique em **Teclas** no menu esquerdo e copie a **tecla DE ACESSO PRIMÁRIO**.
    3. Para utilizar uma piscina existente, clique em **Pools** no menu e note o **ID** da piscina. Se não tiver uma piscina existente, passe para o próximo passo.
2. Crie uma **piscina Azure Batch**.

   1. No [portal Azure,](https://portal.azure.com)clique em **Procurar** no menu esquerdo e clique em **Contas de Lote**.
   2. Selecione a sua conta Azure Batch para abrir a lâmina **'Conta lote'.**
   3. Clique **em azulejo de Piscinas.**
   4. Na lâmina **Pools,** clique em Adicionar o botão na barra de ferramentas para adicionar uma piscina.
      1. Introduza um ID para a piscina (Pool ID). Note o **ID da piscina;** precisa quando se cria a solução Data Factory.
      2. Especifique **o Windows Server 2012 R2** para a definição do Sistema Operativo Família.
      3. Selecione um **nível de preços de nó**.
      4. Introduza **2** como valor para a definição dedicada ao **Alvo.**
      5. Introduza **2** como valor para as **tarefas Max por** definição de nó.
   5. Clique em **OK** para criar o conjunto.
   6. Note a **identificação** da piscina.

### <a name="high-level-steps"></a>Passos gerais
Aqui estão os dois passos de alto nível que executa como parte desta passagem:

1. Crie uma atividade personalizada que contenha uma lógica simples de transformação/processamento de dados.
2. Crie uma fábrica de dados Azure com um oleoduto que utilize a atividade personalizada.

### <a name="create-a-custom-activity"></a>Criar uma atividade personalizada
Para criar uma atividade personalizada .NET, crie um projeto **da Biblioteca classe .NET** com uma classe que implementa a interface **IDotNetActivity.** Esta interface tem apenas um método: [Executar](/dotnet/api/microsoft.azure.management.datafactories.runtime.idotnetactivity) e a sua assinatura é:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

O método tem quatro parâmetros:

- **linkedServices**. Esta propriedade é uma lista enumerada de serviços ligados à Data Store referenciados por conjuntos de dados de entrada/saída para a atividade.
- **conjuntos de dados**. Esta propriedade é uma lista enumerada de conjuntos de dados de entrada/saída para a atividade. Pode utilizar este parâmetro para obter as localizações e esquemas definidos por conjuntos de dados de entrada e saída.
- **atividade**. Esta propriedade representa a atividade atual. Pode ser usado para aceder a propriedades estendidas associadas à atividade personalizada. Consulte [as propriedades estendidas do Access](#access-extended-properties) para mais detalhes.
- **madeireiro**. Este objeto permite-lhe escrever comentários de depurg que aparecem no registo do utilizador para o pipeline.

O método devolve um dicionário que pode ser usado para acorrentar atividades personalizadas no futuro. Esta funcionalidade ainda não foi implementada, por isso devolva um dicionário vazio do método.

### <a name="procedure"></a>Procedimento
1. Criar um projeto **da Biblioteca classe .NET.**
   <ol type="a">
     <li>Lançamento Visual Studio.</li>
     <li>Clique em <b>Ficheiro</b>, aponte para <b>Novo</b> e, em seguida, clique em <b>Projeto</b>.</li>
     <li>Expanda <b>Modelos</b> e selecione <b>Visual C#</b>. Neste walkthrough, você usa C#, mas você pode usar qualquer idioma .NET para desenvolver a atividade personalizada.</li>
     <li>Selecione <b>a Biblioteca</b> de Classe da lista de tipos de projeto à direita. No Estúdio Visual, escolha <b>a Biblioteca de Classes (.NET Framework)</b> </li>
     <li>Insira <b>myDotNetActivity</b> para o <b>nome</b>.</li>
     <li>Selecione <b>C:\ADFGetStarted</b> para a <b>localização</b>.</li>
     <li>Clique em <b>OK</b> para criar o projeto.</li>
   </ol>

2. Clique em **Ferramentas**, aponte para **Gestor de Pacotes NuGet** e clique em **Consola do Gestor de Pacotes**.

3. Na consola Gestor de Pacotes, execute o seguinte comando para importar **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importe o pacote **Azure Storage** NuGet para o projeto.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > O lançador de serviço data factory requer a versão 4.3 do WindowsAzure.Storage. Se adicionar uma referência a uma versão posterior do conjunto de Armazenamento Azure no seu projeto de atividade personalizada, verá um erro quando a atividade é executada. Para resolver o erro, consulte a secção [de isolamento de Appdomain.](#appdomain-isolation)
5. Adicione as **seguintes declarações** ao ficheiro de origem do projeto.

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
6. Altere o nome do **espaço de nomes** para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Altere o nome da classe para **MyDotNetActivity** e o retire da interface **IDotNetActivity,** tal como mostrado no seguinte código:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementar (Adicionar) o método **executar** a interface **IDotNetActivity** para a classe **MyDotNetActivity** e copiar o seguinte código de amostra para o método.

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

    O método GetFolderPath devolve o caminho para a pasta para a qual o conjunto de dados aponta e o método GetFileName devolve o nome do blob/ficheiro que o conjunto de dados aponta. Se tiver pastaSPata define usando variáveis tais como {Year}, {Month}, {Day} etc., o método devolve a cadeia tal como está sem as substituir por valores de tempo de execução. Consulte a secção [de propriedades estendidas access](#access-extended-properties) para obter mais informações sobre o acesso ao SliceStart, SliceEnd, etc.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    O método calcular calcula o número de casos de palavra-chave Microsoft nos ficheiros de entrada (bolhas na pasta). O termo de pesquisa ("Microsoft") está codificado no código.
10. Compilar o projeto. Clique em **Construir** a partir do menu e clique em **Build Solution**.

    > [!IMPORTANT]
    > Desaponte a versão 4.5.2 do Quadro .NET como o quadro-alvo do seu projeto: clique à direita no projeto e clique em **Propriedades** para definir o quadro-alvo. A Data Factory não suporta atividades personalizadas compiladas contra versões .NET Framework mais tarde do que 4.5.2.

11. Inicie **o Windows Explorer** e navegue para a pasta **bin\debug** ou **bin\release** dependendo do tipo de construção.
12. Crie um ficheiro zip **MyDotNetActivity.zip** que contenha todos os binários na \<project folder\> pasta \bin\Debug. Inclua o ficheiro **.pdb MyDotNetActivity** para obter detalhes adicionais, como o número de linha no código fonte que causou o problema se houver uma falha.

    > [!IMPORTANT]
    > Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.

    ![Ficheiros de saída binários](./media/data-factory-use-custom-activities/Binaries.png)
14. Crie um recipiente blob denominado **recipiente de atividades personalizadas** se já não existir.
15. Faça o upload MyDotNetActivity.zip como uma bolha para o dispositivo de atividades **personalizadas** num armazenamento de bolhas Azure (não quente/fresco blob) que é referido pelo AzureStorageLinkedService.

> [!IMPORTANT]
> Se adicionar este projeto de atividade .NET a uma solução no Estúdio Visual que contenha um projeto data Factory, e adicionar uma referência ao projeto de atividade .NET do projeto de aplicação data Factory, não precisa de executar os dois últimos passos de criação manual do ficheiro zip e de o carregar para o armazenamento de bolhas Azure de propósito geral. Quando publica entidades da Data Factory utilizando o Visual Studio, estes passos são feitos automaticamente pelo processo de publicação. Para mais informações, consulte o projeto Data Factory na secção [Estúdio Visual.](#data-factory-project-in-visual-studio)

## <a name="create-a-pipeline-with-custom-activity"></a>Criar um oleoduto com atividade personalizada
Criou uma atividade personalizada e carregou o ficheiro zip com binários para um recipiente de bolhas numa Conta de Armazenamento Azure **de uso geral.** Nesta secção, cria-se uma fábrica de dados Azure com um oleoduto que utiliza a atividade personalizada.

O conjunto de dados de entrada para a atividade personalizada representa bolhas (ficheiros) na pasta de substituição de dados de adftutorial no armazenamento do blob. O conjunto de dados de saída para a atividade representa bolhas de saída na pasta de saída personalizada do recipiente adftutorial no armazenamento do blob.

Crie **file.txt** ficheiro com o seguinte conteúdo e carreme-o para a pasta de **substituição deactividade personalizada** do recipiente **adftutorial.** Crie o recipiente adftutorial se já não existir.

```
test custom activity Microsoft test custom activity Microsoft
```

A pasta de entrada corresponde a uma fatia na Azure Data Factory mesmo que a pasta tenha dois ou mais ficheiros. Quando cada fatia é processada pelo oleoduto, a atividade personalizada itera através de todas as bolhas da pasta de entrada para essa fatia.

Vê-se um ficheiro de saída na pasta adftutorial\customactivityoutput com uma ou mais linhas (o mesmo que o número de bolhas na pasta de entrada):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Aqui estão os passos que executa nesta secção:

1. Criar uma fábrica de **dados.**
2. Crie **serviços Linked** para o conjunto de VMs Azure Batch em que a atividade personalizada funciona e o Azure Storage que detém as bolhas de entrada/saída.
3. Crie **conjuntos de dados de** entrada e saída que representem a entrada e saída da atividade personalizada.
4. Crie um **oleoduto** que utilize a atividade personalizada.

> [!NOTE]
> Crie o **file.txt** e carre deixe-o enviar para um recipiente de bolhas se ainda não o fez. Consulte as instruções na secção anterior.

### <a name="step-1-create-the-data-factory"></a>Passo 1: Criar a fábrica de dados
1. Depois de iniciar sessão no portal Azure, faça os seguintes passos:
   1. Clique **em Criar um recurso** no menu esquerdo.
   2. Clique em **Dados + Analytics** na lâmina **nova.**
   3. Clique em **Data Factory** no painel **Análise de dados**.

      ![Novo menu da Fábrica de Dados Azure](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Na nova lâmina de **fábrica de dados,** **introduza a CustomActivityFactory** para o nome. O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: **O nome da fábrica de dados "CustomActivityFactory" não está disponível,** altere o nome da fábrica de dados (por exemplo, o seu nome **Desemabrico DepertomActivityFactory)** e tente criar novamente.

    ![Nova lâmina da fábrica de dados Azure](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Clique **em RESOURCE GROUP NAME** e selecione um grupo de recursos existente ou crie um grupo de recursos.
4. Verifique se está a utilizar a **subscrição** e **região** corretas onde pretende que a fábrica de dados seja criada.
5. Clique em **Criar** no painel **Nova fábrica de dados**.
6. Vê-se a fábrica de dados a ser criada no Painel de **Instrumentos** do Portal Azure.
7. Depois de a fábrica de dados ter sido criada com sucesso, vê-se a lâmina da Data Factory, que lhe mostra o conteúdo da fábrica de dados.

    ![Painel Data Factory](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Passo 2: Criar serviços ligados
Os serviços ligados ligam os arquivos de dados ou serviços de computação a um Azure Data Factory. Neste passo, ligue a sua conta de Armazenamento Azure e a conta Azure Batch à sua fábrica de dados.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
1. Clique no **Autor e implante** azulejo na lâmina DATA **FACTORY** para **CustomActivityFactory**. Será apresentado o Editor do Data Factory.
2. Clique em **Nova loja de dados** na barra de comando e escolha **o armazenamento Azure**. Deverá ver o script JSON para criar um serviço ligado do Storage do Azure no editor.

    ![Nova loja de dados - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. `<accountname>`Substitua-a pelo nome da sua conta de armazenamento Azure e `<accountkey>` pela chave de acesso da conta de armazenamento Azure. Para aprender a obter a chave de acesso ao armazenamento, consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md).

    ![Azure Storage gostou do serviço](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="create-azure-batch-linked-service"></a>Criar serviço ligado a Azure Batch
1. No Editor de Fábrica de Dados, clique **em ... Mais** na barra de comando, clique em **New compute** e, em seguida, selecione **Azure Batch** a partir do menu.

    ![Novo cálculo - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Faça as seguintes alterações no script JSON:

   1. Especifique o nome da conta Azure Batch para a propriedade do Nome da **conta.** O **URL** da lâmina da **conta Azure Batch** encontra-se no seguinte formato: `http://accountname.region.batch.azure.com` . Para a propriedade **batchUri** no JSON, você precisa remover `accountname.` do URL e usar o para a propriedade `accountname` `accountName` JSON.
   2. Especifique a chave da conta Azure Batch para a propriedade **AccessKey.**
   3. Especifique o nome da piscina que criou como parte dos pré-requisitos para a propriedade **poolName.** Você também pode especificar o ID da piscina em vez do nome da piscina.
   4. Especifique o Azure Batch URI para a propriedade **batchUri.** Exemplo: `https://westus.batch.azure.com`.
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

       Para a **propriedade poolName,** você também pode especificar o ID da piscina em vez do nome da piscina.

### <a name="step-3-create-datasets"></a>Passo 3: Criar conjuntos de dados
Neste passo, cria conjuntos de dados para representar dados de entrada e saída.

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Editor** da Fábrica de Dados, clique **em ... Mais** na barra de comando, clique em **Novo conjunto de dados** e, em seguida, selecione **o armazenamento de Azure Blob** a partir do menu suspenso.
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

   Cria-se um oleoduto mais tarde nesta passagem com a hora de início: 2016-11-16T00:00:00Z e tempo de fim: 2016-11-16T05:00:00Z. Está programado para produzir dados de hora em hora, pelo que existem cinco fatias de entrada/saída (entre as 00:00:00 -> **00** **05:00:00).**

   A **frequência** e **intervalo** para o conjunto de dados de entrada é definido para **Hora** e **1**, o que significa que a fatia de entrada está disponível a cada hora. Nesta amostra, é o mesmo ficheiro (file.txt) no intputfolder.

   Aqui estão os horários de início de cada fatia, que é representada pela variável do sistema SliceStart no snippet JSON acima.
3. Clique em **Implementar** na barra de ferramentas para criar e implementar o **InputDataset**. Confirme que vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.

#### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
1. No editor da **Data Factory,** **clique... Mais** na barra de comando, clique em **Novo conjunto de dados** e, em seguida, selecione **o armazenamento Azure Blob**.
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

     A localização da saída é **adftutorial/customactivityoutput/** e o nome do ficheiro de saída é yyyy-MM-dd-HH.txt onde yyyy-MM-dd-HH é o ano, mês, data e hora da fatia que está a ser produzida. Consulte [a Referência do Desenvolvedor][adf-developer-reference] para obter mais detalhes.

    Uma bolha de saída/ficheiro é gerada para cada fatia de entrada. Aqui está como um ficheiro de saída é nomeado para cada fatia. Todos os ficheiros de saída são gerados numa pasta de saída: **adftutorial\customactivityout .**

   | Fatia | Hora de início | Ficheiro de saída |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Lembre-se que todos os ficheiros de uma pasta de entrada fazem parte de uma fatia com os tempos de início acima mencionados. Quando esta fatia é processada, a atividade personalizada digitaliza através de cada ficheiro e produz uma linha no ficheiro de saída com o número de ocorrências de termo de pesquisa ("Microsoft"). Se houver três ficheiros na pasta de entrada, existem três linhas no ficheiro de saída para cada fatia horária: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt, etc.
3. Para implementar o **Conjunto De Dados de Saída,** clique em **Implementar** na barra de comando.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Criar e executar um oleoduto que usa a atividade personalizada
1. No Editor de Fábrica de Dados, clique **em ... Mais**, e em seguida, selecione **Novo gasoduto** na barra de comando.
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

   * **A concurrency** está definida para **2** de modo a que duas fatias sejam processadas paralelamente por 2 VMs na piscina Azure Batch.
   * Há uma atividade na secção de atividades e é do tipo: **DotNetActivity**.
   * **O nome** de montagem é definido como o nome do DLL: **MyDotnetActivity.dll**.
   * **O Ponto de Entrada** está definido para **MyDotNetActivityNS.MyDotNetActivity**.
   * **O PackageLinkedService** está definido para **AzureStorageLinkedService** que aponta para o armazenamento de bolhas que contém o ficheiro zip de atividade personalizada. Se estiver a utilizar diferentes contas de Armazenamento Azure para ficheiros de entrada/saída e o ficheiro zip de atividade personalizada, criará outro serviço ligado ao Azure Storage. Este artigo pressupõe que está a utilizar a mesma conta Azure Storage.
   * **O PackageFile** está definido para **o "personalactivitycontainer/MyDotNetActivity.zip**. Está no formato: containerforthezip/nameofthezip.zip.
   * A atividade personalizada toma **o InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade linkedServiceName da atividade personalizada aponta para o **AzureBatchLinkedService,** que diz à Azure Data Factory que a atividade personalizada precisa de ser executada em VMs Azure Batch.
   * **isA propriedade é** definida como **falsa** por padrão. O gasoduto funciona imediatamente neste exemplo porque as fatias começam no passado. Você pode definir esta propriedade para ser verdadeira para parar o oleoduto e reesá-lo de volta para falso para reiniciar.
   * A hora **de início** e **os horários finais** são **de cinco** horas de intervalo e as fatias são produzidas de hora a hora, pelo que cinco fatias são produzidas pelo oleoduto.
3. Para implantar o gasoduto, clique em **Implementar** na barra de comando.

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
1. Na lâmina da Fábrica de Dados no portal Azure, clique no **Diagrama**.

    ![Mosaico do diagrama](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Na visualização do diagrama, clique agora no Conjunto De Dados de Saída.

    ![Vista de diagrama](./media/data-factory-use-custom-activities/diagram.png)
3. Devias ver que as cinco fatias de saída estão no estado de Ready. Se não estão no estado de Ready, ainda não foram produzidos.

   ![Fatias de saída](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Verifique se os ficheiros de saída são gerados no armazenamento do blob no recipiente **adftutorial.**

   ![saída da atividade personalizada][image-data-factory-output-from-custom-activity]
5. Se abrir o ficheiro de saída, deverá ver a saída semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Utilize o [portal Azure][azure-preview-portal] ou os cmdlets Azure PowerShell para monitorizar a sua fábrica de dados, oleodutos e conjuntos de dados. Pode ver mensagens do **ActivityLogger** no código para a atividade personalizada nos registos (especificamente utilizador-0.log) que pode descarregar a partir do portal ou utilizar cmdlets.

   ![baixar registos de atividade personalizada][image-data-factory-download-logs-from-custom-activity]

Consulte [Monitor e Gerencie os gasodutos](data-factory-monitor-manage-pipelines.md) para obter etapas detalhadas para monitorizar conjuntos de dados e oleodutos.

## <a name="data-factory-project-in-visual-studio"></a>Projeto data factory em Estúdio Visual
Pode criar e publicar entidades da Data Factory utilizando o Visual Studio em vez de utilizar o portal Azure. Para obter informações detalhadas sobre a criação e publicação de entidades da Data Factory utilizando o Visual Studio, consulte [o seu primeiro pipeline utilizando](data-factory-build-your-first-pipeline-using-vs.md) dados de Visual Studio e Copy de [artigos Azure Blob para Azure SQL.](data-factory-copy-activity-tutorial-using-visual-studio.md)

Faça os seguintes passos adicionais se estiver a criar o projeto Data Factory no Estúdio Visual:

1. Adicione o projeto Data Factory à solução Visual Studio que contém o projeto de atividade personalizada.
2. Adicione uma referência ao projeto de atividade .NET do projeto Data Factory. Clique no direito do projeto data factory, ponto para **Adicionar** e, em seguida, clique em **Referência**.
3. Na caixa de diálogo **Add Reference,** selecione o projeto **MyDotNetActivity** e clique **em OK**.
4. Construa e publique a solução.

    > [!IMPORTANT]
    > Quando publica entidades da Data Factory, um ficheiro zip é automaticamente criado para si e é enviado para o recipiente blob: customactivitycontainer. Se o recipiente de bolhas não existir, também é criado automaticamente.

## <a name="data-factory-and-batch-integration"></a>Integração da Fábrica de Dados e lote
O serviço Data Factory cria um trabalho em Azure Batch com o nome: **adf-poolname: job-xxx**. Clique **em Jobs** a partir do menu esquerdo.

![Azure Data Factory - Trabalhos em lote](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Uma tarefa é criada para cada execução de atividade de uma fatia. Se houver cinco fatias prontas a serem processadas, são criadas cinco tarefas neste trabalho. Se houver vários nós computacional na piscina do Lote, duas ou mais fatias podem ser executadas em paralelo. Se as tarefas máximas por nó de computação estiverem definidas para > 1, também pode ter mais de uma fatia a funcionar no mesmo cálculo.

![Azure Data Factory - Tarefas de trabalho em lote](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

O diagrama seguinte ilustra a relação entre a Azure Data Factory e as tarefas do Lote.

![Lote de & da Fábrica de Dados](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Falhas na resolução de problemas
A resolução de problemas consiste em algumas técnicas básicas:

1. Se vir o seguinte erro, poderá estar a utilizar um armazenamento de bolhas Hot/Cool em vez de utilizar um armazenamento de bolhas Azure de uso geral. Faça o upload do ficheiro zip para uma **conta de armazenamento Azure de uso geral**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Se vir o seguinte erro, confirme que o nome da classe no ficheiro CS corresponde ao nome especificado para a propriedade **EntryPoint** no pipeline JSON. Na passagem, o nome da classe é: MyDotNetActivity, e o EntryPoint no JSON é: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Se os nomes coincidirem, confirme que todos os binários estão na **pasta** raiz do ficheiro zip. Ou seja, quando abrir o ficheiro zip, deve ver todos os ficheiros na pasta raiz, não em nenhuma sub-pasta.
3. Se a fatia de entrada não estiver definida para **Pronto,** confirme que a estrutura da pasta de entrada está correta e **file.txt** existe nas pastas de entrada.
3. No método **Executar** a sua atividade personalizada, utilize o objeto **IActivityLogger** para registar informações que o ajudem a resolver problemas. As mensagens registadas aparecem nos ficheiros de registo do utilizador (um ou mais ficheiros nomeados: user-0.log, user-1.log, user-2.log, etc.).

   Na lâmina **OutputDataset,** clique na fatia para ver a lâmina **DATA SLICE** para esta fatia. Vês **que a atividade corre** para aquela fatia. Devia ver uma atividade correr para a fatia. Se clicar em Executar na barra de comando, pode iniciar outra atividade para a mesma fatia.

   Quando clica na execução da atividade, vê a lâmina **ACTIVITY RUN DETAILS** com uma lista de ficheiros de registo. Vê mensagens registadas no ficheiro user_0.log. Quando ocorre um erro, vê-se três operações a decorrer porque a contagem de repetição está definida para 3 no pipeline/atividade JSON. Quando clica na execução da atividade, vê os ficheiros de registo que pode rever para resolver o erro.

   Na lista de ficheiros de registo, clique no **utilizador-0.log**. No painel direito encontram-se os resultados da utilização do método **IActivityLogger.Write.** Se não vir todas as mensagens, verifique se tem mais ficheiros de registo nomeados: user_1.log, user_2.log etc. Caso contrário, o código pode ter falhado após a última mensagem registada.

   Além disso, verifique **o sistema-0.log** para quaisquer mensagens de erro do sistema e exceções.
4. Inclua o ficheiro **PDB** no ficheiro zip para que os detalhes do erro tenham informações como **a pilha de chamadas** quando ocorre um erro.
5. Todos os ficheiros no ficheiro zip da atividade personalizada têm de estar no **nível superior** sem subpastas.
6. Certifique-se de que o **conjuntoName** (MyDotNetActivity.dll), **o ponto de entrada**(MyDotNetActivityNS.MyDotNetActivity), o **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e o **packageLinkedService** (deve apontar para o armazenamento de bolhas Azure de **uso geral** que contém o ficheiro zip) estão definidos para valores corretos.
7. Se corrigir um erro e pretender processar de novo o setor, clique com o botão direito do rato no setor no painel **OutputDataset** e clique em **Executar**.
8. Se vir o seguinte erro, está a utilizar o pacote de armazenamento Azure da versão > 4.3.0. O lançador de serviço data factory requer a versão 4.3 do WindowsAzure.Storage. Consulte a secção de [isolamento appdomain](#appdomain-isolation) para uma saliência se tiver de utilizar a versão posterior do conjunto de armazenamento Azure.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Se puder utilizar a versão 4.3.0 do pacote Azure Storage, remova a referência existente ao pacote de armazenamento Azure da versão > 4.3.0. Em seguida, executar o seguinte comando a partir da Consola NuGet Package Manager.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Crie o projeto. Excluir Azure.Conjunto de armazenamento da versão > 4.3.0 da pasta bin\Debug. Crie um ficheiro zip com binários e o ficheiro PDB. Substitua o antigo ficheiro zip por este no recipiente blob (recipiente deactividade personalizada). Reexecutar as fatias que falharam (fatia de clique direito e clique em Executar).
8. A atividade personalizada não utiliza o **ficheiroapp.config** do seu pacote. Portanto, se o seu código ler quaisquer cadeias de ligação do ficheiro de configuração, não funciona em tempo de execução. A melhor prática ao utilizar o Azure Batch é guardar quaisquer segredos num **Azure KeyVault,** usar um principal de serviço baseado em certificados para proteger o **teclado,** e distribuir o certificado para a piscina Azure Batch. A atividade personalizada de .NET pode, então, aceder aos segredos do Cofre de Chaves durante o tempo de execução. Esta solução é uma solução genérica e pode escalar para qualquer tipo de segredo, e não apenas cadeia de conexão.

   Existe uma solução mais fácil (mas não uma boa prática): pode criar um **serviço ligado Azure SQL** com definições de cadeias de ligação, criar um conjunto de dados que utiliza o serviço ligado e acorrentar o conjunto de dados como um conjunto de dados de entrada manequim para a atividade personalizada .NET. Em seguida, pode aceder à cadeia de ligação do serviço ligado no código de atividade personalizado.

## <a name="update-custom-activity"></a>Atualizar atividade personalizada
Se atualizar o código para a atividade personalizada, construa-o e carrehe o ficheiro zip que contém novos binários para o armazenamento do blob.

## <a name="appdomain-isolation"></a>Isolamento de Appdomain
Consulte [a Cross AppDomain Sample](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) que mostra como criar uma atividade personalizada que não esteja limitada às versões de montagem utilizadas pelo lançador data Factory (exemplo: WindowsAzure.Storage v4.3.0, Newtonsoft.Jsem v6.0.x, etc.).

## <a name="access-extended-properties"></a>Acesso a propriedades estendidas
Pode declarar propriedades estendidas na atividade JSON como mostrado na amostra seguinte:

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

No exemplo, existem duas propriedades estendidas: **SliceStart** e **DataFactoryName**. O valor para SliceStart baseia-se na variável do sistema SliceStart. Consulte [as Variáveis do Sistema](data-factory-functions-variables.md) para obter uma lista de variáveis suportadas do sistema. O valor para DataFactoryName é codificado em códigos personalizados para o CustomActivityFactory.

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

## <a name="auto-scaling-of-azure-batch"></a>Auto-dimensionamento do Lote Azure
Também pode criar uma piscina Azure Batch com funcionalidade **de autoescala.** Por exemplo, pode criar um lote azul com 0 VMs dedicados e uma fórmula de autoescala com base no número de tarefas pendentes.

A fórmula da amostra aqui consegue o seguinte comportamento: Quando a piscina é criada inicialmente, começa com 1 VM. $PendingTasks métrica define o número de tarefas em execução + estado ativo (em fila).  A fórmula encontra o número médio de tarefas pendentes nos últimos 180 segundos e define o TargetDedicated em conformidade. Garante que o TargetDedicated nunca vai além dos 25 VMs. Assim, à medida que novas tarefas são submetidas, o pool cresce automaticamente e à medida que as tarefas terminam, os VMs tornam-se gratuitos um a um e o autoscaling encolhe esses VMs. startingNumberOfVMs e maxNumberofVMs podem ser ajustados às suas necessidades.

Fórmula de autoescala:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Consulte [os nós computacional de escala automática numa piscina do Azure Batch](../../batch/batch-automatic-scaling.md) para obter mais detalhes.

Se a piscina estiver a utilizar o [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)predefinido, o serviço Batch pode demorar 15 a 30 minutos a preparar o VM antes de executar a atividade personalizada.  Se a piscina estiver a utilizar um autoScaleEvaluationInterval diferente, o serviço Batch pode demorar automaticamente a Reavaliação + 10 minutos.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Crie uma atividade personalizada utilizando .NET SDK
Na passagem deste artigo, cria-se uma fábrica de dados com um oleoduto que utiliza a atividade personalizada utilizando o portal Azure. O código que se segue mostra-lhe como criar a fábrica de dados utilizando .NET SDK. Pode encontrar mais detalhes sobre a utilização do SDK para criar programáticamente oleodutos na [criação de um oleoduto com atividade de cópia utilizando artigo da API .NET.](data-factory-copy-activity-tutorial-using-dotnet-api.md)

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

## <a name="debug-custom-activity-in-visual-studio"></a>Atividade personalizada de Debug no Estúdio Visual
A [Azure Data Factory -](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) amostra de ambiente local no GitHub inclui uma ferramenta que permite depurar atividades personalizadas .NET dentro do Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Experimente atividades personalizadas no GitHub
| Sample | Que atividade personalizada faz |
| --- | --- |
| [HTTP Downloader de Dados](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Descarrega dados de um http endpoint para Azure Blob Storage usando atividade C# personalizada na Data Factory. |
| [Amostra de Análise de Sentimento do Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Invoca um modelo Azure Machine Learning Studio (clássico) e faz análise de sentimentos, pontuação, previsão etc. |
| [Executar O Script R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Invoca o script R executando RScript.exe no seu cluster HDInsight que já tem R Instalado nele. |
| [Atividade Cross AppDomain .NET](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Utiliza diferentes versões de montagem das utilizadas pelo lançador data factory |
| [Reprocessar um modelo em Serviços de Análise Azure](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Reprocessa um modelo nos Serviços de Análise Azure. |

[batch-net-library]: ../../batch/quick-run-dotnet.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]:../../azure-sql/database/sql-database-paas-overview.md
[batch-get-started]: ../../batch/quick-run-dotnet.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: /previous-versions/azure/mt125880(v=azure.100)
[new-azure-batch-pool]: /previous-versions/azure/mt125936(v=azure.100)
[azure-batch-blog]: /archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png