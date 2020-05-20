---
title: Processar conjuntos de dados em larga escala utilizando fábrica de dados e lote
description: Descreve como processar enormes quantidades de dados num oleoduto Azure Data Factory utilizando a capacidade de processamento paralela do Lote Azure.
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
ms.openlocfilehash: 2143546e10b413d1492b8734d2594de42fd37cf3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684401"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Processar conjuntos de dados em larga escala utilizando fábrica de dados e lote
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory, que está geralmente disponível. Se utilizar a versão atual do serviço Data Factory, consulte [atividades personalizadas na Fábrica](../transform-data-using-dotnet-custom-activity.md)de Dados.

Este artigo descreve uma arquitetura de uma solução de amostra que move e processa conjuntos de dados em larga escala de forma automática e programada. Também fornece um walkthrough de ponta a ponta para implementar a solução utilizando data factory e lote azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo é mais longo do que um artigo típico porque contém uma passagem de toda uma solução de amostra. Se você é novo em Batch and Data Factory, você pode aprender sobre estes serviços e como eles trabalham em conjunto. Se sabe alguma coisa sobre os serviços e está a desenhar/arquitetar uma solução, pode focar-se na secção de arquitetura do artigo. Se estiver a desenvolver um protótipo ou uma solução, é melhor experimentar as instruções passo a passo na passagem. Convidamos os seus comentários sobre este conteúdo e como o utiliza.

Primeiro, vamos ver como os serviços de Data Factory e Batch podem ajudá-lo a processar grandes conjuntos de dados na nuvem.     


## <a name="why-azure-batch"></a>Por que Azure Batch?
 Pode utilizar o Batch para executar aplicações de computação paralela e de alto desempenho (HPC) de forma eficiente na nuvem. É um serviço de plataforma que programa trabalho intensivo de computação para funcionar numa coleção gerida de máquinas virtuais (VMs). Pode automaticamente escalar os recursos computacionais para satisfazer as necessidades dos seus trabalhos.

Com o serviço Batch, define os recursos de computação do Azure para executar as aplicações em paralelo e com dimensionamento. Pode executar trabalhos a pedido ou programados. Não é necessário criar manualmente, configurar e gerir manualmente um cluster HPC, VMs individuais, redes virtuais ou uma complexa infraestrutura de trabalho e agendamento de tarefas.

 Se não está familiarizado com o Batch, os seguintes artigos ajudam-no a compreender a arquitetura/implementação da solução descrita neste artigo:   

* [Básicos do Lote](../../batch/batch-technical-overview.md)
* [Descrição geral da funcionalidade do Batch](../../batch/batch-api-basics.md)

Opcionalmente, para saber mais sobre o Lote, consulte [a documentação do Lote.](https://docs.microsoft.com/azure/batch/)

## <a name="why-azure-data-factory"></a>Porquê a Azure Data Factory?
Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o movimento e a transformação de dados. Pode utilizar a Data Factory para criar pipelines de dados geridos que transferem dados das lojas de dados no local e na nuvem para uma loja de dados centralizada. Um exemplo é o armazenamento azure blob. Pode utilizar a Data Factory para processar/transformar dados utilizando serviços como o Azure HDInsight e o Azure Machine Learning. Também pode agendar os gasodutos de dados para funcionar de forma programada (por exemplo, de hora em hora, diariamente e semanalmente). Você pode monitorizar e gerir os oleodutos num ápice para identificar problemas e tomar medidas.

  Se não está familiarizado com a Data Factory, os seguintes artigos ajudam-no a compreender a arquitetura/implementação da solução descrita neste artigo:  

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Construa o seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md)   

Opcionalmente, para saber mais sobre data factory, consulte [a documentação da Fábrica de Dados.](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory)

## <a name="data-factory-and-batch-together"></a>Fábrica de Dados e Lote juntos
A Data Factory inclui atividades incorporadas. Por exemplo, a atividade Copy é usada para copiar/mover dados de uma loja de dados de origem para uma loja de dados de destino. A atividade da Colmeia é usada para processar dados utilizando clusters Hadoop (HDInsight) no Azure. Para uma lista de atividades de transformação apoiadas, consulte atividades de transformação de [dados.](data-factory-data-transformation-activities.md)

Também pode criar atividades personalizadas .NET para mover ou processar dados com a sua própria lógica. Você pode executar estas atividades em um cluster HDInsight ou em um conjunto de VMs. Quando utilizar o Batch, pode configurar a piscina para escala automática (adicionar ou remover VMs com base na carga de trabalho) com base numa fórmula que fornece.     

## <a name="architecture-of-a-sample-solution"></a>Arquitetura de uma solução de amostra
  A arquitetura descrita neste artigo é para uma solução simples. Também é relevante para cenários complexos, como modelação de risco por serviços financeiros, processamento de imagem e renderização, e análise genómica.

