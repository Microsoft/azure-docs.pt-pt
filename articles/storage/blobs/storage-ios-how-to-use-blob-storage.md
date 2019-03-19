---
title: Como utilizar o armazenamento de (Blob objeto) do iOS - Azure | Documentos da Microsoft
description: Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos).
services: storage
author: michaelhauss
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 11/20/2018
ms.author: michaelhauss
ms.subservice: blobs
ms.openlocfilehash: 1ab799ef7eb9d7c591e76ab9180d4e3f4ba6ba59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007272"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Como utilizar o armazenamento de Blobs do iOS

Este artigo mostra como executar tarefas comuns a utilizar o armazenamento de Blobs do Microsoft Azure. Os exemplos são escritos em Objective-C e utilize o [biblioteca de clientes de armazenamento do Azure para iOS](https://github.com/Azure/azure-storage-ios). Os cenários abrangidos incluem carregar, listar, baixar e eliminar blobs. Para obter mais informações sobre blobs, veja a [passos seguintes](#next-steps) secção. Também pode transferir o [aplicação de exemplo](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rapidamente o uso do armazenamento do Azure numa aplicação iOS.

Para saber mais sobre o armazenamento de BLOBs, veja [introdução ao armazenamento de Blobs do Azure](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importar a biblioteca do iOS de armazenamento do Azure na sua aplicação
É possível importar a biblioteca do iOS de armazenamento do Azure na sua aplicação utilizando o [CocoaPod de armazenamento do Azure](https://cocoapods.org/pods/AZSClient) ou ao importar o **Framework** ficheiro. CocoaPod é a forma recomendada, pois isso torna a integração a biblioteca mais fácil, no entanto a importação do ficheiro framework é menos intrusiva para seu projeto existente.

Para utilizar esta biblioteca, precisa do seguinte:
- iOS 8+
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Se ainda não fez isso, [instalar CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) no seu computador abrindo uma janela de terminal e executar o seguinte comando
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Em seguida, no diretório do projeto (o diretório que contém o ficheiro de xcodeproj), crie um novo arquivo chamado _Podfile_(sem extensão de ficheiro). Adicione o seguinte ao _Podfile_ e guardar.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Na janela de terminal, navegue para o diretório de projeto e execute o seguinte comando

    ```shell    
    pod install
    ```

4. Se sua xcodeproj estiver aberto no Xcode, fechá-la. No seu diretório de projeto abra o ficheiro de projeto criado recentemente que terá a extensão de .xcworkspace. Este é o ficheiro que irá trabalhar partir de agora em.

## <a name="framework"></a>Framework
A outra forma utilizar a biblioteca é criar manualmente a estrutura:

1. Em primeiro lugar, transfira ou clone a [repositório azure-storage-ios](https://github.com/azure/azure-storage-ios).
2. Vá até *azure-storage-ios* -> *Lib* -> *biblioteca de clientes de armazenamento do Azure*e abra `AZSClient.xcodeproj` no Xcode.
3. No canto superior esquerdo do Xcode, altere o esquema de Active Directory de "Biblioteca de clientes de armazenamento do Azure" para "Estrutura".
4. Crie o projeto (⌘ + B). Esta ação irá criar um `AZSClient.framework` ficheiro no seu ambiente de trabalho.

Em seguida, pode importar o ficheiro de framework na sua aplicação, fazendo o seguinte:

1. Criar um novo projeto ou abrir seu projeto existente no Xcode.
2. Arraste e largue o `AZSClient.framework` em seu navegador de projetos do Xcode.
3. Selecione *copiar itens se necessário*e clique em *concluir*.
4. Clique no seu projeto no painel de navegação esquerda e o *gerais* separador na parte superior do editor de projeto.
5. Sob o *estruturas e bibliotecas ligadas* secção, clique no botão Adicionar (+).
6. Na lista de bibliotecas que já é fornecido, procure `libxml2.2.tbd` e adicioná-lo ao seu projeto.

## <a name="import-the-library"></a>A biblioteca de importação 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Se estiver a utilizar o Swift, terá de criar um cabeçalho de bridging e importar < AZSClient/AZSClient.h > lá:

1. Criar um arquivo de cabeçalho `Bridging-Header.h`e adicione a instrução de importação acima.
2. Vá para o *definições de criação* separador e procure *cabeçalho de Bridging Objective-C*.
3. Faça duplo clique no campo da *cabeçalho de Bridging Objective-C* e adicione o caminho para o seu arquivo de cabeçalho: `ProjectName/Bridging-Header.h`
4. Crie o projeto (⌘ + B) para verificar que o cabeçalho de bridging foi capturado pelo Xcode.
5. Começar a utilizar a biblioteca diretamente em qualquer arquivo de Swift, não é necessário para declarações de importação.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operações assíncronas
> [!NOTE]
> Todos os métodos que realizam um pedido com o serviço são operações assíncronas. Os exemplos de código, verá que esses métodos têm um processador de conclusão. O código dentro do manipulador de conclusão será executado **depois de** o pedido é concluído. Depois do processador de conclusão será executado de código **enquanto** a solicitação é que está a ser feita.
> 
> 

## <a name="create-a-container"></a>Criar um contentor
Todos os BLOBs no armazenamento do Azure têm de residir num contentor. O exemplo seguinte mostra como criar um contentor, denominado *newcontainer*, na sua conta de armazenamento, se ainda não exista. Ao escolher um nome para o seu contentor, o cuidado de regras de nomenclatura mencionadas acima.

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

Pode confirmar que isto funciona, observando a [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com) e verificar se *newcontainer* está na lista de contentores para a sua conta de armazenamento.

## <a name="set-container-permissions"></a>Definir permissões de contentor
Permissões de um contentor estão configuradas para **privada** acesso por predefinição. No entanto, os contentores fornecem algumas opções diferentes para acesso ao contentor:

* **Privada**: Dados de contentor e blob podem ser lido por apenas o proprietário da conta.
* **Blob**: Dados de BLOBs dentro deste contentor podem ser lidos por meio de pedido anónimo, mas os dados de contentor não estão disponíveis. Os clientes não é possível enumerar os blobs no contentor através do pedido anónimo.
* **contentor**: Dados de contentor e blob podem ser lido por meio de pedido anónimo. Os clientes podem enumerar os blobs no contentor através do pedido anónimo, mas não é possível enumerar os contentores na conta de armazenamento.

O exemplo seguinte mostra como criar um contentor com o **contentor** permissões, que irão permitir o acesso público, só de leitura para todos os utilizadores na Internet de acesso:

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
Conforme mencionado na secção de conceitos do serviço de BLOBs, armazenamento de BLOBs oferece três tipos diferentes de blobs: blobs de blocos, blobs de acréscimo e blobs de páginas. A biblioteca do iOS de armazenamento do Azure suporta todos os três tipos de blobs. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

O exemplo seguinte mostra como carregar um blob de blocos de um NSString. Se já existir um blob com o mesmo nome neste contentor, o conteúdo deste blob será substituído.

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

Pode confirmar que isto funciona, observando a [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com) e verificar se o contentor *containerpublic*, contém o blob, *sampleblob*. Neste exemplo, usamos um contentor público para que também pode verificar que esta aplicação trabalhou ao aceder os blobs do URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Para além de carregar um blob de blocos de um NSString, existem métodos semelhantes para NSData, NSInputStream ou um ficheiro local.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
O exemplo seguinte mostra como listar todos os blobs num contentor. Ao executar a operação, o cuidado dos seguintes parâmetros:     

* **continuationToken** -o representa de token de continuação onde deve começar a operação de listagem. Se não for fornecido nenhum token, ele listará blobs desde o início. Qualquer número de blobs pode ser apresentado de zero até um máximo de conjunto. Mesmo que esse método retorna zero resultados, se `results.continuationToken` není nulas, pode haver mais blobs no serviço que não foram listadas.
* **prefixo** -pode especificar o prefixo a utilizar para a lista de Blobs. Apenas os blobs que começam com este prefixo serão listados.
* **useFlatBlobListing** – tal como mencionado no [atribuir nomes e referenciar contentores e blobs](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) secção, embora o serviço Blob é um esquema de armazenamento simples, é possível criar uma hierarquia virtual ao nomear blobs com o caminho informações. No entanto, não fixa listagem não é atualmente suportada. Esta funcionalidade estará disponível brevemente. Por enquanto, este valor deve ser **Sim**.
* **blobListingDetails** -pode especificar quais os itens a incluir quando lista os blobs
  * _AZSBlobListingDetailsNone_: Listar apenas os blobs consolidados e não retornam metadados do blob.
  * _AZSBlobListingDetailsSnapshots_: Listar blobs consolidadas e instantâneos de blob.
  * _AZSBlobListingDetailsMetadata_: Obter metadados de blob para cada blob devolvido na listagem.
  * _AZSBlobListingDetailsUncommittedBlobs_: Liste blobs de consolidada e não consolidadas.
  * _AZSBlobListingDetailsCopy_: Inclua propriedades de cópia na listagem.
  * _AZSBlobListingDetailsAll_: Lista de todos os blobs confirmadas disponíveis, os blobs não consolidadas e instantâneos e devolver todos os Estados de metadados e de cópia para os blobs.
* **maxResults** -o número máximo de resultados a devolver para esta operação. Utilize -1 para não definir um limite.
* **completionHandler** - o bloco de código seja executado com os resultados da operação de listagem.

Neste exemplo, um método auxiliar é utilizado para chamar recursivamente a lista de blobs de método sempre que é devolvido um token de continuação.

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
O exemplo seguinte mostra como transferir um blob para um objeto de NSString.

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
O exemplo seguinte mostra como eliminar um blob.

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

## <a name="delete-a-blob-container"></a>Eliminar um contentor de BLOBs
O exemplo seguinte mostra como eliminar um contentor.

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

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu como utilizar o armazenamento de Blobs do iOS, siga estas ligações para saber mais sobre a biblioteca do iOS e o serviço de armazenamento.

* [Biblioteca de clientes de armazenamento do Azure para iOS](https://github.com/azure/azure-storage-ios)
* [IOS de armazenamento do Azure documentação de referência](https://azure.github.io/azure-storage-ios/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage)

Se tiver dúvidas sobre esta biblioteca, fique à vontade postar nosso [fórum de MSDN do Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Se tiver sugestões de funcionalidades do armazenamento do Azure, poste [comentários de armazenamento do Azure](https://feedback.azure.com/forums/217298-storage/).

