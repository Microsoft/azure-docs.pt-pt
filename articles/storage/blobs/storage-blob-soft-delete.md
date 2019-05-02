---
title: Eliminação de forma recuperável para blobs de armazenamento do Azure | Documentos da Microsoft
description: O armazenamento do Azure agora oferece a eliminação de forma recuperável para objetos de blob, para que mais facilmente podem recuperar seus dados quando incorretamente é modificada ou eliminada por uma aplicação ou outro utilizador da conta de armazenamento.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: d9055b0c0decbeca0bb43969af4e854c396c3bb6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727428"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Eliminação de forma recuperável para blobs de armazenamento do Azure
O armazenamento do Azure agora oferece a eliminação de forma recuperável para objetos de blob, para que mais facilmente podem recuperar seus dados quando incorretamente é modificada ou eliminada por uma aplicação ou outro utilizador da conta de armazenamento.

## <a name="how-does-it-work"></a>Como funciona?
Quando ativado, eliminação de forma recuperável permite-lhe guardar e recuperar os seus dados de blobs ou instantâneos de blob são eliminados. Esta proteção abrange os dados de BLOBs são apagados como resultado de uma substituição.

Quando dados são eliminados, faz a transição para um Estado eliminado programável, em vez de serem eliminados permanentemente. Quando a eliminação de forma recuperável está ativada e substituir dados, é gerado um instantâneo eliminado de forma recuperável para guardar o estado dos dados substituídos. Objetos eliminados de forma recuperável são invisíveis, a menos que explicitamente listados. Pode configurar a quantidade de tempo, são possível recuperados dados eliminados de forma recuperável antes de expirar permanentemente.

