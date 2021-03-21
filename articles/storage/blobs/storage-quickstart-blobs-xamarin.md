---
title: 'Quickstart: Azure Blob Storage library v12 - Xamarin'
description: Neste arranque rápido, aprende-se a usar a versão 12 da biblioteca do cliente Azure Blob Storage com Xamarin para criar um recipiente e uma bolha no armazenamento blob (objeto). Em seguida, você aprende a baixar a bolha para o seu dispositivo móvel, e como listar todas as bolhas em um recipiente.
author: codemillmatt
ms.author: masoucou
ms.date: 10/09/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: dab938a526aa89f6fe5a014e10869bd8da8b475d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98802355"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Quickstart: Azure Blob Storage biblioteca de clientes v12 com Xamarin

Começa com a biblioteca de clientes Azure Blob Storage v12 com Xamarin. Azure Blob Storage é a solução de armazenamento de objetos da Microsoft para a nuvem. Siga as medidas para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados.

Utilize a biblioteca de clientes Azure Blob Storage v12 com Xamarin para:

* Criar um contentor
* Carregar um blob para o Armazenamento do Microsoft Azure
* Listar todas as bolhas em um recipiente
* Descarregue a bolha para o seu dispositivo
* Eliminar um contentor

Links de referência:

* [Documentação de referência da API](/dotnet/api/azure.storage.blobs)
* [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Sample](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento Azure - [crie uma conta de armazenamento](../common/storage-account-create.md)
* Estúdio Visual com [Desenvolvimento Móvel para carga de trabalho .NET](/xamarin/get-started/installation/?pivots=windows) instalada ou Estúdio Visual para [Mac](/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>Configuração
    
Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes Azure Blob Storage v12 com Xamarin.
    
### <a name="create-the-project"></a>Criar o projeto

1. Abra o Estúdio Visual e crie uma App de Formulários Em Branco.
1. Nome: BlobQuickstartV12

### <a name="install-the-package"></a>Instale o pacote

1. Clique com o botão direito na solução no painel Solution Explorer e selecione **Gerir pacotes nuget para solução.**
1. Procure **Azure.Storage.Blobs** e instale a versão mais recente estável em todos os projetos na sua solução.

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do **diretório BlobQuickstartV12:**

1. Abra o ficheiro *MainPage.xaml* no seu editor
1. Remova tudo entre os `<ContentPage></ContentPage>` elementos e substitua-o pelo seguinte:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Azure Blob Storage está otimizado para armazenar quantidades massivas de dados não estruturados. Os dados não estruturados são dados que não seguem uma definição ou um modelo de dados em particular, como por exemplo, texto ou dados binários. O armazenamento blob oferece três tipos de recursos:

* A conta de armazenamento
* Um recipiente na conta de armazenamento
* Uma bolha no recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento blob](./media/storage-blobs-introduction/blob1.png)

Utilize as seguintes classes .NET para interagir com estes recursos:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): A `BlobServiceClient` classe permite-lhe manipular os recursos de armazenamento do Azure e os recipientes blob.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): A `BlobContainerClient` classe permite-lhe manipular os recipientes de armazenamento Azure e as suas bolhas.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): A `BlobClient` classe permite-lhe manipular as bolhas de armazenamento Azure.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): A `BlobDownloadInfo` classe representa as propriedades e conteúdos devolvidos do download de uma bolha.

## <a name="code-examples"></a>Exemplos de código

Estes excertos de código de exemplo mostram-lhe como executar as seguintes tarefas com a biblioteca cliente de Armazenamento Azure Blob para .NET numa aplicação Xamarin.Forms:

* [Criar variáveis de nível de classe](#create-class-level-variables)
* [Criar um contentor](#create-a-container)
* [Carregar bolhas para um recipiente](#upload-blobs-to-a-container)
* [Listar os blobs num contentor](#list-the-blobs-in-a-container)
* [Transferir blobs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="create-class-level-variables"></a>Criar variáveis de nível de classe

O código abaixo declara várias variáveis de nível de classe. Precisavam de comunicar ao Azure Blob Storage durante todo o resto desta amostra.

Estes são além do fio de ligação para a conta de armazenamento definida na secção de configuração da [sua ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código como variáveis de nível de classe dentro do ficheiro *MainPage.xaml.cs:*

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Criar um contentor

Decida o nome do novo recipiente. O código abaixo anexa um valor GUID ao nome do recipiente para garantir que é único.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient.](/dotnet/api/azure.storage.blobs.blobserviceclient) Em seguida, ligue para o método [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) para criar o recipiente na sua conta de armazenamento.

Adicione este código ao ficheiro *MainPage.xaml.cs:*

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Carregar bolhas para um recipiente

O seguinte corte de código:

1. Cria um `MemoryStream` texto.
1. Envia o texto para uma Blob chamando a função [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) da classe [BlobContainerClient,](/dotnet/api/azure.storage.blobs.blobcontainerclient) passando-o tanto no nome de ficheiro como no `MemoryStream` texto. Este método cria o blob, caso ainda não exista, ou substitui-o se o mesmo já existir.

Adicione este código ao ficheiro *MainPage.xaml.cs:*

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Enuncie as bolhas no recipiente chamando o método [GetBlobsAsync.](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) Neste caso, apenas uma bolha foi adicionada ao recipiente, pelo que a operação de listagem devolve apenas uma bolha.

Adicione este código ao ficheiro *MainPage.xaml.cs:*

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Transferir blobs

Descarregue a bolha anteriormente criada chamando o método [DownloadAsync.](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) O código de exemplo copia `Stream` primeiro a representação da bolha num `MemoryStream` e depois num para que o texto possa ser `StreamReader` exibido.

Adicione este código ao ficheiro *MainPage.xaml.cs:*

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Eliminar um contentor

O seguinte código limpa os recursos que a aplicação criou através da eliminação de todo o recipiente utilizando [o DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync).

A aplicação primeiro solicita para confirmar antes de eliminar a bolha e o recipiente. Esta é uma boa oportunidade para verificar se os recursos foram criados corretamente, antes de serem eliminados.

Adicione este código ao ficheiro *MainPage.xaml.cs:*

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>Executar o código

Quando a aplicação começar, criará primeiro o recipiente como parece. Em seguida, terá de clicar nos botões para fazer upload, listar, descarregar as bolhas e apagar o recipiente.

Para executar a aplicação no Windows pressione F5. Para executar a aplicação no Mac pressione Cmd+Enter.

A aplicação escreve para o ecrã após cada operação. A saída da aplicação é semelhante ao exemplo abaixo:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Antes de iniciar o processo de limpeza, verifique se a saída do conteúdo da bolha no ecrã corresponde ao valor que foi carregado.

Depois de verificar os valores, confirme a solicitação para apagar o recipiente e terminar a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a carregar, descarregar e listar bolhas usando a biblioteca de clientes Azure Blob Storage v12 com Xamarin.

Para ver as aplicações de amostra de armazenamento Blob, continue a:

> [!div class="nextstepaction"]
> [Amostra de Azure Blob Storage SDK v12 Xamarin](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Para tutoriais, amostras, arranques rápidos e outra documentação, visite [a Azure para desenvolvedores móveis.](/azure/mobile-apps)
* Para saber mais sobre Xamarin, consulte [Começar com Xamarin.](/xamarin/get-started/)
