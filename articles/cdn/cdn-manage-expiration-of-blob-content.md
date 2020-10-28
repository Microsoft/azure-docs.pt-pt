---
title: Gerir a expiração do armazenamento da Azure Blob
titleSuffix: Azure Content Delivery Network
description: Saiba mais sobre as opções de controlo do tempo-a-vida para bolhas em caching Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: how-to
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 206ff6f888229356743bebb816cf03e4f7a7504b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778714"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Gerir a expiração do armazenamento da Azure Blob na Azure CDN
> [!div class="op_single_selector"]
> * [Conteúdo Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

O [serviço de armazenamento Blob](../storage/common/storage-introduction.md#blob-storage) em Azure Storage é uma das várias origens baseadas em Azure integradas na Rede de Entrega de Conteúdos Azure (CDN). Qualquer conteúdo blob acessível ao público pode ser em cache em Azure CDN até que o seu tempo de vida (TTL) desdaça. O TTL é determinado pelo `Cache-Control` cabeçalho na resposta HTTP do servidor de origem. Este artigo descreve várias formas de definir o `Cache-Control` cabeçalho numa bolha no Azure Storage.

Também pode controlar as definições de cache a partir do portal Azure definindo as regras de cache CDN. Se criar uma regra de cache e definir o seu comportamento de cache de cache de **substituição** ou **bypass,** as definições de cache fornecidas pela origem discutidas neste artigo são ignoradas. Para obter informações sobre conceitos gerais de caching, consulte [como funciona o caching.](cdn-how-caching-works.md)

> [!TIP]
> Pode optar por não definir TTL numa bolha. Neste caso, o Azure CDN aplica automaticamente um TTL predefinido de sete dias, a menos que tenha configurado regras de caching no portal Azure. Este TTL predefinido aplica-se apenas às otimizações gerais de entrega web. Para grandes otimizações de ficheiros, o TTL padrão é um dia, e para otimizações de streaming de mídia, o TTL padrão é de um ano.
> 
> Para obter mais informações sobre como o Azure CDN funciona para acelerar o acesso a blobs e outros ficheiros, consulte [a visão geral da Rede de Entrega de Conteúdos Azure](cdn-overview.md).
> 
> Para obter mais informações sobre o armazenamento da Azure Blob, consulte [Introdução ao armazenamento blob](../storage/blobs/storage-blobs-introduction.md).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Definição de cabeçalhos Cache-Control utilizando regras de caching CDN
O método preferido para definir o cabeçalho de uma bolha `Cache-Control` é usar as regras de caching no portal Azure. Para obter mais informações sobre as regras de caching CDN, consulte [o comportamento do caching do Control Azure CDN com as regras de caching](cdn-caching-rules.md).

> [!NOTE] 
> As regras de caching estão disponíveis apenas para **Azure CDN Standard da Verizon** e **Azure CDN Standard a partir de** perfis Akamai. Para **o Azure CDN Premium a partir de** perfis Verizon, deve utilizar o motor de [regras Azure CDN](./cdn-verizon-premium-rules-engine.md) no portal **Manage** para obter funcionalidades semelhantes.

**Para navegar na página de regras do caching CDN** :

1. No portal Azure, selecione um perfil CDN e, em seguida, selecione o ponto final para a bolha.

2. No painel esquerdo, em Definições, selecione **Regras de colocação em cache** .

   ![Botão de regras de caching CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache** .

   ![Página de caching CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Para definir os cabeçalhos Cache-Control de um serviço blob utilizando regras globais de caching:**

1. De acordo com **as regras globais de caching** , desempenhar **o comportamento de caching de cordas de consulta** para ignorar as cordas de **consulta** e definir o comportamento **de Caching** para **Override** .
      
2. Para **a duração de validade da Cache** , introduza 3600 na caixa **Seconds** ou 1 na caixa **Horas.** 

   ![CdN global caching regras exemplo](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Esta regra global de cache define uma duração de cache de uma hora e afeta todos os pedidos para o ponto final. Substitui qualquer `Cache-Control` cabeçalho ou `Expires` HTTP que são enviados pelo servidor de origem especificado pelo ponto final.   

3. Selecione **Guardar** .
 
**Para definir os cabeçalhos Cache-Control de um ficheiro blob utilizando regras de caching personalizadas:**

1. De acordo com **as regras de caching personalizado,** crie duas condições de jogo:

     A. Para a primeira condição de jogo, desacordo **a condição de jogo** para **Path** e `/blobcontainer1/*` **introduza o valor de Match** . **Desentuê-lo** para **substituir** e introduza 4 na caixa **Horas.**

    B. Para a segunda condição de jogo, desacordo **a condição de jogo** para **Path** e `/blobcontainer1/blob1.txt` **introduza o valor do Match** . **Desentuê-lo** para **substituir** e introduza 2 na caixa **Horas.**

    ![CdN costume caching regras exemplo](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    A primeira regra de cache personalizada define uma duração de cache de quatro horas para quaisquer ficheiros blob `/blobcontainer1` na pasta no servidor de origem especificado pelo seu ponto final. A segunda regra substitui apenas a primeira regra do `blob1.txt` ficheiro blob e define uma duração de cache de duas horas para o mesmo.

2. Selecione **Guardar** .


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Definição de cabeçalhos Cache-Control utilizando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[A Azure PowerShell](/powershell/azure/) é uma das formas mais rápidas e poderosas de administrar os seus serviços Azure. Use o `Get-AzStorageBlob` cmdlet para obter uma referência à bolha e, em seguida, definir a `.ICloudBlob.Properties.CacheControl` propriedade. 

Por exemplo:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Também pode utilizar o PowerShell para [gerir os perfis e pontos finais da CDN.](cdn-manage-powershell.md)
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Definição de cabeçalhos Cache-Control utilizando .NET
Para especificar o cabeçalho de uma bolha `Cache-Control` utilizando o código .NET, utilize a Biblioteca do [Cliente de Armazenamento Azure para .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) para definir a propriedade [CloudBlob.Properties.CacheControl.](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol)

Por exemplo:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Existem mais amostras de código .NET disponíveis em [Amostras de Armazenamento Azure Blob para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Definição de cabeçalhos Cache-Control utilizando outros métodos

### <a name="azure-storage-explorer"></a>Explorador de Armazenamento do Azure
Com [o Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)pode ver e editar os seus recursos de armazenamento de bolhas, incluindo propriedades como a propriedade *CacheControl.* 

Para atualizar a propriedade *CacheControl* de uma bolha com O Explorador de Armazenamento Azure:
   1. Selecione uma bolha e, em seguida, **selecione Propriedades** do menu de contexto. 
   2. Desloque-se até à propriedade *CacheControl.*
   3. Introduza um valor e, em seguida, **selecione Guardar** .


![Propriedades do Azure Storage Explorer](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interface de Linha de Comandos do Azure
Com a [Interface Azure Command-Line](/cli/azure) (CLI), pode gerir os recursos blob do Azure a partir da linha de comando. Para definir o cabeçalho de controlo de cache quando carregar uma bolha com o Azure CLI, descreva a propriedade *cacheControl* utilizando o `-p` interruptor. O exemplo a seguir mostra como definir o TTL para uma hora (3600 segundos):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Serviços de armazenamento Azure REST API
Pode utilizar os [serviços de armazenamento Azure REST API](/rest/api/storageservices/) para definir explicitamente a propriedade *x-ms-blob-cache-control,* utilizando as seguintes operações a pedido:
  
   - [Coloque Blob](/rest/api/storageservices/Put-Blob)
   - [Colocar lista de blocos](/rest/api/storageservices/Put-Block-List)
   - [Definir propriedades blob](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testar o cabeçalho Cache-Control
Pode verificar facilmente as definições de TTL das suas bolhas. Com as [ferramentas](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)de desenvolvimento do seu navegador, teste que a sua bolha inclui o `Cache-Control` cabeçalho de resposta. Também pode utilizar uma ferramenta como [Wget,](https://www.gnu.org/software/wget/) [Carteiro](https://www.getpostman.com/)ou [Violinista](https://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Passos Seguintes
* [Saiba como gerir a expiração dos conteúdos do Cloud Service no Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Conheça os conceitos de caching](cdn-how-caching-works.md)