---
title: Configurar uma cadeia de conexão para o armazenamento do Azure
description: Configure uma cadeia de conexão para uma conta de armazenamento do Azure. Uma cadeia de conexão contém as informações necessárias para autorizar o acesso a uma conta de armazenamento de seu aplicativo em tempo de execução usando a autorização de chave compartilhada.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0f73871d8248b2f52bab5934eef03d883c72ed79
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985379"
---
# <a name="configure-azure-storage-connection-strings"></a>Configurar Cadeias de Ligação do Storage do Azure

Uma cadeia de conexão inclui as informações de autenticação necessárias para que seu aplicativo acesse dados em uma conta de armazenamento do Azure em tempo de execução usando a autorização de chave compartilhada. Você pode configurar cadeias de conexão para:

* Conecte-se ao emulador de armazenamento do Azure.
* Acesse uma conta de armazenamento no Azure.
* Acesse recursos especificados no Azure por meio de uma assinatura de acesso compartilhado (SAS).

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Exibir e copiar uma cadeia de conexão

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Armazenar uma cadeia de conexão

Seu aplicativo precisa acessar a cadeia de conexão em tempo de execução para autorizar solicitações feitas ao armazenamento do Azure. Você tem várias opções para armazenar a cadeia de conexão:

* Você pode armazenar a cadeia de conexão em uma variável de ambiente.
* Um aplicativo em execução na área de trabalho ou em um dispositivo pode armazenar a cadeia de conexão em um arquivo **app. config** ou **Web. config** . Adicione a cadeia de conexão à seção appSettings nesses arquivos.
* Um aplicativo em execução em um serviço de nuvem do Azure pode armazenar a cadeia de conexão no [arquivo de esquema de configuração do serviço do Azure (. cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Adicione a cadeia de conexão à seção **ConfigurationSettings** do arquivo de configuração de serviço.

Armazenar a cadeia de conexão em um arquivo de configuração torna fácil atualizar a cadeia de conexão para alternar entre o emulador de armazenamento e uma conta de armazenamento do Azure na nuvem. Você só precisa editar a cadeia de conexão para apontar para o seu ambiente de destino.

Você pode usar o [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) para acessar a cadeia de conexão em tempo de execução, independentemente de onde o aplicativo está sendo executado.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Configurar uma cadeia de conexão para o emulador de armazenamento

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre o emulador de armazenamento, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Configurar uma cadeia de conexão para uma conta de armazenamento do Azure

Para criar uma cadeia de conexão para sua conta de armazenamento do Azure, use o formato a seguir. Indique se você deseja se conectar à conta de armazenamento por meio de HTTPS (recomendado) ou `myAccountName` http, substitua pelo nome da sua conta de armazenamento `myAccountKey` e substitua pela chave de acesso da conta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Por exemplo, a cadeia de conexão pode ser semelhante a:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Embora o armazenamento do Azure dê suporte a HTTP e HTTPS em uma cadeia de conexão, o *https é altamente recomendado*.

> [!TIP]
> Você pode encontrar as cadeias de conexão da sua conta de armazenamento no [portal do Azure](https://portal.azure.com). Navegue até **configurações** > **chaves de acesso** na folha do menu da sua conta de armazenamento para ver cadeias de conexão para as chaves de acesso primária e secundária.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Criar uma cadeia de conexão usando uma assinatura de acesso compartilhado

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Criar uma cadeia de conexão para um ponto de extremidade de armazenamento explícito

Você pode especificar pontos de extremidade de serviço explícitos na cadeia de conexão em vez de usar os pontos de extremidade padrão. Para criar uma cadeia de conexão que especifica um ponto de extremidade explícito, especifique o ponto de extremidade de serviço completo para cada serviço, incluindo a especificação de protocolo (HTTPS (recomendado) ou HTTP), no seguinte formato:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Um cenário em que você pode desejar especificar um ponto de extremidade explícito é quando você mapeou o ponto de extremidade do armazenamento de BLOBs para um [domínio personalizado](../blobs/storage-custom-domain-name.md). Nesse caso, você pode especificar seu ponto de extremidade personalizado para o armazenamento de blob na sua cadeia de conexão. Opcionalmente, você pode especificar os pontos de extremidade padrão para os outros serviços se seu aplicativo os usar.

Aqui está um exemplo de uma cadeia de conexão que especifica um ponto de extremidade explícito para o serviço blob:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Este exemplo especifica pontos de extremidade explícitos para todos os serviços, incluindo um domínio personalizado para o serviço blob:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Os valores de ponto de extremidade em uma cadeia de conexão são usados para construir os URIs de solicitação para os serviços de armazenamento e ditam o formulário de quaisquer URIs que são retornados ao seu código.

Se você tiver mapeado um ponto de extremidade de armazenamento para um domínio personalizado e omitir esse ponto de extremidade de uma cadeia de conexão, não será possível usar essa cadeia de conexão para acessar dados nesse serviço a partir do seu código.

> [!IMPORTANT]
> Os valores de ponto de extremidade de serviço em suas cadeias de conexão devem `https://` ser URIs bem formados, incluindo (recomendado) ou. `http://` Como o armazenamento do Azure ainda não dá suporte a HTTPS para domínios personalizados, `http://` você deve especificar para qualquer URI de ponto de extremidade que aponte para um domínio personalizado.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Criar uma cadeia de conexão com um sufixo de ponto de extremidade

Para criar uma cadeia de conexão para um serviço de armazenamento em regiões ou instâncias com sufixos de ponto de extremidade diferentes, como para o Azure China 21Vianet ou Azure governamental, use o seguinte formato de cadeia de conexão. Indique se você deseja se conectar à conta de armazenamento por meio de HTTPS (recomendado) ou `myAccountName` http, substitua pelo nome da sua conta de `myAccountKey` armazenamento, substitua pela chave de acesso da `mySuffix` conta e substitua pelo sufixo do URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Aqui está um exemplo de cadeia de conexão para serviços de armazenamento na 21Vianet do Azure na China:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analisando uma cadeia de conexão

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md)
* [Gerenciadores de armazenamento do Azure](storage-explorers.md)
* [Usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md)
