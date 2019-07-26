---
title: Exclusão reversível para BLOBs de armazenamento do Azure | Microsoft Docs
description: O armazenamento do Azure agora oferece exclusão reversível para objetos de BLOB para que você possa recuperar seus dados com mais facilidade quando eles forem modificados ou excluídos erroneamente por um aplicativo ou outro usuário da conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b0a03eee06ba114ab929c8c584f382861a006bbc
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360751"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Exclusão reversível para BLOBs de armazenamento do Azure
O armazenamento do Azure agora oferece exclusão reversível para objetos de BLOB para que você possa recuperar seus dados com mais facilidade quando eles forem modificados ou excluídos erroneamente por um aplicativo ou outro usuário da conta de armazenamento.

## <a name="how-does-it-work"></a>Como funciona?
Quando ativado, a exclusão reversível permite que você salve e recupere seus dados quando BLOBs ou instantâneos de blob são excluídos. Essa proteção se estende aos dados de BLOB que são apagados como resultado de uma substituição.

Quando os dados são excluídos, ele faz a transição para um estado de exclusão reversível em vez de ser apagado permanentemente. Quando a exclusão reversível está ativada e você substitui dados, um instantâneo com exclusão reversível é gerado para salvar o estado dos dados substituídos. Os objetos com exclusão reversível são invisíveis, a menos que explicitamente listados. Você pode configurar a quantidade de tempo que os dados com exclusão reversível são recuperáveis antes de expirarem permanentemente.

