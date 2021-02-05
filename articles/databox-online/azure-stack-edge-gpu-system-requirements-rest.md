---
title: Requisitos de armazenamento do Microsoft Azure Stack Edge Blob| Microsoft Docs
description: Conheça as versões suportadas para APIs, SDKs e bibliotecas de clientes para armazenamento Azure Stack Edge Blob
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 887aaf32592c74a5884916bc7ad63d79544fb554
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575860"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Requisitos de armazenamento Azure Stack Edge Blob

Este artigo lista as versões das APIs Azure, bibliotecas de clientes Azure e ferramentas suportadas com o armazenamento Azure Stack Edge Blob. O armazenamento Azure Stack Edge Blob fornece funcionalidade de gestão de blob com semântica consistente Azure. Este artigo também resume as diferenças de armazenamento conhecidas Azure Stack Edge Blob dos serviços de Armazenamento Azure.

Recomendamos que reveja as informações cuidadosamente antes de ligar ao armazenamento Azure Stack Edge Blob e, em seguida, reencasse as informações conforme necessário.

## <a name="storage-differences"></a>Diferenças de armazenamento

|     Funcionalidade                                             |     Storage do Azure                                     |     Armazenamento Azure Stack Edge Blob |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Armazenamento de Ficheiros do Azure                                   |    Ações de ficheiro smb baseadas em nuvem suportadas              |    Não suportado      |
|    Tipo de conta de armazenamento                                 |    Contas de armazenamento geral e Azure Blob    |    Apenas v1 para fins gerais|
|    Nome do blob                                            |    1.024 caracteres (2.048 bytes)                     |    880 caracteres (1.760 bytes)|
|    Bloco blob tamanho máximo                              |    4.75 TB (100 MB X 50.000 blocos)                   |    4.75 TB (100 MB x 50.000 blocos) para Azure Stack Edge|
|    Tamanho máximo da bolha de página                               |    8 TB                                               |    1 TB                   |
|    Tamanho da página blob de página                                  |    512 bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versões API suportadas

As seguintes versões do serviço Azure Storage APIs são suportadas com o armazenamento Azure Stack Edge Blob.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 em diante

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Bibliotecas de clientes Azure suportadas

Para o armazenamento do Azure Stack Edge Blob, existem bibliotecas específicas de clientes e requisitos específicos de sufixo de ponto final. Os pontos finais de armazenamento Azure Stack Edge Blob não têm paridade total com a versão mais recente da Azure Blob Storage REST API; consulte as [versões API suportadas para Azure Stack Edge](#supported-api-versions). Para as bibliotecas de clientes de armazenamento, você precisa estar ciente da versão que é compatível com a API REST.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 em diante

As seguintes versões da biblioteca de clientes Azure são suportadas para o armazenamento do Azure Stack Edge Blob.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>Instale o cliente PHP via Compositor - Corrente

Para instalar o cliente PHP via Compositor:

1. Crie um ficheiro nomeado composer.jsna raiz do projeto com o seguinte código (por exemplo, utiliza o serviço Azure Storage Blob).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Descarregue `composer.phar` para a raiz do projeto.

3. Execução: instalação php composer.phar.


## <a name="endpoint-declaration"></a>Declaração de ponto final

No armazenamento Azure Stack Edge Blob SDK, o sufixo do ponto final - `<device serial number>.microsoftdatabox.com` identifica o domínio Azure Stack Edge. Para obter mais informações sobre o ponto final do serviço blob, vá a [transferir dados através de contas de armazenamento com a Azure Stack Edge Pro GPU](azure-stack-edge-j-series-deploy-add-storage-accounts.md).


## <a name="examples"></a>Exemplos

### <a name="net"></a>.NET

Para o armazenamento do Blob Azure Stack Edge, o sufixo do ponto final é especificado no `app.config` ficheiro:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Para o armazenamento do Blob Azure Stack Edge, o sufixo do ponto final é especificado na configuração da cadeia de ligação:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Para o armazenamento do Blob Azure Stack Edge, o sufixo do ponto final é especificado na instância de declaração:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Para o armazenamento do Blob Azure Stack Edge, o sufixo do ponto final é especificado na configuração da cadeia de ligação:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Para o armazenamento do Blob Azure Stack Edge, o sufixo do ponto final é especificado na configuração da cadeia de ligação:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para o armazenamento do Blob Azure Stack Edge, o sufixo do ponto final é especificado na instância de declaração:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Para o armazenamento do Blob Azure Stack Edge, o sufixo do ponto final é especificado na configuração da cadeia de ligação:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Passos seguintes

* [Prepare-se para implementar O Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-deploy-prep.md)
