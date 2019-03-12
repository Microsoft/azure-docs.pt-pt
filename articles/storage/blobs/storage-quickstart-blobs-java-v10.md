---
title: 'Início rápido do Azure: Criar um blob no armazenamento de objetos com V10 de SDK de armazenamento de Java | Documentos da Microsoft'
description: Neste início rápido, vai criar um contentor no armazenamento de objetos (Blob do Azure), carregar um ficheiro, listar os objetos e transferir com o SDK de Armazenamento Java.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: rogarana
ms.openlocfilehash: 136f9912e5a296629f76998e5843d6cccd81d3e3
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570577"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-the-java-storage-sdk-v10"></a>Início rápido: Carregar, transferir e listar os blobs ao utilizar a V10 de SDK de armazenamento de Java

Neste início rápido, vai aprender a utilizar o novo SDK de Armazenamento Java para carregar, transferir e listar blobs de blocos num contentor no armazenamento de Blobs do Azure. O novo SDK Java utiliza o modelo de programação reativa com RxJava, que permite operações assíncronas. Saiba mais sobre as [extensões reativas RxJava para a VM de Java](https://github.com/ReactiveX/RxJava). 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Certifique-se de que tem os seguintes pré-requisitos adicionais instalados:

* [Maven](http://maven.apache.org/download.cgi) para funcionar a partir da linha de comandos, ou qualquer ambiente de desenvolvimento integrado Java que preferir.
* [JDK](https://aka.ms/azure-jdks)

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A [aplicação de exemplo](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) utilizada neste início rápido é uma aplicação de consola básica. 

Utilize o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Este comando clona o repositório para a sua pasta local do git.

Após concluir a importação do projeto, abra **Quickstart.java**, localizado em **src/main/java/quickstart**.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento
Esta solução exige que armazene em segurança o nome e a chave da conta de armazenamento. Armazene-os nas variáveis de ambiente locais do computador que executa o exemplo. Siga o exemplo do Linux ou do Windows, consoante o sistema operativo, para criar as variáveis de ambiente.

### <a name="linux-example"></a>Exemplo do Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Exemplo do Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Executar o exemplo

Este exemplo cria um ficheiro de teste no seu diretório predefinido, **AppData\Local\Temp**, para os utilizadores do Windows. Em seguida, é-lhe pedido que siga os seguintes passos:

1. Introduza os comandos para carregar o ficheiro de teste para o armazenamento de Blobs do Azure.
2. Liste os blobs no contentor.
3. Transfira o ficheiro carregado com um novo nome, para que possa comparar os ficheiros antigos e os novos. 

Se quiser executar o exemplo mediante a utilização do Maven na linha de comandos, abra uma shell e navegue para **storage-blobs-java-v10-quickstart**, dentro do diretório clonado. Em seguida, introduza `mvn compile exec:java`.

Este exemplo mostra o resultado se executar a aplicação no Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Tem controlo do exemplo, por isso, introduza comandos para o código ser executado. As entradas são sensíveis às maiúsculas e minúsculas.

Também pode utilizar uma ferramenta como o [Explorador de Armazenamento do Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver os ficheiros no armazenamento de Blobs. O Explorador de Armazenamento do Azure é uma ferramenta multiplataformas gratuita que lhe dá acesso às informações da sua conta de armazenamento. 

Verifique os ficheiros. Em seguida, selecione **E** e selecione **Enter** para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que o exemplo faz, abra o ficheiro **Quickstart.java** para ver o código. 

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

As secções seguintes percorrem o código de exemplo, para que compreenda como funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento

Em primeiro lugar, cria as referências para os objetos que são utilizados para aceder ao armazenamento de Blobs e geri-lo. Estes objetos dependem uns dos outros. Cada objeto é utilizado pelo objeto seguinte na lista.

1. Crie uma instância do objeto **StorageURL** que aponte para a conta de armazenamento.

    * O objeto [StorageURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-stable) é uma representação da sua conta de armazenamento. Utilize-o para gerar um novo pipeline. 
    * Um pipeline é um conjunto de políticas que é utilizado para manipular pedidos e respostas com mecanismos de autorização, registo e repetição. Para obter mais informações, veja [Pipeline HTTP](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline).  
    * Ao utilizar o pipeline, cria uma instância do objeto [ServiceURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-stable).
    * Ao utilizar o objeto **ServiceURL**, cria uma instância do [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * O **ContainerURL** é necessário para executar operações nos contentores de blobs.

2. Crie uma instância do objeto **ContainerURL** que represente o contentor ao qual está a aceder. Os contentores organizam os blobs, da mesma forma que as pastas no computador organizam os ficheiros.

    * O **ContainerURL** fornece um ponto de acesso ao serviço de contentores. 
    * Pode criar uma instância do objeto [BlobURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-stable) com o [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * O **BlobURL** é necessário criar blobs.

3. Crie uma instância do objeto **BlobURL** que aponte para o blob específico que lhe interessa. 

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre os nomes dos contentores e dos blobs, veja [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e Referência para Contentores, Blobs e Metadados).

### <a name="create-a-container"></a>Criar um contentor 

Nesta secção, vai criar uma instância do **ContainerURL**. Vai criar um novo contentor juntamente com a instância. O contentor no exemplo é designado **quickstart**. 

Este exemplo utiliza [ContainerURL.create](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-stable#com_microsoft_azure_storage_blob__container_u_r_l_create_Metadata_PublicAccessType_Context_), para que consiga criar um contentor novo sempre que o exemplo é executado. Em alternativa, pode criar o contentor com antecedência, para que não tenha de criá-lo no código.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são os mais frequentemente utilizados. São utilizados neste início rápido. 

1. Para carregar um ficheiro para um blob, obtenha uma referência para o blob no contentor de destino. 
2. Depois de obter a referência do blob, pode carregar um ficheiro para o mesmo, com uma das seguintes APIs:

    * APIs de baixo nível. Os exemplos são [BlockBlobURL.upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-stable#com_microsoft_azure_storage_blob__block_blob_u_r_l_upload_Flowable_ByteBuffer__long_BlobHTTPHeaders_Metadata_BlobAccessConditions_Context_), também denominado PutBlob, e [BlockBlobURL.stageBlock](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-stable), também denominado PutBLock, na instância de **BlockBlobURL**. 

    * APIs de alto nível fornecidas na [Classe TransferManager](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-stable). Um exemplo é o método [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-stable). 

    Esta operação cria o blob, caso este ainda não exista. Substitui o blob, caso ele já exista.

O código de exemplo cria um ficheiro local para ser utilizado para o carregamento e a transferência. Guarda o ficheiro que vai ser carregado como **sourceFile** e armazena o URL do blob em **blob**. O exemplo seguinte carrega o ficheiro para o seu contentor com o nome **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {

    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());

    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

Os blobs de blocos podem ser qualquer tipo de ficheiro binário ou de texto. Os blobs de páginas utilizam-se principalmente para os ficheiros VHD utilizados para fazer cópias de VMs de IaaS. Os blobs de acréscimo são utilizados para acrescentar dados ao final e são frequentemente utilizados para registo. A maioria dos objetos guardados no armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Pode obter uma lista de objetos num contentor com [ContainerURL.listBlobsFlatSegment](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-stable). Este método devolve até 5.000 objetos em simultâneo, juntamente com um marcador de continuação, ou marcador seguinte, se existirem mais itens a listar no contentor. Crie uma função auxiliar que se chama a si própria repetidamente quando existe um marcador seguinte na resposta **listBlobsFlatSegment** anterior.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs,
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }

    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */

        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */

        return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
            .flatMap(containersListBlobFlatSegmentResponse ->
                listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Transferir blobs

Transfira blobs para o seu disco local com [BlobURL.download](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-stable).

O código seguinte transfere o blob atualizado numa secção anterior. Adiciona um sufixo de **_DOWNLOADED** ao nome do blob, para que possa ver ambos os ficheiros no disco local. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Limpar recursos

Se não precisa dos blobs carregados neste início rápido, pode eliminar o contentor inteiro com [ContainerURL.delete](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-stable). Este método também elimina os ficheiros no contentor.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a transferir ficheiros entre um disco local e o armazenamento de Blobs do Azure com Java. 

> [!div class="nextstepaction"]
> [SDK V10 de Armazenamento para código fonte de Java](https://github.com/Azure/azure-storage-java/)
> [Referência da API](https://docs.microsoft.com/java/api/overview/azure/storage/client?view=azure-java-stable)
> [Saiba mais sobre RxJava](https://github.com/ReactiveX/RxJava)
