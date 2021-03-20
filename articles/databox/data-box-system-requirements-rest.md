---
title: Requisitos de armazenamento da Caixa de Dados do Microsoft Azure| Microsoft Docs
description: Conheça as versões suportadas para APIs, SDKs e bibliotecas de clientes para armazenamento Azure Data Box Blob
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91744113"
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
|    Bloco blob tamanho máximo                              |    4.75 TB (100 MB X 50.000 blocos)                   |    4.75 TB (100 MB x 50.000 blocos) para Azure Data Box v 3.0 em diante.|
|    Tamanho máximo da bolha de página                               |    8 TB                                               |    1 TB                   |
|    Tamanho da página blob de página                                  |    512 bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versões API suportadas

As seguintes versões do serviço Azure Storage APIs são suportadas com o armazenamento de Data Box Blob.

### <a name="azure-data-box-30-onwards"></a>Caixa de Dados Azure 3.0 em diante

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Bibliotecas de clientes Azure suportadas

Para o armazenamento de Caixa de Dados Blob, existem bibliotecas específicas de clientes e requisitos específicos de sufixo de ponto final. Os pontos finais de armazenamento da Caixa de Dados Blob não têm paridade total com a versão mais recente da Azure Blob Storage REST API; ver as [versões suportadas para Azure Data Box 3.0 em diante](#supported-api-versions). Para as bibliotecas de clientes de armazenamento, você precisa estar ciente da versão que é compatível com a API REST.

### <a name="azure-data-box-30-onwards"></a>Caixa de Dados Azure 3.0 em diante

As seguintes versões da biblioteca de clientes Azure são suportadas para armazenamento de Data Box Blob.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

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

No armazenamento de caixa de dados SDK, o sufixo de ponto final - identifica o domínio da Caixa de `<device serial number>.microsoftdatabox.com` Dados. Para obter mais informações sobre o ponto final do serviço blob, vá ao [Connect via Data Box Blob.](data-box-deploy-copy-data-via-rest.md)
 
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
