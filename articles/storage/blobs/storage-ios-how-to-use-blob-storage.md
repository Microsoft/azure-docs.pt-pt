---
title: Como usar o armazenamento de objeto (BLOB) do iOS – Azure | Microsoft Docs
description: Armazene dados não estruturados na nuvem com o Blob Storage do Azure (armazenamento de objetos).
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 11/20/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: 2d44dabe68900aa45b076f44cc38ed85d6938afa
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249956"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Como usar o armazenamento de BLOBs do iOS

Este artigo mostra como executar cenários comuns usando Microsoft Azure armazenamento de BLOBs. Os exemplos são escritos em Objective-C e usam a [biblioteca de cliente de armazenamento do Azure para IOS](https://github.com/Azure/azure-storage-ios). Os cenários abordados incluem carregar, listar, baixar e excluir BLOBs. Para obter mais informações sobre BLOBs, consulte a seção [próximas etapas](#next-steps) . Você também pode baixar o [aplicativo de exemplo](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rapidamente o uso do armazenamento do Azure em um aplicativo Ios.

Para saber mais sobre o armazenamento de BLOBs, consulte [introdução ao armazenamento de BLOBs do Azure](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importar a biblioteca do iOS de armazenamento do Azure para seu aplicativo
Você pode importar a biblioteca do iOS de armazenamento do Azure para seu aplicativo usando o [CocoaPod de armazenamento do Azure](https://cocoapods.org/pods/AZSClient) ou importando o arquivo de **estrutura** . O CocoaPod é a maneira recomendada, pois torna a integração da biblioteca mais fácil, no entanto, a importação do arquivo de estrutura é menos invasiva para o projeto existente.

Para usar essa biblioteca, você precisará do seguinte:
- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Se você ainda não tiver feito isso, [Instale o CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) em seu computador abrindo uma janela do terminal e executando o comando a seguir
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Em seguida, no diretório do projeto (o diretório que contém o arquivo. xcodeproj), crie um novo arquivo chamado _Podfile_(sem extensão de arquivo). Adicione o seguinte a _Podfile_ e salve.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Na janela do terminal, navegue até o diretório do projeto e execute o comando a seguir

    ```shell    
    pod install
    ```

4. Se o seu. xcodeproj estiver aberto no Xcode, feche-o. No diretório do projeto, abra o arquivo de projeto recém-criado que terá a extensão. xcworkspace. Este é o arquivo no qual você trabalhará por enquanto.

## <a name="framework"></a>Framework
A outra maneira de usar a biblioteca é criar a estrutura manualmente:

1. Primeiro, baixe ou clone o [repositório Azure-Storage-Ios](https://github.com/azure/azure-storage-ios).
2. Vá para *Azure-Storage-Ios* -> *lib* -> *biblioteca de cliente de armazenamento do Azure*e abra `AZSClient.xcodeproj` no Xcode.
3. Na parte superior esquerda do Xcode, altere o esquema ativo de "biblioteca de cliente de armazenamento do Azure" para "estrutura".
4. Compile o projeto (⌘ + B). Isso criará um `AZSClient.framework` arquivo em sua área de trabalho.

Em seguida, você pode importar o arquivo de estrutura para seu aplicativo fazendo o seguinte:

1. Crie um novo projeto ou abra seu projeto existente no Xcode.
2. Arraste e solte o `AZSClient.framework` em seu navegador de projeto do Xcode.
3. Selecione *copiar itens, se necessário*, e clique em *concluir*.
4. Clique em seu projeto no painel de navegação à esquerda e clique na guia *geral* na parte superior do editor de projeto.
5. Na seção *estruturas e bibliotecas vinculadas* , clique no botão Adicionar (+).
6. Na lista de bibliotecas já fornecida, pesquise `libxml2.2.tbd` e adicione-a ao seu projeto.

## <a name="import-the-library"></a>Importar a biblioteca 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Se você estiver usando o Swift, será necessário criar um cabeçalho de ponte e importar \<AZSClient/AZSClient. h > lá:

1. Crie um arquivo `Bridging-Header.h`de cabeçalho e adicione a instrução de importação acima.
2. Vá para a guia *configurações de compilação* e pesquise cabeçalho de *ponte Objective-C*.
3. Clique duas vezes no campo do *cabeçalho de ponte Objective-C* e adicione o caminho ao arquivo de cabeçalho:`ProjectName/Bridging-Header.h`
4. Compile o projeto (⌘ + B) para verificar se o cabeçalho de ponte foi obtido pelo Xcode.
5. Comece a usar a biblioteca diretamente em qualquer arquivo Swift, não há necessidade de instruções de importação.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operações assíncronas
> [!NOTE]
> Todos os métodos que executam uma solicitação no serviço são operações assíncronas. Nos exemplos de código, você descobrirá que esses métodos têm um manipulador de conclusão. O código dentro do manipulador de conclusão será executado **depois** que a solicitação for concluída. O código após o manipulador de conclusão será executado **enquanto** a solicitação estiver sendo feita.
> 
> 

## <a name="create-a-container"></a>Criar um contentor
Cada blob no armazenamento do Azure deve residir em um contêiner. O exemplo a seguir mostra como criar um contêiner, chamado *newcontainer*, na sua conta de armazenamento, se ele ainda não existir. Ao escolher um nome para seu contêiner, lembre-se das regras de nomenclatura mencionadas acima.

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

Você pode confirmar que isso funciona examinando a [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com) e verificando se *newcontainer* está na lista de contêineres para sua conta de armazenamento.

## <a name="set-container-permissions"></a>Definir permissões de contêiner
As permissões de um contêiner são configuradas para acesso **privado** por padrão. No entanto, os contêineres fornecem algumas opções diferentes para o acesso ao contêiner:

* **Particular**: Os dados de contêiner e BLOB podem ser lidos somente pelo proprietário da conta.
* **Blob**: Os dados de blob dentro desse contêiner podem ser lidos por solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar BLOBs dentro do contêiner por meio de solicitação anônima.
* **Contêiner**: Os dados de contêiner e BLOB podem ser lidos por solicitação anônima. Os clientes podem enumerar BLOBs dentro do contêiner por meio de solicitação anônima, mas não podem enumerar contêineres na conta de armazenamento.

O exemplo a seguir mostra como criar um contêiner com permissões de acesso de **contêiner** , que permitirá o acesso público e somente leitura para todos os usuários na Internet:

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
Conforme mencionado na seção conceitos do serviço BLOB, o armazenamento de blob oferece três tipos diferentes de BLOBs: blobs de blocos, blobs de acréscimo e blobs de páginas. A biblioteca iOS do armazenamento do Azure dá suporte a todos os três tipos de BLOBs. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.

O exemplo a seguir mostra como carregar um blob de blocos de um NSString. Se um blob com o mesmo nome já existir nesse contêiner, o conteúdo desse blob será substituído.

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

Você pode confirmar que isso funciona examinando o [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com) e verificando se o contêiner, *containerpublic*, contém o blob, *sampleblob*. Neste exemplo, usamos um contêiner público para que você também possa verificar se esse aplicativo funcionou indo para o URI de BLOBs:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Além de carregar um blob de blocos de um NSString, existem métodos semelhantes para NSData, NSInputStream ou um arquivo local.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor
O exemplo a seguir mostra como listar todos os BLOBs em um contêiner. Ao executar essa operação, lembre-se dos seguintes parâmetros:     

* **continuationToken** -o token de continuação representa o local em que a operação de listagem deve ser iniciada. Se nenhum token for fornecido, ele listará os BLOBs desde o início. Qualquer número de BLOBs pode ser listado, de zero até um máximo definido. Mesmo que esse método retorne zero resultados, `results.continuationToken` se não for nil, pode haver mais BLOBs no serviço que não foram listados.
* **prefixo** – você pode especificar o prefixo a ser usado para a listagem de BLOBs. Somente os blobs que começam com esse prefixo serão listados.
* **useFlatBlobListing** – conforme mencionado na seção [nomenclatura e referência de contêineres e blobs](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , embora o serviço blob seja um esquema de armazenamento simples, você pode criar uma hierarquia virtual por meio da nomenclatura de BLOBs com informações de caminho. No entanto, não há suporte para listagem não simples no momento. Esse recurso estará disponível em breve. Por enquanto, esse valor deve ser **Sim**.
* **blobListingDetails** -você pode especificar quais itens incluir ao listar BLOBs
  * _AZSBlobListingDetailsNone_: Lista somente os BLOBs confirmados e não retorna os metadados do blob.
  * _AZSBlobListingDetailsSnapshots_: Listar BLOBs confirmados e instantâneos de BLOB.
  * _AZSBlobListingDetailsMetadata_: Recuperar metadados de BLOB para cada blob retornado na listagem.
  * _AZSBlobListingDetailsUncommittedBlobs_: Listar BLOBs confirmados e não confirmados.
  * _AZSBlobListingDetailsCopy_: Inclua propriedades de cópia na lista.
  * _AZSBlobListingDetailsAll_: Lista todos os BLOBs confirmados disponíveis, BLOBs não confirmados e instantâneos e retorna todos os metadados e o status da cópia para esses BLOBs.
* **maxResults** -o número máximo de resultados a serem retornados para esta operação. Use-1 para não definir um limite.
* **completionHandler** -o bloco de código a ser executado com os resultados da operação de listagem.

Neste exemplo, um método auxiliar é usado para chamar recursivamente o método listar BLOBs sempre que um token de continuação é retornado.

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
O exemplo a seguir mostra como baixar um blob para um objeto NSString.

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
O exemplo a seguir mostra como excluir um blob.

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

## <a name="delete-a-blob-container"></a>Excluir um contêiner de BLOB
O exemplo a seguir mostra como excluir um contêiner.

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
Agora que você aprendeu como usar o armazenamento de BLOBs do iOS, siga estes links para saber mais sobre a biblioteca do iOS e o serviço de armazenamento.

* [Biblioteca de cliente de armazenamento do Azure para iOS](https://github.com/azure/azure-storage-ios)
* [Documentação de referência do iOS do armazenamento do Azure](https://azure.github.io/azure-storage-ios/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage)

Se você tiver dúvidas sobre esta biblioteca, sinta-se à vontade para postar em nosso [Fórum do MSDN do Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Se você tiver sugestões de recursos para o armazenamento do Azure, envie para os [comentários do armazenamento do Azure](https://feedback.azure.com/forums/217298-storage/).