A exclusão reversível é compatível com versões anteriores; Você não precisa fazer nenhuma alteração em seus aplicativos para aproveitar as proteções que esse recurso proporciona. No entanto, a [recuperação de dados](#recovery) introduz uma nova API para **restaurar blob** .

### <a name="configuration-settings"></a>Definições de configuração
Quando você cria uma nova conta, a exclusão reversível está desativada por padrão. A exclusão reversível também está desativada por padrão para contas de armazenamento existentes. Você pode ativar e desativar o recurso a qualquer momento durante a vida útil de uma conta de armazenamento.

Você ainda poderá acessar e recuperar dados com exclusão reversível quando o recurso estiver desativado, supondo que os dados com exclusão reversível foram salvos quando o recurso foi ativado anteriormente. Quando você ativa a exclusão reversível, também precisa configurar o período de retenção.

O período de retenção indica a quantidade de tempo que os dados com exclusão reversível são armazenados e disponíveis para recuperação. Para BLOBs e instantâneos de BLOB que são excluídos explicitamente, o relógio do período de retenção é iniciado quando os dados são excluídos. Para instantâneos com exclusão reversível gerados pelo recurso de exclusão reversível quando os dados são substituídos, o relógio é iniciado quando o instantâneo é gerado. No momento, você pode reter dados com exclusão reversível entre 1 e 365 dias.

Você pode alterar o período de retenção de exclusão reversível a qualquer momento. Um período de retenção atualizado será aplicado somente aos dados excluídos recentemente. Os dados excluídos anteriormente expirarão com base no período de retenção que foi configurado quando os dados foram excluídos. A tentativa de excluir um objeto com exclusão reversível não afetará sua hora de expiração.

### <a name="saving-deleted-data"></a>Salvando dados excluídos
A exclusão reversível preserva seus dados em muitos casos em que BLOBs ou instantâneos de blob são excluídos ou substituídos.

Quando um blob é substituído usando **Put Blob**, **Put bloquear**, **Put bloquear lista** ou **copiar blob** , um instantâneo do estado do blob antes da operação de gravação é gerado automaticamente. Esse instantâneo é um instantâneo com exclusão reversível; Ela é invisível, a menos que os objetos excluídos por software sejam explicitamente listados. Consulte a seção [recuperação](#recovery) para saber como listar objetos com exclusão reversível.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Os dados com exclusão reversível estão em cinza, enquanto os dados ativos são azuis. Os dados gravados mais recentemente aparecem abaixo dos dados mais antigos. Quando B0 é substituído por B1, um instantâneo com exclusão reversível de B0 é gerado. Quando B1 é substituído por B2, um instantâneo com exclusão reversível de B1 é gerado.*

> [!NOTE]  
> A exclusão reversível só permite substituir a proteção para operações de cópia quando ela está ativada para a conta do blob de destino.

> [!NOTE]  
> A exclusão reversível não permite substituir a proteção para BLOBs na camada de arquivo morto. Se um blob no arquivo for substituído por um novo BLOB em qualquer camada, o blob substituído será permanentemente expirado.

Quando o **blob de exclusão** é chamado em um instantâneo, esse instantâneo é marcado como com exclusão reversível. Um novo instantâneo não é gerado.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Os dados com exclusão reversível estão em cinza, enquanto os dados ativos são azuis. Os dados gravados mais recentemente aparecem abaixo dos dados mais antigos. Quando o **blob de instantâneo** é chamado, B0 torna-se um instantâneo e B1 é o estado ativo do blob. Quando o instantâneo B0 é excluído, ele é marcado como com exclusão reversível.*

Quando **delete blob** é chamado em um blob de base (qualquer BLOB que não seja um instantâneo), esse blob é marcado como com exclusão reversível. Consistente com o comportamento anterior, chamar **delete blob** em um blob com instantâneos ativos retorna um erro. Chamar **delete blob** em um blob com instantâneos com exclusão reversível não retorna um erro. Você ainda pode excluir um blob e todos os seus instantâneos em uma única operação quando a exclusão reversível estiver ativada. Isso marca o blob de base e os instantâneos como com exclusão reversível.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Os dados com exclusão reversível estão em cinza, enquanto os dados ativos são azuis. Os dados gravados mais recentemente aparecem abaixo dos dados mais antigos. Aqui, uma chamada **delete blob** é feita para excluir B2 e todos os instantâneos associados. O blob ativo, B2 e todos os instantâneos associados são marcados como com exclusão reversível.*

> [!NOTE]  
> Quando um blob com exclusão reversível é substituído, um instantâneo com exclusão reversível do estado do blob antes da operação de gravação é gerado automaticamente. O novo BLOB herda a camada do blob substituído.

A exclusão reversível não salva seus dados em casos de exclusões de contêiner ou conta, nem quando os metadados de BLOB e as propriedades de blob são substituídos. Para proteger uma conta de armazenamento contra exclusão errada, você pode configurar um bloqueio usando o Azure Resource Manager. Consulte o artigo Azure Resource Manager [Bloquear recursos para evitar alterações](../../azure-resource-manager/resource-group-lock-resources.md) inesperadas para saber mais.

A tabela a seguir detalha o comportamento esperado quando a exclusão reversível está ativada:

| Operação da API REST | Tipo de recurso | Descrição | Alteração no comportamento |
|--------------------|---------------|-------------|--------------------|
| [Eliminar](/rest/api/storagerp/StorageAccounts/Delete) | Conta | Exclui a conta de armazenamento, incluindo todos os contêineres e blobs que ele contém.                           | Nenhuma alteração. Contêineres e blobs na conta excluída não são recuperáveis. |
| [Excluir contêiner](/rest/api/storageservices/delete-container) | Contentor | Exclui o contêiner, incluindo todos os blobs que ele contém. | Nenhuma alteração. Os BLOBs no contêiner excluído não são recuperáveis. |
| [Colocar blob](/rest/api/storageservices/put-blob) | Blobs de bloco, acréscimo e página | Cria um novo BLOB ou substitui um blob existente em um contêiner | Se for usado para substituir um blob existente, um instantâneo do estado do blob antes da chamada será gerado automaticamente. Isso também se aplica a um blob com exclusão reversível anteriormente se e somente se ele for substituído por um blob do mesmo tipo (bloco, acréscimo ou página). Se ele for substituído por um blob de um tipo diferente, todos os dados de exclusão reversível existentes serão permanentemente expirados. |
| [Excluir blob](/rest/api/storageservices/delete-blob) | Blobs de bloco, acréscimo e página | Marca um instantâneo de BLOB ou BLOB para exclusão. O BLOB ou instantâneo é excluído posteriormente durante a coleta de lixo | Se usado para excluir um instantâneo de BLOB, esse instantâneo é marcado como com exclusão reversível. Se for usado para excluir um blob, esse blob será marcado como com exclusão reversível. |
| [Copiar blob](/rest/api/storageservices/copy-blob) | Blobs de bloco, acréscimo e página | Copia um blob de origem para um blob de destino na mesma conta de armazenamento ou em outra conta de armazenamento. | Se for usado para substituir um blob existente, um instantâneo do estado do blob antes da chamada será gerado automaticamente. Isso também se aplica a um blob com exclusão reversível anteriormente se e somente se ele for substituído por um blob do mesmo tipo (bloco, acréscimo ou página). Se ele for substituído por um blob de um tipo diferente, todos os dados de exclusão reversível existentes serão permanentemente expirados. |
| [Colocar bloco](/rest/api/storageservices/put-block) | Blobs de bloco | Cria um novo bloco a ser confirmado como parte de um blob de blocos. | Se usado para confirmar um bloco em um blob ativo, não há alteração. Se usado para confirmar um bloco em um blob que é excluído de maneira reversível, um novo BLOB é criado e um instantâneo é gerado automaticamente para capturar o estado do blob com exclusão reversível. |
| [Colocar lista de blocos](/rest/api/storageservices/put-block-list) | Blobs de bloco | Confirma um blob especificando o conjunto de IDs de bloco que compõem o blob de blocos. | Se for usado para substituir um blob existente, um instantâneo do estado do blob antes da chamada será gerado automaticamente. Isso também se aplica a um blob com exclusão reversível anteriormente se e somente se ele for um blob de blocos. Se ele for substituído por um blob de um tipo diferente, todos os dados de exclusão reversível existentes serão permanentemente expirados. |
| [Colocar página](/rest/api/storageservices/put-page) | Blobs de Página | Grava um intervalo de páginas em um blob de páginas. | Nenhuma alteração. Os dados de blob de páginas substituídos ou limpos usando essa operação não são salvos e não são recuperáveis. |
| [Bloco de acréscimo](/rest/api/storageservices/append-block) | Acrescentar BLOBs | Grava um bloco de dados no final de um blob de acréscimo | Nenhuma alteração. |
| [Definir propriedades de BLOB](/rest/api/storageservices/set-blob-properties) | Blobs de bloco, acréscimo e página | Define valores para propriedades do sistema definidas para um blob. | Nenhuma alteração. As propriedades de blob substituídas não são recuperáveis. |
| [Definir metadados de BLOB](/rest/api/storageservices/set-blob-metadata) | Blobs de bloco, acréscimo e página | Define os metadados definidos pelo usuário para o blob especificado como um ou mais pares de nome-valor. | Nenhuma alteração. Os metadados de blob substituídos não são recuperáveis. |

É importante observar que chamar "Put Page" para substituir ou limpar os intervalos de um blob de páginas não gerará automaticamente instantâneos. Os discos de máquina virtual são apoiados por blobs de página e usam a **página Put** para gravar dados.

### <a name="recovery"></a>Recuperação
Para facilitar a recuperação de dados excluídos, apresentamos uma nova API de "recuperar BLOB". Chamar a API de restauração em um blob de base com exclusão reversível restaura e todos os instantâneos de exclusão reversível associados como ativos. Chamar a API de restauração em um blob de base ativo restaura todos os instantâneos de exclusão reversível associados como ativos. Quando os instantâneos são restaurados como ativos, eles se parecem com instantâneos gerados pelo usuário; Eles não substituem o blob de base.

Para restaurar um blob para um instantâneo de exclusão reversível específico, você pode chamar undelete **blob** no blob de base. Em seguida, você pode copiar o instantâneo sobre o blob agora ativo. Você também pode copiar o instantâneo para um novo BLOB.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Os dados com exclusão reversível estão em cinza, enquanto os dados ativos são azuis. Os dados gravados mais recentemente aparecem abaixo dos dados mais antigos. Aqui, o **blob** de restauração é chamado no blob B, restaurando assim o blob de base, B1 e todos os instantâneos associados, aqui apenas B0, como ativo. Na segunda etapa, B0 é copiado sobre o blob de base. Essa operação de cópia gera um instantâneo com exclusão reversível de B1.*

Para exibir BLOBs com exclusão reversível e instantâneos de BLOB, você pode optar por incluir dados excluídos em blobs de **lista**. Você pode optar por exibir apenas blobs de base com exclusão reversível ou também para incluir instantâneos de blob com exclusão reversível. Para todos os dados com exclusão reversível, você pode exibir a hora em que os dados foram excluídos, bem como o número de dias antes que os dados sejam permanentemente expirados.

### <a name="example"></a>Exemplo
Veja a seguir a saída do console de um script do .NET que carrega, substitui, faz o instantâneo, exclui e restaura um blob chamado "HelloWorld" quando a exclusão reversível está ativada:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Consulte a seção [próximas etapas](#next-steps) para obter um ponteiro para o aplicativo que produziu essa saída.

## <a name="pricing-and-billing"></a>Preços e faturação
Todos os dados com exclusão reversível são cobrados com a mesma taxa que os dados ativos. Você não será cobrado pelos dados que são excluídos permanentemente após o período de retenção configurado. Para aprofundar-se nos instantâneos e como eles acumulam cobranças, consulte [entender como](storage-blob-snapshots.md)os instantâneos acumulam encargos.

Você não será cobrado pelas transações relacionadas à geração automática de instantâneos. Você será cobrado por restaurar transações de **blob** na taxa de "operações de gravação".

Para obter mais detalhes sobre os preços do armazenamento de BLOBs do Azure em geral, confira a [página de preços do armazenamento de BLOBs do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Quando você ativa a exclusão reversível inicialmente, é recomendável usar um período de retenção pequeno para entender melhor como o recurso afetará sua fatura.

## <a name="quickstart"></a>Início Rápido
### <a name="azure-portal"></a>Portal do Azure
Para habilitar a exclusão reversível, navegue até a opção **exclusão reversível** em **serviço blob**. Em seguida, clique em **habilitado** e insira o número de dias que você deseja manter os dados com exclusão reversível.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Para exibir BLOBs com exclusão reversível, marque a caixa de seleção **Mostrar BLOBs excluídos** .

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Para exibir instantâneos com exclusão reversível para um determinado BLOB, selecione o blob e clique em **Exibir instantâneos**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Verifique se a caixa de seleção **Mostrar instantâneos excluídos** está selecionada.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Ao clicar em um BLOB ou instantâneo com exclusão reversível, observe as novas propriedades de BLOB. Eles indicam quando o objeto foi excluído e quantos dias são deixados até que o BLOB ou o instantâneo de blob expire permanentemente. Se o objeto com exclusão reversível não for um instantâneo, você também terá a opção de refazer a exclusão.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Lembre-se de que a reexclusão de um blob também irá restaurar todos os instantâneos associados. Para restaurar os instantâneos com exclusão reversível de um blob ativo, clique no BLOB e selecione **restaurar todos os instantâneos**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Depois de restaurar os instantâneos de um blob, você pode clicar em **promover** para copiar um instantâneo sobre o blob raiz, restaurando assim o blob para o instantâneo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para habilitar a exclusão reversível, atualize as propriedades de serviço de um cliente de BLOB. O exemplo a seguir habilita a exclusão reversível para um subconjunto de contas em uma assinatura:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Você pode verificar se a exclusão reversível foi ativada usando o seguinte comando:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Para recuperar os blobs que foram excluídos acidentalmente, chame undelete nesses BLOBs. Lembre-se de que chamar undelete **blob**, tanto em BLOBs ativos e com exclusão reversível, irá restaurar todos os instantâneos de exclusão reversível associados como ativos. O exemplo a seguir chama undelete em todos os BLOBs com exclusão reversível e ativos em um contêiner:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Para localizar a política de retenção de exclusão reversível atual, use o seguinte comando:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

### <a name="azure-cli"></a>CLI do Azure 
Para habilitar a exclusão reversível, atualize as propriedades de serviço de um cliente de blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Para verificar se a exclusão reversível está ativada, use o seguinte comando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Biblioteca de cliente do Python
Para habilitar a exclusão reversível, atualize as propriedades de serviço de um cliente de blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

### <a name="net-client-library"></a>Biblioteca de cliente .NET
Para habilitar a exclusão reversível, atualize as propriedades de serviço de um cliente de blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Para recuperar os blobs que foram excluídos acidentalmente, chame undelete nesses BLOBs. Lembre-se de que chamar undelete **blob**, tanto em BLOBs ativos e com exclusão reversível, irá restaurar todos os instantâneos de exclusão reversível associados como ativos. O exemplo a seguir chama undelete em todos os BLOBs com exclusão reversível e ativos em um contêiner:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para recuperar para uma versão de blob específica, primeiro chame undelete em um blob e, em seguida, copie o instantâneo desejado sobre o blob. O exemplo a seguir recupera um blob de blocos para seu instantâneo gerado mais recentemente:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

## <a name="are-there-any-special-considerations-for-using-soft-delete"></a>Há alguma consideração especial para usar a exclusão reversível?
Se houver uma chance de que seus dados sejam acidentalmente modificados ou excluídos por um aplicativo ou outro usuário da conta de armazenamento, recomendamos a ativação da exclusão reversível. Habilitar a exclusão reversível para dados frequentemente substituídos pode resultar em encargos de capacidade de armazenamento maiores e maior latência ao listar BLOBs. Você pode mitigar isso armazenando os dados frequentemente substituídos em uma conta de armazenamento separada com a exclusão reversível desabilitada. 

## <a name="faq"></a>FAQ
**Para quais tipos de armazenamento posso usar a exclusão reversível?**  
Atualmente, a exclusão reversível só está disponível para armazenamento de BLOB (objeto).

**A exclusão reversível está disponível para todos os tipos de conta de armazenamento?**  
Sim, a exclusão reversível está disponível para contas de armazenamento de BLOBs, bem como para BLOBs em contas de armazenamento de uso geral (GPv1 e GPv2). Isso se aplica a contas standard e Premium. A exclusão reversível não está disponível para discos gerenciados.

**A exclusão reversível está disponível para todas as camadas de armazenamento?**  
Sim, a exclusão reversível está disponível para todas as camadas de armazenamento, incluindo quente, frio e arquivo. No entanto, a exclusão reversível não permite a substituição da proteção para BLOBs na camada de arquivo morto.

**Posso usar a API de camada de blob de conjunto para BLOBs de camada com instantâneos com exclusão reversível?**  
Sim. Os instantâneos com exclusão reversível permanecerão na camada original, mas o blob de base será movido para a nova camada. 

**As contas de armazenamento Premium têm um limite de instantâneo por blob de 100. Os instantâneos com exclusão reversível contam para esse limite?**  
Não, os instantâneos com exclusão reversível não contam para esse limite.

**Posso ativar a exclusão reversível para contas de armazenamento existentes?**  
Sim, a exclusão reversível é configurável para contas de armazenamento existentes e novas.

**Se eu excluir uma conta ou contêiner inteiro com exclusão reversível ativada, todos os BLOBs associados serão salvos?**  
Não, se você excluir uma conta ou um contêiner inteiro, todos os BLOBs associados serão excluídos permanentemente. Para saber como proteger uma conta de armazenamento de exclusões acidentais, consulte o artigo Azure Resource Manager [Bloquear recursos para evitar alterações](../../azure-resource-manager/resource-group-lock-resources.md)inesperadas.

**Posso exibir as métricas de capacidade dos dados excluídos?**  
Os dados com exclusão reversível são incluídos como parte da capacidade total da conta de armazenamento. Para obter mais informações sobre como controlar e monitorar a capacidade de armazenamento, consulte o artigo [análise de armazenamento](../common/storage-analytics.md) .

**Se eu desativar a exclusão reversível, ainda poderei acessar os dados com exclusão reversível?**  
Sim, você ainda poderá acessar e recuperar dados com exclusão reversível não expirada quando a exclusão reversível estiver desativada.

**Posso ler e copiar instantâneos com exclusão reversível do meu blob?**  
Sim, mas você deve chamar undelete no blob primeiro.

**A exclusão reversível está disponível para todos os tipos de BLOB?**  
Sim, a exclusão reversível está disponível para BLOBs de blocos, blobs de acréscimo e blobs de páginas.

**A exclusão reversível está disponível para discos de máquina virtual?**  
A exclusão reversível está disponível para discos não gerenciados Premium e Standard. A exclusão reversível só ajudará a recuperar os dados excluídos por **excluir blob**, **colocar blob**, **colocar lista de blocos**, **colocar bloco** e **copiar blob**. Os dados substituídos por uma chamada à **página Put** não são recuperáveis.

**Preciso alterar meus aplicativos existentes para usar a exclusão reversível?**  
É possível tirar proveito da exclusão reversível, independentemente da versão da API que você está usando. No entanto, para listar e recuperar blobs com exclusão reversível e instantâneos de BLOB, você precisará usar a versão 2017-07-29 da [API REST dos serviços de armazenamento](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) ou superior. Em geral, é sempre recomendável usar a versão mais recente, independentemente de você estar usando esse recurso.

## <a name="next-steps"></a>Passos Seguintes
* [Código de exemplo do .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [API REST do serviço blob](/rest/api/storageservices/blob-service-rest-api)
* [Replicação do armazenamento do Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Criando aplicativos altamente disponíveis usando o RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Recuperação de desastres e failover de conta de armazenamento (versão prévia) no armazenamento do Azure](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
