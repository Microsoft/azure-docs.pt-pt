---
title: Processar conjuntos de dados em larga escala utilizando a Data Factory e o Batch
description: Descreve como processar grandes quantidades de dados num oleoduto Azure Data Factory utilizando a capacidade de processamento paralelo do Azure Batch.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 3d0e608e1afae77afd44d7351b7c3f1f269bd8a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998089"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Processar conjuntos de dados em larga escala utilizando a Data Factory e o Batch
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory, que está geralmente disponível. Se utilizar a versão atual do serviço Data Factory, consulte [as atividades personalizadas na Data Factory.](../transform-data-using-dotnet-custom-activity.md)

Este artigo descreve uma arquitetura de uma solução de amostra que move e processa conjuntos de dados em larga escala de forma automática e programada. Também fornece uma passagem de ponta a ponta para implementar a solução utilizando a Data Factory e o Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo é mais longo do que um artigo típico porque contém uma passagem de uma solução de amostra inteira. Se é novo na Batch and Data Factory, pode aprender sobre estes serviços e como funcionam em conjunto. Se sabe algo sobre os serviços e está projetando/arquitetando uma solução, pode focar-se na secção de arquitetura do artigo. Se está a desenvolver um protótipo ou uma solução, talvez queira experimentar as instruções passo a passo no passo. Convidamos os seus comentários sobre este conteúdo e como o utiliza.

Primeiro, vamos ver como os serviços de Data Factory e Batch podem ajudá-lo a processar grandes conjuntos de dados na nuvem.     


## <a name="why-azure-batch"></a>Por que Azure Batch?
 Pode utilizar o Batch para executar aplicações de computação paralela e de alto desempenho (HPC) de grande escala na nuvem. É um serviço de plataforma que programa trabalho intensivo compute-intensivo para executar numa coleção gerida de máquinas virtuais (VMs). Pode escalar automaticamente os recursos de cálculo para atender às necessidades dos seus empregos.

Com o serviço Batch, define os recursos de computação do Azure para executar as aplicações em paralelo e com dimensionamento. Pode executar trabalhos a pedido ou programados. Não é necessário criar, configurar e gerir manualmente um cluster HPC, VMs individuais, redes virtuais ou uma complexa infraestrutura de trabalho e agendamento de tarefas.

 Se não está familiarizado com o Batch, os seguintes artigos ajudam-no a compreender a arquitetura/implementação da solução descrita neste artigo:   

* [Fundamentos do Lote](../../azure-sql/database/sql-database-paas-overview.md)
* [Descrição geral da funcionalidade do Batch](../../batch/batch-service-workflow-features.md)

Opcionalmente, para saber mais sobre o Lote, consulte [a documentação do Lote.](https://docs.microsoft.com/azure/batch/)

## <a name="why-azure-data-factory"></a>Por que Azure Data Factory?
Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o movimento e a transformação de dados. Pode utilizar a Data Factory para criar oleodutos de dados geridos que movem dados de lojas de dados no local e em nuvem para uma loja de dados centralizada. Um exemplo é o armazenamento Azure Blob. Pode utilizar a Data Factory para processar/transformar dados utilizando serviços como Azure HDInsight e Azure Machine Learning. Também pode agendar os oleodutos de dados para funcionar de forma programada (por exemplo, hora, dia e semanalmente). Pode monitorizar e gerir os oleodutos num ápice para identificar problemas e tomar medidas.

  Se não está familiarizado com a Data Factory, os seguintes artigos ajudam-no a compreender a arquitetura/implementação da solução descrita neste artigo:  

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Construa o seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md)   

Opcionalmente, para saber mais sobre a Data Factory, consulte [a documentação da Data Factory.](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory)

## <a name="data-factory-and-batch-together"></a>Fábrica de Dados e Lote juntos
A Data Factory inclui atividades integradas. Por exemplo, a atividade Copy é usada para copiar/mover dados de uma loja de dados de origem para uma loja de dados de destino. A atividade da Colmeia é usada para processar dados usando clusters Hadoop (HDInsight) em Azure. Para obter uma lista de atividades de transformação apoiadas, consulte [as atividades de transformação de dados.](data-factory-data-transformation-activities.md)

Também pode criar atividades personalizadas .NET para mover ou processar dados com a sua própria lógica. Você pode executar estas atividades em um cluster HDInsight ou em um lote de VMs. Quando utilizar o Batch, pode configurar a piscina para autoescalar (adicionar ou remover VMs com base na carga de trabalho) com base numa fórmula que fornece.     

## <a name="architecture-of-a-sample-solution"></a>Arquitetura de uma solução de amostra
  A arquitetura descrita neste artigo é para uma solução simples. É também relevante para cenários complexos, como modelação de risco por serviços financeiros, processamento de imagem e renderização, e análise genómica.

