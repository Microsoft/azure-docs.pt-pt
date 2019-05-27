---
title: Configurar uma cadeia de ligação do armazenamento do Azure | Documentos da Microsoft
description: Configure uma cadeia de ligação para uma conta de armazenamento do Azure. Uma cadeia de ligação contém as informações necessárias para autorizar o acesso a uma conta de armazenamento a partir da sua aplicação em tempo de execução.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/12/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ef01cf194ea7a0afea033d2888df946208073e41
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65874132"
---
# <a name="configure-azure-storage-connection-strings"></a>Configurar Cadeias de Ligação do Storage do Azure

Uma cadeia de ligação inclui as informações de autenticação necessárias para a sua aplicação para aceder a dados numa conta de armazenamento do Azure em tempo de execução. Pode configurar cadeias de ligação para:

* Ligue-se para o emulador de armazenamento do Azure.
* Aceda a uma conta de armazenamento no Azure.
* Aceder aos recursos especificados no Azure através de uma assinatura de acesso partilhado (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Armazenar a cadeia de ligação
Seu aplicativo precisa acessar a cadeia de ligação em tempo de execução para autorizar os pedidos efetuados ao armazenamento do Azure. Tem várias opções para armazenar a cadeia de ligação:

* Um aplicativo em execução no ambiente de trabalho ou num dispositivo pode armazenar a cadeia de ligação num **App. config** ou **Web. config** ficheiro. Adicione a cadeia de ligação para o **AppSettings** secção nesses arquivos.
* Uma aplicação em execução num serviço cloud do Azure pode armazenar a cadeia de ligação no [ficheiro de esquema (. cscfg) de configuração de serviço do Azure](https://msdn.microsoft.com/library/ee758710.aspx). Adicione a cadeia de ligação para o **ConfigurationSettings** secção do ficheiro de configuração do serviço.
* Pode utilizar a cadeia de ligação diretamente no seu código. No entanto, recomendamos que armazene a cadeia de ligação num ficheiro de configuração na maioria dos cenários.

Armazenar a cadeia de ligação num ficheiro de configuração facilita a atualizar a cadeia de ligação para alternar entre o emulador de armazenamento e uma conta de armazenamento do Azure na cloud. Apenas terá de editar a cadeia de ligação para apontar para o seu ambiente de destino.

Pode utilizar o [Gestor de configuração do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) para aceder a cadeia de ligação em tempo de execução, independentemente de onde a sua aplicação está em execução.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Criar uma cadeia de ligação para o emulador de armazenamento
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para obter mais informações sobre o emulador de armazenamento, consulte [utilizar o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Criar uma cadeia de ligação para uma conta de armazenamento do Azure
Para criar uma cadeia de ligação para a sua conta de armazenamento do Azure, utilize o seguinte formato. Indicar se pretende ligar à conta de armazenamento através de HTTPS (recomendado) ou HTTP, substitua `myAccountName` com o nome da conta de armazenamento e replace `myAccountKey` pela chave de acesso da conta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Por exemplo, a cadeia de ligação poderá ter um aspeto semelhante a:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Embora o armazenamento do Azure oferece suporte a HTTP e HTTPS numa cadeia de conexão *HTTPS é altamente recomendável*.

> [!TIP]
> Pode encontrar as cadeias de ligação da conta de armazenamento na [portal do Azure](https://portal.azure.com). Navegue para **configurações** > **chaves de acesso** no painel de menu da conta de armazenamento para ver cadeias de ligação para ambas as chaves de acesso primária e secundária.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Criar uma cadeia de ligação com uma assinatura de acesso partilhado
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Criar uma cadeia de ligação para um ponto de final de armazenamento explícita
Pode especificar pontos finais de serviço explícito na sua cadeia de ligação em vez de utilizar os pontos finais predefinida. Para criar uma cadeia de caracteres de conexão que especifica um ponto final explícito, especifique o ponto de final de serviço completa para cada serviço, incluindo a especificação de protocolo (HTTPS (recomendado) ou HTTP), no seguinte formato:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Um cenário em que talvez deseje especificar um ponto final explícito é quando tenha mapeado o ponto de final de armazenamento de BLOBs para uma [domínio personalizado](../blobs/storage-custom-domain-name.md). Nesse caso, pode especificar o seu ponto final personalizado para o armazenamento de BLOBs na sua cadeia de ligação. Opcionalmente, pode especificar os pontos de extremidade padrão para os outros serviços, se seu aplicativo as utiliza.

Eis um exemplo de uma cadeia de caracteres de conexão que especifica um ponto de extremidade explícito para o serviço de BLOBs:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Este exemplo Especifica pontos de extremidade explícitos para todos os serviços, incluindo um domínio personalizado para o serviço de BLOBs:

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

Os valores de ponto final numa cadeia de ligação são utilizados para construir o pedido de URIs para os serviços de armazenamento e determinam a forma de qualquer URIs que são devolvidos ao seu código.

Se tive mapeado um ponto de final de armazenamento para um domínio personalizado e omitir esse ponto final de uma cadeia de ligação, em seguida, não será possível usar essa cadeia de ligação para aceder a dados no serviço a partir do código.

> [!IMPORTANT]
> Valores de ponto final de serviço nas suas cadeias de ligação tem de ser bem formados URIs, incluindo `https://` (recomendado) ou `http://`. Porque o armazenamento do Azure ainda não suporta o HTTPS para domínios personalizados, *tem* especificar `http://` para qualquer ponto final de URI que aponta para um domínio personalizado.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Criar uma cadeia de ligação com um sufixo de ponto final
Para criar uma cadeia de ligação para um serviço de armazenamento em regiões ou em instâncias com sufixos de ponto de extremidade diferente, tal como para o Azure China 21Vianet ou o Azure Government, utilize o seguinte formato de cadeia de ligação. Indicar se pretende ligar à conta de armazenamento através de HTTPS (recomendado) ou HTTP, substitua `myAccountName` com o nome da conta de armazenamento, substitua `myAccountKey` com a sua chave de acesso de conta e replace `mySuffix` com o sufixo do URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Esta é uma cadeia de ligação de exemplo para serviços de armazenamento no Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analisar uma cadeia de ligação
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Passos Seguintes
* [Utilizar o emulador de armazenamento do Azure para desenvolvimento e teste](storage-use-emulator.md)
* [Exploradores do armazenamento do Azure](storage-explorers.md)
* [Utilizar assinaturas de acesso partilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md)

