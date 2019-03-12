---
title: Gerir a expiração de armazenamento de Blobs do Azure numa rede de entrega de conteúdos do Azure | Documentos da Microsoft
description: Saiba mais sobre as opções para controlar o tempo de vida para blobs em cache da CDN do Azure.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 1b2009b54c7f436667c316b7ca002314bc966a1b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531934"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Gerir a expiração de armazenamento de Blobs do Azure na CDN do Azure
> [!div class="op_single_selector"]
> * [Conteúdo Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

O [serviço de armazenamento de BLOBs](../storage/common/storage-introduction.md#blob-storage) no armazenamento do Azure é uma das várias origens e baseado no Azure integrada com rede de entrega de conteúdos (CDN) do Azure. Qualquer conteúdo acessível publicamente pode ser colocadas em cache na CDN do Azure até que o time-to-live (TTL) decorrida. O valor de TTL é determinado pelo `Cache-Control` cabeçalho na resposta HTTP do servidor de origem. Este artigo descreve várias formas que pode definir o `Cache-Control` cabeçalho num blob no armazenamento do Azure.

Também pode controlar as definições de cache do portal do Azure pelas regras de colocação em cache de CDN de definição. Se criar uma regra de colocação em cache e definir seu comportamento de colocação em cache **substituir** ou **ignorar a cache**, as definições de colocação em cache fornecido de origem discutidas neste artigo são ignoradas. Para obter informações sobre os conceitos gerais de colocação em cache, consulte [funciona como o cache](cdn-how-caching-works.md).

> [!TIP]
> Pode optar por não definir nenhum valor de TTL num blob. Neste caso, o CDN do Azure aplica automaticamente um TTL predefinido de sete dias, a menos que tiver definido as regras no portal do Azure de cache. Esta predefinição TTL só se aplica a otimizações de entrega geral web. Para otimizações de ficheiros grandes, o TTL predefinido é um dia e, para suporte de dados de transmissão em fluxo otimizações, o TTL predefinido é de um ano.
> 
> Para obter mais informações sobre como funciona o CDN do Azure para acelerar o acesso a blobs e outros ficheiros, consulte [descrição geral da rede de entrega de conteúdos de Azure](cdn-overview.md).
> 
> Para obter mais informações sobre o armazenamento de Blobs do Azure, consulte [introdução ao armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Definir cabeçalhos Cache-Control ao utilizar regras de colocação em cache de CDN
O método preferencial para a definição de um blob `Cache-Control` cabeçalho consiste em utilizar regras de colocação em cache no portal do Azure. Para obter mais informações sobre as regras de cache de CDN, veja [comportamento de cache com regras de colocação em cache de CDN do Azure de controle](cdn-caching-rules.md).

> [!NOTE] 
> Estão disponíveis apenas para regras de colocação em cache **CDN do Azure Standard da Verizon** e **CDN do Azure Standard da Akamai** perfis. Para **CDN do Azure Premium da Verizon** perfis, tem de utilizar o [motor de regras de CDN do Azure](cdn-rules-engine.md) no **gerir** portal para uma funcionalidade semelhante.

**Para navegar para a página de regras de colocação em cache de CDN**:

1. No portal do Azure, selecione um perfil da CDN, em seguida, selecione o ponto final para o blob.

2. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

   ![Botão de regras de colocação em cache da CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache**.

   ![Página de colocação em cache de CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Para definir os cabeçalhos Cache-Control de um serviço de armazenamento de BLOBs ao utilizar regras de colocação em cache global:**

1. Sob **Global, as regras de cache**, defina **comportamento de colocação em cache de cadeia de consulta** para **ignorar cadeias de consulta** e defina **comportamento de colocação em cache** para  **Substituir**.
      
2. Para **duração de expiração da Cache**, introduza 3600 no **segundos** caixa ou 1 no **horas** caixa. 

   ![Exemplo de regras de colocação em cache global de CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Esta regra de colocação em cache global define uma duração de cache de uma hora e afeta todos os pedidos para o ponto final. Substitui qualquer `Cache-Control` ou `Expires` cabeçalhos HTTP que são enviados pelo servidor de origem especificado pelo ponto final.   

3. Selecione **Guardar**.
 
**Para definir um blob de cabeçalhos de Cache-Control do ficheiro ao utilizar regras de colocação em cache personalizadas:**

1. Sob **personalizada, regras de colocação em cache**, criar duas condições de correspondência:

     R. Para a primeira condição de correspondência, defina **corresponde à condição** ao **caminho** e introduza `/blobcontainer1/*` para **corresponde ao valor**. Definir **comportamento de colocação em cache** ao **substituir** e introduza 4 no **horas** caixa.

    B. Para a segunda condição de correspondência, defina **corresponde à condição** ao **caminho** e introduza `/blobcontainer1/blob1.txt` para **corresponde ao valor**. Definir **comportamento de colocação em cache** ao **substituir** e introduzir 2 no **horas** caixa.

    ![Exemplo de regras de colocação em cache personalizado de CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    A primeira regra de colocação em cache personalizada define uma duração de cache de quatro horas para todos os ficheiros no blob o `/blobcontainer1` pasta no servidor de origem especificado pelo seu ponto final. A segunda regra substitui a primeira regra para o `blob1.txt` apenas ficheiro de BLOBs e define uma duração da cache de duas horas para o mesmo.

2. Selecione **Guardar**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Definir cabeçalhos Cache-Control com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[O Azure PowerShell](/powershell/azure/overview) é uma das formas mais rápidas e mais poderosas para administrar os serviços do Azure. Utilize o `Get-AzStorageBlob` cmdlet para obter uma referência para o blob, em seguida, defina o `.ICloudBlob.Properties.CacheControl` propriedade. 

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
> Também pode utilizar o PowerShell para [gerir os seus perfis de CDN e os pontos de extremidade](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Definir cabeçalhos Cache-Control com o .NET
Para especificar um blob `Cache-Control` cabeçalho com o código do .NET, utilize o [biblioteca de clientes de armazenamento do Azure para .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para definir o [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) propriedade.

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
> Existem mais exemplos de código do .NET disponível no [exemplos de armazenamento de Blobs do Azure para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Definir cabeçalhos Cache-Control ao utilizar outros métodos

### <a name="azure-storage-explorer"></a>Explorador do Storage do Azure
Com o [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), pode ver e editar os seus recursos de armazenamento de BLOBs, incluindo propriedades, tais como o *CacheControl* propriedade. 

Para atualizar o *CacheControl* propriedade de um blob com o Explorador de armazenamento do Azure:
   1. Selecionar um blob, em seguida, selecione **propriedades** no menu de contexto. 
   2. Desloque para baixo para o *CacheControl* propriedade.
   3. Introduza um valor, em seguida, selecione **guardar**.


![Propriedades do Explorador de armazenamento do Azure](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interface de Linha de Comandos do Azure
Com o [Interface de linha de comandos do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) (CLI), pode gerir os recursos de Blobs do Azure na linha de comando. Para definir o cabeçalho cache-control ao carregar um blob com a CLI do Azure, definir o *cacheControl* propriedade utilizando o `-p` mudar. O exemplo seguinte mostra como definir o valor de TTL para uma hora (3600 segundos):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>REST API dos serviços de armazenamento do Azure
Pode utilizar o [REST API dos serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx) para definir explicitamente o *x-ms-BLOBs-cache-control* propriedade utilizando as seguintes operações num pedido:
  
   - [Colocar o Blob](https://msdn.microsoft.com/library/azure/dd179451.aspx)
   - [Colocar a lista de bloqueios](https://msdn.microsoft.com/library/azure/dd179467.aspx)
   - [Definir as propriedades do Blob](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Teste o cabeçalho Cache-Control
Pode verificar facilmente as definições de TTL dos blobs. Com o seu browser [ferramentas de programação](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), teste, que inclui o blob a `Cache-Control` cabeçalho de resposta. Também pode utilizar uma ferramenta como [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/), ou [Fiddler](https://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Próximos Passos
* [Saiba como gerir a expiração de conteúdo de serviço em nuvem na CDN do Azure](cdn-manage-expiration-of-cloud-service-content.md)
* [Saiba mais sobre conceitos de colocação em cache](cdn-how-caching-works.md)

