---
title: Processar conjuntos de grandes escalas usando Data Factory e lote
description: Descreve como processar enormes quantidades de dados em um pipeline Azure Data Factory usando o recurso de processamento paralelo do lote do Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 699aab617e56ab87eb0bd6d6c4ceabf9aac4c4fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438894"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Processar conjuntos de grandes escalas usando Data Factory e lote
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory, que está geralmente disponível. Se você usar a versão atual do serviço de Data Factory, consulte [atividades personalizadas no data Factory](../transform-data-using-dotnet-custom-activity.md).

Este artigo descreve uma arquitetura de uma solução de exemplo que move e processa conjuntos de grandes escalas de forma automática e agendada. Ele também fornece uma explicação de ponta a ponta para implementar a solução usando o Data Factory e o lote do Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo é mais longo do que um artigo típico, pois contém uma explicação de uma solução de exemplo inteira. Se você for novo no lote e Data Factory, poderá aprender sobre esses serviços e como eles funcionam juntos. Se você souber algo sobre os serviços e estiver projetando/arquitetando uma solução, poderá se concentrar na seção de arquitetura do artigo. Se você estiver desenvolvendo um protótipo ou uma solução, talvez queira experimentar as instruções passo a passo no passo a passos. Convidamos seus comentários sobre esse conteúdo e como você o utiliza.

Primeiro, vamos ver como Data Factory e os serviços de lote podem ajudá-lo a processar grandes conjuntos de altos na nuvem.     


## <a name="why-azure-batch"></a>Por que o lote do Azure?
 Você pode usar o lote para executar aplicativos paralelos de grande escala e de HPC (computação de alto desempenho) com eficiência na nuvem. É um serviço de plataforma que agenda o trabalho de computação intensiva para ser executado em uma coleção gerenciada de VMs (máquinas virtuais). Ele pode dimensionar automaticamente os recursos de computação para atender às necessidades de seus trabalhos.

Com o serviço Batch, define os recursos de computação do Azure para executar as aplicações em paralelo e com dimensionamento. Você pode executar trabalhos sob demanda ou agendados. Você não precisa criar, configurar e gerenciar manualmente um cluster HPC, VMs individuais, redes virtuais ou uma infraestrutura complexa de agendamento de tarefas e trabalhos.

 Se você não estiver familiarizado com o lote, os artigos a seguir o ajudarão a entender a arquitetura/implementação da solução descrita neste artigo:   

* [Noções básicas do lote](../../batch/batch-technical-overview.md)
* [Descrição geral da funcionalidade do Batch](../../batch/batch-api-basics.md)

Opcionalmente, para saber mais sobre o lote, consulte [a documentação do lote](https://docs.microsoft.com/azure/batch/).

## <a name="why-azure-data-factory"></a>Porquê o Azure Data Factory?
O Data Factory é um serviço de integração de dados com base na cloud que organiza e automatiza o movimento e a transformação dos dados. Você pode usar Data Factory para criar pipelines de dados gerenciados que movem dados de armazenamentos de dados locais e na nuvem para um armazenamento de dados centralizado. Um exemplo é o armazenamento de BLOBs do Azure. Você pode usar Data Factory para processar/transformar dados usando serviços como o Azure HDInsight e Azure Machine Learning. Você também pode agendar os pipelines de dados para execução de forma programada (por exemplo, por hora, diariamente e semanalmente). Você pode monitorar e gerenciar os pipelines rapidamente para identificar problemas e tomar medidas.

  Se você não estiver familiarizado com Data Factory, os artigos a seguir o ajudarão a entender a arquitetura/implementação da solução descrita neste artigo:  

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Crie seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md)   