O diagrama ilustra como a Data Factory orquestra o movimento e o processamento de dados. Também mostra como o Batch processa os dados de forma paralela. Descarregue e imprima o diagrama para uma referência fácil (11 x 17 polegadas ou tamanho A3). Para aceder ao diagrama para que possa imprimi-lo, consulte [HPC e orquestração de dados utilizando O Lote e a Fábrica](https://go.microsoft.com/fwlink/?LinkId=717686)de Dados .

[![Diagrama de processamento de dados em larga escala](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

A lista seguinte fornece os passos básicos do processo. A solução inclui código e explicações para construir a solução de ponta a ponta.

* **Configure o Lote com um conjunto de nódosos de computação (VMs).** Pode especificar o número de nós e o tamanho de cada nó.

* **Crie uma instância data Factory** que esteja configurada com entidades que representam o armazenamento de blob, o serviço de computação Batch, dados de entrada/saída, e um fluxo de trabalho/pipeline com atividades que movem e transformam dados.

* **Crie uma atividade personalizada .NET no pipeline Data Factory.** A atividade é o seu código de utilizador que funciona na piscina do Lote.

* **Guarde grandes quantidades de dados de entrada como bolhas no Armazenamento Azure.** Os dados são divididos em fatias lógicas (geralmente pelo tempo).

* **Data Factory copia dados que são processados em paralelo** com a localização secundária.

* **A Data Factory executa a atividade personalizada utilizando a piscina atribuída pelo Batch.** A Data Factory pode realizar atividades em simultâneo. Cada atividade processa uma fatia de dados. Os resultados estão guardados no armazém.

* **Data Factory move os resultados finais para um terceiro local,** quer para distribuição através de uma app, quer para posterior processamento por outras ferramentas.

## <a name="implementation-of-the-sample-solution"></a>Implementação da solução da amostra
A solução da amostra é intencionalmente simples. Foi projetado para mostrar como usar data factory e batch juntos para processar conjuntos de dados. A solução conta o número de ocorrências do termo de pesquisa "Microsoft" nos ficheiros de entrada que são organizados numa série temporal. Em seguida, produz a contagem para ficheiros de saída.

**Tempo:** Se estiver familiarizado com os fundamentos de Azure, Data Factory e Batch e tiver completado os seguintes pré-requisitos, esta solução leva de uma a duas horas para ser concluída.

### <a name="prerequisites"></a>Pré-requisitos
#### <a name="azure-subscription"></a>Subscrição do Azure
Se não tiver uma subscrição Azure, pode criar uma conta de teste gratuita rapidamente. Para mais informações, consulte o [free trial](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Usa uma conta de armazenamento para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento, consulte Criar uma conta de [armazenamento.](../../storage/common/storage-account-create.md) A solução de amostra utiliza o armazenamento de bolhas.

#### <a name="azure-batch-account"></a>Conta do Azure Batch
Crie uma conta Batch utilizando o [portal Azure](https://portal.azure.com/). Para mais informações, consulte [Criar e gerir uma conta De Lote](../../batch/batch-account-create-portal.md). Note o nome da conta do Lote e a chave da conta. Também pode utilizar o cmdlet [New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) para criar uma conta Batch. Para obter instruções sobre como utilizar este cmdlet, consulte [Iniciar com cmdlets De Lote PowerShell](../../batch/batch-powershell-cmdlets-get-started.md).

A solução de amostra utiliza o Batch (indiretamente através de um gasoduto de fábrica de dados) para processar dados paralelamente num conjunto de nós de cálculo (uma recolha gerida de VMs).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Piscina de lotes azure de máquinas virtuais
Crie uma piscina de lote com pelo menos dois nódosos de computação.

1. No [portal Azure,](https://portal.azure.com) **selecione Navegar** no menu esquerdo e selecione **Contas de Lote**.

1. Selecione a sua conta 'Lote' para abrir a lâmina **da Conta lote.**

1. Selecione o azulejo **Pools.**

1. Na lâmina **Pools,** selecione o botão **Adicionar** na barra de ferramentas para adicionar uma piscina.

   a. Introduza um ID para a piscina (ID da**piscina).** Reparem na identificação da piscina. Precisa dele quando cria a solução de fábrica de dados.

   b. Especifique o **Windows Server 2012 R2** para a definição familiar do **sistema operativo.**

   c. Selecione um **nível**de preços do nó .

   d. Introduza **2** como valor para a definição dedicada ao **alvo.**

   e. Introduza **2** como valor para as **tarefas Max por** definição de nó.

   f. Selecione **OK** para criar a piscina.

#### <a name="azure-storage-explorer"></a>Explorador do Storage do Azure
Utiliza o [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) ou [o CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (do Software DesajeitadoLeaf) para inspecionar e alterar os dados dos seus projetos de Armazenamento. Também pode inspecionar e alterar os dados nos registos das suas aplicações hospedadas na nuvem.

1. Crie um recipiente chamado **mycontainer** com acesso privado (sem acesso anónimo).

1. Se utilizar o CloudXplorer, crie pastas e subpastas com a seguinte estrutura:

   ![Estrutura de pasta e subpasta](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`e `outputfolder` são pastas de alto nível em `mycontainer` . A `inputfolder` pasta tem subpastas com selos de data (YYYY-MM-DD-HH).

   Se utilizar o Storage Explorer, no próximo passo, faça o upload de ficheiros com os seguintes nomes: `inputfolder/2015-11-16-00/file.txt` , e assim por `inputfolder/2015-11-16-01/file.txt` diante. Este passo cria automaticamente as pastas.

1. Crie um ficheiro de ficheiro de **texto.txt** na sua máquina com conteúdo que tenha a palavra-chave **Microsoft**. Um exemplo é "testar atividade personalizada Microsoft testar atividade personalizada Microsoft."

1. Faça upload do ficheiro para as seguintes pastas de entrada no armazenamento de blob:

   ![Pastas de entrada](./media/data-factory-data-processing-using-batch/image4.png)

   Se utilizar o Storage Explorer, faça o upload do **ficheiro.txt** para **o meu recipiente**. Selecione **Copiar** na barra de ferramentas para criar uma cópia da bolha. Na caixa de diálogo **Copy Blob,** mude o nome da bolha de **destino** para `inputfolder/2015-11-16-00/file.txt` . Repita este passo para `inputfolder/2015-11-16-01/file.txt` `inputfolder/2015-11-16-02/file.txt` criar, `inputfolder/2015-11-16-03/file.txt` , e assim `inputfolder/2015-11-16-04/file.txt` por diante. Esta ação cria automaticamente as pastas.

1. Crie outro recipiente chamado `customactivitycontainer` . Faça upload do ficheiro zip de atividade personalizada para este recipiente.

#### <a name="visual-studio"></a>Visual Studio
Instale o Visual Studio 2012 ou mais tarde para criar a atividade personalizada do Lote a ser utilizada na solução de fábrica de dados.

### <a name="high-level-steps-to-create-the-solution"></a>Passos de alto nível para criar a solução
1. Crie uma atividade personalizada que contenha a lógica de processamento de dados.

1. Criar uma fábrica de dados que utilize a atividade personalizada.

### <a name="create-the-custom-activity"></a>Criar a atividade personalizada
A atividade personalizada da fábrica de dados é o coração desta solução de amostra. A solução de amostra utiliza o Batch para executar a atividade personalizada. Para obter informações sobre como desenvolver atividades personalizadas e usá-las em oleodutos de fábrica de dados, consulte [Utilizar atividades personalizadas num oleoduto](data-factory-use-custom-activities.md)de fábrica de dados.

Para criar uma atividade personalizada .NET que pode utilizar num pipeline de fábrica de dados, cria um projeto de biblioteca de classe .NET com uma classe que implementa a interface IDotNetActivity. Esta interface tem apenas um método: Executar. Aqui está a assinatura do método:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

O método tem alguns componentes-chave que precisa entender:

* O método leva quatro parâmetros:

  * **linkedServices**. Este parâmetro é uma lista enumerada de serviços ligados que ligam fontes de dados de entrada/saída (por exemplo, armazenamento de blob) à fábrica de dados. Nesta amostra, existe apenas um serviço ligado do tipo Azure Storage utilizado tanto para a entrada como para a saída.
  * **conjuntos de dados.** Este parâmetro é uma lista enumerada de conjuntos de dados. Pode utilizar este parâmetro para obter as localizações e esquemas definidos por conjuntos de dados de entrada e saída.
  * **atividade**. Este parâmetro representa a entidade computacional atual. Neste caso, é um serviço de lote.
  * **madeireiro.** Pode utilizar o madeireiro para escrever comentários de depurados que surgem como o registo "Utilizador" para o gasoduto.
* O método devolve um dicionário que pode ser usado para acorrentar atividades personalizadas no futuro. Esta funcionalidade ainda não foi implementada, por isso devolve um dicionário vazio do método.

#### <a name="procedure-create-the-custom-activity"></a>Procedimento: Criar a atividade personalizada
1. Crie um projeto de biblioteca de classe .NET no Estúdio Visual.

   a. Iniciar o Estúdio Visual 2012/2013/2015.

   b. Selecione **File**  >  **New**  >  **Project**.

   c. Expandir **modelos,** e selecionar **Visual \# C**. Neste walkthrough, você usa C \# , mas você pode usar qualquer idioma .NET para desenvolver a atividade personalizada.

   d. Selecione Biblioteca de **Classes** a partir da lista de tipos de projeto à direita.

   e. Introduza **MyDotNetActivity** para o **nome**.

   f. Selecione **C: \\ ADF** para a **localização**. Crie a pasta **ADF** se não existir.

   g. Selecione **OK** para criar o projeto.

1. Selecione **ferramentas**  >  **nuget pacote manager**  >  **consola**.

1. Na Consola do Gestor de Pacotes, execute o seguinte comando para importar Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importe o pacote NuGet de **Armazenamento Azure** para o projeto. Você precisa deste pacote porque você usa a API de armazenamento blob nesta amostra:

    ```powershell
    Install-Package Az.Storage
    ```
1. Adicione as seguintes diretivas utilizando o ficheiro fonte do projeto:

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
1. Altere o nome do espaço de nome para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Mude o nome da classe para **MyDotNetActivity,** e obtenha-o da interface **IDotNetActivity,** como mostrado:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementar (adicionar) o método **executar** da interface **IDotNetActivity** para a classe **MyDotNetActivity.** Copie o seguinte código de amostra para o método. Para obter uma explicação da lógica utilizada neste método, consulte a secção do [método Executar.](#execute-method)

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
1. Adicione os seguintes métodos de ajudante à classe. Estes métodos são invocados pelo método **Executar.** Mais importante, o método **Calcular** isola o código que iterates através de cada bolha.

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
    O método GetFolderPath devolve o caminho para a pasta a que o conjunto de dados aponta e o método GetFileName devolve o nome da bolha/ficheiro a que o conjunto de dados aponta.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    O método Calcular calcula o número de instâncias da palavra-chave "Microsoft" nos ficheiros de entrada (bolhas na pasta). O termo de pesquisa "Microsoft" está codificado no código.

1. Compile o projeto. Selecione **Construir** a partir do menu e, em seguida, selecione **Build Solution**.

1. Inicie o Windows Explorer e vá para a pasta de ** \\ desbloqueio** do **lixo \\ ** ou do lixo. A escolha da pasta depende do tipo de construção.

1. Crie um ficheiro zip **MyDotNetActivity.zip** que contenha todos os binários na pasta ** \\ Bin \\ Debug.** Talvez queira incluir a MyDotNetActivity. **ficheiro pdb** para que obtenha detalhes adicionais, como o número de linha no código fonte que causou o problema quando ocorre uma falha.

   ![A lista de pastas bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Carregue **MyDotNetActivity.zip** como uma bolha para o recipiente blob `customactivitycontainer` no armazenamento blob que o serviço ligado ao StorageLinkedService no ADFTutorialDataFactory utiliza. Crie o recipiente de bolhas `customactivitycontainer` se já não existir.

#### <a name="execute-method"></a>Executar método
Esta secção fornece mais detalhes sobre o código no método Executar.

1. Os membros para iterar através da coleção de entrada encontram-se no espaço de nome [Microsoft.WindowsAzure.Storage.Blob.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) Para iterar através da coleção blob, você é obrigado a usar a classe **BlobContinuationToken.** No essencial, deve utilizar um laço de do-tempo com o símbolo como mecanismo para sair do laço. Para mais informações, consulte [Use O armazenamento blob a partir de .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Um loop básico é mostrado aqui:

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
   Para mais informações, consulte a documentação para o método [ListBlobsSegmented.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)

1. O código para trabalhar através do conjunto de bolhas logicamente vai dentro do loop do-while. No método **Executar,** o loop do-while passa a lista de bolhas para um método chamado **Calcular**. O método devolve uma variável de cadeia chamada **saída** que é o resultado de ter iterado através de todas as bolhas do segmento.

   Devolve o número de ocorrências do termo de pesquisa "Microsoft" na bolha passada para o método **Calcular.**

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Após a terminação do método **Calcular,** deve ser escrito a uma nova bolha. Para cada conjunto de bolhas processadas, uma nova bolha pode ser escrita com os resultados. Para escrever para uma nova bolha, primeiro encontre o conjunto de dados de saída.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. O código também chama o método do ajudante **GetFolderPath** para recuperar o caminho da pasta (o nome do recipiente de armazenamento).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   O método GetFolderPath lança o objeto DataSet para um AzureBlobDataSet, que tem uma propriedade chamada FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. O código chama o método **GetFileName** para recuperar o nome do ficheiro (nome blob). O código é semelhante ao código anterior que foi usado para obter o caminho da pasta.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. O nome do ficheiro é escrito criando um objeto URI. O construtor URI utiliza a propriedade **BlobEndpoint** para devolver o nome do contentor. O caminho da pasta e o nome do ficheiro são adicionados para construir o blob URI de saída.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Depois de escrito o nome do ficheiro, pode escrever a cadeia de saída do método **Calcular** para uma nova bolha:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Criar a fábrica de dados
Na secção [Criar a atividade personalizada,](#create-the-custom-activity) criou uma atividade personalizada e carregou o ficheiro zip com binários e o ficheiro PDB para um recipiente de bolhas. Nesta secção, cria-se uma fábrica de dados com um pipeline que utiliza a atividade personalizada.

O conjunto de dados de entrada para a atividade personalizada representa as bolhas (ficheiros) na pasta de entrada `mycontainer\\inputfolder` () no armazenamento de blob. O conjunto de dados de saída para a atividade representa as bolhas de saída na pasta de saída ( `mycontainer\\outputfolder` ) no armazenamento de bolhas.

Deixe cair um ou mais ficheiros nas pastas de entrada:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Por exemplo, deixe cair um ficheiro (file.txt) com o seguinte conteúdo em cada uma das pastas:

```
test custom activity Microsoft test custom activity Microsoft
```

Cada pasta de entrada corresponde a uma fatia na fábrica de dados, mesmo que a pasta tenha dois ou mais ficheiros. Quando cada fatia é processada pelo gasoduto, a atividade personalizada iterates através de todas as bolhas na pasta de entrada para essa fatia.

Vês cinco ficheiros de saída com o mesmo conteúdo. Por exemplo, o ficheiro de saída do processamento do ficheiro na pasta 2015-11-16-00 tem o seguinte conteúdo:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Se deixar cair vários ficheiros (file.txt, file2.txt, file3.txt) com o mesmo conteúdo na pasta de entrada, consulte o seguinte conteúdo no ficheiro de saída. Cada pasta (2015-11-16-00, etc.) corresponde a uma fatia desta amostra, mesmo que a pasta tenha vários ficheiros de entrada.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

O ficheiro de saída tem agora três linhas, uma para cada ficheiro de entrada (blob) na pasta associada à fatia (2015-11-16-00).

Uma tarefa é criada para cada execução de atividade. Nesta amostra, há apenas uma atividade no oleoduto. Quando uma fatia é processada pelo oleoduto, a atividade personalizada corre no Lote para processar a fatia. Como existem cinco fatias (cada fatia pode ter várias bolhas ou ficheiro), cinco tarefas são criadas em Lote. Quando uma tarefa corre no Batch, é a atividade personalizada que está a decorrer.

A seguinte passagem fornece detalhes adicionais.

#### <a name="step-1-create-the-data-factory"></a>Passo 1: Criar a fábrica de dados
1. Depois de iniciar sessão no [portal Azure,](https://portal.azure.com/)tome os seguintes passos:

   a. Selecione **NOVO** no menu esquerdo.

   b. Selecione **Dados + Analytics** na lâmina **Nova.**

   c. Selecione **Data Factory** na lâmina de análise de **dados.**

1. Na nova lâmina da fábrica de **dados,** introduza **customActivityFactory** para o nome. O nome da fábrica de dados tem de ser globalmente exclusivo. Se receber o erro "O nome de fábrica de dados CustomActivityFactory não está disponível", altere o nome da fábrica de dados. Por exemplo, use o seu nomeCustomActivityFactory e crie novamente a fábrica de dados.

1. Selecione NOME DE **GRUPO DE RECURSOS**, e selecione um grupo de recursos existente ou crie um grupo de recursos.

1. Verifique se a subscrição e a região onde pretende que a fábrica de dados seja criada estão corretas.

1. Selecione **Criar** na nova lâmina de fábrica de **dados.**

1. A fábrica de dados é criada no painel de instrumentos do portal.

1. Depois de a fábrica de dados ser criada com sucesso, vê a página da **fábrica de Dados,** que lhe mostra o conteúdo da fábrica de dados.

   ![Página de fábrica de dados](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Passo 2: Criar serviços ligados
Os serviços ligados ligam lojas de dados ou serviços de computação a uma fábrica de dados. Neste passo, liga a sua conta de armazenamento e a conta de Lote à sua fábrica de dados.

#### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure
1. Selecione o **Autor e implante** azulejos na lâmina de fábrica de **dados** para **CustomActivityFactory**. O Editor da Fábrica de Dados aparece.

1. Selecione **Nova loja** de dados na barra de comando e escolha o **armazenamento Azure.** Aparece o script JSON que utiliza para criar um serviço ligado ao Armazenamento no editor.

   ![Nova loja de dados](./media/data-factory-data-processing-using-batch/image7.png)

1. Substitua o **nome da conta** pelo nome da sua conta de armazenamento. Substitua a **chave da conta** pela chave de acesso da sua conta de armazenamento. Para aprender como obter a sua chave de acesso ao armazenamento, consulte [Gerir as chaves](../../storage/common/storage-account-keys-manage.md)de acesso à conta de armazenamento .

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

   ![Implementação](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Criar um serviço ligado ao Lote Azure
Neste passo, cria um serviço ligado à sua conta Batch que é utilizado para executar a atividade personalizada da fábrica de dados.

1. Selecione **Novo compute** na barra de comando e escolha **o Lote Azure.** Aparece o script JSON que utiliza para criar um serviço ligado ao Lote no editor.

1. No guião da JSON:

   a. Substitua o nome da **conta** pelo nome da sua conta Batch.

   b. Substitua a chave de **acesso** pela chave de acesso da conta Batch.

   c. Insira a identificação da piscina para a propriedade **poolName.** Para esta propriedade, você pode especificar o nome da piscina ou o ID da piscina.

   d. Introduza o lote URI para a propriedade **uri** JSON.

      > [!IMPORTANT]
      > O URL da lâmina da **Conta lote** encontra-se no seguinte formato: nome de \< conta \> . \< região \> .batch.azure.com. Para a propriedade **batchUri** no script JSON, você precisa remover o nome de conta a88." ** da URL. Um exemplo é `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Lâmina de conta de lote](./media/data-factory-data-processing-using-batch/image9.png)

      Para a propriedade **poolName,** você também pode especificar a id da piscina em vez do nome da piscina.

      > [!NOTE]
      > O serviço Data Factory não suporta uma opção a pedido para o Batch como faz para o HDInsight. Você pode usar apenas o seu próprio pool batch em uma fábrica de dados.
      >
      >

   e. Especifique **o StorageLinkedService** para a propriedade **linkedServiceName.** Criou este serviço ligado no passo anterior. Este armazenamento é usado como uma área de preparação para ficheiros e registos.

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="step-3-create-datasets"></a>Passo 3: Criar conjuntos de dados
Neste passo, cria conjuntos de dados para representar dados de entrada e saída.

#### <a name="create-the-input-dataset"></a>Criar o conjunto de dados de entrada
1. No Editor da Fábrica de Dados, selecione o novo botão **dataset** na barra de ferramentas. Selecione **o armazenamento azure blob** na lista de lançamentos.

1. Substitua o script JSON no painel direito pelo seguinte corte JSON:

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

    Cria-se um oleoduto mais tarde nesta passagem com a hora de início 2015-11-16T00:00:00Z e o tempo final 2015-11-16T05:00:00Z. Está programado para produzir dados de hora a hora, por isso existem cinco fatias de entrada/saída (entre as 00:00:00 e as **00** \> **05:00:00).**

    A **frequência** e **o intervalo** para o conjunto de dados de entrada estão definidos para **Hora** e **1**, o que significa que a fatia de entrada está disponível de hora em hora.

    O tempo de início de cada fatia é representado pela variável do sistema **SliceStart** no corte JSON anterior. Aqui estão os tempos de início para cada fatia.

    | **Fatia** | **Hora de início**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**00:00:00 |
    | 2         | 2015-11-16T**01**01:00:00 |
    | 3         | 2015-11-16T**02**02:00:00 |
    | 4         | 2015-11-16T**03**03:00:00 |
    | 5         | 2015-11-16T**04**04:00:00 |

    A **pastaPath** é calculada utilizando a parte do ano, mês, dia e hora da hora de início da fatia **(SliceStart).** Aqui está como uma pasta de entrada é mapeada para uma fatia.

    | **Fatia** | **Hora de início**          | **Pasta de entrada**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**00:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**01:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**02:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**03:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**04:00:00 | 2015-11-16-**04** |

1. Selecione **Implementar** na barra de ferramentas para criar e implementar a tabela **InputDataset.**

#### <a name="create-the-output-dataset"></a>Criar o conjunto de dados de saída
Neste passo, cria-se outro conjunto de dados do tipo AzureBlob para representar os dados de saída.

1. No Editor da Fábrica de Dados, selecione o novo botão **dataset** na barra de ferramentas. Selecione **o armazenamento azure blob** na lista de lançamentos.

1. Substitua o script JSON no painel direito pelo seguinte corte JSON:

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

    Uma bolha/ficheiro de saída é gerada para cada fatia de entrada. Aqui está como um ficheiro de saída é nomeado para cada fatia. Todos os ficheiros de saída são gerados numa pasta de `mycontainer\\outputfolder` saída, .

    | **Fatia** | **Hora de início**          | **Ficheiro de saída**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**00:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**01:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**02:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**03:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**04:00:00 | 2015-11-16-**04.txt** |

    Lembre-se que todos os ficheiros de uma pasta de entrada (por exemplo, 2015-11-16-00) fazem parte de uma fatia com o tempo de início 2015-11-16-00. Quando esta fatia é processada, a atividade personalizada digitaliza cada ficheiro e produz uma linha no ficheiro de saída com o número de ocorrências do termo de pesquisa "Microsoft". Se houver três ficheiros na pasta 2015-11-16-00, existem três linhas no ficheiro de saída 2015-11-16-00.txt.

1. Selecione **Implementar** na barra de ferramentas para criar e implementar o **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Passo 4: Criar e executar o oleoduto com uma atividade personalizada
Neste passo, cria-se um pipeline com uma única atividade, a atividade personalizada que criou anteriormente.

> [!IMPORTANT]
> Se não tiver carregado **o ficheiro.txt** para inserir pastas no recipiente de bolha, faça-o antes de criar o gasoduto. A propriedade **isPaused** está definida para falso no oleoduto JSON, por isso o gasoduto funciona imediatamente porque a data de **início** é no passado.
>
>

1. No Editor da Fábrica de Dados, selecione **Novo oleoduto** na barra de comando. Se não vir o comando, selecione o símbolo de elipse para exibi-lo.

1. Substitua o script JSON no painel direito pelo seguinte corte JSON:

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

   * Apenas uma atividade está no oleoduto, e é do tipo **DotNetActivity**.
   * **O Nome de Montagem** está definido para o nome do DLL **MyDotNetActivity.dll**.
   * **O EntryPoint** está definido para **MyDotNetActivityNS.MyDotNetActivity**. É basicamente \< espaço para \> nomes. \< nome de classe \> no seu código.
   * **O PackageLinkedService** está definido para **StorageLinkedService,** que aponta para o armazenamento blob que contém o ficheiro zip de atividade personalizada. Se utilizar diferentes contas de armazenamento para ficheiros de entrada/saída e o ficheiro zip de atividade personalizada, tem de criar outro serviço ligado ao Armazenamento. Este artigo pressupõe que você usa a mesma conta de armazenamento.
   * **O PackageFile** é definido para **customactivitycontainer/MyDotNetActivity.zip**. Está no recipiente de formato \< para \> / \< o nomezipofthezip.zip. \>
   * A atividade personalizada toma o **InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade **linkedServiceName** da atividade personalizada aponta para **AzureBatchLinkedService**, que diz à Data Factory que a atividade personalizada precisa de ser executada no Batch.
   * A definição de **condivisões** é importante. Se utilizar o valor predefinido, que é 1, mesmo que tenha dois ou mais nós de computação na piscina do Lote, as fatias são processadas uma após a outra. Portanto, não está a aproveitar-se da capacidade de processamento paralelo do Batch. Se definir a **moeda** para um valor mais elevado, digamos 2, significa que duas fatias (corresponde a duas tarefas em Lote) podem ser processadas ao mesmo tempo. Neste caso, ambos os VMs na piscina do Lote são utilizados. Desloque a propriedade de concurrency adequadamente.
   * Apenas uma tarefa (fatia) é executada num VM em qualquer ponto por defeito. Por predefinição, **as tarefas máximas por VM** estão definidas para 1 para uma piscina de lote. Como parte dos pré-requisitos, criou uma piscina com esta propriedade definida para 2. Portanto, duas fatias de fábrica de dados podem ser executadas num VM ao mesmo tempo.
     - A propriedade **isPaused** está definida como falsa por padrão. O gasoduto funciona imediatamente neste exemplo porque as fatias começam no passado. Você pode definir esta propriedade para **verdadeira** para parar o oleoduto e reduzi-lo para **falso** para reiniciar.
     -   Os tempos de **início** e **fim** são cinco horas de diferença. As fatias são produzidas de hora em hora, pelo que cinco fatias são produzidas pelo gasoduto.

1. Selecione **Implementar** na barra de comandos para implementar o pipeline.

#### <a name="step-5-test-the-pipeline"></a>Passo 5: Testar o gasoduto
Neste passo, testa-se o gasoduto largando ficheiros nas pastas de entrada. Comece por testar o pipeline com um ficheiro para cada pasta de entrada.

1. Na lâmina de **fábrica data** no portal Azure, selecione **Diagrama**.

   ![Diagrama](./media/data-factory-data-processing-using-batch/image10.png)

1. Na vista **Diagrama,** clique duas vezes no conjunto de dados de entrada **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. A lâmina **InputDataset** aparece com todas as cinco fatias prontas. Note a HORA DE INÍCIO DA **FATIA** E A HORA DE **CORTE PARA** cada fatia.

   ![Entrada fatia início e fim dos tempos](./media/data-factory-data-processing-using-batch/image12.png)

1. Na vista **Diagrama,** selecione **OutputDataset**.

1. As cinco fatias de saída aparecem no estado **Ready** se forem produzidas.

   ![Saída de corte de início e fim](./media/data-factory-data-processing-using-batch/image13.png)

1. Utilize o portal para visualizar as tarefas associadas às fatias e veja o vM em que cada fatia correu. Para mais informações, consulte a secção de [integração data Factory e Batch.](#data-factory-and-batch-integration)

1. Os ficheiros de saída aparecem `mycontainer` no seu armazenamento de `outputfolder` bolhas.

   ![Ficheiros de saída no armazenamento](./media/data-factory-data-processing-using-batch/image15.png)

   Estão listados cinco ficheiros de saída, um para cada fatia de entrada. Cada um dos ficheiros de saída tem conteúdo semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   O diagrama que se segue ilustra como a fábrica de dados corta o mapa para tarefas em Lote. Neste exemplo, uma fatia tem apenas uma corrida.

   ![Diagrama de mapeamento de fatias](./media/data-factory-data-processing-using-batch/image16.png)

1. Agora tente com vários ficheiros numa pasta. Crie os **ficheiros file2.txt,** **file3.txt,** **file4.txt**, e **file5.txt** com o mesmo conteúdo que em file.txt na pasta **2015-11-06-01**.

1. Na pasta de saída, elimine o ficheiro de saída **2015-11-16-01.txt**.

1. Na lâmina **OutputDataset,** clique à direita na fatia com o TEMPO DE INÍCIO DE **FATIA** definido para **11/16/2015 01:00:00 AM**. Selecione **Executar** para reexecutar/reprocessar a fatia. A fatia tem agora cinco ficheiros em vez de um ficheiro.

    ![Executar](./media/data-factory-data-processing-using-batch/image17.png)

1. Depois de a fatia ser e o seu estado **pronto,** verifique o conteúdo no ficheiro de saída para esta fatia **(2015-11-16-01.txt).** O ficheiro de saída aparece `mycontainer` no seu armazenamento de `outputfolder` bolhas. Deve haver uma linha para cada ficheiro da fatia.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Se não apagou o ficheiro de saída 2015-11-16-01.txt antes de experimentar com cinco ficheiros de entrada, vê uma linha da execução da fatia anterior e cinco linhas da execução da fatia atual. Por predefinição, o conteúdo é anexado ao ficheiro de saída se já existir.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integração de Fábrica de Dados e Lote
O serviço Data Factory cria um trabalho em Batch com o nome `adf-poolname:job-xxx` .

![Trabalhos de lote](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Uma tarefa no trabalho é criada para cada execução de atividade de uma fatia. Se 10 fatias estiverem prontas para serem processadas, 10 tarefas são criadas no trabalho. Você pode ter mais de uma fatia em paralelo se você tiver vários nós de computação na piscina. Se o número máximo de tarefas por nó de cálculo for definido para maior do que uma, mais de uma fatia pode ser executada no mesmo cálculo.

Neste exemplo, existem cinco fatias, por isso existem cinco tarefas em Lote. Com **a moeda** fixada em **5** no pipeline JSON na fábrica de dados e **tarefas máximas por VM definidas** para **2** na piscina de Lote com **2** VMs, as tarefas são executadas rapidamente. (Verifique os tempos de início e fim das tarefas.)

Utilize o portal para visualizar o trabalho do Lote e as suas tarefas que estão associadas às fatias e veja em que VM cada fatia correu.

![Tarefas de trabalho de lote](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Depurar o pipeline
A depuração consiste em algumas técnicas básicas.

1. Se a fatia de entrada não estiver definida para **Ready**, confirme se a estrutura da pasta de entrada está correta e que o ficheiro.txt existe nas pastas de entrada.

   ![Estrutura da pasta de entrada](./media/data-factory-data-processing-using-batch/image3.png)

1. No método **executar** da sua atividade personalizada, utilize o objeto **IActivityLogger** para registar informações que o ajudem a resolver problemas. As mensagens registadas aparecem no \_ ficheiro 0.log do utilizador.

   Na lâmina **OutputDataset,** selecione a fatia para ver a lâmina de **corte data** para essa fatia. No âmbito **da Atividade**corre, vê-se uma atividade a correr para a fatia. Se selecionar **Correr** na barra de comando, pode iniciar outra corrida de atividade para a mesma fatia.

   Ao selecionar a execução da atividade, consulte a lâmina de **execução** de dados da Atividade com uma lista de ficheiros de registo. Vê mensagens registadas no \_ ficheiro 0.log do utilizador. Quando ocorre um erro, vê-se três funcionamentos de atividade porque a contagem de retry está definida para 3 no pipeline/atividade JSON. Ao selecionar a execução da atividade, consulte os ficheiros de registo que pode rever para resolver o erro.

   ![Folhas de corte de Dataset e Dados](./media/data-factory-data-processing-using-batch/image18.png)

   Na lista de ficheiros de registo, selecione **user-0.log**. No painel certo, aparecem os resultados da utilização do método **IActivityLogger.Write.**

   ![Lâmina de detalhes de execução de atividade](./media/data-factory-data-processing-using-batch/image19.png)

   Verifique se o sistema-0.log está a ver se existem mensagens e exceções de erro do sistema.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Inclua o ficheiro **PDB** no ficheiro zip para que os detalhes do erro tenham informações como a pilha de chamadas quando ocorre um erro.

1. Todos os ficheiros do ficheiro zip para a atividade personalizada devem estar no nível superior sem subpastas.

   ![Lista de ficheiros zip de atividade personalizada](./media/data-factory-data-processing-using-batch/image20.png)

1. Certifique-se de que o nome de **montagem** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e **packageLinkedService** (deve apontar para o armazenamento de blob que contém o ficheiro zip) são definidos para os valores corretos.

1. Se corrigir um erro e pretender reprocessar a fatia, clique à direita na fatia da lâmina **OutputDataset** e selecione **Executar**.

   ![Opção execução da lâmina OutputDataset](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Um recipiente está no seu depósito de bolhas chamado `adfjobs` . Este recipiente não é eliminado automaticamente, mas pode eliminá-lo com segurança depois de terminar de testar a solução. Da mesma forma, a solução de fábrica de dados cria um trabalho de Lote chamado `adf-\<pool ID/name\>:job-0000000001` . Pode eliminar este trabalho depois de testar a solução, se quiser.
   >
   >
1. A atividade personalizada não utiliza o ficheiro **app.config** do seu pacote. Portanto, se o seu código ler quaisquer cordas de ligação do ficheiro de configuração, não funciona no tempo de execução. A melhor prática quando usa o Batch é guardar segredos no Cofre de Chaves Azure. Em seguida, utilize um diretor de serviço baseado em certificados para proteger o cofre chave e distribuir o certificado para a piscina do Lote. A atividade personalizada .NET pode aceder a segredos do cofre chave em tempo de funcionação. Esta solução genérica pode escalar para qualquer tipo de segredo, não apenas uma corda de ligação.

    Há uma sobra mais fácil, mas não é uma boa prática. Pode criar um serviço ligado à base de dados SQL com definições de cordas de ligação. Em seguida, pode criar um conjunto de dados que utiliza o serviço ligado e acorrentar o conjunto de dados como um conjunto de dados de entrada manequim para a atividade personalizada .NET. Em seguida, pode aceder à cadeia de ligação do serviço ligado no código de atividade personalizado. Deve funcionar bem na hora de funcionar.  

#### <a name="extend-the-sample"></a>Estender a amostra
Pode estender esta amostra para saber mais sobre as funcionalidades data Factory e Batch. Por exemplo, para processar fatias numa gama de tempo diferente, tome os seguintes passos:

1. Adicione as seguintes subpastas `inputfolder` em: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 e 2015-11-16-09. Coloque os ficheiros de entrada nessas pastas. Mude o tempo final do gasoduto `2015-11-16T05:00:00Z` de . `2015-11-16T10:00:00Z` Na vista **Diagrama,** clique duas vezes **no InputDataset** e confirme se as fatias de entrada estão prontas. Clique duas vezes no **OutputDataset** para ver o estado das fatias de saída. Se estiverem no estado **Ready,** verifique a pasta de saída dos ficheiros de saída.

1. Aumente ou diminua a configuração da **moeda** para entender como afeta o desempenho da sua solução, especialmente o processamento que ocorre no Lote. Para obter mais informações sobre a definição de **moeda,** consulte "Passo 4: Criar e executar o pipeline com uma atividade personalizada".

1. Crie uma piscina com **tarefas máximas**mais altas/inferiores por VM . Para utilizar o novo pool que criou, atualize o serviço ligado ao Lote na solução de fábrica de dados. Para obter mais informações sobre as **tarefas máximas por** definição vM, consulte "Passo 4: Criar e executar o pipeline com uma atividade personalizada".

1. Crie uma piscina de lote com a função **de escala automática.** A escala automática dos nódos os nódosos de cálculo numa piscina de lote é o ajuste dinâmico da potência de processamento utilizada pela sua aplicação.

    A fórmula da amostra aqui consegue o seguinte comportamento. Quando a piscina é inicialmente criada, começa com um VM. A métrica $PendingTasks define o número de tarefas nos estados de funcionamento e ativos (em fila). A fórmula encontra o número médio de tarefas pendentes nos últimos 180 segundos e define o TargetDedicated em conformidade. Garante que o TargetDedicated nunca vai além de 25 VMs. À medida que novas tarefas são submetidas, a piscina cresce automaticamente. À medida que as tarefas completam, os VMs tornam-se gratuitos um a um e o autoscalcificador encolhe esses VMs. Pode ajustar os números de partidaOfVMs e os maxNumberOfVMs às suas necessidades.

    Fórmula de escala automática:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Para mais informações, consulte automaticamente a escala de [nós computacionais numa piscina](../../batch/batch-automatic-scaling.md)de Lote .

   Se a piscina utilizar o [intervalo de avaliação automática](https://msdn.microsoft.com/library/azure/dn820173.aspx)predefinido, o serviço Batch pode demorar 15 a 30 minutos a preparar o VM antes de executar a atividade personalizada. Se a piscina utilizar um intervalo de avaliação autoScaleinterval diferente, o serviço Batch poderá demorar a avaliação automática mais 10 minutos.

1. Na solução da amostra, o método **Executar** invoca o método **Calcular** que processa uma fatia de dados de entrada para produzir uma fatia de dados de saída. Pode escrever o seu próprio método para processar os dados de entrada e substituir a chamada do método **Calcular** no método **Executar** por uma chamada ao seu método.

### <a name="next-steps-consume-the-data"></a>Próximos passos: Consumir os dados
Depois de processar dados, pode consumi-lo com ferramentas online como o Power BI. Aqui estão links para ajudá-lo a entender O Power BI e como usá-lo em Azure:

* [Explore um conjunto de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Introdução ao Power BI Desktop](https://docs.microsoft.com/power-bi/fundamentals/desktop-getting-started)
* [Atualizar dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure e Power BI: Visão geral básica](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referências
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introdução ao serviço data factory](data-factory-introduction.md)
  * [Começar com data factory](data-factory-build-your-first-pipeline.md)
  * [Utilize atividades personalizadas num oleoduto data factory](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Básicos do Lote](../../batch/batch-technical-overview.md)
  * [Visão geral das características do lote](../../batch/batch-api-basics.md)
  * [Criar e gerir uma conta de Lote no portal Azure](../../batch/batch-account-create-portal.md)
  * [Começar com a biblioteca de clientes batch para .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
