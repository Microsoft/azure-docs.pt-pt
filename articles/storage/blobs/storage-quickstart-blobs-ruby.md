---
title: 'Quickstart: Biblioteca de clientes Azure Blob Storage - Ruby'
description: Crie uma conta de armazenamento e um recipiente no Azure Blob Storage. Use a biblioteca do cliente de armazenamento para ruby para criar uma bolha, baixar uma bolha e listar as bolhas em um recipiente.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ec3fc490466f5fce36b67b2f3744e4ee5cc0ae79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96781101"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Quickstart: Biblioteca de clientes Azure Blob Storage para Ruby

Aprenda a usar a Ruby para criar, descarregar e listar bolhas num recipiente no Microsoft Azure Blob Storage.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Certifique-se de que tem os seguintes pré-requisitos adicionais instalados:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Biblioteca de armazenamento Azure para Ruby,](https://github.com/azure/azure-storage-ruby)utilizando o [pacote RubyGem:](https://rubygems.org/gems/azure-storage-blob)

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A [aplicação de exemplo](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) utilizada neste início rápido é uma aplicação de Ruby básica.

Use [o Git](https://git-scm.com/) para descarregar uma cópia da aplicação para o seu ambiente de desenvolvimento. Este comando clona o repositório da sua máquina local:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Navegue para a pasta *de armazenamento-blobs-ruby-quickstart* e abra o ficheiro *exemplo.rb* no seu editor de código.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Forneça o nome da sua conta de armazenamento e a chave da conta para criar uma instância [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) para a sua aplicação.

O código seguinte no ficheiro *exemplo.rb* instantaneamente um novo objeto [BlobService.](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) Substitua os valores *accountname* e *accountkey* pelo nome da conta e a chave.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Executar o exemplo

A amostra cria um recipiente no Blob Storage, cria uma nova bolha no recipiente, lista as bolhas no recipiente e transfere a bolha para um ficheiro local.

Execute o exemplo. Aqui está um exemplo da saída da execução da aplicação:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Quando pressiona Enter para continuar, o programa de amostras elimina o recipiente de armazenamento e o ficheiro local. Antes de continuar, verifique a pasta *Documentos* para obter o ficheiro descarregado.

Também pode utilizar [o Azure Storage Explorer](https://storageexplorer.com) para visualizar os ficheiros na sua conta de armazenamento. O Explorador de Armazenamento do Azure é uma ferramenta multiplataformas gratuita que lhe permite aceder às informações da sua conta de armazenamento.

Depois de verificar os ficheiros, prima a tecla 'Entrar' para eliminar os ficheiros de teste e terminar a demonstração. Abra o ficheiro *exemplo.rb* para ver o código.

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Em seguida, passamos pelo código da amostra para que possa entender como funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento

A primeira coisa a fazer é criar casos dos objetos usados para aceder e gerir o Blob Storage. Estes objetos dependem uns dos outros. Cada objeto é utilizado pelo objeto seguinte na lista.

- Crie uma instância do objeto do armazenamento do Azure [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) para configurar as credenciais de ligação.
- Crie o objeto [do Recipiente,](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) que representa o recipiente a que está a aceder. Os contentores são utilizados para organizar os blobs, da mesma forma como utiliza pastas para organizar os ficheiros.

Uma vez que tenha o objeto do recipiente, pode criar um objeto [blob block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) que aponta para uma bolha específica na qual está interessado. Utilize o objeto [Bloco](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) para criar, transferir e copiar bolhas.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre os nomes dos recipientes e blobs, consulte [os recipientes de nomeação e referência, bolhas e metadados.](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)

O seguinte código de exemplo:

- Cria um novo recipiente
- Define permissões no recipiente para que as bolhas sejam públicas. O recipiente é chamado *quickstartblobs* com um ID único anexado.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Criar uma bolha no recipiente

O Blob Storage suporta bolhas de blocos, bolhas de apêndice e bolhas de página. Para criar uma bolha, ligue para o método [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) que passa nos dados para a bolha.

O exemplo a seguir cria uma bolha chamada *QuickStart_* com um ID único e uma extensão de ficheiro *.txt* no recipiente criado anteriormente.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

As bolhas de bloco podem ser tão grandes como 4.7 TB, e podem ser qualquer coisa, desde folhas de cálculo a grandes ficheiros de vídeo. As bolhas de página são usadas principalmente para os ficheiros VHD que fazem parte das máquinas virtuais IaaS. As bolhas de apêndice são geralmente usadas para registar registos, como quando pretende escrever para um ficheiro e, em seguida, continuar a adicionar mais informações.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Obtenha uma lista de ficheiros no contentor com o método [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method). O código seguinte recupera a lista de bolhas e, em seguida, apresenta os seus nomes.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Transferir um blob

Faça o download de uma bolha para o seu disco local utilizando o método [get_blob.](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) O código seguinte descarrega a bolha criada numa secção anterior.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Limpar os recursos

Se deixar de ser necessária uma bolha, utilize [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) para a remover. Elimine um recipiente inteiro utilizando o método [delete_container.](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) A eliminação de um recipiente também elimina quaisquer bolhas armazenadas no recipiente.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Recursos para desenvolver aplicações Ruby com blobs

Consulte estes recursos adicionais para o desenvolvimento da Ruby:

- Veja e transfira o [código fonte da biblioteca de cliente Ruby](https://github.com/Azure/azure-storage-ruby) para o Armazenamento do Azure no GitHub.
- Explore [as amostras do Azure escritas](/samples/browse/?products=azure&languages=ruby) utilizando a biblioteca de clientes Ruby.
- [Amostra: Começar com armazenamento Azure em Ruby](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a transferir ficheiros entre o Azure Blob Storage e um disco local utilizando a Ruby. Para saber mais sobre como trabalhar com o Blob Storage, continue para a visão geral da conta de Armazenamento.

> [!div class="nextstepaction"]
> [Descrição geral da conta de armazenamento](../common/storage-account-overview.md)

Para obter mais informações sobre o Explorador de Armazenamento e Blobs, consulte [gerir os recursos de armazenamento de Azure Blob com o Storage Explorer.](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
