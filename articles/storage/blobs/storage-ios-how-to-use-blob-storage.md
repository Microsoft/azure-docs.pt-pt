---
title: Como utilizar o armazenamento de objetos (Blob) do iOS - Azure Microsoft Docs
description: Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 7fc1b2638c2ab17c4cd58ca8d4508d2e6d244cfa
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95996826"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Como utilizar o armazenamento blob do iOS

Este artigo mostra como executar cenários comuns usando o armazenamento microsoft Azure Blob. As amostras são escritas no Objective-C e utilizam a Biblioteca do [Cliente de Armazenamento Azure para iOS](https://github.com/Azure/azure-storage-ios). Os cenários abrangidos incluem upload, listagem, download e eliminação de bolhas. Para obter mais informações sobre bolhas, consulte a secção [Next Steps.](#next-steps) Também pode descarregar a [aplicação](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) da amostra para ver rapidamente a utilização do Azure Storage numa aplicação iOS.

Para saber mais sobre o armazenamento blob, consulte [o armazenamento Introdução ao Azure Blob](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importe a biblioteca IOS de Armazenamento Azure na sua aplicação

Pode importar a biblioteca IOS de Armazenamento Azure para a sua aplicação, utilizando o [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) ou importando o ficheiro **Framework.** O cacau é a forma recomendada, pois facilita a integração da biblioteca, no entanto a importação do ficheiro-quadro é menos intrusiva para o seu projeto existente.

Para utilizar esta biblioteca, precisa do seguinte:

- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>Cacau

1. Se ainda não o fez, [instale cacau](https://guides.cocoapods.org/using/getting-started.html#toc_3) no seu computador abrindo uma janela de terminal e executando o seguinte comando

    ```shell
    sudo gem install cocoapods
    ```

2. Em seguida, no diretório do projeto (o diretório que contém o seu ficheiro .xcodeproj), crie um novo ficheiro chamado _Podfile_(sem extensão de ficheiro). Adicione o seguinte ao _Podfile e guarde._

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Na janela do terminal, navegue para o diretório do projeto e executar o seguinte comando

    ```shell
    pod install
    ```

4. Se o seu .xcodeproj estiver aberto no Xcode, feche-o. No seu diretório de projeto abra o novo arquivo de projeto que terá a extensão .xcworkspace. Este é o ficheiro que vais trabalhar a partir de agora.

## <a name="framework"></a>Arquitetura

A outra forma de usar a biblioteca é construir a estrutura manualmente:

1. Primeiro, baixe ou clone o [azure-storage-ios repo](https://github.com/azure/azure-storage-ios).
2. Vá para a biblioteca de clientes de *armazenamento azure-ios*  ->  *Lib*  ->  *Azure Storage,* e abra `AZSClient.xcodeproj` em Xcode.
3. No topo esquerdo do Xcode, altere o esquema ativo de "Azure Storage Client Library" para "Framework".
4. Construir o projeto (⌘+B). Isto criará um `AZSClient.framework` ficheiro no seu Ambiente de Trabalho.

Em seguida, pode importar o ficheiro-quadro para a sua aplicação, fazendo o seguinte:

1. Crie um novo projeto ou abra o seu projeto existente no Xcode.
2. Arraste e deixe cair `AZSClient.framework` o navegador do seu projeto Xcode.
3. Selecione *copiar itens se necessário*, e clique em *Terminar*.
4. Clique no seu projeto na navegação à esquerda e clique no separador *Geral* no topo do editor do projeto.
5. Na secção *Quadros e Bibliotecas Ligados,* clique no botão Adicionar (+).
6. Na lista de bibliotecas já fornecidas, procure `libxml2.2.tbd` e adicione-as ao seu projeto.

## <a name="import-the-library"></a>Importar a Biblioteca

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Se estiver a utilizar o Swift, terá de criar um cabeçalho de ponte e importar \<AZSClient/AZSClient.h> lá:

1. Crie um ficheiro de cabeçalho `Bridging-Header.h` e adicione a declaração de importação acima.
2. Vá ao separador *Definições de Construção* e procure *o cabeçalho de ponte Objective-C*.
3. Clique duas vezes no campo do Cabeçalho de *Ponte Objective-C* e adicione o caminho ao seu ficheiro de cabeçalho: `ProjectName/Bridging-Header.h`
4. Construa o projeto (⌘+B) para verificar se o cabeçalho de ponte foi captado pela Xcode.
5. Comece a usar a biblioteca diretamente em qualquer ficheiro Swift, não há necessidade de declarações de importação.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operações Assíncronos

> [!NOTE]
> Todos os métodos que realizam um pedido contra o serviço são operações assíncronos. Nas amostras de código, verá que estes métodos têm um manipulador de conclusão. O código dentro do manipulador de conclusão será executado **após** o pedido estar concluído. O código após o manipulador de conclusão será executado **enquanto** o pedido está sendo feito.

## <a name="create-a-container"></a>Criar um contentor

Todas as bolhas do Azure Storage devem residir num recipiente. O exemplo a seguir mostra como criar um recipiente, chamado *newcontainer,* na sua conta de Armazenamento se já não existir. Ao escolher um nome para o seu recipiente, esteja atento às regras de nomeação acima mencionadas.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Pode confirmar que isto funciona olhando para o [Microsoft Azure Storage Explorer](https://storageexplorer.com) e verificando se o novo *dispositivo* está na lista de contentores da sua conta de Armazenamento.

## <a name="set-container-permissions"></a>Definir permissões de contentores

As permissões de um contentor são configuradas para acesso **privado** por padrão. No entanto, os contentores fornecem algumas opções diferentes para o acesso ao contentor:

- **Privado**: Os dados do contentor e do blob só podem ser lidos pelo titular da conta.
- **Blob**: Os dados blob dentro deste recipiente podem ser lidos por pedido anónimo, mas os dados do contentor não estão disponíveis. Os clientes não podem enumerar bolhas dentro do contentor através de um pedido anónimo.
- **Recipiente**: Os dados do recipiente e da bolha podem ser lidos através de pedidos anónimos. Os clientes podem enumerar bolhas dentro do contentor através de pedidos anónimos, mas não podem enumerar contentores dentro da conta de armazenamento.

O exemplo a seguir mostra como criar um recipiente com permissões de acesso ao **Contentor,** que permitirá o acesso público, apenas de leitura para todos os utilizadores na Internet:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Como mencionado na secção de conceitos de serviço Blob, o Blob Storage oferece três tipos diferentes de bolhas: blobs de bloco, bolhas de apêndice e bolhas de página. A biblioteca Azure Storage iOS suporta os três tipos de bolhas. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

O exemplo a seguir mostra como carregar uma bolha de bloco de um NSString. Se já existir uma bolha com o mesmo nome neste recipiente, o conteúdo desta bolha será substituído.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Pode confirmar que isto funciona olhando para o [Microsoft Azure Storage Explorer](https://storageexplorer.com) e verificando se o recipiente, *contentorpublic,* contém o blob, *sampleblob*. Nesta amostra, usamos um recipiente público para que você também possa verificar que esta aplicação funcionou indo para as bolhas URI:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Além de carregar uma bolha de bloco de um NSString, existem métodos semelhantes para NSData, NSInputStream ou um ficheiro local.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

O exemplo a seguir mostra como listar todas as bolhas num recipiente. Ao realizar esta operação, esteja atento aos seguintes parâmetros:

- **continuaçãoToken** - O token de continuação representa onde a operação de cotação deve começar. Se não for fornecido nenhum símbolo, irá listar as bolhas desde o início. Qualquer número de bolhas pode ser listada, de zero a um máximo definido. Mesmo que este método devolva zero resultados, se `results.continuationToken` não for nulo, pode haver mais blobs no serviço que não foram listados.
- **prefixo** - Pode especificar o prefixo a utilizar para a listagem de bolhas. Apenas bolhas que comecem com este prefixo serão listadas.
- **useFlatBlobListing** - Como mencionado na secção [de Naming e referências e blobs, embora](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) o serviço Blob seja um esquema de armazenamento plano, pode criar uma hierarquia virtual nomeando bolhas com informações sobre o caminho. No entanto, a listagem não plana não é atualmente suportada. Esta funcionalidade está para breve. Por enquanto, este valor deve ser **SIM.**
- **blobListingDetails** - Pode especificar quais os itens a incluir ao listar bolhas
  - _AZSBlobListingDetailsNone_: Lista apenas bolhas comprometidas e não devolva metadados blob.
  - _AZSBlobListingDetailsSnapshots_: Lista de bolhas comprometidas e instantâneos blob.
  - _AZSBlobListingDetailsMetadata_: Recupere os metadados blob para cada bolha devolvida na listagem.
  - _AZSBlobListingDetailsUncommittedBlobs_: Lista de bolhas comprometidas e não comprometidas.
  - _AZSBlobListingDetailsCopy_: Inclua propriedades de cópia na listagem.
  - _AZSBlobListingDetailsAll_: Liste todas as bolhas comprometidas disponíveis, bolhas não comprometidas e instantâneos, e devolva todos os metadados e o estado de cópia para essas bolhas.
- **maxResults** - O número máximo de resultados a devolver para esta operação. Utilize -1 para não fixar um limite.
- **acabamentoHandler** - O bloco de código a executar com os resultados da operação de listagem.

Neste exemplo, um método de ajuda é usado para chamar repetidamente o método blobs de lista sempre que um token de continuação é devolvido.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Transferir um blob

O exemplo a seguir mostra como baixar uma bolha para um objeto NSString.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Eliminar um blob

O exemplo a seguir mostra como apagar uma bolha.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Apagar um recipiente de bolhas

O exemplo a seguir mostra como apagar um recipiente.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a usar o Blob Storage do iOS, siga estas ligações para saber mais sobre a biblioteca iOS e o serviço de Armazenamento.

- [Biblioteca do Cliente de Armazenamento Azure para iOS](https://github.com/azure/azure-storage-ios)
- [Documentação de referência do IOS de Armazenamento Azure](https://azure.github.io/azure-storage-ios/)
- [API REST dos Serviços do Armazenamento do Azure](/rest/api/storageservices/)
- [Blog da equipa de armazenamento Azure](/archive/blogs/windowsazurestorage/)

Se tiver dúvidas sobre esta biblioteca, sinta-se à vontade para publicar no nosso [Microsoft Q&Página de perguntas](/answers/topics/azure-blob-storage.html) ou Stack [Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Se tiver sugestões de recursos para armazenamento Azure, por favor publique no [Feedback de Armazenamento Azure](https://feedback.azure.com/forums/217298-storage/).