---
title: Requisitos de armazenamento de BLOBs de caixa de dados do Microsoft Azure | Documentos da Microsoft
description: Saiba mais sobre as versões suportadas para APIs, SDKs e bibliotecas de cliente para o armazenamento de BLOBs de caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436499"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Requisitos de armazenamento de BLOBs de caixa de dados do Azure

Este artigo lista as versões dos APIs do Azure, bibliotecas de cliente do Azure e ferramentas de suporte com o armazenamento de BLOBs de caixa de dados. Armazenamento de BLOBs de caixa de dados proporciona funcionalidades de gestão de Blobs com uma semântica consistente para o Azure. Este artigo também resume as diferenças de armazenamento de BLOBs de caixa de dados do Azure conhecidas dos serviços de armazenamento do Azure.

Recomendamos que reveja as informações cuidadosamente antes de ligar ao armazenamento de BLOBs de caixa de dados e, em seguida, regressar à mesma conforme necessário.


## <a name="storage-differences"></a>Diferenças de armazenamento

|     Funcionalidade                                             |     Storage do Azure                                     |     Armazenamento de BLOBs de caixa de dados |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Armazenamento de ficheiros do Azure                                   |    Com base na cloud partilhas de ficheiros SMB suportadas              |    Não suportado      |
|    Encriptação do serviço para dados Inativos                  |    encriptação AES de 256 bits                             |    encriptação AES de 256 bits |
|    Tipo de conta de armazenamento                                 |    Contas de armazenamento de Blobs do Azure e para fins gerais    |    Apenas para fins gerais v1|
|    Nome do blob                                            |    os 1024 carateres (2.048 bytes)                     |    880 carateres (1,760 bytes)|
|    Tamanho máximo de blob de bloco                              |    4.75 TB (100 MB X 50 000 blocos)                   |    4.75 TB (100 MB x 50 000 blocos) ou posterior do Azure Data Box v 1.8.|
|    Tamanho máximo de blob de página                               |    8 TB                                               |    1 TB                   |
|    Tamanho de página do blob de página                                  |    512 bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versões de API suportadas

São suportadas as seguintes versões de APIs de serviço de armazenamento do Azure com o armazenamento de BLOBs de caixa de dados:

O Azure Data Box 1.8 e posteriores

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>Suporte a bibliotecas de cliente do Azure

Para armazenamento de BLOBs de caixa de dados, existem requisitos de sufixo de ponto final específico e bibliotecas de cliente específico. Os pontos de extremidade de armazenamento de BLOBs de caixa de dados não ter paridade completa com a versão mais recente do API de REST do armazenamento de Blobs do Azure, consulte a [versões suportadas do Azure Data Box 1.8 e posteriores](#supported-api-versions). Para as bibliotecas de cliente de armazenamento, precisa estar atento a versão compatível com a API REST.

### <a name="azure-data-box-18-onwards"></a>O Azure Data Box 1.8 e posteriores

| Biblioteca de cliente     |Versão suportada do armazenamento de BLOBs de caixa de dados     | Ligação   |     Especificação de ponto final      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Pacote de Nuget:   https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Versão do GitHub:   https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    app.config file                 |
|    Java                |    7.0.0                                           |    Pacote maven:   https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Versão do GitHub:   https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Configuração de cadeia de ligação         |
|    Node.js             |    2.8.3                                           |    Ligação do NPM:   https://www.npmjs.com/package/azure-storage   (Run: `npm install azure-storage@2.7.0`)   <br>Versão do GitHub:   https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Declaração de instância de serviço    |
|    C++                 |    5.2.0                                           |    Pacote de Nuget:   https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Versão do GitHub:   https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Configuração de cadeia de ligação         |
|    PHP                 |    1.2.0                                           |    Versão do GitHub:<br>Comuns: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Instalar através do compositor (para obter mais informações, consulte os detalhes abaixo.)                                                                                                             |    Configuração de cadeia de ligação         |
|    Python              |    1.1.0                                           |    Versão do GitHub:<br>Comuns:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:   https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Declaração de instância de serviço    |
|    Ruby                |    1.0.1                                           |    Pacote do RubyGems:<br>Comuns:   https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Versão do GitHub:<br>Comuns: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Configuração de cadeia de ligação         |



### <a name="install-php-client-via-composer---current"></a>Instalar o cliente PHP através do compositor - atual

Para instalar através do compositor: (blob de tome como exemplo).
1. Crie um ficheiro denominado Composer. JSON na raiz do projeto com o código a seguir:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Transferir `composer.phar` na raiz do projeto.

3. Run: instalar o php composer.phar.

### <a name="endpoint-declaration"></a>Declaração de ponto final

Um ponto de extremidade de armazenamento de BLOBs de caixa de dados do Azure inclui duas partes: o nome de uma região e o domínio do Data Box. No armazenamento de BLOBs de caixa de dados SDK, o ponto final predefinido é `\<serial no. of the device>.microsoftdatabox.com`.  Para obter mais informações sobre o ponto final de serviço de BLOBs, aceda a [ligar através do armazenamento de BLOBs de caixa de dados](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Exemplos

### <a name="net"></a>.NET

Para armazenamento de BLOBs de caixa de dados, o sufixo de ponto final é especificado no `app.config` ficheiro:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Para armazenamento de BLOBs de caixa de dados, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Para armazenamento de BLOBs de caixa de dados, o sufixo de ponto final é especificado na instância da declaração:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Para armazenamento de BLOBs de caixa de dados, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Para armazenamento de BLOBs de caixa de dados, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para armazenamento de BLOBs de caixa de dados, o sufixo de ponto final é especificado na instância da declaração:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Para armazenamento de BLOBs de caixa de dados, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Passos Seguintes

* [Implementar o Azure Data Box](data-box-deploy-ordered.md)
