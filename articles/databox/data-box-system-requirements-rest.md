---
title: Requisitos de armazenamento da Caixa de Dados do Microsoft Azure/ Microsoft Docs
description: Conheça as versões suportadas para APIs, SDKs e bibliotecas de clientes para armazenamento Azure Data Box Blob
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "61436499"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Requisitos de armazenamento de blob de caixa de dados Azure

Este artigo lista as versões das APIs Azure, bibliotecas de clientes Azure e ferramentas suportadas com o armazenamento da Caixa de Dados Blob. O armazenamento da Bolha de Caixa de Dados fornece funcionalidade de gestão de blob com semântica consistente com a Azure. Este artigo também resume as diferenças conhecidas de armazenamento Azure Data Box Blob dos serviços de Armazenamento Azure.

Recomendamos que reveja as informações cuidadosamente antes de ligar ao armazenamento da Caixa de Dados Blob e, em seguida, reencasse as informações conforme necessário.


## <a name="storage-differences"></a>Diferenças de armazenamento

|     Funcionalidade                                             |     Storage do Azure                                     |     Armazenamento de blobs do Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Armazenamento de Ficheiros do Azure                                   |    Ações de ficheiro smb baseadas em nuvem suportadas              |    Não suportado      |
|    Encriptação de serviço para dados em Repouso                  |    Encriptação AES de 256 bits                             |    Encriptação AES de 256 bits |
|    Tipo de conta de armazenamento                                 |    Contas de armazenamento de bolhas gerais e Azure    |    Apenas v1 para fins gerais|
|    Nome do blob                                            |    1.024 caracteres (2.048 bytes)                     |    880 caracteres (1.760 bytes)|
|    Bloco blob tamanho máximo                              |    4.75 TB (100 MB X 50.000 blocos)                   |    4.75 TB (100 MB x 50.000 blocos) para Azure Data Box v 1.8 em diante.|
|    Tamanho máximo da bolha de página                               |    8 TB                                               |    1 TB                   |
|    Tamanho da página blob de página                                  |    512 bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versões API suportadas

As seguintes versões do serviço Azure Storage APIs são suportadas com o armazenamento de Data Box Blob:

Caixa de Dados Azure 1.8 em diante

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Bibliotecas de clientes Azure suportadas

Para o armazenamento de Caixa de Dados Blob, existem bibliotecas específicas de clientes e requisitos específicos de sufixo de ponto final. Os pontos finais de armazenamento da Caixa de Dados Blob não têm paridade total com a versão mais recente da Azure Blob Storage REST API, consulte as [versões suportadas para Azure Data Box 1.8 em diante](#supported-api-versions). Para as bibliotecas de clientes de armazenamento, você precisa estar ciente da versão que é compatível com a API REST.

### <a name="azure-data-box-18-onwards"></a>Caixa de Dados Azure 1.8 em diante

| Biblioteca de cliente     |Versão suportada por armazenamento de caixa de dados Blob     | Ligação   |     Especificação de ponto final      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Pacote Nuget:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Lançamento do GitHub:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config arquivo                 |
|    Java                |    7.0.0                                           |    Pacote Maven:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Lançamento do GitHub:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Configuração de cadeia de ligação         |
|    Node.js             |    2.8.3                                           |    Ligação NPM: https://www.npmjs.com/package/azure-storage (Executar: `npm install azure-storage@2.7.0` )   <br>Lançamento do GitHub:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Declaração de instância de serviço    |
|    C++                 |    5.2.0                                           |    Pacote Nuget:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Lançamento do GitHub:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Configuração de cadeia de ligação         |
|    PHP                 |    1.2.0                                           |    Lançamento do GitHub:<br>Comum:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Instale via Compositor (Para saber mais, consulte os detalhes abaixo.)                                                                                                             |    Configuração de cadeia de ligação         |
|    Python              |    1.1.0                                           |    Lançamento do GitHub:<br>Comum:https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Declaração de instância de serviço    |
|    Ruby                |    1.0.1                                           |    Pacote RubyGems:<br>Comum:https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob:https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Lançamento do GitHub:<br>Comum:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Configuração de cadeia de ligação         |



### <a name="install-php-client-via-composer---current"></a>Instalar cliente PHP via Compositor - corrente

Para instalar via Compositor: (tome blob como exemplo).
1. Crie um ficheiro nomeado composer.jsna raiz do projeto com o seguinte código:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Descarregue `composer.phar` para a raiz do projeto.

3. Execução: instalação php composer.phar.

### <a name="endpoint-declaration"></a>Declaração de ponto final

Um ponto final de armazenamento Azure Data Box Blob inclui duas partes: o nome de uma região e o domínio da Caixa de Dados. No SDK de armazenamento da caixa de dados, o ponto final padrão é `\<serial no. of the device>.microsoftdatabox.com` .  Para obter mais informações sobre o ponto final do serviço blob, aceda ao [Connect via Data Box Blob.](data-box-deploy-copy-data-via-rest.md)
 
## <a name="examples"></a>Exemplos

### <a name="net"></a>.NET

Para o armazenamento de Bloco de Caixa de Dados, o sufixo do ponto final é especificado no `app.config` ficheiro:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Para o armazenamento de blocos de caixa de dados, o sufixo do ponto final é especificado na configuração da cadeia de ligação:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Para o armazenamento de bolhas de caixa de dados, o sufixo do ponto final é especificado na instância da declaração:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Para o armazenamento de blocos de caixa de dados, o sufixo do ponto final é especificado na configuração da cadeia de ligação:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Para o armazenamento de blocos de caixa de dados, o sufixo do ponto final é especificado na configuração da cadeia de ligação:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para o armazenamento de bolhas de caixa de dados, o sufixo do ponto final é especificado na instância da declaração:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Para o armazenamento de blocos de caixa de dados, o sufixo do ponto final é especificado na configuração da cadeia de ligação:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Passos seguintes

* [Implemente a sua Caixa de Dados Azure](data-box-deploy-ordered.md)
