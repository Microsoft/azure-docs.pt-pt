---
title: Processar grandes conjuntos de dados através do Data Factory e lote | Documentos da Microsoft
description: Descreve como processar quantidades enormes de dados num pipeline do Azure Data Factory, utilizando o capacidade do Azure Batch de processamento de paralelo.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f78275af5faaf19a4993a5ae4414b0163f9a4d9d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124155"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Conjuntos de dados em grande escala do processo com o Data Factory e o Batch
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory, que está geralmente disponível. Se utilizar a versão atual do serviço Data Factory, veja [atividades personalizadas no Data Factory](../transform-data-using-dotnet-custom-activity.md).

Este artigo descreve uma arquitetura de uma solução de exemplo que se move e processa grandes conjuntos de dados de maneira automática e agendada. Ele também fornece uma passo a passo-a-ponto para implementar a solução com o Data Factory e o Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo é a mais de um artigo típico porque contém um passo a passo de uma solução de exemplo completo. Se estiver familiarizado com o Batch e o Data Factory, pode aprender sobre estes serviços, e como eles funcionam em conjunto. Se sabe algo sobre os serviços e é design/arquitetar uma solução, que se possa concentrar na seção de arquitetura do artigo. Se estiver desenvolvendo um protótipo ou uma solução, poderá querer experimentar as instruções passo a passo no passo a passo. Convidamos seus comentários sobre este conteúdo e como usá-lo.

Em primeiro lugar, vamos dar uma olhada em como os serviços de fábrica de dados e em lote podem ajudá-lo a processo grandes conjuntos de dados na cloud.     


## <a name="why-azure-batch"></a>Por que o Azure Batch?
 Pode utilizar o Batch para executar aplicações de computação em larga escala paralela e de alto desempenho (HPC) de forma eficaz na cloud. É um serviço de plataforma que agenda trabalho de computação intensivo para ser executado numa coleção gerida de máquinas virtuais (VMs). Pode dimensionar automaticamente recursos de computação para satisfazer as necessidades das suas tarefas.

Com o serviço Batch, define os recursos de computação do Azure para executar as aplicações em paralelo e com dimensionamento. Pode executar a pedido ou as tarefas agendadas. Não precisa de criar, configurar e gerir um cluster HPC, VMs individuais, redes virtuais, ou uma tarefa complexa e infraestrutura de agendamento de tarefas manualmente.

 Se não estiver familiarizado com o Batch, os artigos seguintes ajudá-lo a compreender a arquitetura de implementação da solução descrita neste artigo:   

* [Noções básicas do Batch](../../batch/batch-technical-overview.md)
* [Descrição geral da funcionalidade do Batch](../../batch/batch-api-basics.md)

Opcionalmente, para saber mais sobre o Batch, veja [a documentação do Batch](https://docs.microsoft.com/azure/batch/).

## <a name="why-azure-data-factory"></a>Porquê o Azure Data Factory?
Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o movimento e a transformação de dados. Pode utilizar o Data Factory para criar pipelines de dados geridos que mover os dados no local e na cloud armazenamentos de dados para um arquivo de dados centralizado. Um exemplo é o armazenamento de Blobs do Azure. Pode utilizar o Data Factory para processar/transformar dados com os serviços, como o Azure HDInsight e Azure Machine Learning. Também pode agendar pipelines de dados para executar de forma agendada (por exemplo, hora a hora, diariamente e semanalmente). Pode monitorizar e gerir pipelines de rapidamente identificar problemas e tomar medidas.

  Se não estiver familiarizado com o Data Factory, os artigos seguintes ajudá-lo a compreender a arquitetura de implementação da solução descrita neste artigo:  

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Crie seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md)   

