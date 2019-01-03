---
title: Introdução às ferramentas de desenvolvimento de armazenamento do Azure Stack | Documentos da Microsoft
description: Documentação de orientação para começar a utilizar com o uso de ferramentas de desenvolvimento de armazenamento do Azure Stack
services: azure-stack
author: mattbriggs
ms.author: mabrigg
ms.date: 12/03/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 56814cc44874fe0c169e5fb51dc6cbff5a225231
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727858"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Introdução às ferramentas de desenvolvimento de armazenamento do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Microsoft Azure Stack fornece um conjunto de serviços de armazenamento que inclui o blob, tabela e armazenamento de filas.

Utilize este artigo como guia para começar a utilizar ferramentas de desenvolvimento de armazenamento do Azure Stack. Pode encontrar informações mais detalhadas e código de exemplo nos tutoriais de armazenamento do Azure correspondente.

> [!NOTE]
> É conhecido que são as diferenças entre o armazenamento do Azure Stack e o armazenamento do Azure, incluindo requisitos específicos para cada plataforma. Por exemplo, existem requisitos de sufixo de ponto final específico para o Azure Stack e bibliotecas de cliente específico. Para obter mais informações, consulte [armazenamento do Azure Stack: Diferenças e considerações](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliotecas de cliente do Azure

Para as bibliotecas de cliente de armazenamento, lembre-se de que a versão compatível com a API REST. Também tem de especificar o ponto de final do Azure Stack em seu código.

### <a name="1811-update-or-newer-versions"></a>1811 atualização ou as versões mais recentes

| Biblioteca de cliente | Uma versão suportada do Azure Stack | Ligação | Especificação de ponto final |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Pacote de Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | ficheiro App. config |
| Java | 6.1.0 | Pacote maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Configuração de cadeia de ligação |
| Node.js | 2.7.0 | Ligação do NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Executar: `npm install azure-storage@2.7.0`)<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Declaração de instância de serviço |
| C++ | 3.1.0 | Pacote de Nuget:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Configuração de cadeia de ligação |
| PHP | 1.0.0 | Versão do GitHub:<br>Comuns: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Fila:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tabela: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Instalar através do compositor (para saber mais, [ver os detalhes abaixo](#install-php-client-via-composer---current).) | Configuração de cadeia de ligação |
| Python | 1.0.0 | Versão do GitHub:<br>Comuns:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Fila:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Declaração de instância de serviço |
| Ruby | 1.0.1 | Pacote do RubyGems:<br>Comuns:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Fila: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tabela: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Versão do GitHub:<br>Comuns: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Fila: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabela: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Configuração de cadeia de ligação |

#### <a name="install-php-client-via-composer---current"></a>Instalar o cliente PHP através do compositor - atual

Para instalar através do compositor: (demorar blob como um exemplo).

1. Crie um ficheiro denominado **Composer. JSON** na raiz do projeto com o código a seguir:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Baixe [composer.phar](http://getcomposer.org/composer.phar) na raiz do projeto.
3. Run: `php composer.phar install`.

### <a name="previous-versions-1802-to-1809-update"></a>Versões anteriores (atualização 1802 para 1809)

|Biblioteca de cliente|Uma versão suportada do Azure Stack|Ligação|Especificação de ponto final|
|---------|---------|---------|---------|
|.NET     |6.2.0|Pacote de Nuget:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|ficheiro App. config|
|Java|4.1.0|Pacote maven:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Versão do GitHub:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Configuração de cadeia de ligação|
|Node.js     |1.1.0|Ligação do NPM:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(executar: `npm install azure-storage@1.1.0)`<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Declaração de instância de serviço||C++|2.4.0|Pacote de Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuração de cadeia de ligação|
|C++|2.4.0|Pacote de Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuração de cadeia de ligação|
|PHP|0.15.0|Versão do GitHub:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Instalar através do compositor (ver detalhes abaixo)|Configuração de cadeia de ligação|
|Python     |0.30.0|Pacote PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Executar: `pip install -v azure-storage==0.30.0)`<br><br>Versão do GitHub:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Declaração de instância de serviço|
|Ruby|0.12.1<br>Pré-visualização|Pacote do RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Versão do GitHub:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Configuração de cadeia de ligação|

#### <a name="install-php-client-via-composer---previous"></a>Instalar o cliente PHP através do compositor - anterior

Para instalar através do compositor: (blob de tome como exemplo).

1. Crie um ficheiro denominado **Composer. JSON** na raiz do projeto com o código a seguir:

  ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. Baixe [composer.phar](http://getcomposer.org/composer.phar) na raiz do projeto.
3. Run: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Declaração de ponto final

Um ponto de final do Azure Stack inclui duas partes: o nome de uma região e o domínio do Azure Stack.
O Kit de desenvolvimento do Azure Stack, o ponto final predefinido é **local.azurestack.external**.
Se não tiver a certeza sobre o ponto final, contacte o administrador da nuvem.

## <a name="examples"></a>Exemplos

### <a name="net"></a>.NET

Para o Azure Stack, o sufixo de ponto final é especificado no ficheiro App. config:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Para o Azure Stack, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Para o Azure Stack, o sufixo de ponto final é especificado na instância da declaração:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Para o Azure Stack, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Para o Azure Stack, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```php
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para o Azure Stack, o sufixo de ponto final é especificado na instância da declaração:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Para o Azure Stack, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Armazenamento de blobs

Os tutoriais de armazenamento de Blobs do Azure seguintes são aplicáveis ao Azure Stack. Tenha em atenção o requisito de sufixo de ponto final específico para o Azure Stack descrito no anterior [exemplos](#examples) secção.

* [Introdução ao armazenamento de Blobs do Azure através do .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Como utilizar o Armazenamento de blobs do Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de blobs do Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Como utilizar o armazenamento de Blobs do C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como utilizar o Armazenamento de blobs do PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Como utilizar o armazenamento de Blobs do Azure do Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Como utilizar o Armazenamento de blobs do Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Armazenamento de filas

Os tutoriais de armazenamento de filas do Azure seguintes são aplicáveis ao Azure Stack. Tenha em atenção o requisito de sufixo de ponto final específico para o Azure Stack descrito no anterior [exemplos](#examples) secção.

* [Introdução ao Armazenamento de filas do Azure através do .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Como utilizar o Armazenamento de filas do Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Como utilizar o Armazenamento de filas do Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Como utilizar o armazenamento de filas do C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Como utilizar o Armazenamento de filas do PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Como utilizar o Armazenamento de filas do Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Como utilizar o Armazenamento de filas do Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>Table Storage

Os tutoriais de armazenamento de tabelas do Azure seguintes são aplicáveis ao Azure Stack. Tenha em atenção o requisito de sufixo de ponto final específico para o Azure Stack descrito no anterior [exemplos](#examples) secção.

* [Introdução ao armazenamento de Tabelas do Azure através do .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Como utilizar o Armazenamento de tabelas do Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Como utilizar o Armazenamento de Tabelas do Azure a partir de Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Como utilizar o armazenamento de tabelas do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como utilizar o Armazenamento de tabelas do PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Como utilizar o armazenamento de tabelas em Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Como utilizar o Armazenamento de tabelas do Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
