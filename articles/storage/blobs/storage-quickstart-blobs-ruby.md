---
title: Início Rápido do Azure – Criar um blob no armazenamento de objetos com o Ruby | Microsoft Docs
description: Neste início rápido, crie uma conta de armazenamento e um contentor no armazenamento de objetos (Blobs). Em seguida, utilize a biblioteca de clientes de armazenamento para Ruby, para carregar um blob para o Armazenamento do Microsoft Azure, transferir um blob e listar os blobs num contentor.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: seguler
ms.openlocfilehash: 47143a76c2b57bc889b74f29d709785d0c19451a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008233"
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>Início rápido: Carregar, transferir e listar blobs através de Ruby

Neste início rápido, vai aprender a utilizar Ruby para carregar, transferir e listar blobs de blocos num contentor no armazenamento de Blobs do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Certifique-se de que tem os seguintes pré-requisitos adicionais instalados:

* [Ruby](https://www.ruby-lang.org/en/downloads/)
* [Biblioteca de armazenamento do Azure para Ruby](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage), através do pacote rubygem: 

    ```
    gem install azure-storage-blob
    ```
    
## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo
A [aplicação de exemplo](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) utilizada neste início rápido é uma aplicação de Ruby básica.  

Utilize o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Este comando clona o repositório para a sua pasta local do git. Para abrir a aplicação de exemplo Ruby, procure a pasta storage-blobs-ruby-quickstart e abra o ficheiro example.rb.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento
Na aplicação, tem de indicar o nome da conta de armazenamento e a chave de conta para criar a instância `BlobService` para a aplicação. Abra o ficheiro `example.rb` no Explorador de Soluções no seu IDE. Substitua os valores **accountname** e **accountkey** pelo nome da conta e a chave. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>Executar o exemplo
Este exemplo cria um ficheiro de teste na pasta "Documentos". O programa de exemplo carrega o ficheiro de teste para o armazenamento de Blobs, lista os blobs no contentor e transfere o ficheiro com um novo nome. 

Execute o exemplo. O resultado seguinte é um exemplo do resultado devolvido ao executar a aplicação:
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Quando prime qualquer tecla para continuar, o programa de exemplo elimina o contentor de armazenamento e os ficheiros. Antes de continuar, procure os dois ficheiros na pasta "Documentos". Pode abrir e ver que são idênticos.

Também pode utilizar uma ferramenta como o [Explorador de Armazenamento do Azure](https://storageexplorer.com) para ver os ficheiros no armazenamento de Blobs. O Explorador de Armazenamento do Azure é uma ferramenta multiplataformas gratuita que lhe permite aceder às informações da sua conta de armazenamento. 

Depois de verificar os ficheiros, prima qualquer tecla para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que o exemplo faz, abra o ficheiro example.rb para ver o código. 

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Em seguida, vamos analisar o código de exemplo, para que saiba como funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento
A primeira coisa a fazer é criar as referências para os objetos utilizados para aceder e gerir ao armazenamento de Blobs. Estes objetos dependem uns dos outros e cada um é utilizado pelo que vem a seguir na lista.

* Crie uma instância do objeto do armazenamento do Azure **BlobService** para configurar as credenciais de ligação. 
* Crie o objeto **Container**, que represente o contentor a que está aceder. Os contentores servem para organizar os blobs, da mesma forma como utiliza pastas para organizar os ficheiros.

Quando tiver o contentor de Blobs da Cloud, pode criar o objeto de blob **Block**, que aponta para o blob específico no qual está interessado e realizar operações, como carregamento, transferência e cópia.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter informações sobre os nomes dos contentores e dos blobs, veja [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Dar Nomes e Referenciar Contentores, Blobs e Metadados).

Nesta secção, vai configurar uma instância do cliente de armazenamento do Azure, instanciar o objeto do serviço Blob, criar um contentor novo e, em seguida, definir as permissões no contentor, para que os blobs sejam públicos. O contentor é designado **quickstartblobs**. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são os mais utilizados e vamos utilizá-los neste guia de introdução.  

Para carregar um ficheiro para um blob, obtenha o caminho completo do ficheiro ao associar o nome de diretório e o nome de ficheiro no disco local. Em seguida, pode carregar o ficheiro para o caminho especificado, com o método **create\_block\_blob()**. 

O código de exemplo cria um ficheiro local que vai servir para o carregamento e a transferência, armazenando o ficheiro a carregar como **file\_path\_to\_file** e o nome do blob como **local\_file\_name**. O exemplo seguinte carrega o ficheiro para o seu contentor com o nome **quickstartblobs**.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Para efetuar uma atualização parcial do conteúdo de um blob de blocos, utilize o método **create\_block\_list()**. Os blobs de blocos podem ter no máximo 4,7 TB e podem ser qualquer coisa, desde folhas de cálculo do Excel a ficheiros grandes de vídeo. Os blobs de páginas utilizam-se principalmente para os ficheiros VHD utilizados para fazer cópias de VMs de IaaS. Os blobs de acréscimo servem para registo, como quando quer escrever num ficheiro e continuar a adicionar mais informações. Deve anexar o blob num único modelo de escritor. A maioria dos objetos guardados no armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Pode obter uma lista de ficheiros no contentor com o método **list\_blobs()**. O código seguinte obtém a lista de blobs, depois percorre-os e mostra os nomes dos blobs encontrados num contentor.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>Transferir os blobs

Transfira blobs para o seu disco local com o método **get\_blob()**. O código seguinte transfere o blob atualizado numa secção anterior. "_DOWNLOADED" é adicionado como um sufixo ao nome do blob, para que possa ver ambos os ficheiros no disco local. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Limpar recursos
Se já não precisa dos blobs carregados neste guia de introdução, pode eliminar o contentor inteiro com o método **delete\_container()**. Se os ficheiros criados já não são necessários, utilize o método **delete\_blob()** para eliminar os ficheiros.

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```
## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Recursos para desenvolver aplicações Ruby com blobs

Veja estes recursos adicionais para desenvolvimento de Ruby com Armazenamento de blobs:

- Veja e transfira o [código fonte da biblioteca de cliente Ruby](https://github.com/Azure/azure-storage-ruby) para o Armazenamento do Azure no GitHub.
- Explore os [Exemplos de armazenamento de blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=ruby&term=blob) escritos com a biblioteca de cliente Ruby.

## <a name="next-steps"></a>Passos Seguintes
 
Neste início rápido, aprendeu a transferir ficheiros entre um disco local e o armazenamento de blobs do Azure com Ruby. Para saber mais sobre a utilização do armazenamento de blobs, avance para os Procedimentos do armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Procedimentos de Operações de Armazenamento de Blobs](./storage-ruby-how-to-use-blob-storage.md)


Para obter mais informações sobre o Explorador de armazenamento e os Blobs, veja [Manage Azure Blob storage resources with Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Gerir recursos do armazenamento de Blobs do Azure com o Explorador de Armazenamento).