O diagrama ilustra como a Data Factory orquestra o movimento e o processamento de dados. Também mostra como o Batch processa os dados de forma paralela. Faça o download e imprima o diagrama para facilitar a referência (11 x 17 polegadas ou tamanho A3). Para aceder ao diagrama para que possa imprimi-lo, consulte [o HPC e a orquestração de dados utilizando o Batch e a Data Factory.](https://go.microsoft.com/fwlink/?LinkId=717686)

[![Diagrama de processamento de dados em larga escala](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

A lista a seguir fornece os passos básicos do processo. A solução inclui código e explicações para construir a solução de ponta a ponta.

* **Configure o Lote com um conjunto de nóns computacional (VMs).** Pode especificar o número de nós e o tamanho de cada nó.

* **Crie uma instância da Data Factory** que esteja configurada com entidades que representem o armazenamento de blob, o serviço de computação Batch, dados de entrada/saída, e um fluxo de trabalho/pipeline com atividades que movem e transformam dados.

* **Crie uma atividade personalizada .NET no oleoduto Data Factory.** A atividade é o seu código de utilizador que funciona na piscina do Lote.

* **Armazenar grandes quantidades de dados de entrada como bolhas no Azure Storage.** Os dados dividem-se em fatias lógicas (geralmente pelo tempo).

* **Data Factory copia dados que são processados paralelamente** à localização secundária.

* **A Data Factory executa a atividade personalizada utilizando a piscina atribuída pelo Batch.** A Data Factory pode executar atividades em simultâneo. Cada atividade processa uma fatia de dados. Os resultados são armazenados em armazenamento.

* **A Data Factory move os resultados finais para um terceiro local,** seja para distribuição através de uma app ou para posterior processamento por outras ferramentas.

## <a name="implementation-of-the-sample-solution"></a>Implementação da solução de amostra
A solução da amostra é intencionalmente simples. É projetado para mostrar como usar data factory e Batch juntos para processar conjuntos de dados. A solução conta o número de ocorrências do termo de pesquisa "Microsoft" em ficheiros de entrada que são organizados numa série de tempo. Em seguida, faz a contagem para os ficheiros de saída.

**Tempo:** Se está familiarizado com os fundamentos da Azure, Data Factory e Batch e completou os seguintes pré-requisitos, esta solução leva uma a duas horas para ser concluída.

### <a name="prerequisites"></a>Pré-requisitos
#### <a name="azure-subscription"></a>Subscrição do Azure
Se não tiver uma subscrição do Azure, pode criar uma conta de teste gratuita rapidamente. Para mais informações, consulte [o julgamento gratuito.](https://azure.microsoft.com/pricing/free-trial/)

#### <a name="azure-storage-account"></a>Conta de armazenamento do Azure
Utilize uma conta de armazenamento para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento, consulte [criar uma conta de armazenamento.](../../storage/common/storage-account-create.md) A solução de amostra utiliza o armazenamento de bolhas.

#### <a name="azure-batch-account"></a>Conta do Azure Batch
Criar uma conta Batch utilizando o [portal Azure](https://portal.azure.com/). Para obter mais informações, consulte [Criar e gerir uma conta Batch](../../batch/batch-account-create-portal.md). Note o nome da conta batch e a chave da conta. Também pode utilizar o [cmdlet New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) para criar uma conta Batch. Para obter instruções sobre como utilizar este cmdlet, consulte [Começar com cmdlets PowerShell do lote](../../batch/batch-powershell-cmdlets-get-started.md).

A solução de amostra utiliza o Batch (indiretamente através de um oleoduto de fábrica de dados) para processar dados de forma paralela num conjunto de nós computacional (uma recolha gerida de VMs).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Piscina de máquinas virtuais Azure Batch
Crie uma piscina de lote com pelo menos dois nós computacional.

1. No [portal Azure,](https://portal.azure.com) **selecione Procurar** no menu esquerdo e selecione **Contas de Lote**.

1. Selecione a sua conta Batch para abrir a lâmina **'Conta lote'.**

1. Selecione o azulejo **pools.**

1. Na lâmina **Pools,** selecione o botão **Adicionar** na barra de ferramentas para adicionar uma piscina.

   a. Introduza um ID para a piscina **(Pool ID).** Reparem na identificação da piscina. Precisa quando cria a solução de fábrica de dados.

   b. Especifique **o Windows Server 2012 R2** para a definição **do Sistema Operativo Família.**

   c. Selecione um **nível de preços de nó**.

   d. Introduza **2** como o valor para a definição **dedicada ao Alvo.**

   e. Introduza **2** como o valor para as **tarefas Max por** definição de nó.

   f. Selecione **OK** para criar a piscina.

#### <a name="azure-storage-explorer"></a>Explorador do Storage do Azure
Utiliza [o Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) ou [o CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (do Software DesajeitadoLeaf) para inspecionar e alterar os dados nos seus projetos de Armazenamento. Também pode inspecionar e alterar os dados nos registos das suas aplicações hospedadas na nuvem.

1. Crie um contentor chamado **mycontainer** com acesso privado (sem acesso anónimo).

1. Se utilizar o CloudXplorer, crie pastas e sub-dobras com a seguinte estrutura:

   ![Estrutura de pasta e sub-dobragem](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` e `outputfolder` são pastas de alto nível em `mycontainer` . A `inputfolder` pasta tem sub-dobradeiras com carimbos de data (YYYYY-MM-DD-HH).

   Se utilizar o Storage Explorer, no passo seguinte, faça o upload de ficheiros com os seguintes nomes: `inputfolder/2015-11-16-00/file.txt` , e assim por `inputfolder/2015-11-16-01/file.txt` diante. Este passo cria automaticamente as pastas.

1. Crie um ficheiro de texto **file.txt** na sua máquina com conteúdo que tenha a palavra-chave **Microsoft**. Um exemplo é "testar atividade personalizada Microsoft test personal activity Microsoft.".

1. Faça o upload do ficheiro para as seguintes pastas de entrada no armazenamento de bolhas:

   ![Pastas de entrada](./media/data-factory-data-processing-using-batch/image4.png)

   Se utilizar o Storage Explorer, faça o upload do ficheiro **file.txt** para **o mycontainer**. Selecione **Copiar** na barra de ferramentas para criar uma cópia da bolha. Na caixa de diálogo **Copy Blob,** altere o **nome da bolha de destino** para `inputfolder/2015-11-16-00/file.txt` . Repita este passo para `inputfolder/2015-11-16-01/file.txt` `inputfolder/2015-11-16-02/file.txt` criar, `inputfolder/2015-11-16-03/file.txt` e assim por `inputfolder/2015-11-16-04/file.txt` diante. Esta ação cria automaticamente as pastas.

1. Criar outro recipiente chamado `customactivitycontainer` . Faça o upload do ficheiro zip da atividade personalizada para este recipiente.

#### <a name="visual-studio"></a>Visual Studio
Instale o Visual Studio 2012 ou mais tarde para criar a atividade personalizada do Batch a ser utilizada na solução de fábrica de dados.

### <a name="high-level-steps-to-create-the-solution"></a>Passos de alto nível para criar a solução
1. Crie uma atividade personalizada que contenha a lógica de processamento de dados.

1. Crie uma fábrica de dados que utilize a atividade personalizada.

### <a name="create-the-custom-activity"></a>Criar a atividade personalizada
A atividade personalizada da fábrica de dados é o coração desta solução de amostra. A solução de amostra utiliza o Batch para executar a atividade personalizada. Para obter informações sobre como desenvolver atividades personalizadas e usá-las em oleodutos de fábrica de dados, consulte [utilizar as atividades personalizadas num oleoduto de fábrica de dados.](data-factory-use-custom-activities.md)

Para criar uma atividade personalizada .NET que pode utilizar num oleoduto de fábrica de dados, cria um projeto de biblioteca de classe .NET com uma classe que implementa a interface IDotNetActivity. Esta interface tem apenas um método: Executar. Aqui está a assinatura do método:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

O método tem alguns componentes-chave que precisa de compreender:

* O método tem quatro parâmetros:

  * **linkedServices**. Este parâmetro é uma lista enumerada de serviços ligados que ligam fontes de dados de entrada/saída (por exemplo, armazenamento de bolhas) à fábrica de dados. Nesta amostra, existe apenas um serviço ligado do tipo Azure Storage utilizado tanto para a entrada como para a saída.
  * **conjuntos de dados**. Este parâmetro é uma lista enumerada de conjuntos de dados. Pode utilizar este parâmetro para obter as localizações e esquemas definidos por conjuntos de dados de entrada e saída.
  * **atividade**. Este parâmetro representa a entidade computacional atual. Neste caso, é um serviço de Lote.
  * **madeireiro**. Pode utilizar o madeirão para escrever comentários de depurg que surgem como o registo "Utilizador" para o pipeline.
* O método devolve um dicionário que pode ser usado para acorrentar atividades personalizadas no futuro. Esta funcionalidade ainda não foi implementada, por isso basta devolver um dicionário vazio do método.

#### <a name="procedure-create-the-custom-activity"></a>Procedimento: Criar a atividade personalizada
1. Crie um projeto de biblioteca de classe .NET no Estúdio Visual.

   a. Iniciar o Visual Studio 2012/2013/2015.

   b. Selecione **o**  >  **novo**  >  **projeto de arquivo**.

   c. Expandir **modelos**e selecionar **Visual C \# **. Nesta passagem, você usa \# C, mas você pode usar qualquer idioma .NET para desenvolver a atividade personalizada.

   d. Selecione **a Biblioteca** de Classe da lista de tipos de projeto à direita.

   e. Insira **myDotNetActivity** para o **nome**.

   f. Selecione **C: \\ ADF** para a **localização**. Crie a pasta **ADF** se não existir.

   exemplo, Selecione **OK** para criar o projeto.

1. Selecione **ferramentas**  >  **NuGet Package Manager**Package Manager  >  **Consola**.

1. Na consola Gestor de Pacotes, execute o seguinte comando para importar Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importe o pacote **Azure Storage** NuGet para o projeto. Você precisa deste pacote porque você usa a API de armazenamento blob nesta amostra:

    ```powershell
    Install-Package Az.Storage
    ```
1. Adicione as seguintes diretivas usando o ficheiro de origem no projeto:

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
1. Altere o nome da classe para **MyDotNetActivity**e o retire da interface **IDotNetActivity** como mostrado:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementar (adicionar) o método **executar** a interface **IDotNetActivity** para a classe **MyDotNetActivity.** Copie o seguinte código de amostra para o método. Para obter uma explicação da lógica utilizada neste método, consulte a secção [de método executar.](#execute-method)

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
1. Adicione os seguintes métodos de ajuda à classe. Estes métodos são invocados pelo método **Executar.** Mais importante, o método **calcular** isola o código que itera através de cada bolha.

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
    O método GetFolderPath devolve o caminho para a pasta para a qual o conjunto de dados aponta e o método GetFileName devolve o nome do blob/ficheiro que o conjunto de dados aponta.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    O método calcular calcula o número de casos da palavra-chave "Microsoft" nos ficheiros de entrada (bolhas na pasta). O termo de pesquisa "Microsoft" está codificado no código.

1. Compilar o projeto. Selecione **Construir** a partir do menu e, em seguida, selecione **Build Solution**.

1. Inicie o Windows Explorer e vá para a pasta ** \\ de desbloqueio do caixote** do lixo ou do caixote do **lixo. \\ ** A escolha da pasta depende do tipo de construção.

1. Crie um ficheiro zip **MyDotNetActivity.zip** que contenha todos os binários na pasta ** \\ bin \\ Debug.** É melhor incluir o MyDotNetActivity. **ficheiro pdb** para obter detalhes adicionais, como o número de linha no código fonte que causou o problema quando ocorre uma falha.

   ![A lista de pastas bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. O upload **MyDotNetActivity.zip** como uma bolha para o recipiente blob `customactivitycontainer` no armazenamento de bolhas que o serviço de armazenamentoLinkedService ligou no ADFTutorialDataFactory. Crie o recipiente de bolhas `customactivitycontainer` se já não existir.

#### <a name="execute-method"></a>Método de execução
Esta secção fornece mais detalhes sobre o código no método Executar.

1. Os membros para iteração através da coleção de entradas encontram-se no espaço de [nomes Microsoft.WindowsAzure.Storage.Blob.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) Para iterar através da coleção blob, você é obrigado a usar a classe **BlobContinuationToken.** Na sua essência, deve utilizar um laço de "fazer-enquanto" com o token como mecanismo para sair do laço. Para obter mais informações, consulte [o armazenamento da Blob a partir de .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Um ciclo básico é mostrado aqui:

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

1. O código para trabalhar através do conjunto de bolhas logicamente vai dentro do ciclo do-while. No método **Executar,** o ciclo do-while passa a lista de bolhas para um método chamado **Calcular**. O método devolve uma variável de cadeia denominada **saída** que é o resultado de ter iterado através de todas as bolhas do segmento.

   Devolve o número de ocorrências do termo de pesquisa "Microsoft" na bolha transmitida ao método **Calcular.**

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Depois de terminado o método **calcular,** deve ser escrito para uma nova bolha. Para cada conjunto de bolhas processadas, uma nova bolha pode ser escrita com os resultados. Para escrever para uma nova bolha, primeiro encontre o conjunto de dados de saída.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. O código também chama o método de ajuda **GetFolderPath** para recuperar o caminho da pasta (o nome do recipiente de armazenamento).

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
1. O nome do ficheiro é escrito criando um objeto URI. O construtor URI utiliza a propriedade **BlobEndpoint** para devolver o nome do recipiente. O caminho da pasta e o nome do ficheiro são adicionados para construir a bolha de saída URI.  

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
Na secção Criar a secção [de atividades personalizadas,](#create-the-custom-activity) criou uma atividade personalizada e carregou o ficheiro zip com binários e o ficheiro PDB para um recipiente blob. Nesta secção, cria-se uma fábrica de dados com um oleoduto que utiliza a atividade personalizada.

O conjunto de dados de entrada para a atividade personalizada representa as bolhas (ficheiros) na pasta de entrada `mycontainer\\inputfolder` () no armazenamento de bolhas. O conjunto de dados de saída para a atividade representa as bolhas de saída na pasta de saída `mycontainer\\outputfolder` () no armazenamento de bolhas.

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

Cada pasta de entrada corresponde a uma fatia na fábrica de dados, mesmo que a pasta tenha dois ou mais ficheiros. Quando cada fatia é processada pelo oleoduto, a atividade personalizada itera através de todas as bolhas da pasta de entrada para essa fatia.

Vê cinco ficheiros de saída com o mesmo conteúdo. Por exemplo, o ficheiro de saída do processamento do ficheiro na pasta 2015-11-16-00 tem o seguinte conteúdo:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Se deixar cair vários ficheiros (file.txt, file2.txt, file3.txt) com o mesmo conteúdo na pasta de entrada, vê o seguinte conteúdo no ficheiro de saída. Cada pasta (2015-11-16-00, etc.) corresponde a uma fatia nesta amostra, mesmo que a pasta tenha vários ficheiros de entrada.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

O ficheiro de saída tem agora três linhas, uma para cada ficheiro de entrada (blob) na pasta associada à fatia (2015-11-16-00).

Uma tarefa é criada para cada atividade executada. Nesta amostra, há apenas uma atividade no oleoduto. Quando uma fatia é processada pelo oleoduto, a atividade personalizada funciona em Batch para processar a fatia. Como existem cinco fatias (cada fatia pode ter várias bolhas ou ficheiro), são criadas cinco tarefas em Batch. Quando uma tarefa funciona em Batch, é a atividade personalizada que está a decorrer.

A seguinte passagem de walkthrough fornece detalhes adicionais.

#### <a name="step-1-create-the-data-factory"></a>Passo 1: Criar a fábrica de dados
1. Depois de iniciar seduca no [portal Azure,](https://portal.azure.com/)tome os seguintes passos:

   a. Selecione **NEW** no menu esquerdo.

   b. Selecione **Data + Analytics** na lâmina **nova.**

   c. Selecione **Data Factory** na lâmina **de análise de dados.**

1. Na nova lâmina de **fábrica de dados,** **introduza a CustomActivityFactory** para o nome. O nome da fábrica de dados tem de ser globalmente exclusivo. Se receber o erro "Data factory name CustomActivityFactory não está disponível", altere o nome da fábrica de dados. Por exemplo, use o seu nome Desemabrico de Atividades e crie novamente a fábrica de dados.

1. Selecione **RESOURCE GROUP NAME**e selecione um grupo de recursos existente ou crie um grupo de recursos.

1. Verifique se a subscrição e a região onde pretende que a fábrica de dados seja criada estão corretas.

1. Selecione **Criar** na nova lâmina de **fábrica de dados.**

1. A fábrica de dados é criada no painel de instrumentos do portal.

1. Depois de a fábrica de dados ser criada com sucesso, vê-se a página da **fábrica de dados,** que lhe mostra o conteúdo da fábrica de dados.

   ![Página de fábrica de dados](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Passo 2: Criar serviços ligados
Os serviços ligados ligam lojas de dados ou serviços de computação a uma fábrica de dados. Neste passo, ligue a sua conta de armazenamento e a conta Batch à sua fábrica de dados.

#### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço ligado do Armazenamento do Azure
1. Selecione o **Autor e implante** azulejo na lâmina da fábrica de **dados** para **CustomActivityFactory**. O Editor da Fábrica de Dados aparece.

1. Selecione **Nova loja de dados** na barra de comando e escolha **o armazenamento Azure.** Aparece o script JSON que utiliza para criar um serviço ligado ao Armazenamento no editor.

   ![Nova loja de dados](./media/data-factory-data-processing-using-batch/image7.png)

1. Substitua o **nome da conta** pelo nome da sua conta de armazenamento. Substitua a **chave da conta** pela chave de acesso da sua conta de armazenamento. Para aprender a obter a chave de acesso ao armazenamento, consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md).

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

   ![Implementação](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Criar um serviço ligado a Azure Batch
Neste passo, cria um serviço ligado para a sua conta Batch que é utilizado para executar a atividade personalizada da fábrica de dados.

1. **Selecione Novo cálculo** na barra de comando e escolha **Azure Batch.** Aparece o script JSON que utiliza para criar um serviço ligado ao Batch no editor.

1. No guião JSON:

   a. Substitua o nome da **conta** pelo nome da sua conta Batch.

   b. Substitua a chave de **acesso** pela chave de acesso da conta Lote.

   c. Insira o ID da piscina para a propriedade **poolName.** Para esta propriedade, você pode especificar o nome da piscina ou o ID da piscina.

   d. Introduza o lote URI para a propriedade **batchUri** JSON.

      > [!IMPORTANT]
      > O URL da lâmina **de conta de lote** está no seguinte formato: \<accountname\> \<region\> . batch.azure.com. Para a propriedade **batchUri** no script JSON, você precisa remover o nome de conta a88." ** da URL. Um exemplo é `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Lâmina de conta de lote](./media/data-factory-data-processing-using-batch/image9.png)

      Para a **propriedade poolName,** você também pode especificar o ID da piscina em vez do nome da piscina.

      > [!NOTE]
      > O serviço Data Factory não suporta uma opção a pedido para o Batch como faz para o HDInsight. Você pode usar apenas o seu próprio lote de lote numa fábrica de dados.
      >
      >

   e. Especifique **o StorageLinkedService** para a propriedade **linkedServiceName.** Criou este serviço ligado no passo anterior. Este armazenamento é utilizado como uma área de preparação para ficheiros e registos.

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

#### <a name="step-3-create-datasets"></a>Passo 3: Criar conjuntos de dados
Neste passo, cria conjuntos de dados para representar dados de entrada e saída.

#### <a name="create-the-input-dataset"></a>Criar o conjunto de dados de entrada
1. No Editor de Fábrica de Dados, selecione o novo botão **de conjunto de dados** na barra de ferramentas. Selecione **o armazenamento Azure Blob** da lista de drop-down.

1. Substitua o guião JSON no painel direito pelo seguinte corte JSON:

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

    Você cria um oleoduto mais tarde nesta passagem com a hora de início 2015-11-16T00:00:00Z e o tempo final 2015-11-16T05:00:00Z. Está programado para produzir dados de hora em hora, por isso existem cinco fatias de entrada/saída (entre as 00:00:00 e as **00** \> **05:00:00).**

    A **frequência** **e** intervalo para o conjunto de dados de entrada são definidos para **Hora** e **1**, o que significa que a fatia de entrada está disponível a cada hora.

    A hora de início de cada fatia é representada pela variável do sistema **SliceStart** no corte JSON anterior. Aqui estão os horários de início de cada fatia.

    | **Fatia** | **Hora de início**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**00:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**02:00:00 |
    | 4         | 2015-11-16T**03**03:00:00 |
    | 5         | 2015-11-16T**04**04:00:00 |

    A **pastaApapa** é calculada utilizando a parte do ano, mês, dia e hora da parte inicial da fatia **(SliceStart).** Aqui está como uma pasta de entrada é mapeada para uma fatia.

    | **Fatia** | **Hora de início**          | **Pasta de entrada**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**00:00:00 | 2015-11-16-00**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-01**01** |
    | 3         | 2015-11-16T**02**02:00:00 | 2015-11-16-02**02** |
    | 4         | 2015-11-16T**03**03:00:00 | 2015-11-16-03**03** |
    | 5         | 2015-11-16T**04**04:00:00 | 2015-11-16-04**04** |

1. Selecione **Implementar** na barra de ferramentas para criar e implementar a tabela **InputDataset.**

#### <a name="create-the-output-dataset"></a>Criar o conjunto de dados de saída
Neste passo, cria-se outro conjunto de dados do tipo AzureBlob para representar os dados de saída.

1. No Editor de Fábrica de Dados, selecione o novo botão **de conjunto de dados** na barra de ferramentas. Selecione **o armazenamento Azure Blob** da lista de drop-down.

1. Substitua o guião JSON no painel direito pelo seguinte corte JSON:

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

    Uma bolha de saída/ficheiro é gerada para cada fatia de entrada. Aqui está como um ficheiro de saída é nomeado para cada fatia. Todos os ficheiros de saída são gerados numa pasta de saída, `mycontainer\\outputfolder` .

    | **Fatia** | **Hora de início**          | **Ficheiro de saída**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**00:00:00 | 2015-11-16-00.txt** ** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-01.txt** ** |
    | 3         | 2015-11-16T**02**02:00:00 | 2015-11-16-02.txt** ** |
    | 4         | 2015-11-16T**03**03:00:00 | 2015-11-16-03.txt** ** |
    | 5         | 2015-11-16T**04**04:00:00 | 2015-11-16-04.txt** ** |

    Lembre-se que todos os ficheiros de uma pasta de entrada (por exemplo, 2015-11-16-00) fazem parte de uma fatia com a hora de início 2015-11-16-00. Quando esta fatia é processada, a atividade personalizada verifica-se através de cada ficheiro e produz uma linha no ficheiro de saída com o número de ocorrências do termo de pesquisa "Microsoft". Se houver três ficheiros na pasta 2015-11-16-00, existem três linhas no ficheiro de saída 2015-11-16-00.txt.

1. Selecione **Implementar** na barra de ferramentas para criar e implementar o **Conjunto De Dados de Saída**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Passo 4: Criar e executar o gasoduto com uma atividade personalizada
Neste passo, cria-se um pipeline com uma atividade, a atividade personalizada que criou anteriormente.

> [!IMPORTANT]
> Se ainda não tiver carregado **file.txt** para inserir pastas no recipiente blob, faça-o antes de criar o gasoduto. A propriedade **isPaused** está definida como falsa no gasoduto JSON, por isso o gasoduto funciona imediatamente porque a data **de início** é no passado.
>
>

1. No Editor de Fábrica de Dados, selecione **Novo oleoduto** na barra de comando. Se não vir o comando, selecione o símbolo da elipse para o exibir.

1. Substitua o guião JSON no painel direito pelo seguinte corte JSON:

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

   * Apenas uma atividade está no oleoduto, e é do tipo **DotNetActivity.**
   * **O nome de montagem** é definido como o nome do ** DLLMyDotNetActivity.dll**.
   * **O Ponto de Entrada** está definido para **MyDotNetActivityNS.MyDotNetActivity**. É \<namespace\> basicamente.\<classname\> no seu código.
   * **O PackageLinkedService** está definido para **StorageLinkedService,** que aponta para o armazenamento de bolhas que contém o ficheiro zip de atividade personalizada. Se utilizar diferentes contas de armazenamento para ficheiros de entrada/saída e o ficheiro zip de atividade personalizada, tem de criar outro serviço ligado ao Armazenamento. Este artigo pressupõe que use a mesma conta de armazenamento.
   * **O PackageFile** está definido para **o "personalactivitycontainer/MyDotNetActivity.zip**. Está no \<containerforthezip\> / \<nameofthezip.zip\> formato.
   * A atividade personalizada toma **o InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade **linkedServiceName** da atividade personalizada aponta para **AzureBatchLinkedService,** que diz à Data Factory que a atividade personalizada precisa de ser executada no Batch.
   * A **regulação da concordância** é importante. Se utilizar o valor predefinido, que é 1, mesmo que tenha dois ou mais nós de cálculo na piscina do Lote, as fatias são processadas uma após a outra. Portanto, não está a aproveitar-se da capacidade de processamento paralela do Batch. Se definir **a concordância** para um valor mais elevado, por exemplo 2, significa que duas fatias (corresponde a duas tarefas em Lote) podem ser processadas ao mesmo tempo. Neste caso, ambos os VMs na piscina lote são utilizados. Desaproprie a propriedade de concordância adequadamente.
   * Apenas uma tarefa (fatia) é executada num VM em qualquer ponto por defeito. Por predefinição, **as tarefas máximas por VM** estão definidas para 1 para um pool de lote. Como parte dos pré-requisitos, você criou uma piscina com esta propriedade definida para 2. Portanto, duas fatias de fábrica de dados podem funcionar num VM ao mesmo tempo.
     - A propriedade **isPaused** é definida como falsa por padrão. O gasoduto funciona imediatamente neste exemplo porque as fatias começam no passado. Você pode definir esta propriedade para **ser verdadeira** para parar o oleoduto e reesá-lo de volta para **falso** para reiniciar.
     -   Os horários **de início** e **fim** estão separados por cinco horas. As fatias são produzidas de hora em hora, pelo que cinco fatias são produzidas pelo oleoduto.

1. Selecione **Implementar** na barra de comandos para implementar o pipeline.

#### <a name="step-5-test-the-pipeline"></a>Passo 5: Testar o gasoduto
Neste passo, testa o pipeline deixando cair ficheiros nas pastas de entrada. Comece por testar o gasoduto com um ficheiro para cada pasta de entrada.

1. Na lâmina da **fábrica de dados** no portal Azure, selecione **Diagrama**.

   ![Diagrama](./media/data-factory-data-processing-using-batch/image10.png)

1. Na vista **diagrama,** clique duas vezes no conjunto de dados de entrada **InputDataset**.

   ![Conjunto de Dados de Entrada](./media/data-factory-data-processing-using-batch/image11.png)

1. A lâmina **InputDataset** aparece com as cinco fatias prontas. Observe o **TEMPO DE ARRANQUE DA FATIA** E CORTE O TEMPO **FINAL** para cada fatia.

   ![Início e fim da fatia de entrada](./media/data-factory-data-processing-using-batch/image12.png)

1. Na vista **diagrama,** selecione **OutputDataset**.

1. As cinco fatias de saída aparecem no estado **Ready** se forem produzidas.

   ![Corte de saída início e fim dos tempos](./media/data-factory-data-processing-using-batch/image13.png)

1. Utilize o portal para visualizar as tarefas associadas às fatias e ver em que VM cada fatia funcionava. Para mais informações, consulte a secção de integração da [Data Factory e do Lote.](#data-factory-and-batch-integration)

1. Os ficheiros de saída aparecem `mycontainer` no seu armazenamento de `outputfolder` bolhas.

   ![Ficheiros de saída no armazenamento](./media/data-factory-data-processing-using-batch/image15.png)

   Estão listados cinco ficheiros de saída, um para cada fatia de entrada. Cada um dos ficheiros de saída tem um conteúdo semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   O diagrama seguinte ilustra como a fábrica de dados corta o mapa para tarefas em Batch. Neste exemplo, uma fatia tem apenas uma corrida.

   ![Diagrama de mapeamento de fatias](./media/data-factory-data-processing-using-batch/image16.png)

1. Agora tente com vários ficheiros numa pasta. Crie os ficheiros **file2.txt**, **file3.txt**, **file4.txt**, e **file5.txt** com o mesmo conteúdo que em file.txt na pasta **2015-11-06-01**.

1. Na pasta de saída, elimine o ficheiro de saída **2015-11-16-01.txt**.

1. Na lâmina **OutputDataset,** clique com o botão direito da fatia com **o TEMPO DE INÍCIO DA FATIA** definido para **11/16/2015 01:00:00 AM**. Selecione **Executar** para reprocessar/reprocessar a fatia. A fatia tem agora cinco ficheiros em vez de um ficheiro.

    ![Executar](./media/data-factory-data-processing-using-batch/image17.png)

1. Depois de a fatia escassear e o seu estado estiver **pronto,** verifique o conteúdo do ficheiro de saída para esta fatia** (2015-11-16-01.txt**). O ficheiro de saída aparece `mycontainer` no seu armazenamento de `outputfolder` bolhas. Deve haver uma linha para cada ficheiro da fatia.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Se não eliminou o ficheiro de saída 2015-11-16-01.txt antes de experimentar com cinco ficheiros de entrada, vê uma linha da execução da fatia anterior e cinco linhas da execução da fatia atual. Por predefinição, o conteúdo é anexado ao ficheiro de saída se já existir.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integração da Fábrica de Dados e lote
O serviço Data Factory cria um trabalho em Batch com o nome `adf-poolname:job-xxx` .

![Trabalhos em lote](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Uma tarefa no trabalho é criada para cada atividade executada de uma fatia. Se 10 fatias estiverem prontas para serem processadas, 10 tarefas são criadas no trabalho. Você pode ter mais de uma fatia correndo em paralelo se você tiver múltiplos nós computacional na piscina. Se o número máximo de tarefas por nó de computação for definido para maior do que uma, mais de uma fatia pode ser executada no mesmo cálculo.

Neste exemplo, existem cinco fatias, pelo que existem cinco tarefas em Batch. Com **a concordância** definida para **5** no gasoduto JSON na fábrica de dados e **as tarefas máximas por VM definidas** para **2** no lote lote com **2** VMs, as tarefas são rápidas. (Verifique os horários de início e de fim das tarefas.)

Utilize o portal para visualizar o trabalho do Lote e as suas tarefas que estão associadas às fatias e ver em que VM cada fatia funcionava.

![Tarefas de trabalho em lote](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Depurar o pipeline
Depuração consiste em algumas técnicas básicas.

1. Se a fatia de entrada não estiver definida para **Pronto,** confirme que a estrutura da pasta de entrada está correta e que existe file.txt nas pastas de entrada.

   ![Estrutura de pasta de entrada](./media/data-factory-data-processing-using-batch/image3.png)

1. No método **Executar** a sua atividade personalizada, utilize o objeto **IActivityLogger** para registar informações que o ajudem a resolver problemas. As mensagens registadas aparecem no \_ ficheiro utilizador 0.log.

   Na lâmina **OutputDataset,** selecione a fatia para ver a lâmina **da fatia de dados** para essa fatia. No âmbito **da Atividade decorre,** vê-se uma atividade a correr para a fatia. Se selecionar **Executar** na barra de comando, pode iniciar outra atividade para a mesma fatia.

   Quando selecionar a execução da atividade, vê a lâmina **de detalhes da execução da Atividade** com uma lista de ficheiros de registo. Vê mensagens registadas no \_ ficheiro utilizador 0.log. Quando ocorre um erro, vê-se três operações a decorrer porque a contagem de repetição está definida para 3 no pipeline/atividade JSON. Quando seleciona a execução da atividade, vê os ficheiros de registo que pode rever para resolver o erro.

   ![Folhas de datação Dataset e data slice](./media/data-factory-data-processing-using-batch/image18.png)

   Na lista de ficheiros de registo, selecione **user-0.log**. No painel direito, aparecem os resultados da utilização do método **IActivityLogger.Write.**

   ![Lâmina de detalhes de execução de atividade](./media/data-factory-data-processing-using-batch/image19.png)

   Verifique se o sistema-0.log é para obter quaisquer mensagens de erro do sistema e exceções.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Inclua o ficheiro **PDB** no ficheiro zip para que os detalhes do erro tenham informações como a pilha de chamadas quando ocorre um erro.

1. Todos os ficheiros do ficheiro zip para a atividade personalizada devem estar ao nível superior sem sub-dobradeiras.

   ![Lista de ficheiros zip de atividade personalizada](./media/data-factory-data-processing-using-batch/image20.png)

1. Certifique-se de que **o conjuntoName** (MyDotNetActivity.dll), **o ponto de entrada** (MyDotNetActivityNS.MyDotNetActivity), o **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e o **packageLinkedService** (deve apontar para o armazenamento do blob que contém o ficheiro zip) estão definidos para os valores corretos.

1. Se corrigir um erro e quiser reprocessar a fatia, clique com o botão direito na lâmina **do OutputDataset** e selecione **Executar**.

   ![Saída A opção de execução da lâmina dodataset](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Um recipiente está no seu depósito de bolhas chamado `adfjobs` . Este recipiente não é apagado automaticamente, mas pode eliminá-lo com segurança depois de terminar de testar a solução. Da mesma forma, a solução de fábrica de dados cria um trabalho de Batch chamado `adf-\<pool ID/name\>:job-0000000001` . Pode apagar este trabalho depois de testar a solução, se quiser.
   >
   >
1. A atividade personalizada não utiliza o ficheiro **app.config** do seu pacote. Portanto, se o seu código ler quaisquer cadeias de ligação do ficheiro de configuração, não funciona em tempo de execução. A melhor prática quando se usa o Batch é guardar segredos no Cofre da Chave Azure. Em seguida, utilize um principal de serviço baseado em certificado para proteger o cofre de chaves e distribuir o certificado para a piscina batch. A atividade personalizada .NET pode aceder a segredos a partir do cofre chave em tempo de execução. Esta solução genérica pode escalar para qualquer tipo de segredo, não apenas uma cadeia de conexão.

    Há uma solução mais fácil, mas não é uma boa prática. Pode criar um serviço de base de dados SQL com definições de cadeias de ligação. Em seguida, pode criar um conjunto de dados que utiliza o serviço ligado e acorrentar o conjunto de dados como um conjunto de dados de entrada falso para a atividade personalizada .NET. Em seguida, pode aceder à cadeia de ligação do serviço ligado no código de atividade personalizado. Deve funcionar bem no tempo de execução.  

#### <a name="extend-the-sample"></a>Estender a amostra
Pode estender esta amostra para saber mais sobre as funcionalidades data factory e batch. Por exemplo, para processar fatias num intervalo de tempo diferente, tome os seguintes passos:

1. Adicione as seguintes sub-dobradeiras em `inputfolder` : 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 e 2015-11-16-09. Coloque os ficheiros de entrada nessas pastas. Mude o tempo final para o gasoduto de `2015-11-16T05:00:00Z` `2015-11-16T10:00:00Z` . Na vista **diagrama,** clique duas vezes **no InputDataset** e confirme se as fatias de entrada estão prontas. Clique duas vezes no **Conjunto de Dados de Saída** para ver o estado das fatias de saída. Se estiverem no estado **de Ready,** verifique a pasta de saída para os ficheiros de saída.

1. Aumente ou diminua a definição **de conuncy** para entender como afeta o desempenho da sua solução, especialmente o processamento que ocorre no Batch. Para obter mais informações sobre a definição **de concordância,** consulte "Passo 4: Criar e executar o gasoduto com uma atividade personalizada".

1. Criar uma piscina com tarefas máximas mais altas/inferiores **por VM.** Para utilizar o novo pool que criou, atualize o serviço de ligação Batch na solução de fábrica de dados. Para obter mais informações sobre as **tarefas máximas por** definição de VM, consulte "Passo 4: Criar e executar o gasoduto com uma atividade personalizada".

1. Crie uma piscina batch com a **função de autoescala.** Escalar automaticamente os nós computacional num pool de Lote é o ajuste dinâmico do poder de processamento utilizado pela sua aplicação.

    A fórmula da amostra aqui consegue o seguinte comportamento. Quando a piscina é inicialmente criada, começa com um VM. A métrica $PendingTasks define o número de tarefas nos estados em execução e ativos (em fila). A fórmula encontra o número médio de tarefas pendentes nos últimos 180 segundos e define o TargetDedicated em conformidade. Garante que o TargetDedicated nunca vai além dos 25 VMs. À medida que novas tarefas são submetidas, o pool cresce automaticamente. À medida que as tarefas terminam, os VMs tornam-se gratuitos um a um e a autoscalagem diminui esses VMs. Pode ajustar a partir de 131 de setembro e maxNumberofVMs às suas necessidades.

    Fórmula de autoescala:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Para obter mais informações, consulte [os nós computacional](../../batch/batch-automatic-scaling.md)de escala automática numa piscina de Lote .

   Se a piscina utilizar o [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)predefinido, o serviço Batch pode demorar entre 15 a 30 minutos a preparar o VM antes de executar a atividade personalizada. Se a piscina utilizar um autoScaleEvaluationInterval diferente, o serviço Batch poderá demorar automaticamente a Reavaliação de Escala mais 10 minutos.

1. Na solução de amostra, o método **Executar** invoca o método Calcular que **processa** uma fatia de dados de entrada para produzir uma fatia de dados de saída. Pode escrever o seu próprio método para processar dados de entrada e substituir a chamada de método **calcular** no método **Executar** por uma chamada para o seu método.

### <a name="next-steps-consume-the-data"></a>Próximos passos: Consumir os dados
Depois de processar dados, pode consumi-lo com ferramentas online como o Power BI. Aqui estão links para ajudá-lo a entender o Power BI e como usá-lo em Azure:

* [Explore um conjunto de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Introdução ao Power BI Desktop](https://docs.microsoft.com/power-bi/fundamentals/desktop-getting-started)
* [Atualizar dados em Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure e Power BI: Visão geral básica](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referências
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introdução ao serviço data factory](data-factory-introduction.md)
  * [Começar com a Data Factory](data-factory-build-your-first-pipeline.md)
  * [Utilize atividades personalizadas num oleoduto da Data Factory](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Fundamentos do Lote](../../azure-sql/database/sql-database-paas-overview.md)
  * [Visão geral das características do Lote](../../batch/batch-service-workflow-features.md))
  * [Criar e gerir uma conta Batch no portal Azure](../../batch/batch-account-create-portal.md)
  * [Começa com a biblioteca de clientes batch para .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
