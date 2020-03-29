---
title: Requisitos de armazenamento da Caixa de Dados do Microsoft Azure Microsoft Docs
description: Conheça as versões suportadas para APIs, SDKs e bibliotecas de clientes para armazenamento Blob De Dados Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436499"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Requisitos de armazenamento de caixas de dados Azure Blob

Este artigo lista as versões das APIs Azure, bibliotecas de clientes Azure e ferramentas suportadas com o armazenamento data Box Blob. O armazenamento de Caixa de Dados Blob fornece funcionalidade de gestão de blob com semântica consistente com Azure. Este artigo também resume as conhecidas diferenças de armazenamento da Caixa de Dados Azure blob dos serviços de Armazenamento Azure.

Recomendamos que reveja a informação cuidadosamente antes de se ligar ao armazenamento de Caixa de Dados Blob e, em seguida, consulte-a de volta, se necessário.


## <a name="storage-differences"></a>Diferenças de armazenamento

|     Funcionalidade                                             |     Storage do Azure                                     |     Armazenamento de blobs do Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Armazenamento de Ficheiros do Azure                                   |    Ações de ficheiroS SMB baseadas na nuvem suportadas              |    Não suportado      |
|    Encriptação de serviço para dados em Repouso                  |    Encriptação AES de 256 bits                             |    Encriptação AES de 256 bits |
|    Tipo de conta de armazenamento                                 |    Contas de armazenamento de bolhas de uso geral e azure    |    V1 de propósito geral|
|    Nome do blob                                            |    1.024 caracteres (2.048 bytes)                     |    880 caracteres (1.760 bytes)|
|    Bloco de bloco sintetiza tamanho máximo                              |    4.75 TB (100 MB X 50.000 blocos)                   |    4.75 TB (100 MB x 50.000 blocos) para a Caixa de Dados Azure v 1.8 em diante.|
|    Página blob tamanho máximo                               |    8 TB                                               |    1 TB                   |
|    Tamanho da página blob                                  |    512 bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versões API suportadas

As seguintes versões do serviço de armazenamento Azure SÃO suportadas com armazenamento de Caixa de Dados Blob:

Caixa de dados Azure 1.8 em diante

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Bibliotecas de clientes Azure suportadas

Para armazenamento de Caixa de Dados Blob, existem bibliotecas específicas de clientes e requisitos específicos de sufixo final. Os pontos finais de armazenamento da Caixa de Dados Blob não têm total paridade com a versão mais recente da API de Armazenamento De Armazenamento De Caixa De Dados Azure, ver as [versões suportadas para a Caixa de Dados Azure 1.8.](#supported-api-versions) Para as bibliotecas de clientes de armazenamento, você precisa estar ciente da versão que é compatível com a API REST.

### <a name="azure-data-box-18-onwards"></a>Caixa de dados Azure 1.8 em diante

| Biblioteca de cliente     |Versão suportada pelo armazenamento de Caixa de Dados Blob     | Ligação   |     Especificação de ponto final      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Pacote Nuget:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Lançamento gitHub:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    ficheiro app.config                 |
|    Java                |    7.0.0                                           |    Pacote Maven:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Lançamento gitHub:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Configuração de cordas de ligação         |
|    Node.js             |    2.8.3                                           |    Ligação NPM: https://www.npmjs.com/package/azure-storage `npm install azure-storage@2.7.0`(Executar: )   <br>Lançamento gitHub:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Declaração de instância de serviço    |
|    C++                 |    5.2.0                                           |    Pacote Nuget:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Lançamento gitHub:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Configuração de cordas de ligação         |
|    PHP                 |    1.2.0                                           |    Lançamento gitHub:<br>Comum:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Instale via Compositor (Para saber mais, Consulte os detalhes abaixo.)                                                                                                             |    Configuração de cordas de ligação         |
|    Python              |    1.1.0                                           |    Lançamento gitHub:<br>Comum:https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Declaração de instância de serviço    |
|    Ruby                |    1.0.1                                           |    Pacote RubyGems:<br>Comum:https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob:https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Lançamento gitHub:<br>Comum:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Configuração de cordas de ligação         |



### <a name="install-php-client-via-composer---current"></a>Instalar cliente PHP via Compositor - atual

Para instalar via Compositor: (tome blob como exemplo).
1. Crie um ficheiro chamado compositor.json na raiz do projeto com o seguinte código:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Descarregue `composer.phar` para a raiz do projeto.

3. Executar: php compositor.phar instalar.

### <a name="endpoint-declaration"></a>Declaração de ponto final

Um ponto final de armazenamento de caixa de dados Azure blob inclui duas partes: o nome de uma região e o domínio data box. No Armazenamento De Caixa de Dados SDK, `\<serial no. of the device>.microsoftdatabox.com`o ponto final predefinido é .  Para mais informações sobre o ponto final do serviço blob, vá a [Connect via armazenamento de Caixa de Dados Blob](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Exemplos

### <a name="net"></a>.NET

Para o armazenamento de caixa de dados Blob, `app.config` o sufixo de ponto final é especificado no ficheiro:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Para o armazenamento de caixa de dados Blob, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Para o armazenamento de caixa de dados Blob, o sufixo final é especificado na instância de declaração:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Para o armazenamento de caixa de dados Blob, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Para o armazenamento de caixa de dados Blob, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para o armazenamento de caixa de dados Blob, o sufixo final é especificado na instância de declaração:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Para o armazenamento de caixa de dados Blob, o sufixo de ponto final é especificado na configuração da cadeia de ligação:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Passos seguintes

* [Implemente a sua Caixa de Dados Azure](data-box-deploy-ordered.md)