Opcionalmente, para saber mais sobre o Data Factory, veja [a documentação do Data Factory](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory e o Batch em conjunto
Fábrica de dados inclui atividades incorporadas. Por exemplo, a atividade de cópia é utilizada para copiar/mover dados de um arquivo de dados de origem para um arquivo de dados de destino. A atividade de Hive é utilizada para processar dados através de clusters do Hadoop (HDInsight) no Azure. Para obter uma lista de atividades de transformação suportados, consulte [atividades de transformação de dados](data-factory-data-transformation-activities.md).

Também pode criar atividades personalizadas do .NET para mover ou processar dados com a sua própria lógica. Pode executar estas atividades num cluster do HDInsight ou num conjunto do Batch de VMs. Quando utiliza o Batch, pode configurar o conjunto de dimensionamento automático (adicionar ou remover as VMs com base na carga de trabalho) com base numa fórmula que fornecer.     

## <a name="architecture-of-a-sample-solution"></a>Arquitetura de uma solução de exemplo
  A arquitetura descrita neste artigo destina-se uma solução simples. Também é relevante para cenários complexos, tais como modelação de risco ao serviços financeiros, processamento de imagens e processamento e análise de genoma.

O diagrama ilustra como o Data Factory orquestra o movimento de dados e processamento. Ela também mostra como o Batch processa os dados de forma paralela. Transferir e imprimir o diagrama para fácil referência (11 x 17 polegadas ou tamanho A3). Para acessar o diagrama para que pode imprimi-lo, consulte [HPC e orquestração de dados com o Batch e o Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagrama de processamento de dados em grande escala](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

A lista seguinte fornece os passos básicos do processo. A solução inclui código e explicações para compilar a solução ponto-a-ponto.

* **Configure o Batch com um conjunto de nós de computação (VMs).** Pode especificar o número de nós e o tamanho de cada nó.

* **Criar uma instância de fábrica de dados** que está configurado com as entidades que representam o armazenamento de BLOBs, o serviço de computação do Batch, dados de entrada/saída e um pipeline/fluxo de trabalho com atividades que mover e transformar dados.

* **Crie uma atividade .NET personalizada no pipeline do Data Factory.** A atividade é o seu código de utilizador que executa no conjunto do Batch.

* **Store grandes quantidades de dados de entrada como blobs no armazenamento do Azure.** Dados estão divididos em setores lógicos (normalmente por hora).

* **Fábrica de dados copia os dados que são processados em paralelo** para a localização secundária.

* **Fábrica de dados é executada a atividade personalizada usando o pool alocado pelo Batch.** Fábrica de dados pode executar atividades ao mesmo tempo. Cada atividade processa um setor de dados. Os resultados são armazenados no armazenamento.

* **Fábrica de dados move os resultados finais para uma localização de terceiro,** para distribuição por meio de uma aplicação ou para processamento adicional por outras ferramentas.

## <a name="implementation-of-the-sample-solution"></a>Implementação da solução de exemplo
A solução de exemplo é intencionalmente simples. Foi concebido para lhe mostrar como utilizar o Data Factory e o Batch em conjunto para conjuntos de dados do processo. A solução conta o número de ocorrências do termo de pesquisa "Microsoft" nos ficheiros de entrada que estejam organizados numa série de tempo. Em seguida, gera a saída de contagem de ficheiros de saída.

**Hora:** Se estiver familiarizado com os fundamentos básicos do Azure, o Data Factory e o Batch e concluir os seguintes pré-requisitos, esta solução assume uma ou duas horas a concluir.

### <a name="prerequisites"></a>Pré-requisitos
#### <a name="azure-subscription"></a>Subscrição do Azure
Se não tiver uma subscrição do Azure, pode criar rapidamente uma conta de avaliação gratuita. Para obter mais informações, consulte [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Utilize uma conta de armazenamento para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento, consulte [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md). A solução de exemplo utiliza o armazenamento de Blobs.

#### <a name="azure-batch-account"></a>Conta de Batch do Azure
Criar uma conta do Batch com o [portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [criar e gerir uma conta do Batch](../../batch/batch-account-create-portal.md). Tenha em atenção a chave de conta e o nome da conta de Batch. Também pode utilizar o [New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) cmdlet para criar uma conta do Batch. Para obter instruções sobre como utilizar este cmdlet, consulte [introdução aos cmdlets do PowerShell do Batch](../../batch/batch-powershell-cmdlets-get-started.md).

A solução de exemplo utiliza o Batch (indiretamente por meio de um pipeline de fábrica de dados) para processar dados de forma paralela num conjunto de nós de computação (uma coleção gerida de VMs).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Conjunto do Batch do Azure de máquinas virtuais
Crie um conjunto do Batch com, pelo menos, dois nós de computação.

1. Na [portal do Azure](https://portal.azure.com), selecione **procurar** no menu à esquerda e selecione **contas do Batch**.

1. Selecione a sua conta do Batch para abrir o **conta do Batch** painel.

1. Selecione o **conjuntos** mosaico.

1. Na **agrupamentos** painel, selecione a **adicionar** botão na barra de ferramentas para adicionar um conjunto.

   a. Introduza um ID para o conjunto (**ID do conjunto**). Tenha em atenção o ID do conjunto. Irá precisar dele quando criar a solução de fábrica de dados.

   b. Especifique **Windows Server 2012 R2** para o **família do sistema operativo** definição.

   c. Selecione um **escalão de preço de nó**.

   d. Introduza **2** como o valor para o **dedicados de destino** definição.

   e. Introduza **2** como o valor para o **máx. tarefas por nó** definição.

   f. Selecione **OK** para criar o conjunto.

#### <a name="azure-storage-explorer"></a>Explorador do Storage do Azure
Utilizar [6 de Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) ou [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (a partir de ClumsyLeaf Software) para inspecionar e alterar os dados em seus projetos de armazenamento. Também pode inspecionar e alterar os dados nos registos das suas aplicações alojadas na cloud.

1. Criar um contentor com o nome **mycontainer** com acesso privado (sem acesso anónimo).

1. Se usar CloudXplorer, crie pastas e subpastas com a seguinte estrutura:

   ![Estrutura de pasta e subpasta](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` e `outputfolder` são as pastas de nível superior no `mycontainer`. O `inputfolder` pasta contém subpastas com carimbos de data / hora (AAAA-MM-DD-HH).

   Se utilizar o Explorador de armazenamento, no próximo passo, carregar ficheiros com os seguintes nomes: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`e assim por diante. Este passo cria automaticamente as pastas.

1. Criar um arquivo de texto **file.txt** no seu computador com o conteúdo que tenha a palavra-chave **Microsoft**. Um exemplo é "atividade personalizada do atividade personalizada Microsoft teste Microsoft de teste".

1. Carregar o ficheiro para as seguintes pastas de entrada no armazenamento de BLOBs:

   ![Pastas de entrada](./media/data-factory-data-processing-using-batch/image4.png)

   Se utilizar o Explorador de armazenamento, carregue o **file.txt** do ficheiro para **mycontainer**. Selecione **cópia** na barra de ferramentas para criar uma cópia do blob. Na **Blob de cópia** caixa de diálogo, altere a **nome do blob de destino** para `inputfolder/2015-11-16-00/file.txt`. Repita este passo para criar `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`e assim por diante. Esta ação cria automaticamente as pastas.

1. Criar outro contentor com o nome `customactivitycontainer`. Carregar o ficheiro de zip da atividade personalizada para este contentor.

#### <a name="visual-studio"></a>Visual Studio
Instale o Visual Studio 2012 ou posterior para criar a atividade de lote personalizada a ser utilizado na solução de fábrica de dados.

### <a name="high-level-steps-to-create-the-solution"></a>Passos de alto nível para criar a solução
1. Crie uma atividade personalizada que contém a lógica de processamento de dados.

1. Crie uma fábrica de dados que utiliza a atividade personalizada.

### <a name="create-the-custom-activity"></a>Criar a atividade personalizada
A atividade personalizada da fábrica de dados é o coração desta solução de exemplo. A solução de exemplo utiliza o Batch para executar a atividade personalizada. Para obter informações sobre como desenvolver atividades personalizadas e usá-los em pipelines da fábrica de dados, consulte [utilizar atividades personalizadas num pipeline de fábrica de dados](data-factory-use-custom-activities.md).

Para criar uma atividade personalizada .NET, que podem ser usados num pipeline de fábrica de dados, vai criar um projeto de biblioteca de classes do .NET com uma classe que implementa a interface IDotNetActivity. Essa interface possui apenas um método: Execute. Esta é a assinatura do método:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

O método tem alguns componentes principais que precisa entender:

* O método assume quatro parâmetros:

  * **linkedServices**. Este parâmetro é uma lista enumerável de serviços ligados que ligar a origens de dados de entrada/saída (por exemplo, o armazenamento de BLOBs) à fábrica de dados. Neste exemplo, há apenas um serviço ligado do tipo armazenamento do Azure utilizado para entrada e saída.
  * **conjuntos de dados**. Este parâmetro é uma lista enumerável de conjuntos de dados. Pode utilizar este parâmetro para obter as localizações e os esquemas definidos por conjuntos de dados de entrada e saídos.
  * **atividade**. Este parâmetro representa a entidade de computação atual. Neste caso, é um serviço de Batch.
  * **logger**. Pode utilizar o agente de log para escrever comentários de depuração essa superfície como o registo de "Usuário" para o pipeline.
* O método retorna um dicionário que pode ser utilizado para encadear atividades personalizadas em conjunto no futuro. Esta funcionalidade ainda não está implementada, então basta retornar um dicionário vazio do método.

#### <a name="procedure-create-the-custom-activity"></a>Procedimento: Criar a atividade personalizada
1. Crie um projeto de biblioteca de classes do .NET no Visual Studio.

   a. Inicie o Visual Studio 2012/2013/2015.

   b. Selecione **Ficheiro** > **Novo** > **Projeto**.

   c. Expanda **modelos**e selecione **Visual C#\#**. Este passo a passo, vai utilizar o C\#, mas pode usar qualquer linguagem .NET para desenvolver a atividade personalizada.

   d. Selecione **biblioteca de classes** na lista de tipos de projeto à direita.

   e. Introduza **MyDotNetActivity** para o **nome**.

   f. Selecione **c:\\ADF** para o **localização**. Crie a pasta **ADF** se não existir.

   g. Selecione **OK** para criar o projeto.

1. Selecione **ferramentas** > **Gestor de pacotes NuGet** > **Package Manager Console**.

1. Na consola do Gestor de pacotes, execute o seguinte comando para importar Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importar os **armazenamento do Azure** pacote NuGet no projeto. Este pacote é necessário uma vez que usar a API de armazenamento de BLOBs neste exemplo:

    ```powershell
    Install-Package Az.Storage
    ```
1. Adicione o seguinte usando diretivas para o ficheiro de origem no projeto:

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
1. Altere o nome do espaço de nomes para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Altere o nome da classe para **MyDotNetActivity**e derivá-lo a partir do **IDotNetActivity** interface conforme mostrado:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementar (adicionar) a **Execute** método o **IDotNetActivity** interface para o **MyDotNetActivity** classe. Copie o seguinte código de exemplo para o método. Para obter uma explicação sobre a lógica usada nesse método, consulte a [executar o método](#execute-method) secção.

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
1. Adicione os seguintes métodos auxiliares à classe. Esses métodos são chamados pela **Execute** método. Mais importante, o **Calculate** método isola o código que itera através de cada blob.

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
    O método GetFolderPath retorna o caminho para a pasta que o conjunto de dados aponta para e o método GetFileName devolve o nome do que o conjunto de dados aponta para ficheiro/blob.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    O método de Calculate calcula o número de instâncias da palavra-chave "Microsoft" nos ficheiros de entrada (blobs na pasta). O termo de pesquisa "Microsoft" está embutido no código.

1. Compile o projeto. Selecione **crie** no menu e, em seguida, selecione **compilar solução**.

1. Iniciar o Explorador do Windows e vá para o **bin\\depurar** ou **bin\\versão** pasta. A escolha de pasta depende do tipo de compilação.

1. Criar um ficheiro zip **MyDotNetActivity.zip** que contém todos os binários no  **\\bin\\depurar** pasta. Pode querer incluir o MyDotNetActivity. **pdb** para que obtenham detalhes adicionais, como o número de linha no código-fonte que causou o problema quando ocorre uma falha de ficheiros.

   ![Lista de pastas de bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Carregue **MyDotNetActivity.zip** como um blob para o contentor de BLOBs `customactivitycontainer` no armazenamento de BLOBs que o StorageLinkedService ligada serviço no ADFTutorialDataFactory utiliza. Criar o contentor de BLOBs `customactivitycontainer` se ainda não exista.

#### <a name="execute-method"></a>Executar o método
Esta seção fornece mais detalhes sobre o código no método Execute.

1. Os membros para iterar por meio da coleção de entrada são encontrados no [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) espaço de nomes. Para iterar na coleção de BLOBs, tem de utilizar o **BlobContinuationToken** classe. Em essência, tem de utilizar um fazer-loop com o token de como o mecanismo para sair do loop while. Para obter mais informações, consulte [armazenamento de BLOBs de utilização do .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Um loop básico é mostrado aqui:

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
   Para obter mais informações, consulte a documentação para o [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) método.

1. O código para o trabalho por meio de conjunto de blobs logicamente vai dentro do fazer-while loop. Na **Execute** método, a fazer-enquanto loop passa a lista de blobs para um método chamado **Calculate**. O método retorna uma variável de cadeia de caracteres chamada **saída** ou seja, o resultado de ter iteração de todos os blobs no segmento.

   Devolve o número de ocorrências do termo de pesquisa "Microsoft" no blob passado para o **Calculate** método.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Depois do **Calculate** método termina, ele deve ser escrito para um blob de novo. Para cada conjunto de blobs processados, um novo blob pode ser escrito com os resultados. Para escrever para um blob novo, primeiro localize o conjunto de dados de saída.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. O código também chama o método auxiliar **GetFolderPath** para obter o caminho da pasta (o nome do contentor de armazenamento).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   O método GetFolderPath converte o objeto de conjunto de dados para um AzureBlobDataSet, que tem uma propriedade chamada FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
1. O código chama o **GetFileName** método para recuperar o nome de ficheiro (nome do blob). O código é semelhante ao código anterior que foi utilizado para obter o caminho da pasta.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
1. O nome do ficheiro é gravado através da criação de um objeto URI. O construtor URI usa o **BlobEndpoint** propriedade para retornar o nome do contentor. O nome de ficheiro e caminho de pasta são adicionadas para construir o URI de blob de saída.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Após o nome do ficheiro é escrito, pode escrever a cadeia de saída a partir do **Calculate** método para um blob novo:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Criar a fábrica de dados
Na [criar a atividade personalizada](#create-the-custom-activity) secção, criou uma atividade personalizada e carregou o ficheiro zip com binários e o arquivo PDB para um contentor de Blobs. Nesta secção, vai criar uma fábrica de dados com um pipeline que utiliza a atividade personalizada.

O conjunto de dados de entrada para a atividade personalizada representa os blobs (ficheiros) na pasta de entrada (`mycontainer\\inputfolder`) no armazenamento de Blobs. O conjunto de dados de saída para a atividade representa os blobs de saída na pasta de saída (`mycontainer\\outputfolder`) no armazenamento de Blobs.

Remova um ou mais ficheiros em pastas de entrada:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Por exemplo, solte um arquivo (file.txt) com o seguinte conteúdo para cada uma das pastas:

```
test custom activity Microsoft test custom activity Microsoft
```

Cada pasta de entrada corresponde a um setor na fábrica de dados, mesmo que a pasta tem dois ou mais arquivos. Quando cada setor é processado pelo pipeline, a atividade personalizada itera em todos os blobs na pasta de entrada para essas fatias.

Ver cinco ficheiros de saída com o mesmo conteúdo. Por exemplo, o ficheiro de saída de processar o ficheiro na pasta 2015-11-16-00 tem o seguinte conteúdo:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Se remover vários ficheiros (file.txt, file2.txt, file3.txt) com o mesmo conteúdo para a pasta de entrada, verá o seguinte conteúdo no arquivo de saída. Cada pasta (2015-11-16-00, etc.) corresponde a um setor neste exemplo, apesar da pasta tem múltiplos ficheiros de entrada.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

O ficheiro de saída tem três linhas agora, um para cada ficheiro de entrada (blob) na pasta associada com o setor (2015-11-16-00).

Uma tarefa é criada para cada execução de atividade. Neste exemplo, há apenas uma atividade no pipeline. Quando é processado um setor pelo pipeline, a atividade personalizada é executado no Batch para processar o setor. Como há cinco setores (cada setor pode ter vários blobs ou arquivo), são criadas cinco tarefas no Batch. Quando uma tarefa é executada no Batch, é a atividade personalizada que está a executar.

A instrução a seguir fornece detalhes adicionais.

#### <a name="step-1-create-the-data-factory"></a>Passo 1: Criar a fábrica de dados
1. Depois de iniciar sessão para o [portal do Azure](https://portal.azure.com/), siga os passos seguintes:

   a. Selecione **NEW** no menu da esquerda.

   b. Selecione **dados + análise** sobre o **New** painel.

   c. Selecione **Data Factory** sobre o **análise de dados** painel.

1. Sobre o **nova fábrica de dados** painel, introduza **CustomActivityFactory** para o nome. O nome da fábrica de dados tem de ser globalmente exclusivo. Se receber o erro "o nome da fábrica de dados CustomActivityFactory não está disponível", altere o nome da fábrica de dados. Por exemplo, utilize yournameCustomActivityFactory e volte a criar a fábrica de dados.

1. Selecione **nome do grupo de recursos**e selecione um grupo de recursos existente ou crie um grupo de recursos.

1. Certifique-se de que a subscrição e região onde pretende que a fábrica de dados a ser criado estão corretos.

1. Selecione **Create** sobre o **nova fábrica de dados** painel.

1. É criado a fábrica de dados no dashboard do portal.

1. Depois da fábrica de dados é criada com êxito, verá os **fábrica de dados** página, que mostra o conteúdo da fábrica de dados.

   ![Página da fábrica de dados](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Passo 2: Criar serviços ligados
Serviços ligados ligam os arquivos de dados ou serviços de computação à fábrica de dados. Neste passo, vai ligar a sua conta de armazenamento e a conta do Batch para a fábrica de dados.

#### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure
1. Selecione o **autor e implementar** mosaico a **fábrica de dados** painel **CustomActivityFactory**. É apresentado o Editor do Data Factory.

1. Selecione **novo arquivo de dados** na barra de comandos e escolha **armazenamento do Azure.** O script JSON, que vai utilizar para criar um serviço ligado no editor aparece de armazenamento.

   ![Novo arquivo de dados](./media/data-factory-data-processing-using-batch/image7.png)

1. Substitua o **nome da conta** pelo nome da sua conta de armazenamento. Substitua a **chave da conta** pela chave de acesso da sua conta de armazenamento. Para saber como obter a chave de acesso de armazenamento, veja [ver, copiar e voltar a gerar armazenamento de chaves de acesso](../../storage/common/storage-account-manage.md#access-keys).

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

   ![Implementação](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Criar um serviço ligado do Azure Batch
Neste passo, vai criar um serviço ligado para a sua conta do Batch que é utilizada para executar a atividade personalizada da fábrica de dados.

1. Selecione **nova computação** na barra de comandos e escolha **do Azure Batch.** O script JSON, que vai utilizar para criar um lote de serviço ligado no editor será exibido.

1. No script de JSON:

   a. Substitua **nome da conta** com o nome da sua conta do Batch.

   b. Substitua **chave de acesso** com a chave de acesso da conta do Batch.

   c. Introduza o ID do conjunto para o **poolName** propriedade. Para esta propriedade, pode especificar o nome do conjunto ou o ID do conjunto.

   d. Introduza o URI do batch para o **batchUri** propriedade JSON.

      > [!IMPORTANT]
      > O URL a partir da **conta do Batch** painel é no seguinte formato: \<accountname\>.\< região\>. batch.azure.com. Para o **batchUri** propriedade no script de JSON, terá de remover a88 "accountname." * * da URL. Um exemplo é `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Painel conta do batch](./media/data-factory-data-processing-using-batch/image9.png)

      Para o **poolName** propriedade, também pode especificar o ID do conjunto em vez do nome do conjunto.

      > [!NOTE]
      > O serviço Data Factory não suporta uma opção de sob demanda para o Batch, como o faz para HDInsight. Pode usar apenas seu próprio conjunto do Batch numa fábrica de dados.
      >
      >
   
   e. Especifique **StorageLinkedService** para o **linkedServiceName** propriedade. Criou este serviço ligado no passo anterior. Este armazenamento é utilizado como uma área de teste para ficheiros e registos.

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="step-3-create-datasets"></a>Passo 3: Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de entrada e saídos.

#### <a name="create-the-input-dataset"></a>Criar o conjunto de dados de entrada
1. No Editor do Data Factory, selecione o **novo conjunto de dados** botão na barra de ferramentas. Selecione **armazenamento de Blobs do Azure** na lista pendente.

1. Substitua o script JSON no painel direito pelo seguinte fragmento JSON:

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

    Irá criar um pipeline mais tarde nestas instruções com a hora de início de 2015-11-16T00:00:00Z e final tempo 2015-11-16T05:00:00Z. Está agendado para produzir os dados por hora, pelo que existem cinco setores de entrada/saída (entre **00**: 00:00 -\> **05**: 00:00).

    O **frequência** e **intervalo** para o conjunto de dados de entrada estiver definido como **hora** e **1**, que significa que o setor de entrada está disponível uma vez por hora.

    A hora de início de cada setor é representada pela **SliceStart** variável do sistema no fragmento JSON anterior. Aqui estão as horas de início de cada setor.

    | **Setor** | **Start time** (Hora de início)          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    O **folderPath** é calculado utilizando a parte ano, mês, dia e hora a hora de início do setor (**SliceStart**). Eis como uma pasta de entrada está mapeada para um setor.

    | **Setor** | **Start time** (Hora de início)          | **Pasta de entrada**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Selecione **Deploy** na barra de ferramentas para criar e implementar a **InputDataset** tabela.

#### <a name="create-the-output-dataset"></a>Criar o conjunto de dados de saída
Neste passo, vai criar outro conjunto de dados do tipo AzureBlob para representar os dados de saída.

1. No Editor do Data Factory, selecione o **novo conjunto de dados** botão na barra de ferramentas. Selecione **armazenamento de Blobs do Azure** na lista pendente.

1. Substitua o script JSON no painel direito pelo seguinte fragmento JSON:

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

    Um ficheiro/blob de saída é gerado para cada setor de entrada. Eis como um ficheiro de saída com o nome de cada setor. Todos os ficheiros de saída são gerados numa pasta de saída, `mycontainer\\outputfolder`.

    | **Setor** | **Start time** (Hora de início)          | **Ficheiro de saída**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Lembre-se de que todos os arquivos numa pasta de entrada (por exemplo, 2015-11-16-00) fazem parte de um setor com a hora de início de 2015-11-16-00. Quando esse setor é processado, a atividade personalizada examina cada arquivo e produz uma linha no arquivo de saída com o número de ocorrências do termo de pesquisa "Microsoft". Se existirem três arquivos na pasta 2015-11-16-00, existem três linhas da saída ficheiro 2015-11-16-00.txt.

1. Selecione **Deploy** na barra de ferramentas para criar e implementar a **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Passo 4: Criar e executar o pipeline com uma atividade personalizada
Neste passo, vai criar um pipeline com uma atividade, a atividade personalizada que criou anteriormente.

> [!IMPORTANT]
> Se ainda não carregou **file.txt** para pastas no contentor de BLOBs de entrada, fazê-lo antes de criar o pipeline. O **isPaused** estiver definida como false no pipeline JSON, para que o pipeline é executado imediatamente porque o **iniciar** data está no passado.
>
>

1. No Editor do Data Factory, selecione **novo pipeline** na barra de comandos. Se não vir o comando, selecione o símbolo de reticências para apresentá-la.

1. Substitua o script JSON no painel direito pelo seguinte fragmento JSON:

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

   * Apenas uma atividade no pipeline, e é do tipo **DotNetActivity**.
   * **AssemblyName** está definido como o nome da DLL **mydotnetactivity. dll**.
   * **Ponto de entrada** está definido como **mydotnetactivityns. Mydotnetactivity**. Ele é basicamente \<espaço de nomes\>.\< ClassName\> em seu código.
   * **PackageLinkedService** está definido como **StorageLinkedService**, que aponta para o armazenamento de BLOBs que contém o ficheiro de zip da atividade personalizada. Se utilizar contas de armazenamento diferentes para ficheiros de entrada/saída e o ficheiro de zip da atividade personalizada, terá de criar outro serviço ligado do armazenamento. Este artigo pressupõe que usar a mesma conta de armazenamento.
   * **PackageFile** está definido como **customactivitycontainer**. Ele está no formato \<containerforthezip\>/\<nameofthezip.zip\>.
   * A atividade personalizada obtém **InputDataset** como entrada e **OutputDataset** como saída.
   * O **linkedServiceName** propriedade da atividade personalizada, aponte para **AzureBatchLinkedService**, que informa a fábrica de dados que a atividade personalizada precisa ser executado no Batch.
   * O **simultaneidade** definição é importante. Se utilizar o valor predefinido, que é 1, mesmo se tiver dois ou mais nós de computação no conjunto do Batch, os setores são processados um após o outro. Por conseguinte, não está aproveitando o capacidade de lote de processamento de paralelo. Se definir **simultaneidade** para um valor mais alto, digamos que a 2, isso significa que dois reparte (corresponde à duas tarefas no Batch) podem ser processados ao mesmo tempo. Neste caso, ambas as VMs no conjunto do Batch são utilizadas. Defina a propriedade de simultaneidade adequadamente.
   * Por predefinição, apenas uma tarefa (fatia) é executada numa VM, a qualquer momento. Por predefinição, **tarefas de máximo por VM** está definido como 1 para um conjunto do Batch. Como parte dos pré-requisitos, criou um conjunto com esta propriedade definida como 2. Desta forma, os setores de fábrica de dados de dois podem executar numa VM ao mesmo tempo.
     - O **isPaused** propriedade é definida como false por padrão. O pipeline executa imediatamente neste exemplo, uma vez que os setores de início no passado. Pode definir esta propriedade **true** colocar em pausa o pipeline e o conjunto de volta ao **falso** para reiniciar.
     -   O **começar** e **final** tempos são cinco horas de distância. Setores são produzidos de hora a hora, para que os setores de cinco são produzidos pelo pipeline.

1. Selecione **Implementar** na barra de comandos para implementar o pipeline.

#### <a name="step-5-test-the-pipeline"></a>Passo 5: Testar o pipeline
Neste passo, vai testar o pipeline soltando ficheiros em pastas de entrada. Comece por testar o pipeline com um ficheiro para cada pasta de entrada.

1. Sobre o **fábrica de dados** painel no portal do Azure, selecione **diagrama**.

   ![Diagrama](./media/data-factory-data-processing-using-batch/image10.png)

1. Na **diagrama** ver, faça duplo clique no conjunto de dados de entrada **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. O **InputDataset** é apresentado o painel com todos os setores de cinco prontos. Observe que o **hora de início do SETOR** e **hora de fim do SETOR** para cada setor.

   ![Início do setor de entrada e de fim vezes](./media/data-factory-data-processing-using-batch/image12.png)

1. Na **diagrama** visualizar, selecione **OutputDataset**.

1. Os setores de cinco saída aparecem na **pronto** se foram produzidas de estado.

   ![Início do setor de saída e tempos de fim](./media/data-factory-data-processing-using-batch/image13.png)

1. Utilizar o portal para ver as tarefas associadas os setores e ver que VM de cada setor foi executado em. Para obter mais informações, consulte a [integração do Data Factory e Batch](#data-factory-and-batch-integration) secção.

1. Os ficheiros de saída são apresentados em `mycontainer` em `outputfolder` no armazenamento de Blobs.

   ![Ficheiros de saída no armazenamento](./media/data-factory-data-processing-using-batch/image15.png)

   Cinco ficheiros de saída são listados, um para cada setor de entrada. Cada um dos ficheiros de saída tem conteúdo semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   O diagrama seguinte ilustra como o mapeamento do setor da fábrica de dados para tarefas no Batch. Neste exemplo, um setor tem apenas uma execução.

   ![Diagrama de mapeamento do setor](./media/data-factory-data-processing-using-batch/image16.png)

1. Experimente agora o com vários ficheiros numa pasta. Criar os arquivos **file2.txt**, **file3.txt**, **file4.txt**, e **file5.txt** com o mesmo conteúdo que file.txt na pasta **2015-11-06-01**.

1. Na pasta de saída, elimine o ficheiro de saída **2015-11-16-01.txt**.

1. Sobre o **OutputDataset** painel, com o setor com o botão direito **hora de início do SETOR** definido como **11/16/2015 01:00:00 AM**. Selecione **executar** para voltar a executar/reprocessamento o setor. Agora, o setor tem cinco ficheiros em vez de um ficheiro.

    ![Executar](./media/data-factory-data-processing-using-batch/image17.png)

1. Depois do setor é executado e o respetivo estado é **pronto**, verifique se o conteúdo do ficheiro de saída neste setor (**2015-11-16-01.txt**). O ficheiro de saída é apresentado em `mycontainer` em `outputfolder` no armazenamento de Blobs. Deve haver uma linha para cada ficheiro do setor.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Se não eliminar a saída ficheiro 2015-11-16-01.txt antes de tentar com cinco ficheiros de entrada, verá uma linha da execução anterior do setor e cinco linhas de execução de setor atual. Por predefinição, o conteúdo será acrescentado para o ficheiro de saída se já existir.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integração do Data Factory e o Batch
O serviço Data Factory cria uma tarefa no Batch com o nome `adf-poolname:job-xxx`.

![Tarefas de lote](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

É criada uma tarefa no trabalho para cada execução de atividade de um setor. Se 10 setores estiver prontos para ser processado, são criadas 10 tarefas no trabalho. Pode ter mais do que um setor em execução em paralelo, se tiver vários nós de computação no conjunto. Se o número máximo de tarefas por nó de computação é definido como maior que um, pode executar mais de um setor na mesma computação.

Neste exemplo, existem cinco setores, pelo que existem cinco tarefas no Batch. Com o **simultaneidade** definida como **5** no pipeline JSON da fábrica de dados e **tarefas de máximo por VM** definido como **2** no conjunto do Batch com **2** VMs, as tarefas são executadas rapidamente. (Verifique as horas de início e de fim para tarefas).

Utilizar o portal para ver a tarefa de lote e as respetivas tarefas associadas os setores e ver que VM de cada setor foi executado em.

![Tarefas de trabalho do batch](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Depurar o pipeline
A depuração inclui algumas técnicas básicas.

1. Se o setor de entrada não está definido como **pronto**, confirme que a estrutura de pastas de entrada está correta e que file.txt existe nas pastas de entrada.

   ![Estrutura de pastas de entrada](./media/data-factory-data-processing-using-batch/image3.png)

1. Na **Execute** método de sua atividade personalizada, utilize o **IActivityLogger** objeto para registar informações que o ajuda a resolver problemas. As mensagens anteriormente registadas apresentados ao utilizador\_0. ficheiro de registo.

   Sobre o **OutputDataset** painel, selecione o setor para ver a **setor de dados** painel para essas fatias. Sob **execuções de atividades**, verá uma execução para o setor de atividade. Se selecionou **executar** na barra de comandos, pode começar a executar o setor mesmo de outra atividade.

   Quando seleciona a execução da atividade, consulte a **detalhes da execução da atividade** painel com uma lista de ficheiros de registo. Verá as mensagens anteriormente registadas no utilizador\_0. ficheiro de registo. Quando ocorre um erro, verá três execuções de atividades porque a contagem de repetições está definida como 3 no pipeline/JSON de atividade. Quando seleciona a execução de atividade, verá os ficheiros de registo que poderá consultar para resolver o erro.

   ![Painéis de setor de dados e OutputDataset](./media/data-factory-data-processing-using-batch/image18.png)

   Na lista de ficheiros de registo, selecione **user-0.log**. No painel à direita, os resultados de utilizar o **IActivityLogger.Write** método são apresentados.

   ![Painel de detalhes de execução da atividade](./media/data-factory-data-processing-using-batch/image19.png)

   Verifique o sistema-0.log para quaisquer mensagens de erro do sistema e exceções.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Incluir o **PDB** no ficheiro zip do ficheiro para que os detalhes do erro tenham informações como a pilha de chamadas quando ocorre um erro.

1. Todos os ficheiros no ficheiro zip para a atividade personalizada têm de estar no nível superior com sem subpastas.

   ![Lista de ficheiros zip de atividade personalizada](./media/data-factory-data-processing-using-batch/image20.png)

1. Certifique-se de que **assemblyName** (mydotnetactivity. dll), **entryPoint** (mydotnetactivityns. Mydotnetactivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), e **packageLinkedService** (devem apontar para o armazenamento de BLOBs que contém o ficheiro zip) estão definidos para os valores corretos.

1. Se corrigir um erro e pretender processar de novo o setor, com o botão direito do setor no **OutputDataset** painel e selecione **executar**.

   ![Painel de OutputDataset opção executar](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > É de um contentor no armazenamento de Blobs com o nome `adfjobs`. Este contentor não é eliminado automaticamente, mas pode eliminar em segurança depois de concluir o teste da solução. Da mesma forma, a solução de fábrica de dados cria uma tarefa do Batch com o nome `adf-\<pool ID/name\>:job-0000000001`. É possível eliminar esta tarefa depois de testar a solução se assim o desejar.
   >
   >
1. A atividade personalizada não utiliza a **App. config** ficheiro a partir do seu pacote. Por conseguinte, se seu código ler quaisquer cadeias de ligação do arquivo de configuração, não funciona em tempo de execução. A prática recomendada quando utiliza o Batch é guardar segredos no Azure Key Vault. Em seguida, utilize um principal de serviço baseada em certificados para proteger o Cofre de chaves e distribuir o certificado para o conjunto do Batch. A atividade personalizada .NET pode a aceder aos segredos do Cofre de chaves no tempo de execução. Esta solução genérica pode ser dimensionado para qualquer tipo de segredo, não apenas uma cadeia de ligação.

    Existe uma solução mais fácil, mas não é uma prática recomendada. Pode criar um serviço ligado da base de dados do SQL com ligação de definições de cadeia de caracteres. Em seguida, pode criar um conjunto de dados que utiliza o serviço ligado e o conjunto de dados da cadeia como um conjunto de dados de entrada fictício para a atividade .NET personalizada. Em seguida, pode aceder a cadeia de ligação do serviço ligado no código de atividade personalizada. Deve funcionar bem no tempo de execução.  

#### <a name="extend-the-sample"></a>Alargar a amostra
Pode estender este exemplo para saber mais sobre as funcionalidades do Data Factory e o Batch. Por exemplo, para processar o setor de um intervalo de tempo diferente, siga os passos seguintes:

1. Adicionar às subpastas seguintes no `inputfolder`: 2015-11-16-05, 2015-11-16-06, 201-11-16-07 2011-11-16-08 e 2015-11-16-09. Coloca ficheiros de entrada nessas pastas. Alterar a hora de fim para o pipeline partir `2015-11-16T05:00:00Z` para `2015-11-16T10:00:00Z`. Na **diagrama** ver, faça duplo clique em **InputDataset** e confirme que os setores de entrada estão prontos. Faça duplo clique em **OutputDataset** para ver o estado dos setores de saída. Caso se encontrem no **pronto** de estado, verifique a pasta de saída para os ficheiros de saída.

1. Aumentar ou diminuir a **simultaneidade** definição para compreender como ele afeta o desempenho da sua solução, especialmente o processamento que ocorre no Batch. Para obter mais informações sobre o **simultaneidade** definição, consulte "passo 4: Criar e executar o pipeline com uma atividade personalizada."

1. Criar um conjunto com superiores/inferiores **tarefas de máximo por VM**. Para utilizar o novo conjunto que criou, atualize o serviço de Batch ligado na solução de fábrica de dados. Para obter mais informações sobre o **tarefas de máximo por VM** definição, consulte "passo 4: Criar e executar o pipeline com uma atividade personalizada."

1. Criar um conjunto do Batch com o **dimensionamento automático** funcionalidade. Dimensionar automaticamente nós de computação de um conjunto do Batch é o ajuste dinâmico do poder utilizado pela sua aplicação de processamento. 

    A fórmula de exemplo aqui alcança o seguinte comportamento. Quando o conjunto for criado inicialmente, ele começa com uma VM. Estados do Active Directory (em fila) e a métrica de $PendingTasks define o número de tarefas em execução. A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated em conformidade. Ele garante que TargetDedicated nunca vai além de 25 VMs. Conforme novas tarefas submetidas, o conjunto automaticamente cresce. Como tarefas são concluídas, as VMs se tornar um gratuito por um e o dimensionamento automático diminui essas VMs. Pode ajustar startingNumberOfVMs e maxNumberofVMs às suas necessidades.
 
    Fórmula de dimensionamento automático:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Para obter mais informações, consulte [Dimensionar automaticamente nós de um conjunto de Batch computação](../../batch/batch-automatic-scaling.md).

   Se o conjunto utiliza a predefinição [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), o serviço Batch pode demorar entre 15 a 30 minutos para preparar a VM antes de executar a atividade personalizada. Se o conjunto utiliza um autoScaleEvaluationInterval diferente, o serviço Batch pode demorar autoScaleEvaluationInterval mais de 10 minutos.

1. Na solução de exemplo, o **Execute** método invoca o **Calculate** método que processa um setor de dados de entrada para produzir um setor de dados de saída. Pode escrever seu próprio método para processar dados de entrada e substitua a **Calculate** chamada de método **Execute** método com uma chamada para seu método.

### <a name="next-steps-consume-the-data"></a>Passos seguintes: Consuma os dados
Depois de processar dados, pode consumir ferramentas online, como o Power BI. Seguem-se ligações para ajudar a compreender o Power BI e como utilizá-la no Azure:

* [Explore um conjunto de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Atualizar dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure e o Power BI: Descrição geral básica](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referências
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introdução ao serviço do Data Factory](data-factory-introduction.md)
  * [Introdução ao Data Factory](data-factory-build-your-first-pipeline.md)
  * [Utilizar atividades personalizadas num pipeline do Data Factory](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Noções básicas do Batch](../../batch/batch-technical-overview.md)
  * [Descrição geral das funcionalidades do Batch](../../batch/batch-api-basics.md)
  * [Criar e gerir uma conta do Batch no portal do Azure](../../batch/batch-account-create-portal.md)
  * [Introdução à biblioteca de cliente do Batch para .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