Eliminação de forma recuperável é retrocompatível; não tem de fazer alterações às suas aplicações para tirar partido das proteções que permite esta funcionalidade. No entanto, [recuperação de dados](#recovery) introduz um novo **anular a eliminação de BLOBs** API.

### <a name="configuration-settings"></a>Definições de configuração
Quando cria uma nova conta, eliminação de forma recuperável está desativada por predefinição. Eliminação de forma recuperável também está desativada por predefinição para as contas de armazenamento existentes. Pode ativar/desativar a funcionalidade e desativar a qualquer momento durante o ciclo de vida de uma conta de armazenamento.

Continuará a ser capaz de aceder e recuperar dados eliminados de forma recuperável quando o recurso estiver desativado, supondo que os dados eliminados de forma recuperável foi guardados, quando o recurso tiver sido ativado. Quando ativar a eliminação de forma recuperável, terá também de configurar o período de retenção.

O período de retenção indica a quantidade de tempo que os dados eliminados de forma recuperável são armazenados e disponíveis para recuperação. Para blobs e instantâneos de blob são eliminados de forma explícita, o relógio de período de retenção é iniciado quando os dados são eliminados. Para instantâneos eliminados soft gerados pela funcionalidade de eliminação de forma recuperável quando dados são substituídos, o período começa quando o instantâneo é gerado. Atualmente pode manter os dados eliminados soft para entre 1 e 365 dias.

Pode alterar o período de retenção de eliminação de forma recuperável em qualquer altura. Um período de retenção foi atualizada só será aplicada à dados eliminados recentemente. Anteriormente dados eliminados irão expirar com base no período de retenção que foi configurado quando esses dados foi eliminados. A tentar eliminar um objeto eliminado de forma recuperável não afetará seu tempo de expiração.

### <a name="saving-deleted-data"></a>A guardar dados eliminados
Eliminação de forma recuperável preserva os dados em muitos casos em que blobs ou instantâneos de blob são excluídos ou substituídos.

Quando um blob é substituído pelos **colocar Blob**, **colocação blocos**, **colocar a lista de bloqueios** ou **Blob de cópia** um instantâneo do Estado do blob antes o Escreva a operação é gerada automaticamente. Este instantâneo é um instantâneo eliminado de forma recuperável; é invisível, a menos que os objetos eliminados de forma recuperável explicitamente estão listados. Consulte a [recuperação](#recovery) seção para aprender a lista de objetos eliminados de forma recuperável.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Dados eliminados de forma recuperável são cinzento, enquanto os dados do Active Directory são azuis. Mais recentemente escritos dados aparecem por baixo de dados mais antigos. Quando B0 seja substituído pelos B1, é gerado um instantâneo de eliminado de forma recuperável de B0. Quando o B1 seja substituído pelos B2, é gerado um instantâneo de eliminado de forma recuperável de B1.*

> [!NOTE]  
> Eliminação de forma recuperável apenas permite substituir a proteção para operações de cópia quando ele estiver ativado para a conta do blob de destino.

> [!NOTE]  
> Eliminação de forma recuperável não conseguir pagar substituir a proteção para os blobs na camada de arquivo. Se um blob no arquivo é substituído com um blob novo em qualquer camada, o blob substituído permanentemente expirou.

Quando **eliminar Blob** denomina-se num instantâneo, nesse instantâneo está marcado como programável eliminado. Não é gerado um novo instantâneo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Dados eliminados de forma recuperável são cinzento, enquanto os dados do Active Directory são azuis. Mais recentemente escritos dados aparecem por baixo de dados mais antigos. Quando **Blob de instantâneo** é chamado, B0 torna-se um instantâneo e B1 Estado ativo do blob. Quando o instantâneo de B0 é eliminado, é marcado como programável eliminado.*

Quando **eliminar Blob** denomina-se num blob base (qualquer blob ou seja não em si um instantâneo), esse blob está marcado como programável eliminado. Consistente com o comportamento anterior, chamando **eliminar Blob** num blob que tenha instantâneos de Active Directory devolve um erro. A invocar **eliminar Blob** num blob com instantâneos eliminados de forma recuperável não devolve um erro. Ainda pode eliminar um blob e todos os respetivos instantâneos numa única operação quando a eliminação de forma recuperável está ativada. Se o fizer, a marca o blob de base e instantâneos de forma recuperável como eliminado.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Dados eliminados de forma recuperável são cinzento, enquanto os dados do Active Directory são azuis. Mais recentemente escritos dados aparecem por baixo de dados mais antigos. Aqui, um **eliminar Blob** é feita uma chamada para eliminar B2 e associados todos os instantâneos. O blob de Active Directory, B2 e associados todos os instantâneos são marcados como programável eliminado.*

> [!NOTE]  
> Quando um blob de eliminado de forma recuperável é substituído, um instantâneo de eliminado de forma recuperável do Estado do blob antes da operação de escrita é gerado automaticamente. O blob novo herda a camada do blob substituído.

Eliminação de forma recuperável não guarde os seus dados em casos de contentor ou eliminações de conta, nem quando os metadados do blob e propriedades de blob são substituídas. Para proteger uma conta de armazenamento de eliminação incorretamente, pode configurar um bloqueio com o Azure Resource Manager. Consulte o artigo do Azure Resource Manager [bloquear recursos para impedir alterações inesperadas](../../azure-resource-manager/resource-group-lock-resources.md) para saber mais.

A tabela seguinte detalha o comportamento esperado quando a eliminação de forma recuperável está ativada:

| Operação de REST API | Tipo de recurso | Descrição | Alteração no comportamento |
|--------------------|---------------|-------------|--------------------|
| [Eliminar](/rest/api/storagerp/StorageAccounts/Delete) | Conta | Elimina a conta de armazenamento, incluindo todos os contentores e blobs que contém.                           | Nenhuma alteração. Contentores e blobs na conta eliminada não são recuperáveis. |
| [Eliminar contentor](/rest/api/storageservices/delete-container) | Contentor | Elimina o contentor, incluindo todos os blobs que contém. | Nenhuma alteração. BLOBs no contentor eliminado não são recuperáveis. |
| [Colocar o Blob](/rest/api/storageservices/put-blob) | Blocos e de acréscimo e Blobs de páginas | Cria um novo blob ou substitui um blob existente num contentor | Se for utilizado para substituir um blob existente, um instantâneo do Estado do blob antes da chamada é gerado automaticamente. Isto também se aplica a um blob eliminado anteriormente de forma recuperável se e somente se ele é substituído por um blob do mesmo tipo (bloco, acrescentar ou página). Se ele é substituído por um blob de um tipo diferente, todos os dados eliminados soft existentes irão expirar permanentemente. |
| [Eliminar Blob](/rest/api/storageservices/delete-blob) | Blocos e de acréscimo e Blobs de páginas | Marca um blob ou um instantâneo de blob para eliminação. O blob ou um instantâneo é mais tarde, eliminado durante a coleta de lixo | Se utilizado para eliminar que um instantâneo de blob, nesse instantâneo está marcado como programável é eliminado. Se utilizada para eliminar um blob, esse blob está marcado como programável eliminado. |
| [Copiar Blob](/rest/api/storageservices/copy-blob) | Blocos e de acréscimo e Blobs de páginas | Copia um blob de origem para um blob de destino na mesma conta de armazenamento ou em outra conta de armazenamento. | Se for utilizado para substituir um blob existente, um instantâneo do Estado do blob antes da chamada é gerado automaticamente. Isto também se aplica a um blob eliminado anteriormente de forma recuperável se e somente se ele é substituído por um blob do mesmo tipo (bloco, acrescentar ou página). Se ele é substituído por um blob de um tipo diferente, todos os dados eliminados soft existentes irão expirar permanentemente. |
| [Colocar o bloco](/rest/api/storageservices/put-block) | Blobs de bloco | Cria um novo bloco para ser confirmada como parte de um blob de blocos. | Se for utilizado para consolidar um bloco para um blob que seja o Active Directory não há nenhuma alteração. Se for utilizado para consolidar um bloco para um blob que seja soft eliminado, um blob novo é criado e automaticamente é gerado um instantâneo para capturar o estado do software blob eliminado. |
| [Colocar a lista de bloqueios](/rest/api/storageservices/put-block-list) | Blobs de bloco | Compromete-se um blob, especificando o conjunto de IDs que compõem o blob de blocos de bloco. | Se for utilizado para substituir um blob existente, um instantâneo do Estado do blob antes da chamada é gerado automaticamente. Isto também se aplica a um blob eliminado anteriormente de forma recuperável se, e apenas se, é um Blob de blocos. Se ele é substituído por um blob de um tipo diferente, todos os dados eliminados soft existentes irão expirar permanentemente. |
| [Colocar a página](/rest/api/storageservices/put-page) | Blobs de Página | Escreve um intervalo de páginas para um Blob de página. | Nenhuma alteração. Dados de Blob de páginas que serão substituídos ou limpo usando essa operação não são guardados e não são recuperáveis. |
| [Bloco de acréscimo](/rest/api/storageservices/append-block) | Blobs de acréscimo | Escreve um bloco de dados ao final de um Blob de acréscimo | Nenhuma alteração. |
| [Definir as propriedades do Blob](/rest/api/storageservices/set-blob-properties) | Blocos e de acréscimo e Blobs de páginas | Define valores para propriedades do sistema definidas para um blob. | Nenhuma alteração. Propriedades do blob substituídos não são recuperáveis. |
| [Definir metadados do Blob](/rest/api/storageservices/set-blob-metadata) | Blocos e de acréscimo e Blobs de páginas | Conjuntos metadados definidos pelo utilizador para o blob especificado como um ou mais pares de nome-valor. | Nenhuma alteração. Metadados do blob substituídos não são recuperável. |

É importante notar que chamar "Colocar página" para substituir ou desmarque os intervalos de um Blob de página não gerará automaticamente instantâneos. Discos da máquina virtual são apoiados por Blobs de páginas e utilizar **colocar página** para escrever dados.

### <a name="recovery"></a>Recuperação
Para tornar mais fácil de recuperar dados eliminados, introduzimos uma nova API de "Anular a eliminação de BLOBs". Chamar a API de anular a eliminação num blob de base foi eliminado soft restaurá-lo e todas as associadas soft instantâneos eliminados como ativa. Chamar a API de anular a eliminação num blob de base ativo, restaura todos os associados soft instantâneos eliminados como ativa. Quando os instantâneos forem restaurados como o Active Directory, como se parecem instantâneos gerados pelo utilizador; Não substituir o blob de base.

Para restaurar um blob para um instantâneo eliminado soft específico pode chamar **anular a eliminação de BLOBs** no base blob. Em seguida, pode copiar o instantâneo sobre o blob agora-ativo. Também pode copiar o instantâneo para um blob de novo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Dados eliminados de forma recuperável são cinzento, enquanto os dados do Active Directory são azuis. Mais recentemente escritos dados aparecem por baixo de dados mais antigos. Aqui, **anular a eliminação de BLOBs** é chamado no blob B, restaurar, deste modo, o blob de base, B1 e associados todos os instantâneos, aqui apenas B0, como o Active Directory. Na segunda etapa, é copiada B0 o blob de base. Esta operação de cópia gera um instantâneo de eliminado de forma recuperável de B1.*

Para ver blobs eliminados de forma recuperável e instantâneos de blob, pode optar por incluir dados eliminados em **listar os Blobs**. Pode escolher para visualizar apenas soft bases blobs eliminados ou para incluir também os instantâneos de blob foi eliminado de forma recuperável. Para todos os dados eliminados soft, pode ver a hora quando os dados foram eliminados, bem como o número de dias antes dos dados serão permanentemente expirou.

### <a name="example"></a>Exemplo
Segue-se a saída da consola de um script de .NET que carrega, substitui, instantâneos, eliminações e restaurações de eliminar um blob com o nome "HelloWorld" quando o software está ativado:

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

Consulte a [próximos passos](#next-steps) secção para um ponteiro para a aplicação que produziu esta saída.

## <a name="pricing-and-billing"></a>Preços e faturação
Todos os dados eliminados de forma recuperável é cobrado à mesma tarifa como dados do Active Directory. Não são cobradas para dados que são eliminados permanentemente após o período de retenção configurado. Para se aprofundar nas instantâneos e como eles acumulam despesas, veja [compreender como instantâneos de acumulam despesas](storage-blob-snapshots.md).

Não vai ser Taxado para as transações relacionadas com a geração automática de instantâneos. Será cobrado pela **anular a eliminação de BLOBs** transações à tarifa "Operações de escrita".

Para obter mais detalhes sobre os preços para armazenamento de Blobs do Azure em geral, consulte a [página de preços do Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Quando ativar inicialmente eliminação de forma recuperável, recomendamos que utilize um período de retenção pequeno para compreender melhor como o recurso afetará sua fatura.

## <a name="quickstart"></a>Início Rápido
### <a name="azure-portal"></a>Portal do Azure
Para ativar a eliminação de forma recuperável, navegue para o **eliminação de forma recuperável** opção sob **serviço Blob**. Em seguida, clique em **ativado** e introduza o número de dias que pretende manter os dados eliminados de forma recuperável.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Para ver os blobs eliminados de forma recuperável, selecione o **Show eliminado blobs** caixa de verificação.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Para ver os instantâneos eliminados soft para um determinado blob, selecione o blob, em seguida, clique em **ver instantâneos**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Certifique-se de que o **Show eliminar instantâneos** caixa de verificação está selecionada.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Quando clica num blob foi eliminado de forma recuperável ou um instantâneo, tenha em atenção as novas propriedades do blob. Indicam quando o objeto foi eliminado e o número de dias restantes até o blob ou um instantâneo de blob permanentemente expirou. Se o objeto excluído de forma recuperável não é um instantâneo, também terá a opção de anular ele.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Lembre-se de que undeleting um blob será também anular todos os instantâneos associados. Para anular a eliminação de forma recuperável instantâneos eliminados para um blob de Active Directory, clique no blob e selecione **anular a eliminação de todos os instantâneos**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Depois de anular a eliminação de instantâneos de um blob, pode clicar em **promover** devem transitar um instantâneo de blob de raiz, restaurar, deste modo, o blob para o instantâneo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para ativar a eliminação de forma recuperável, Atualize as propriedades do serviço de um cliente de blob. O exemplo seguinte ativa a eliminação de forma recuperável para um subconjunto de contas numa subscrição:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Pode verificar que essa eliminação de forma recuperável foi ativada com o seguinte comando:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Para recuperar blobs eliminados acidentalmente, chame desfazer a exclusão desses BLOBs. Lembre-se de que a chamada **anular a eliminação de BLOBs**, tanto em blobs eliminados de forma recuperável e Active Directory, irá restaurar instantâneos eliminados de forma recuperável todos os respectivos como ativa. O exemplo a seguir chama desfazer a exclusão em todos os soft eliminados e Active Directory blobs num contentor:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Para localizar a política de retenção de eliminação de forma recuperável atual, utilize o seguinte comando:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

### <a name="azure-cli"></a>CLI do Azure 
Para ativar a eliminação de forma recuperável, Atualize as propriedades do serviço de um cliente de blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Para verificar de forma recuperável delete estiver ativada, utilize o seguinte comando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Biblioteca de cliente Python
Para ativar a eliminação de forma recuperável, Atualize as propriedades do serviço de um cliente de blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Biblioteca de cliente .NET
Para ativar a eliminação de forma recuperável, Atualize as propriedades do serviço de um cliente de blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Para recuperar blobs eliminados acidentalmente, chame desfazer a exclusão desses BLOBs. Lembre-se de que a chamada **anular a eliminação de BLOBs**, tanto em blobs eliminados de forma recuperável e Active Directory, irá restaurar instantâneos eliminados de forma recuperável todos os respectivos como ativa. O exemplo a seguir chama desfazer a exclusão em todos os soft eliminados e Active Directory blobs num contentor:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para recuperar para uma versão de blob específico, primeira chamada desfazer a exclusão num blob, em seguida, copie o instantâneo pretendido sobre o blob. O exemplo a seguir recupera um blob de blocos para o instantâneo mais recentemente gerado:

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

## <a name="should-i-use-soft-delete"></a>Devo utilizar a eliminação de forma recuperável?
Se é provável que os seus dados acidentalmente são modificados ou eliminados por uma aplicação ou outro utilizador da conta de armazenamento, recomendamos que Ativando a eliminação de forma recuperável. Eliminação de forma recuperável é uma parte de uma estratégia de proteção de dados e pode ajudar a evitar perda de dados acidentais.

## <a name="faq"></a>FAQ
**Existem implicam considerações especiais para utilizar a eliminação de forma recuperável?**  
Ativar eliminação de forma recuperável para dados frequentemente substituídas poderá resultar em encargos de capacidade de armazenamento maiores e maior latência quando lista os blobs. Pode mitigar isso ao armazenar os dados com frequência substituídos numa conta de armazenamento separada com eliminação de forma recuperável desativada. 

**Para quais tipos de armazenamento posso utilizar eliminação de forma recuperável?**  
Atualmente, a eliminação de forma recuperável só está disponível para armazenamento de blob (objeto).

**Eliminação de forma recuperável está disponível para todos os tipos de conta de armazenamento?**  
Sim, está disponível para contas de armazenamento de BLOBs, bem como para os blobs para fins gerais na eliminação de forma recuperável contas de armazenamento (GPv1 e GPv2). Isto aplica-se para as contas standard e premium. Eliminação de forma recuperável não está disponível para os discos geridos.

**Eliminação de forma recuperável está disponível para todas as camadas de armazenamento?**  
Sim, a eliminação de forma recuperável está disponível para todas as camadas de armazenamento, incluindo as camadas frequente, esporádica e de arquivo. No entanto, a eliminação de forma recuperável não pode substituir a proteção para os blobs na camada de arquivo.

**Pode utilizar a API de camada do Blob de definir a uma camada de blobs com instantâneos eliminados de forma recuperável?**  
Sim. Os instantâneos de eliminado de forma recuperável irão permanecer no escalão original, mas o blob base será movido para o novo escalão. 

**Contas de armazenamento Premium têm um limite de instantâneo de blob de 100 por. De forma recuperável instantâneos eliminados contam para este limite?**  
Não, eliminado de forma recuperável instantâneos não contam para este limite.

**Pode ativar na eliminação de forma recuperável para contas de armazenamento existentes?**  
Sim, a eliminação de forma recuperável é configurável novas e existentes para contas de armazenamento.

**Se eliminar uma conta de toda ou o contentor com a eliminação de forma recuperável ativada, serão todos os blobs associados guardados?**  
Não, se eliminar uma conta de toda ou o contentor, todos os blobs associados serão permanentemente eliminados. Para saber como proteger uma conta de armazenamento de eliminações acidentais, consulte o artigo do Azure Resource Manager [bloquear recursos para impedir alterações inesperadas](../../azure-resource-manager/resource-group-lock-resources.md).

**Pode ver as métricas de capacidade de dados eliminados?**  
Dados eliminados de forma recuperável são incluídos como parte da sua capacidade de conta de armazenamento total. Para obter mais informações sobre o controlo e a capacidade de armazenamento de monitorização, consulte a [a análise de armazenamento](../common/storage-analytics.md) artigo.

**Se desativar eliminação de forma recuperável, ainda será capaz de acessar dados eliminados de forma recuperável?**  
Sim, ainda será capaz de aceder e recuperar dados eliminados de forma recuperável existe quando a eliminação de forma recuperável estiver desativada.

**Pode ler e copiar instantâneos eliminados soft do meu blob?**  
Sim, mas deve chamar primeiro desfazer a exclusão no blob.

**Eliminação de forma recuperável está disponível para todos os tipos de BLOBs?**  
Sim, a eliminação de forma recuperável está disponível para Blobs de blocos, Blobs de acréscimo e Blobs de páginas.

**Eliminação de forma recuperável está disponível para discos de máquinas virtuais?**  
Eliminação de forma recuperável está disponível para discos não geridos premium e standard. Eliminação de forma recuperável só ajudarão recuperar dados eliminados por **eliminar Blob**, **colocar Blob**, **colocar a lista de bloqueios**, **colocação blocos** e o **Copiar BLOBs**. Dados substituídos por uma chamada para **colocar página** não é recuperável.

**É necessário alterar as minhas aplicações existentes para utilizar a eliminação de forma recuperável?**  
É possível tirar partido da eliminação de forma recuperável, independentemente da versão de API que está a utilizar. No entanto, para listar e recuperar blobs eliminados de forma recuperável e instantâneos de blob, terá de utilizar a versão 2017-07 de 29 do [API de REST dos serviços de armazenamento](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) ou superior. Em geral, recomendamos sempre utilizar a versão mais recente, independentemente de se está a utilizar esta funcionalidade.

## <a name="next-steps"></a>Passos Seguintes
* [Código de exemplo do .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [REST API do serviço de BLOBs](/rest/api/storageservices/blob-service-rest-api)
* [Replicação de armazenamento do Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Conceber aplicações altamente disponíveis com RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Após desastre recuperação e o armazenamento de conta ativação pós-falha (pré-visualização) no armazenamento do Azure](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
