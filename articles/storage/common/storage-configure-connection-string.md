---
title: Configure uma cadeia de conexão
titleSuffix: Azure Storage
description: Configure uma cadeia de ligação para uma conta de armazenamento Azure. Uma cadeia de ligação contém as informações necessárias para autorizar o acesso a uma conta de armazenamento a partir da sua aplicação em tempo de execução, utilizando a autorização da Chave Partilhada.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3f2fa6cffd63811df596c8fca80afdc888a0eea9
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091615"
---
# <a name="configure-azure-storage-connection-strings"></a>Configurar Cadeias de Ligação do Storage do Azure

Um fio de ligação inclui as informações de autorização necessárias para a sua aplicação aceder aos dados numa conta de Armazenamento Azure em tempo de execução utilizando a autorização da Chave Partilhada. Pode configurar as cordas de ligação para:

* Ligue-se ao emulador de armazenamento Azurite.
* Aceda a uma conta de armazenamento em Azure.
* Aceder a recursos especificados em Azure através de uma assinatura de acesso partilhado (SAS).

Para saber como ver as chaves de acesso à sua conta e copiar uma cadeia de ligação, consulte [as teclas de acesso à conta de armazenamento](storage-account-keys-manage.md).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="store-a-connection-string"></a>Armazenar uma cadeia de conexão

A sua aplicação precisa de aceder à cadeia de ligação em tempo de execução para autorizar pedidos feitos ao Azure Storage. Tem várias opções para armazenar a sua cadeia de ligação:

* Pode armazenar a sua cadeia de ligação numa variável ambiental.
* Uma aplicação em execução no ambiente de trabalho ou num dispositivo pode armazenar a cadeia de ligação num **ficheiroapp.config** ou **web.config.** Adicione o fio de ligação à secção **AppSettings nestes** ficheiros.
* Uma aplicação em execução num serviço de nuvem Azure pode armazenar a cadeia de ligação no esquema de configuração do [serviço Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Adicione o fio de ligação à secção **ConfiguraçõesSettings** do ficheiro de configuração de serviço.

Armazenar a sua cadeia de ligação num ficheiro de configuração facilita a atualização da cadeia de ligação para alternar entre o [emulador de armazenamento Azurite](../common/storage-use-azurite.md) e uma conta de armazenamento Azure na nuvem. Basta editar a cadeia de ligação para apontar para o ambiente alvo.

Pode utilizar o [Gestor de Configuração do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) para aceder à sua cadeia de ligação em tempo de execução, independentemente do local onde a sua aplicação esteja a ser executada.

## <a name="configure-a-connection-string-for-azurite"></a>Configure uma cadeia de ligação para a azurite

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre a Azurite, consulte [o emulador Azurite para o desenvolvimento local do armazenamento Azure.](../common/storage-use-azurite.md)

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Configure uma cadeia de conexão para uma conta de armazenamento Azure

Para criar um string de ligação para a sua conta de armazenamento Azure, utilize o seguinte formato. Indique se pretende ligar à conta de armazenamento através de HTTPS (recomendado) ou HTTP, `myAccountName` substitua-o pelo nome da sua conta de armazenamento e substitua-a pela chave de `myAccountKey` acesso à conta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Por exemplo, a sua cadeia de ligação pode parecer semelhante a:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Embora o Azure Storage suporte http e HTTPS numa cadeia de ligação, *HTTPS é altamente recomendado*.

> [!TIP]
> Pode encontrar as cordas de ligação da sua conta de armazenamento no [portal Azure](https://portal.azure.com). Navegue para **DEFINIÇÕES**  >  **As teclas** de acesso na lâmina do menu da sua conta de armazenamento para ver as cordas de ligação para as teclas de acesso primária e secundária.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Criar uma cadeia de conexão usando uma assinatura de acesso compartilhado

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Crie uma cadeia de ligação para um ponto final de armazenamento explícito

Pode especificar pontos finais de serviço explícitos na sua cadeia de ligação em vez de utilizar os pontos finais predefinidos. Para criar uma cadeia de ligação que especifique um ponto final explícito, especifique o ponto final de serviço completo para cada serviço, incluindo a especificação do protocolo (HTTPS (recomendado) ou HTTP), no seguinte formato:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Um dos cenários em que poderá desejar especificar um ponto final explícito é quando mapeou o seu ponto final de armazenamento Blob para um [domínio personalizado.](../blobs/storage-custom-domain-name.md) Nesse caso, pode especificar o seu ponto final personalizado para o armazenamento blob na sua cadeia de ligação. Pode especificar opcionalmente os pontos finais predefinidos para os outros serviços se a sua aplicação os utilizar.

Aqui está um exemplo de uma cadeia de ligação que especifica um ponto final explícito para o serviço Blob:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Este exemplo especifica pontos finais explícitos para todos os serviços, incluindo um domínio personalizado para o serviço Blob:

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

Os valores do ponto final numa cadeia de ligação são utilizados para construir os URIs de pedido aos serviços de armazenamento e ditar a forma de quaisquer URIs que sejam devolvidos ao seu código.

Se mapeou um ponto final de armazenamento para um domínio personalizado e omite esse ponto final a partir de uma cadeia de ligação, então não poderá utilizar esse fio de ligação para aceder a dados nesse serviço a partir do seu código.

Para obter mais informações sobre a configuração de um domínio personalizado para o Armazenamento Azure, consulte [mapear um domínio personalizado para um ponto final de armazenamento Azure Blob](../blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> Os valores do ponto final de serviço nas cordas de ligação devem ser URIs bem formados, incluindo `https://` (recomendado) ou `http://` .

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Criar uma cadeia de ligação com um sufixo de ponto final

Para criar uma cadeia de ligação para um serviço de armazenamento em regiões ou instâncias com diferentes sufixos de ponto final, tais como para Azure China 21Vianet ou Azure Government, utilize o seguinte formato de cadeia de ligação. Indique se pretende ligar à conta de armazenamento através de HTTPS (recomendado) ou HTTP, `myAccountName` substitua-o pelo nome da sua conta de armazenamento, substitua-a `myAccountKey` pela chave de acesso à conta e substitua-a pelo `mySuffix` sufixo URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Aqui está um fio de conexão de exemplo para serviços de armazenamento em Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analisar uma cadeia de ligação

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Passos seguintes

* [Utilize o emulador Azurite para o desenvolvimento local do armazenamento Azure](../common/storage-use-azurite.md)
* [Exploradores de armazenamento Azure](storage-explorers.md)
* [Utilização de assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)
