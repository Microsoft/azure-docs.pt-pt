---
title: Configurar uma cadeia de ligação
titleSuffix: Azure Storage
description: Configure uma cadeia de ligação para uma conta de armazenamento Azure. Uma cadeia de ligação contém as informações necessárias para autorizar o acesso a uma conta de armazenamento a partir da sua aplicação em tempo de execução utilizando a autorização da Chave Partilhada.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268356"
---
# <a name="configure-azure-storage-connection-strings"></a>Configurar Cadeias de Ligação do Storage do Azure

Uma cadeia de ligação inclui as informações de autorização necessárias para a sua aplicação aceder a dados numa conta de Armazenamento Azure em tempo de execução utilizando a autorização da Chave Partilhada. Pode configurar as cordas de ligação para:

* Ligue-se ao emulador de armazenamento Azure.
* Aceda a uma conta de armazenamento em Azure.
* Acesso a recursos especificados em Azure através de uma assinatura de acesso partilhado (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Ver e copiar uma cadeia de ligação

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Armazenar uma corda de ligação

A sua aplicação necessita de aceder à cadeia de ligação em tempo de execução para autorizar pedidos feitos ao Armazenamento Azure. Tem várias opções para armazenar a sua cadeia de ligação:

* Pode armazenar a sua cadeia de ligação numa variável ambiental.
* Uma aplicação em execução no ambiente de trabalho ou num dispositivo pode armazenar a cadeia de ligação num ficheiro **app.config** ou **web.config.** Adicione a cadeia de ligação à secção **AppSettings** nestes ficheiros.
* Uma aplicação em execução num serviço de nuvem Azure pode armazenar a cadeia de ligação no ficheiro de configuração de [serviço Azure (.cscfg).](https://msdn.microsoft.com/library/ee758710.aspx) Adicione a cadeia de ligação à secção **Configurações Definições** do ficheiro de configuração do serviço.

Armazenar a sua cadeia de ligação num ficheiro de configuração facilita a atualização da cadeia de ligação para alternar entre o emulador de armazenamento e uma conta de armazenamento Azure na nuvem. Basta editar a cadeia de ligação para apontar para o seu ambiente alvo.

Pode utilizar o [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) para aceder à sua cadeia de ligação em tempo de execução, independentemente do local onde a sua aplicação esteja em execução.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Configure uma corda de ligação para o emulador de armazenamento

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre o emulador de armazenamento, consulte [Utilize o emulador de armazenamento Azure para desenvolvimento e teste.](storage-use-emulator.md)

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Configure uma cadeia de ligação para uma conta de armazenamento Azure

Para criar uma cadeia de ligação para a sua conta de armazenamento Azure, utilize o seguinte formato. Indique se pretende ligar-se à conta de armazenamento através `myAccountName` de HTTPS (recomendado) ou `myAccountKey` HTTP, substitua-o pelo nome da sua conta de armazenamento e substitua-o pela chave de acesso à conta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Por exemplo, a sua cadeia de ligação pode parecer semelhante a:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Embora o Armazenamento Azure suporte tanto http como HTTPS numa cadeia de ligação, *HTTPS é altamente recomendado*.

> [!TIP]
> Pode encontrar as cordas de ligação da sua conta de armazenamento no [portal Azure.](https://portal.azure.com) Navegue para **DEFINIÇÕES** > **As teclas** de acesso na lâmina do menu da sua conta de armazenamento para ver cordas de ligação tanto para as teclas de acesso primária seleção como secundária.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Criar uma cadeia de ligação usando uma assinatura de acesso partilhado

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Criar uma cadeia de ligação para um ponto final de armazenamento explícito

Pode especificar pontos finais de serviço explícitos na sua cadeia de ligação em vez de utilizar os pontos finais predefinidos. Para criar uma cadeia de ligação que especifique um ponto final explícito, especifique o ponto final completo do serviço para cada serviço, incluindo a especificação protocolar (HTTPS (recomendado) ou HTTP), no seguinte formato:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Um cenário em que poderá especificar um ponto final explícito é quando mapeou o seu ponto final de armazenamento Blob para um [domínio personalizado](../blobs/storage-custom-domain-name.md). Nesse caso, pode especificar o seu ponto final personalizado para o armazenamento blob na sua cadeia de ligação. Pode especificar opcionalmente os pontos finais por defeito dos outros serviços se a sua aplicação os utilizar.

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

Os valores de ponto final numa cadeia de ligação são usados para construir os URIs de pedido para os serviços de armazenamento, e ditar a forma de quaisquer URIs que são devolvidos ao seu código.

Se mapeou um ponto final de armazenamento para um domínio personalizado e omitir esse ponto final a partir de uma cadeia de ligação, então não poderá utilizar essa cadeia de ligação para aceder a dados nesse serviço a partir do seu código.

> [!IMPORTANT]
> Os valores finais do serviço nas cordas de `https://` ligação devem `http://`ser URIs bem formados, incluindo (recomendado) ou . Uma vez que o Armazenamento Azure ainda *must* não `http://` suporta HTTPS para domínios personalizados, deve especificar para qualquer ponto final URI que aponte para um domínio personalizado.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Criar uma cadeia de ligação com um sufixo de ponto final

Para criar uma cadeia de ligação para um serviço de armazenamento em regiões ou instâncias com diferentes sufixos de ponto final, como para azure China 21Vianet ou Azure Government, utilize o seguinte formato de cadeia de ligação. Indique se pretende ligar-se à conta de armazenamento através `myAccountName` de HTTPS (recomendado) ou `myAccountKey` HTTP, substituir pelo `mySuffix` nome da sua conta de armazenamento, substituir pela chave de acesso à sua conta e substituir pelo sufixo URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Aqui está uma cadeia de conexão exemplo para serviços de armazenamento em Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analisar uma corda de ligação

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Passos seguintes

* [Utilize o emulador de armazenamento Azure para desenvolvimento e teste](storage-use-emulator.md)
* [Exploradores de armazenamento azure](storage-explorers.md)
* [Utilização de assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)