Opcionalmente, para saber mais sobre Data Factory, consulte [a documentação do data Factory](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory e lote juntos
Data Factory inclui atividades internas. Por exemplo, a atividade de cópia é usada para copiar/mover dados de um armazenamento de dados de origem para um armazenamento de dados de destino. A atividade hive é usada para processar dados usando clusters Hadoop (HDInsight) no Azure. Para obter uma lista de atividades de transformação com suporte, consulte [atividades de transformação de dados](data-factory-data-transformation-activities.md).

Você também pode criar atividades personalizadas do .NET para mover ou processar dados com sua própria lógica. Você pode executar essas atividades em um cluster HDInsight ou em um pool de VMs do lote. Ao usar o lote, você pode configurar o pool para dimensionamento automático (adicionar ou remover VMs com base na carga de trabalho) com base em uma fórmula fornecida por você.     

## <a name="architecture-of-a-sample-solution"></a>Arquitetura de uma solução de exemplo
  A arquitetura descrita neste artigo destina-se a uma solução simples. Também é relevante para cenários complexos, como a modelagem de risco por serviços financeiros, processamento e renderização de imagens e análise de genoma.

O diagrama ilustra como o Data Factory orquestra a movimentação e o processamento de dados. Ele também mostra como o lote processa os dados de maneira paralela. Baixe e imprima o diagrama para facilitar a referência (11 x 17 polegadas ou o tamanho a3). Para acessar o diagrama para que você possa imprimi-lo, consulte [a orquestração de dados e HPC usando o lote e data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[diagrama de processamento de dados em grande escala ![](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

A lista a seguir fornece as etapas básicas do processo. A solução inclui código e explicações para criar a solução de ponta a ponta.

* **Configure o lote com um pool de nós de computação (VMs).** Você pode especificar o número de nós e o tamanho de cada nó.

* **Crie uma instância de data Factory** configurada com entidades que representam o armazenamento de BLOBs, o serviço de computação em lotes, os dados de entrada/saída e um fluxo de trabalho/pipeline com atividades que movem e transformam dados.

* **Crie uma atividade personalizada do .NET no pipeline de Data Factory.** A atividade é o código do usuário que é executado no pool do lote.

* **Armazene grandes quantidades de dados de entrada como BLOBs no armazenamento do Azure.** Os dados são divididos em fatias lógicas (geralmente por hora).

* **Data Factory copia dados que são processados em paralelo** para o local secundário.

* **Data Factory executa a atividade personalizada usando o pool alocado pelo lote.** Data Factory pode executar atividades simultaneamente. Cada atividade processa uma fatia de dados. Os resultados são armazenados no armazenamento.

* **Data Factory move os resultados finais para um terceiro local,** seja para distribuição por meio de um aplicativo ou para processamento adicional por outras ferramentas.

## <a name="implementation-of-the-sample-solution"></a>Implementação da solução de exemplo
A solução de exemplo é intencionalmente simples. Ele foi projetado para mostrar como usar Data Factory e lote em conjunto para processar conjuntos de os. A solução conta o número de ocorrências do termo de pesquisa "Microsoft" em arquivos de entrada que são organizados em uma série temporal. Em seguida, ele gera a contagem para os arquivos de saída.

**Hora:** Se você estiver familiarizado com os conceitos básicos do Azure, Data Factory e lote e tiver concluído os pré-requisitos a seguir, essa solução levará de uma a duas horas para ser concluída.

### <a name="prerequisites"></a>Pré-requisitos
#### <a name="azure-subscription"></a>Subscrição do Azure
Se você não tiver uma assinatura do Azure, poderá criar uma conta de avaliação gratuita rapidamente. Para obter mais informações, consulte [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Você usa uma conta de armazenamento para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento, consulte [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md). A solução de exemplo usa o armazenamento de BLOBs.

#### <a name="azure-batch-account"></a>Conta do Azure Batch
Crie uma conta do lote usando o [portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [criar e gerenciar uma conta do lote](../../batch/batch-account-create-portal.md). Anote o nome da conta do lote e a chave da conta. Você também pode usar o cmdlet [New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) para criar uma conta do lote. Para obter instruções sobre como usar esse cmdlet, consulte Introdução [aos cmdlets do PowerShell do lote](../../batch/batch-powershell-cmdlets-get-started.md).

A solução de exemplo usa o lote (indiretamente por meio de um pipeline data factory) para processar dados de maneira paralela em um pool de nós de computação (uma coleção gerenciada de VMs).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Pool de máquinas virtuais do lote do Azure
Crie um pool do lote com pelo menos dois nós de computação.

1. Na [portal do Azure](https://portal.azure.com), selecione **procurar** no menu à esquerda e selecione contas do **lote**.

1. Selecione sua conta do lote para abrir a folha **conta do lote** .

1. Selecione o bloco **pools** .

1. Na folha **pools** , selecione o botão **Adicionar** na barra de ferramentas para adicionar um pool.

   a. Insira uma ID para o pool (**ID do pool**). Anote a ID do pool. Você precisará dela quando criar a solução de data factory.

   b. Especifique o **Windows Server 2012 R2** para a configuração **da família do sistema operacional** .

   c. Selecione um **tipo de preço de nó**.

   d. Digite **2** como o valor para a configuração **dedicada de destino** .

   e. Digite **2** como o valor para a configuração **máximo de tarefas por nó** .

   f. Selecione **OK** para criar o pool.

#### <a name="azure-storage-explorer"></a>Explorador do Armazenamento do Azure
Você usa [Gerenciador de armazenamento do Azure 6](https://azurestorageexplorer.codeplex.com/) ou [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (do ClumsyLeaf software) para inspecionar e alterar os dados em seus projetos de armazenamento. Você também pode inspecionar e alterar os dados nos logs de seus aplicativos hospedados na nuvem.

1. Crie um contêiner chamado **MyContainer** com acesso privado (sem acesso anônimo).

1. Se você usar CloudXplorer, crie pastas e subpastas com a seguinte estrutura:

   ![Estrutura de pastas e subpastas](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` e `outputfolder` são pastas de nível superior no `mycontainer`. A pasta `inputfolder` tem subpastas com carimbos de data/hora (AAAA-MM-DD-HH).

   Se você usar Gerenciador de Armazenamento, na próxima etapa, carregará arquivos com os seguintes nomes: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`e assim por diante. Esta etapa cria automaticamente as pastas.

1. Crie um arquivo de texto **File. txt** em seu computador com conteúdo que tenha a palavra-chave **Microsoft**. Um exemplo é "testar atividade personalizada da Microsoft Test personalizada Activity Microsoft".

1. Carregue o arquivo nas seguintes pastas de entrada no armazenamento de BLOBs:

   ![Pastas de entrada](./media/data-factory-data-processing-using-batch/image4.png)

   Se você usar Gerenciador de Armazenamento, carregue o arquivo **File. txt** para **MyContainer**. Selecione **copiar** na barra de ferramentas para criar uma cópia do blob. Na caixa de diálogo **copiar blob** , altere o **nome do blob de destino** para `inputfolder/2015-11-16-00/file.txt`. Repita essa etapa para criar `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`e assim por diante. Essa ação cria automaticamente as pastas.

1. Crie outro contêiner chamado `customactivitycontainer`. Carregue o arquivo zip da atividade personalizada para esse contêiner.

#### <a name="visual-studio"></a>Visual Studio
Instale o Visual Studio 2012 ou posterior para criar a atividade de lote personalizada a ser usada na solução data factory.

### <a name="high-level-steps-to-create-the-solution"></a>Etapas de alto nível para criar a solução
1. Crie uma atividade personalizada que contenha a lógica de processamento de dados.

1. Crie um data factory que usa a atividade personalizada.

### <a name="create-the-custom-activity"></a>Criar a atividade personalizada
A data factory atividade personalizada é o coração desta solução de exemplo. A solução de exemplo usa o lote para executar a atividade personalizada. Para obter informações sobre como desenvolver atividades personalizadas e usá-las em pipelines data factory, consulte [usar atividades personalizadas em um pipeline de data Factory](data-factory-use-custom-activities.md).

Para criar uma atividade personalizada do .NET que você pode usar em um pipeline data factory, você cria um projeto de biblioteca de classes .NET com uma classe que implementa a interface IDotNetActivity. Esta interface tem apenas um método: execute. Aqui está a assinatura do método:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

O método tem alguns componentes principais que você precisa entender:

* O método usa quatro parâmetros:

  * **linkedServices**. Esse parâmetro é uma lista enumerável de serviços vinculados que vinculam fontes de dados de entrada/saída (por exemplo, armazenamento de BLOBs) ao data factory. Neste exemplo, há apenas um serviço vinculado do tipo armazenamento do Azure usado para entrada e saída.
  * **conjuntos**de os. Esse parâmetro é uma lista enumerável de conjuntos de valores. Você pode usar esse parâmetro para obter os locais e esquemas definidos pelos conjuntos de dados de entrada e saída.
  * **atividade**. Esse parâmetro representa a entidade de computação atual. Nesse caso, é um serviço de lote.
  * **logger**. Você pode usar o agente para gravar comentários de depuração que são exibidos como o log de "usuário" para o pipeline.
* O método retorna um dicionário que pode ser usado para encadear atividades personalizadas em conjunto no futuro. Esse recurso ainda não está implementado, portanto, basta retornar um dicionário vazio do método.

#### <a name="procedure-create-the-custom-activity"></a>Procedimento: criar a atividade personalizada
1. Crie um projeto de biblioteca de classes .NET no Visual Studio.

   a. Inicie o Visual Studio 2012/2013/2015.

   b. Selecione **Ficheiro** > **Novo** > **Projeto**.

   c. Expanda **modelos**e selecione **Visual C\#** . Neste tutorial, você usa o C\#, mas pode usar qualquer linguagem .NET para desenvolver a atividade personalizada.

   d. Selecione **biblioteca de classes** na lista de tipos de projeto à direita.

   e. Digite **MyDotNetActivity** para o **nome**.

   f. Selecione **C:\\ADF** para o **local**. Crie a pasta **ADF** se ela não existir.

   g. Selecione **OK** para criar o projeto.

1. Selecione **Tools** (Ferramentas)  > **NuGet Package Manager** (Gestor de Pacotes NuGet)  > **Package Manager Console** (Consola do Gestor de Pacotes).

1. No console do Gerenciador de pacotes, execute o seguinte comando para importar Microsoft. Azure. Management. datafactorings:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importe o pacote NuGet do **armazenamento do Azure** para o projeto. Você precisa desse pacote porque usa a API de armazenamento de BLOBs neste exemplo:

    ```powershell
    Install-Package Az.Storage
    ```
1. Adicione as seguintes diretivas using ao arquivo de origem no projeto:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Altere o nome do namespace para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Altere o nome da classe para **MyDotNetActivity**e derive-a da interface **IDotNetActivity** , conforme mostrado:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implemente (adicione) o método **Execute** da interface **IDotNetActivity** para a classe **MyDotNetActivity** . Copie o código de exemplo a seguir para o método. Para obter uma explicação da lógica usada neste método, consulte a seção [Executar método](#execute-method) .

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // Initialize the continuation token before using it in the do-while loop.
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
    
           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Adicione os seguintes métodos auxiliares à classe. Esses métodos são invocados pelo método **Execute** . Mais importante, o método **Calculate** isola o código que itera em cada blob.

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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
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
    O método GetFolderPath retorna o caminho para a pasta que o conjunto de pontos aponta e o método GetFileName Retorna o nome do blob/arquivo para o qual o conjunto de pontos aponta.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    O método Calculate calcula o número de instâncias da palavra-chave "Microsoft" nos arquivos de entrada (BLOBs na pasta). O termo de pesquisa "Microsoft" é embutido no código.

1. Compile o projeto. Selecione **Compilar** no menu e, em seguida, selecione **Compilar solução**.

1. Inicie o Windows Explorer e vá para a pasta **bin\\Debug** ou **bin\\Release** . A escolha da pasta depende do tipo de compilação.

1. Crie um arquivo zip **MyDotNetActivity. zip** que contenha todos os binários no **\\bin\\depurar** pasta. Talvez você queira incluir o MyDotNetActivity. arquivo **PDB** para obter detalhes adicionais, como o número de linha no código-fonte que causou o problema quando ocorre uma falha.

   ![A lista de pastas bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Carregue **MyDotNetActivity. zip** como um blob para o contêiner de blob `customactivitycontainer` no armazenamento de BLOBs que o serviço vinculado StorageLinkedService no ADFTutorialDataFactory usa. Crie o contêiner de blob `customactivitycontainer` se ele ainda não existir.

#### <a name="execute-method"></a>Método execute
Esta seção fornece mais detalhes sobre o código no método execute.

1. Os membros para iteração por meio da coleção de entrada são encontrados no namespace [Microsoft. WindowsAzure. Storage. blob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) . Para iterar pela coleção de BLOBs, você precisa usar a classe **BlobContinuationToken** . Em essência, você deve usar um loop do-while com o token como o mecanismo para sair do loop. Para obter mais informações, consulte [usar o armazenamento de BLOBs do .net](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Um loop básico é mostrado aqui:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   Para obter mais informações, consulte a documentação do método [ListBlobsSegmented](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobssegmented) .

1. O código para trabalhar com o conjunto de BLOBs é logicamente dentro do loop do-while. No método **Execute** , o loop do-while passa a lista de BLOBs para um método chamado **Calculate**. O método retorna uma variável de cadeia de caracteres chamada **output** , que é o resultado da iteração em todos os BLOBs no segmento.

   Ele retorna o número de ocorrências do termo de pesquisa "Microsoft" no blob passado para o método **Calculate** .

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Depois que o método **Calculate** for concluído, ele deverá ser gravado em um novo BLOB. Para cada conjunto de BLOBs processados, um novo blob pode ser gravado com os resultados. Para gravar em um novo BLOB, primeiro localize o conjunto de resultados de saída.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. O código também chama o método auxiliar **GetFolderPath** para recuperar o caminho da pasta (o nome do contêiner de armazenamento).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   O método GetFolderPath converte o objeto DataSet em um AzureBlobDataSet, que tem uma propriedade chamada FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
1. O código chama o método **GetFileName** para recuperar o nome do arquivo (nome do blob). O código é semelhante ao código anterior que foi usado para obter o caminho da pasta.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
1. O nome do arquivo é gravado pela criação de um objeto URI. O Construtor URI usa a propriedade **BlobEndpoint** para retornar o nome do contêiner. O caminho da pasta e o nome do arquivo são adicionados para construir o URI do blob de saída.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Depois que o nome do arquivo for gravado, você poderá gravar a cadeia de caracteres de saída do método **Calculate** em um novo blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Criar o data factory
Na seção [criar a atividade personalizada](#create-the-custom-activity) , você criou uma atividade personalizada e carregou o arquivo zip com binários e o arquivo PDB em um contêiner de BLOB. Nesta seção, você cria um data factory com um pipeline que usa a atividade personalizada.

O conjunto de dados de entrada para a atividade personalizada representa os BLOBs (arquivos) na pasta de entrada (`mycontainer\\inputfolder`) no armazenamento de BLOBs. O conjunto de resultados de saída para a atividade representa os blobs de saída na pasta de saída (`mycontainer\\outputfolder`) no armazenamento de BLOBs.

Solte um ou mais arquivos nas pastas de entrada:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Por exemplo, descarte um arquivo (File. txt) com o seguinte conteúdo em cada uma das pastas:

```
test custom activity Microsoft test custom activity Microsoft
```

Cada pasta de entrada corresponde a uma fatia no data factory mesmo que a pasta tenha dois ou mais arquivos. Quando cada fatia é processada pelo pipeline, a atividade personalizada é iterada por todos os BLOBs na pasta de entrada para essa fatia.

Você vê cinco arquivos de saída com o mesmo conteúdo. Por exemplo, o arquivo de saída do processamento do arquivo na pasta 2015-11-16-00 tem o seguinte conteúdo:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Se você remover vários arquivos (File. txt, file2. txt, arquivo3. txt) com o mesmo conteúdo na pasta de entrada, você verá o conteúdo a seguir no arquivo de saída. Cada pasta (2015-11-16-00, etc.) corresponde a uma fatia neste exemplo, embora a pasta tenha vários arquivos de entrada.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

O arquivo de saída tem três linhas agora, uma para cada arquivo de entrada (BLOB) na pasta associada à fatia (2015-11-16-00).

Uma tarefa é criada para cada execução de atividade. Neste exemplo, há apenas uma atividade no pipeline. Quando uma fatia é processada pelo pipeline, a atividade personalizada é executada no lote para processar a fatia. Como há cinco fatias (cada fatia pode ter vários BLOBs ou arquivos), cinco tarefas são criadas no lote. Quando uma tarefa é executada no lote, é a atividade personalizada que está em execução.

O instruções a seguir fornece detalhes adicionais.

#### <a name="step-1-create-the-data-factory"></a>Etapa 1: criar o data factory
1. Depois de entrar no [portal do Azure](https://portal.azure.com/), execute as seguintes etapas:

   a. Selecione **novo** no menu à esquerda.

   b. Selecione **dados + análise** na folha **novo** .

   c. Selecione **Data Factory** na folha **análise de dados** .

1. Na folha **novo data Factory** , digite **CustomActivityFactory** para o nome. O nome da fábrica de dados tem de ser globalmente exclusivo. Se você receber o erro "o nome do data Factory CustomActivityFactory não está disponível", altere o nome do data factory. Por exemplo, use yournameCustomActivityFactory e crie a data factory novamente.

1. Selecione **nome do grupo de recursos**e selecione um grupo de recursos existente ou crie um grupo de recursos.

1. Verifique se a assinatura e a região em que você deseja que a data factory seja criada estão corretas.

1. Selecione **criar** na folha **novo data Factory** .

1. O data factory é criado no painel do Portal.

1. Depois que o data factory for criado com êxito, você verá a página **Data Factory** , que mostra o conteúdo da data Factory.

   ![Página do data Factory](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Etapa 2: criar serviços vinculados
Os serviços vinculados vinculam armazenamentos de dados ou serviços de computação a um data factory. Nesta etapa, você vincula sua conta de armazenamento e conta do lote ao seu data factory.

#### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure
1. Selecione o bloco **criar e implantar** na folha **Data Factory** para **CustomActivityFactory**. O editor de Data Factory é exibido.

1. Selecione **novo armazenamento de dados** na barra de comandos e escolha **armazenamento do Azure.** O script JSON que você usa para criar um serviço vinculado de armazenamento no editor é exibido.

   ![Novo armazenamento de dados](./media/data-factory-data-processing-using-batch/image7.png)

1. Substitua o **nome da conta** pelo nome da sua conta de armazenamento. Substitua a **chave da conta** pela chave de acesso da sua conta de armazenamento. Para saber como obter sua chave de acesso de armazenamento, consulte [gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md).

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

   ![Implementar](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Criar um serviço vinculado do lote do Azure
Nesta etapa, você cria um serviço vinculado para sua conta do lote que é usado para executar a data factory atividade personalizada.

1. Selecione **nova computação** na barra de comandos e escolha **lote do Azure.** O script JSON que você usa para criar um serviço vinculado do lote no editor é exibido.

1. No script JSON:

   a. Substitua **nome da conta** pelo nome da sua conta do lote.

   b. Substitua a **chave de acesso** pela chave de acesso da conta do lote.

   c. Insira a ID do pool para a propriedade **PoolName** . Para essa propriedade, você pode especificar o nome do pool ou a ID do pool.

   d. Insira o URI do lote para a propriedade JSON **batchUri** .

      > [!IMPORTANT]
      > A URL da folha **conta do lote** está no seguinte formato: \<AccountName\>. região de\<\>. batch.azure.com. Para a propriedade **batchUri** no script JSON, você precisa remover A88 "AccountName". * * da URL. Um exemplo é `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Folha conta do lote](./media/data-factory-data-processing-using-batch/image9.png)

      Para a propriedade **PoolName** , você também pode especificar a ID do pool em vez do nome do pool.

      > [!NOTE]
      > O serviço de Data Factory não dá suporte a uma opção sob demanda para o lote como faz para o HDInsight. Você pode usar apenas seu próprio pool do lote em um data factory.
      >
      >
   
   e. Especifique **StorageLinkedService** para a propriedade **linkedServiceName** . Você criou esse serviço vinculado na etapa anterior. Esse armazenamento é usado como uma área de preparação para arquivos e logs.

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="step-3-create-datasets"></a>Etapa 3: criar conjuntos de os
Nesta etapa, você cria conjuntos de dados para representar a entrada e a saída.

#### <a name="create-the-input-dataset"></a>Criar o conjunto de dados de entrada
1. No editor de Data Factory, selecione o botão **novo conjunto de novos** na barra de ferramentas. Selecione **armazenamento de BLOBs do Azure** na lista suspensa.

1. Substitua o script JSON no painel direito pelo seguinte trecho JSON:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
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

    Você criará um pipeline mais adiante neste guia de introdução com a hora de início 2015-11-16T00:00:00Z e a hora de término 2015-11-16T05:00:00Z. Ele está agendado para gerar dados por hora, portanto, há cinco fatias de entrada/saída (entre **00**: 00:00-\> **05**: 00:00).

    A **frequência** e o **intervalo** do conjunto de dados de entrada são definidos como **hora** e **1**, o que significa que a fatia de entrada está disponível por hora.

    A hora de início de cada fatia é representada pela variável de sistema **SliceStart** no trecho de JSON anterior. Aqui estão as horas de início para cada fatia.

    | **Slicer** | **Start time** (Hora de início)          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    O **FolderPath** é calculado usando a parte de ano, mês, dia e hora da hora de início da fatia (**SliceStart**). Veja como uma pasta de entrada é mapeada para uma fatia.

    | **Slicer** | **Start time** (Hora de início)          | **Pasta de entrada**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Selecione **implantar** na barra de ferramentas para criar e implantar a tabela **InputDataset** .

#### <a name="create-the-output-dataset"></a>Criar o conjunto de dados de saída
Nesta etapa, você criará outro conjunto de dados do tipo AzureBlob para representar o dado de saída.

1. No editor de Data Factory, selecione o botão **novo conjunto de novos** na barra de ferramentas. Selecione **armazenamento de BLOBs do Azure** na lista suspensa.

1. Substitua o script JSON no painel direito pelo seguinte trecho JSON:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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

    Um blob/arquivo de saída é gerado para cada fatia de entrada. Veja como um arquivo de saída é nomeado para cada fatia. Todos os arquivos de saída são gerados em uma pasta de saída, `mycontainer\\outputfolder`.

    | **Slicer** | **Start time** (Hora de início)          | **Arquivo de saída**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Lembre-se de que todos os arquivos em uma pasta de entrada (por exemplo, 2015-11-16-00) fazem parte de uma fatia com a hora de início 2015-11-16-00. Quando essa fatia é processada, a atividade personalizada examina cada arquivo e produz uma linha no arquivo de saída com o número de ocorrências do termo de pesquisa "Microsoft". Se houver três arquivos na pasta 2015-11-16-00, haverá três linhas no arquivo de saída 2015-11-16-00. txt.

1. Selecione **implantar** na barra de ferramentas para criar e implantar o **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Etapa 4: criar e executar o pipeline com uma atividade personalizada
Nesta etapa, você cria um pipeline com uma atividade, a atividade personalizada que você criou anteriormente.

> [!IMPORTANT]
> Se você não carregou **File. txt** em pastas de entrada no contêiner de BLOBs, faça isso antes de criar o pipeline. A propriedade **IsPaused** é definida como false no JSON do pipeline, portanto, o pipeline é executado imediatamente porque a data de **início** está no passado.
>
>

1. No editor de Data Factory, selecione **novo pipeline** na barra de comandos. Se você não vir o comando, selecione o símbolo de reticências para exibi-lo.

1. Substitua o script JSON no painel direito pelo seguinte trecho JSON:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Tenha em atenção os seguintes pontos:

   * Apenas uma atividade está no pipeline e é do tipo **DotNetActivity**.
   * **AssemblyName** é definido como o nome da dll **MyDotNetActivity. dll**.
   * **EntryPoint** é definido como **MyDotNetActivityNS. MyDotNetActivity**. É basicamente \<namespace\>.\<ClassName\> no seu código.
   * **PackageLinkedService** é definido como **StorageLinkedService**, que aponta para o armazenamento de BLOBs que contém o arquivo zip da atividade personalizada. Se você usar contas de armazenamento diferentes para arquivos de entrada/saída e o arquivo zip da atividade personalizada, será necessário criar outro serviço vinculado de armazenamento. Este artigo pressupõe que você use a mesma conta de armazenamento.
   * O **pacotefile** está definido como **customactivitycontainer/MyDotNetActivity. zip**. Ele está no formato \<containerforthezip\>/\<nameofthezip. zip\>.
   * A atividade personalizada usa **InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade **linkedServiceName** da atividade personalizada aponta para **AzureBatchLinkedService**, que informa data Factory que a atividade personalizada precisa ser executada no lote.
   * A configuração de **simultaneidade** é importante. Se você usar o valor padrão, que é 1, mesmo se você tiver dois ou mais nós de computação no pool do lote, as fatias serão processadas uma após a outra. Portanto, você não está aproveitando a capacidade de processamento paralelo do lote. Se você definir **simultaneidade** como um valor mais alto, digamos 2, isso significa que duas fatias (correspondendo a duas tarefas no lote) podem ser processadas ao mesmo tempo. Nesse caso, ambas as VMs no pool do lote são utilizadas. Defina a propriedade Concurrency adequadamente.
   * Apenas uma tarefa (fatia) é executada em uma VM em qualquer ponto por padrão. Por padrão, **as tarefas máximas por VM** são definidas como 1 para um pool do lote. Como parte dos pré-requisitos, você criou um pool com essa propriedade definida como 2. Portanto, duas fatias data factory podem ser executadas em uma VM ao mesmo tempo.
     - A propriedade **IsPaused** é definida como false por padrão. O pipeline é executado imediatamente neste exemplo porque as fatias começam no passado. Você pode definir essa propriedade como **true** para pausar o pipeline e defini-lo de volta como **false** para reiniciar.
     -   As horas de **início** e de **término** são separadas por cinco horas. As fatias são produzidas por hora, portanto, cinco fatias são produzidas pelo pipeline.

1. Selecione **Implementar** na barra de comandos para implementar o pipeline.

#### <a name="step-5-test-the-pipeline"></a>Etapa 5: testar o pipeline
Nesta etapa, você testará o pipeline soltando os arquivos nas pastas de entrada. Comece testando o pipeline com um arquivo para cada pasta de entrada.

1. Na folha **Data Factory** da portal do Azure, selecione **diagrama**.

   ![Diagrama](./media/data-factory-data-processing-using-batch/image10.png)

1. No modo de exibição de **diagrama** , clique duas vezes no conjunto de dados de entrada **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. A folha **InputDataset** aparece com todas as cinco fatias prontas. Observe a **hora de início da fatia** e a **hora de término da fatia** para cada fatia.

   ![Horas de início e término da fatia de entrada](./media/data-factory-data-processing-using-batch/image12.png)

1. No modo de exibição de **diagrama** , selecione **OutputDataset**.

1. As cinco fatias de saída aparecerão no estado **pronto** se forem produzidas.

   ![Horas de início e término da fatia de saída](./media/data-factory-data-processing-using-batch/image13.png)

1. Use o portal para exibir as tarefas associadas às fatias e veja em qual VM cada fatia foi executada. Para obter mais informações, consulte a seção [Data Factory e integração do lote](#data-factory-and-batch-integration) .

1. Os arquivos de saída aparecem em `mycontainer` em `outputfolder` no armazenamento de BLOBs.

   ![Arquivos de saída no armazenamento](./media/data-factory-data-processing-using-batch/image15.png)

   Cinco arquivos de saída são listados, um para cada fatia de entrada. Cada um dos arquivos de saída tem conteúdo semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   O diagrama a seguir ilustra como as fatias de data factory são mapeadas para as tarefas no lote. Neste exemplo, uma fatia tem apenas uma execução.

   ![Diagrama de mapeamento de fatia](./media/data-factory-data-processing-using-batch/image16.png)

1. Agora, tente com vários arquivos em uma pasta. Crie os arquivos **file2. txt**, **arquivo3. txt**, **Arquivo4. txt**e **arquivo5. txt** com o mesmo conteúdo do arquivo. txt na pasta **2015-11-06-01**.

1. Na pasta de saída, exclua o arquivo de saída **2015-11-16-01. txt**.

1. Na folha **OutputDataset** , clique com o botão direito do mouse na fatia com a **hora de início da fatia** definida como **11/16/2015 01:00:00 am**. Selecione **executar** para executar novamente/reprocessar a fatia. A fatia agora tem cinco arquivos em vez de um arquivo.

    ![Executar](./media/data-factory-data-processing-using-batch/image17.png)

1. Depois que a fatia for executada e seu status estiver **pronto**, verifique o conteúdo no arquivo de saída para esta fatia (**2015-11-16-01. txt**). O arquivo de saída aparece em `mycontainer` em `outputfolder` no armazenamento de BLOBs. Deve haver uma linha para cada arquivo da fatia.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Se você não excluiu o arquivo de saída 2015-11-16-01. txt antes de tentar com cinco arquivos de entrada, verá uma linha da execução da fatia anterior e cinco linhas da execução da fatia atual. Por padrão, o conteúdo é anexado ao arquivo de saída, caso ele já exista.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integração de Data Factory e lote
O serviço de Data Factory cria um trabalho em lote com o nome `adf-poolname:job-xxx`.

![Trabalhos em lotes](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Uma tarefa no trabalho é criada para cada execução de atividade de uma fatia. Se 10 fatias estiverem prontas para serem processadas, 10 tarefas serão criadas no trabalho. Você pode ter mais de uma fatia em execução em paralelo se tiver vários nós de computação no pool. Se o número máximo de tarefas por nó de computação for definido como maior que um, mais de uma fatia poderá ser executada na mesma computação.

Neste exemplo, há cinco fatias, portanto, há cinco tarefas no lote. Com a **simultaneidade** definida como **5** no JSON de pipeline no data Factory e o **máximo de tarefas por VM** definido como **2** no pool do lote com **duas** VMS, as tarefas são executadas rapidamente. (Verifique as horas de início e de término das tarefas.)

Use o portal para exibir o trabalho em lotes e suas tarefas que estão associadas às fatias e veja em qual VM cada fatia foi executada.

![Tarefas do trabalho em lotes](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Depurar o pipeline
A depuração consiste em algumas técnicas básicas.

1. Se a fatia de entrada não estiver definida como **pronto**, confirme se a estrutura da pasta de entrada está correta e se File. txt existe nas pastas de entrada.

   ![Estrutura da pasta de entrada](./media/data-factory-data-processing-using-batch/image3.png)

1. No método **Execute** de sua atividade personalizada, use o objeto **IActivityLogger** para registrar informações que ajudam a solucionar problemas. As mensagens registradas aparecem no arquivo\_0. log do usuário.

   Na folha **OutputDataset** , selecione a fatia para ver a folha **fatia de dados** dessa fatia. Em **execuções de atividade**, você vê uma execução de atividade para a fatia. Se você selecionar **executar** na barra de comandos, poderá iniciar outra execução de atividade para a mesma fatia.

   Ao selecionar a execução da atividade, você verá a folha **detalhes da execução da atividade** com uma lista de arquivos de log. Você vê mensagens registradas no arquivo\_0. log do usuário. Quando ocorrer um erro, você verá três execuções de atividade porque a contagem de repetição é definida como 3 no JSON de atividade/pipeline. Ao selecionar a execução da atividade, você verá os arquivos de log que podem ser examinados para solucionar o erro.

   ![OutputDataset e folhas de fatia de dados](./media/data-factory-data-processing-using-batch/image18.png)

   Na lista de arquivos de log, selecione **User-0. log**. No painel direito, os resultados do uso do método **IActivityLogger. Write** são exibidos.

   ![Folha detalhes da execução da atividade](./media/data-factory-data-processing-using-batch/image19.png)

   Verifique o System-0. log em busca de mensagens de erro e exceções do sistema.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Inclua o arquivo **PDB** no arquivo zip para que os detalhes do erro tenham informações como pilha de chamadas quando ocorrer um erro.

1. Todos os arquivos no arquivo zip para a atividade personalizada devem estar no nível superior sem subpastas.

   ![Lista de arquivos zip de atividade personalizada](./media/data-factory-data-processing-using-batch/image20.png)

1. Verifique se **AssemblyName** (MyDotNetActivity. dll), **EntryPoint** (MyDotNetActivityNS. MyDotNetActivity), **PackageFile** (customactivitycontainer/MyDotNetActivity. zip) e **packageLinkedService** (devem apontar para o armazenamento de BLOBs que contém o arquivo zip) estão definidos com os valores corretos.

1. Se você corrigiu um erro e deseja reprocessar a fatia, clique com o botão direito do mouse na fatia na folha **OutputDataset** e selecione **executar**.

   ![Opção de execução de folha OutputDataset](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Um contêiner está em seu armazenamento de BLOBs chamado `adfjobs`. Esse contêiner não é excluído automaticamente, mas você pode excluí-lo com segurança depois de concluir o teste da solução. Da mesma forma, a solução data factory cria um trabalho em lotes chamado `adf-\<pool ID/name\>:job-0000000001`. Você pode excluir esse trabalho depois de testar a solução, se desejar.
   >
   >
1. A atividade personalizada não usa o arquivo **app. config** do seu pacote. Portanto, se o código ler as cadeias de conexão do arquivo de configuração, ele não funcionará em tempo de execução. A prática recomendada ao usar o lote é manter os segredos em Azure Key Vault. Em seguida, use uma entidade de serviço baseada em certificado para proteger o cofre de chaves e distribuir o certificado para o pool do lote. A atividade personalizada do .NET pode acessar segredos do cofre de chaves em tempo de execução. Essa solução genérica pode ser dimensionada para qualquer tipo de segredo, não apenas uma cadeia de conexão.

    Há uma solução alternativa mais fácil, mas não é uma prática recomendada. Você pode criar um serviço vinculado do banco de dados SQL com configurações de cadeia de conexão. Em seguida, você pode criar um conjunto de dados que usa o serviço vinculado e encadear o conjunto de dados como um conjunto de informações de entrada fictício para a atividade personalizada do .NET. Em seguida, você pode acessar a cadeia de conexão do serviço vinculado no código de atividade personalizado. Ele deve funcionar bem em tempo de execução.  

#### <a name="extend-the-sample"></a>Estender o exemplo
Você pode estender este exemplo para saber mais sobre Data Factory e recursos do lote. Por exemplo, para processar fatias em um intervalo de tempo diferente, execute as seguintes etapas:

1. Adicione as seguintes subpastas em `inputfolder`: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 e 2015-11-16-09. Coloque os arquivos de entrada nessas pastas. Altere a hora de término do pipeline de `2015-11-16T05:00:00Z` para `2015-11-16T10:00:00Z`. No modo de exibição de **diagrama** , clique duas vezes em **InputDataset** e confirme se as fatias de entrada estão prontas. Clique duas vezes em **OutputDataset** para ver o estado das fatias de saída. Se eles estiverem no estado **pronto** , verifique a pasta de saída para os arquivos de saída.

1. Aumente ou diminua a configuração de **simultaneidade** para entender como ela afeta o desempenho da solução, especialmente o processamento que ocorre no lote. Para obter mais informações sobre a configuração de **simultaneidade** , consulte a "etapa 4: criar e executar o pipeline com uma atividade personalizada".

1. Crie um pool com um máximo de tarefas mais altas/inferiores **por VM**. Para usar o novo pool que você criou, atualize o serviço vinculado do lote na solução data factory. Para obter mais informações sobre a configuração **máximo de tarefas por VM** , consulte a "etapa 4: criar e executar o pipeline com uma atividade personalizada".

1. Crie um pool do lote com o recurso de **dimensionamento automático** . O dimensionamento automático de nós de computação em um pool do lote é o ajuste dinâmico da capacidade de processamento usada pelo seu aplicativo. 

    A fórmula de exemplo obtém o comportamento a seguir. Quando o pool é inicialmente criado, ele começa com uma VM. A métrica de $PendingTasks define o número de tarefas nos Estados em execução e ativo (na fila). A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated de acordo. Ele garante que o TargetDedicated nunca vá além de 25 VMs. À medida que novas tarefas são enviadas, o pool aumenta automaticamente. À medida que as tarefas são concluídas, as VMs tornam-se gratuitas uma a uma e o dimensionamento automático reduz essas VMs. Você pode ajustar startingNumberOfVMs e maxNumberofVMs às suas necessidades.
 
    Fórmula de autoescala:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Para obter mais informações, consulte [dimensionar automaticamente nós de computação em um pool do lote](../../batch/batch-automatic-scaling.md).

   Se o pool usar o [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)padrão, o serviço de lote poderá levar de 15 a 30 minutos para preparar a VM antes de executar a atividade personalizada. Se o pool usar um autoScaleEvaluationInterval diferente, o serviço de lote poderá levar autoScaleEvaluationInterval mais 10 minutos.

1. Na solução de exemplo, o método **Execute** invoca o método **Calculate** que processa uma fatia de dados de entrada para produzir uma fatia de dados de saída. Você pode escrever seu próprio método para processar dados de entrada e substituir a chamada do método **Calculate** no método **Execute** por uma chamada para seu método.

### <a name="next-steps-consume-the-data"></a>Próximas etapas: consumir os dados
Depois de processar os dados, você pode consumi-los com ferramentas online, como Power BI. Aqui estão os links para ajudá-lo a entender Power BI e como usá-lo no Azure:

* [Explorar um conjunto de Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Atualizar dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure e Power BI: visão geral básica](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referências
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introdução ao serviço de Data Factory](data-factory-introduction.md)
  * [Introdução ao Data Factory](data-factory-build-your-first-pipeline.md)
  * [Usar atividades personalizadas em um pipeline Data Factory](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Noções básicas do lote](../../batch/batch-technical-overview.md)
  * [Visão geral dos recursos do lote](../../batch/batch-api-basics.md)
  * [Criar e gerenciar uma conta do lote no portal do Azure](../../batch/batch-account-create-portal.md)
  * [Introdução à biblioteca de cliente do lote para .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
