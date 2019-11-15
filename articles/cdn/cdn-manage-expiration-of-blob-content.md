---
title: Gerenciar a expiração do armazenamento de BLOBs do Azure
titleSuffix: Azure Content Delivery Network
description: Saiba mais sobre as opções para controlar o tempo de vida para BLOBs no cache da CDN do Azure.
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
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f28282a802e4b38fadc05c7090fa2a2af154de54
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083165"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Gerenciar a expiração do armazenamento de BLOBs do Azure na CDN do Azure
> [!div class="op_single_selector"]
> * [Conteúdo Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

O [serviço de armazenamento de BLOBs](../storage/common/storage-introduction.md#blob-storage) no armazenamento do Azure é uma das várias origens baseadas no Azure integradas à CDN (rede de distribuição de conteúdo) do Azure. Qualquer conteúdo de blob acessível publicamente pode ser armazenado em cache na CDN do Azure até que sua TTL (vida útil) decorra. O TTL é determinado pelo cabeçalho de `Cache-Control` na resposta HTTP do servidor de origem. Este artigo descreve várias maneiras pelas quais você pode definir o cabeçalho de `Cache-Control` em um blob no armazenamento do Azure.

Você também pode controlar as configurações de cache do portal do Azure definindo as regras de cache da CDN. Se você criar uma regra de cache e definir seu comportamento de cache para **substituir** ou **ignorar o cache**, as configurações de cache fornecidas pela origem abordadas neste artigo serão ignoradas. Para obter informações sobre conceitos gerais de cache, consulte [como funciona o Caching](cdn-how-caching-works.md).

> [!TIP]
> Você pode optar por não definir nenhum TTL em um blob. Nesse caso, a CDN do Azure aplica automaticamente um TTL padrão de sete dias, a menos que você tenha configurado regras de cache no portal do Azure. Esse TTL padrão aplica-se somente a otimizações gerais de entrega na Web. Para otimizações de arquivo grande, o TTL padrão é de um dia e, para otimizações de streaming de mídia, o TTL padrão é de um ano.
> 
> Para obter mais informações sobre como a CDN do Azure funciona para acelerar o acesso a BLOBs e outros arquivos, consulte [visão geral da rede de distribuição de conteúdo do Azure](cdn-overview.md).
> 
> Para obter mais informações sobre o armazenamento de BLOBs do Azure, consulte [introdução ao armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Definindo cabeçalhos de Cache-Control usando as regras de cache da CDN
O método preferencial para definir o cabeçalho de `Cache-Control` de um blob é usar regras de cache no portal do Azure. Para obter mais informações sobre as regras de cache da CDN, consulte [controlar o comportamento de cache da CDN do Azure com regras de cache](cdn-caching-rules.md).

> [!NOTE] 
> As regras de cache estão disponíveis apenas para os perfis da **CDN standard do Azure da Verizon** e **da CDN standard do Azure da Akamai** . Para perfis **da CDN Premium do Azure da Verizon** , você deve usar o [mecanismo de regras da CDN do Azure](cdn-rules-engine.md) no portal **gerenciar** para funcionalidade semelhante.

**Para navegar até a página de regras de cache da CDN**:

1. No portal do Azure, selecione um perfil CDN e, em seguida, selecione o ponto de extremidade para o blob.

2. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

   ![Botão de regras de cache da CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache**.

   ![Página cache da CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Para definir os cabeçalhos de controle de cache do serviço de armazenamento de BLOBs usando regras de cache globais:**

1. Em **regras de cache globais**, defina **comportamento de cache de cadeia de caracteres de consulta** para **ignorar cadeias** de caracteres de consulta e definir **comportamento de cache** para **substituir**.
      
2. Para **duração da expiração do cache**, digite 3600 na caixa **segundos** ou 1 na caixa **horas** . 

   ![Exemplo de regras de cache global da CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Essa regra de cache global define uma duração de cache de uma hora e afeta todas as solicitações para o ponto de extremidade. Ele substitui qualquer `Cache-Control` ou `Expires` cabeçalhos HTTP que são enviados pelo servidor de origem especificado pelo ponto de extremidade.   

3. Selecione **Guardar**.
 
**Para definir os cabeçalhos de controle de cache de um arquivo de BLOB usando regras de cache personalizadas:**

1. Em **regras de cache personalizadas**, crie duas condições de correspondência:

     A. Para a primeira condição de correspondência, defina **condição de correspondência** para **caminho** e insira `/blobcontainer1/*` para o valor de **correspondência**. Defina o **comportamento de cache** para **substituir** e insira 4 na caixa **horas** .

    B. Para a segunda condição de correspondência, defina **condição de correspondência** para **caminho** e insira `/blobcontainer1/blob1.txt` para o valor de **correspondência**. Defina o **comportamento de cache** para **substituir** e digite 2 na caixa **horas** .

    ![Exemplo de regras de cache personalizadas da CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    A primeira regra de cache Personalizada define uma duração de cache de quatro horas para qualquer arquivo de blob na pasta `/blobcontainer1` no servidor de origem especificado por seu ponto de extremidade. A segunda regra substitui a primeira regra somente para o arquivo de blob `blob1.txt` e define uma duração de cache de duas horas para ela.

2. Selecione **Guardar**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Definindo cabeçalhos de Cache-Control usando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/overview) é uma das maneiras mais rápidas e eficientes de administrar seus serviços do Azure. Use o cmdlet `Get-AzStorageBlob` para obter uma referência ao blob e, em seguida, defina a propriedade `.ICloudBlob.Properties.CacheControl`. 

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
> Você também pode usar o PowerShell para [gerenciar seus perfis e pontos de extremidade CDN](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Definindo cabeçalhos de Cache-Control usando .NET
Para especificar o cabeçalho de `Cache-Control` de um BLOB usando o código .NET, use a [biblioteca de cliente de armazenamento do Azure para .net](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para definir a propriedade [CloudBlob. Properties. CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) .

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
> Há mais exemplos de código .NET disponíveis em [exemplos de armazenamento de BLOBs do Azure para .net](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Definindo cabeçalhos de Cache-Control usando outros métodos

### <a name="azure-storage-explorer"></a>Explorador do Storage do Azure
Com [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), você pode exibir e editar os recursos de armazenamento de BLOBs, incluindo propriedades como a propriedade *CacheControl* . 

Para atualizar a propriedade *CacheControl* de um blob com Gerenciador de armazenamento do Azure:
   1. Selecione um blob e, em seguida, selecione **Propriedades** no menu de contexto. 
   2. Role para baixo até a propriedade *CacheControl* .
   3. Insira um valor e, em seguida, selecione **salvar**.


![Propriedades de Gerenciador de Armazenamento do Azure](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interface de Linha de Comandos do Azure
Com a CLI ( [interface de linha de comando](https://docs.microsoft.com/cli/azure) ) do Azure, você pode gerenciar recursos de blob do Azure na linha de comando. Para definir o cabeçalho Cache-Control ao carregar um blob com o CLI do Azure, defina a propriedade *CacheControl* usando a opção `-p`. O exemplo a seguir mostra como definir o TTL para uma hora (3600 segundos):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>API REST dos serviços de armazenamento do Azure
Você pode usar a [API REST dos serviços de armazenamento do Azure](/rest/api/storageservices/) para definir explicitamente a propriedade *x-ms-blob-cache-control* usando as seguintes operações em uma solicitação:
  
   - [Colocar blob](/rest/api/storageservices/Put-Blob)
   - [Colocar lista de blocos](/rest/api/storageservices/Put-Block-List)
   - [Definir propriedades de BLOB](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testando o cabeçalho Cache-Control
Você pode verificar facilmente as configurações de TTL de seus BLOBs. Com as ferramentas de [desenvolvedor](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)do seu navegador, teste se o blob inclui o cabeçalho de resposta `Cache-Control`. Você também pode usar uma ferramenta como [wget](https://www.gnu.org/software/wget/), [postmaster](https://www.getpostman.com/)ou [Fiddler](https://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Passos Seguintes
* [Saiba como gerenciar a expiração do conteúdo do serviço de nuvem na CDN do Azure](cdn-manage-expiration-of-cloud-service-content.md)
* [Saiba mais sobre os conceitos de cache](cdn-how-caching-works.md)

