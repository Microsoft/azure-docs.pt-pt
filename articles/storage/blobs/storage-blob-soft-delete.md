---
title: Eliminação suave para bolhas de armazenamento Azure  Microsoft Docs
description: O Azure Storage oferece agora uma eliminação suave para objetos blob para que possa recuperar mais facilmente os seus dados quando estes forem erroneamente modificados ou eliminados por uma aplicação ou outro utilizador de conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 360930b708d6358692de2af7325701b73d5cf9c9
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370564"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Eliminação suave para bolhas de armazenamento Azure

O Azure Storage oferece agora uma eliminação suave para objetos blob para que possa recuperar mais facilmente os seus dados quando estes forem erroneamente modificados ou eliminados por uma aplicação ou outro utilizador de conta de armazenamento.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>Como a eliminação suave funciona

Quando ativado, a eliminação suave permite-lhe guardar e recuperar os seus dados quando as bolhas ou as imagens blob são eliminadas. Esta proteção estende-se aos dados blob que são apagados como resultado de uma sobreescrita.

Quando os dados são eliminados, transita para um estado suave apagado em vez de ser permanentemente apagado. Quando o soft delete está ligado e você sobrepor dados, um instantâneo suave apagado é gerado para salvar o estado dos dados sobreescritos. Os objetos apagados macios são invisíveis a menos que explicitamente listados. Pode configurar o período de tempo durante o qual os dados eliminados de forma recuperável são recuperáveis antes de expirarem de forma permanente.

O soft delete é compatível com o retrocesso, pelo que não é preciso fazer alterações nas suas aplicações para tirar partido das proteções que esta funcionalidade oferece. No entanto, [a recuperação de dados](#recovery) introduz uma nova API Não **DeleteB.**

### <a name="configuration-settings"></a>Definições de configuração

Quando cria uma nova conta, o soft delete é desligado por defeito. A eliminação suave também está desligada por padrão para as contas de armazenamento existentes. Pode alternar a funcionalidade a qualquer momento durante a vida útil de uma conta de armazenamento.

Ainda poderá aceder e recuperar dados apagados suaves quando a funcionalidade estiver desligada, assumindo que os dados eliminados suaves foram guardados quando a funcionalidade foi previamente ligada. Quando liga o soft delete, também precisa de configurar o período de retenção.

O período de retenção indica a quantidade de tempo que os dados eliminados suaves são armazenados e disponíveis para recuperação. Para bolhas e instantâneos blob que são explicitamente eliminados, o relógio do período de retenção começa quando os dados são eliminados. Para imagens suaves e eliminadas geradas pela função soft delete quando os dados são substituídos, o relógio começa quando o instantâneo é gerado. Atualmente pode reter dados apagados suaves entre 1 e 365 dias.

Pode alterar o período de retenção de eliminação suave a qualquer momento. Um período de retenção atualizado só se aplicará aos dados recentemente eliminados. Os dados previamente eliminados expirarão com base no período de retenção que foi configurado quando esses dados foram eliminados. Tentar eliminar um objeto eliminado suave não afetará o seu tempo de validade.

### <a name="saving-deleted-data"></a>Guardar dados eliminados

O soft delete preserva os seus dados em muitos casos em que bolhas ou instantâneos blob são eliminados ou substituídos.

Quando uma bolha é substituída usando **put blob**, **Put Block,** **Put Block List**, ou **Copy Blob** uma imagem do estado da bolha antes da operação de escrita é automaticamente gerada. Este instantâneo é um instantâneo suave apagado; é invisível a menos que objetos apagados suaves sejam explicitamente listados. Consulte a secção [Recovery](#recovery) para aprender a listar objetos apagados macios.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Os dados eliminados suaves são cinzentos, enquanto os dados ativos são azuis. Mais recentemente, os dados escritos aparecem abaixo de dados mais antigos. Quando B0 é substituído com B1, é gerada uma imagem suave apagada de B0. Quando b1 é substituído com B2, é gerada uma imagem suave apagada de B1.*

> [!NOTE]  
> A eliminação suave só oferece proteção de sobreescrita para operações de cópia quando é ligada para a conta da bolha de destino.

> [!NOTE]  
> A eliminação suave não oferece proteção de sobreescrita para bolhas no nível de arquivo. Se uma bolha no arquivo for substituída por uma nova bolha em qualquer nível, a bolha por escrito é permanentemente expirada.

Quando **o Delete Blob** é chamado num instantâneo, este instantâneo é marcado como suave mente apagado. Não é gerado um novo instantâneo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Os dados eliminados suaves são cinzentos, enquanto os dados ativos são azuis. Mais recentemente, os dados escritos aparecem abaixo de dados mais antigos. Quando **o Snapshot Blob** é chamado, b0 torna-se um instantâneo e B1 é o estado ativo da bolha. Quando o instantâneo B0 é apagado, é marcado como suave mente apagado.*

Quando **delete Blob** é chamado em uma bolha base (qualquer bolha que não seja em si um instantâneo), essa bolha é marcada como suave eliminada. Consistente com o comportamento anterior, chamar **Delete Blob** numa bolha que tem instantâneos ativos devolve um erro. Chamar **apagar blob** numa bolha com imagens apagadas suaves não devolve um erro. Ainda pode eliminar uma bolha e todas as suas imagens numa única operação quando a eliminação suave é ligada. Ao fazê-lo marca a bolha base e os instantâneos como apagados suaves.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Os dados eliminados suaves são cinzentos, enquanto os dados ativos são azuis. Mais recentemente, os dados escritos aparecem abaixo de dados mais antigos. Aqui, é feita uma chamada **Delete Blob** para eliminar B2 e todas as imagens associadas. A bolha ativa, B2 e todos os instantâneos associados são marcados como suavemente apagados.*

> [!NOTE]  
> Quando uma bolha suave apagada é substituída, uma imagem suave apagada do estado da bolha antes da operação de escrita é automaticamente gerada. A nova bolha herda o nível da bolha escrita.

A eliminação suave não guarda os seus dados em casos de eliminação de contentores ou conta, nem quando os metadados blob e as propriedades blob são substituídos. Para proteger uma conta de armazenamento de uma eliminação errónea, pode configurar um bloqueio utilizando o Gestor de Recursos Azure. Consulte o artigo Do Gestor de Recursos Azure Para [Evitar Alterações Inesperadas](../../azure-resource-manager/management/lock-resources.md) para saber mais.

Os seguintes detalhes da tabela esperam comportamento quando o soft delete é ligado:

| Operação REST API | Tipo de recurso | Descrição | Mudança de comportamento |
|--------------------|---------------|-------------|--------------------|
| [Eliminar](/rest/api/storagerp/StorageAccounts/Delete) | Conta | Elimina a conta de armazenamento, incluindo todos os recipientes e bolhas que contém.                           | Sem mudanças. Os recipientes e bolhas na conta eliminada não são recuperáveis. |
| [Eliminar recipiente](/rest/api/storageservices/delete-container) | Contentor | Elimina o recipiente, incluindo todas as bolhas que contém. | Sem mudanças. As bolhas no recipiente apagado não são recuperáveis. |
| [Coloque Blob](/rest/api/storageservices/put-blob) | Bloco, Apêndice e Page Blobs | Cria uma nova bolha ou substitui uma bolha existente dentro de um recipiente | Se utilizado para substituir uma bolha existente, uma imagem do estado da bolha antes da chamada é gerada automaticamente. Isto aplica-se também a uma bolha eliminada anteriormente macia se e apenas se for substituída por uma bolha do mesmo tipo (Bloco, Apêndice ou Página). Se for substituído por uma bolha de um tipo diferente, todos os dados eliminados e macios existentes serão permanentemente expirados. |
| [Eliminar blob](/rest/api/storageservices/delete-blob) | Bloco, Apêndice e Page Blobs | Marca uma bolha ou uma imagem de bolha para eliminação. A bolha ou instantâneo é mais tarde apagado durante a recolha de lixo | Se for utilizado para apagar uma imagem de bolha, esta imagem é marcada como suave. Se for utilizado para apagar uma bolha, esta bolha é marcada como suave. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | Bloco, Apêndice e Page Blobs | Copia uma bolha de origem para uma bolha de destino na mesma conta de armazenamento ou noutra conta de armazenamento. | Se utilizado para substituir uma bolha existente, uma imagem do estado da bolha antes da chamada é gerada automaticamente. Isto aplica-se também a uma bolha eliminada anteriormente macia se e apenas se for substituída por uma bolha do mesmo tipo (Bloco, Apêndice ou Página). Se for substituído por uma bolha de um tipo diferente, todos os dados eliminados e macios existentes serão permanentemente expirados. |
| [Bloquear](/rest/api/storageservices/put-block) | Bloco blobs | Cria um novo bloco para ser cometido como parte de uma bolha de bloco. | Se usado para comprometer um bloco a uma bolha ativa, não há mudança. Se usado para comprometer um bloco a uma bolha que é suave mente apagada, uma nova bolha é criada e um instantâneo é automaticamente gerado para capturar o estado da bolha suave apagada. |
| [Lista de blocos](/rest/api/storageservices/put-block-list) | Bloco blobs | Comete uma bolha especificando o conjunto de IDs de bloco que compõem a bolha do bloco. | Se utilizado para substituir uma bolha existente, uma imagem do estado da bolha antes da chamada é gerada automaticamente. Isto aplica-se também a uma bolha apagada anteriormente macia se e apenas se for uma Bolha de Bloco. Se for substituído por uma bolha de um tipo diferente, todos os dados eliminados e macios existentes serão permanentemente expirados. |
| [Página de colocação](/rest/api/storageservices/put-page) | Blobs de página | Escreve uma série de páginas para um Page Blob. | Sem mudanças. Os dados da Página Blob que são substituídos ou limpos usando esta operação não são guardados e não são recuperáveis. |
| [Bloco de apêndice](/rest/api/storageservices/append-block) | Blocos de apêndice | Escreve um bloco de dados até ao fim de um Append Blob | Sem mudanças. |
| [Definir propriedades blob](/rest/api/storageservices/set-blob-properties) | Bloco, Apêndice e Page Blobs | Define valores para propriedades do sistema definidas para uma bolha. | Sem mudanças. Propriedades de bolhas por escrito não são recuperáveis. |
| [Definir Metadados Blob](/rest/api/storageservices/set-blob-metadata) | Bloco, Apêndice e Page Blobs | Define os metadados definidos pelo utilizador para a bolha especificada como um ou mais pares de valor de nome. | Sem mudanças. Os metadados de blob por escrito não são recuperáveis. |

É importante notar que chamar "Página de Colocação" para substituir ou limpar gamas de um Page Blob não gerará automaticamente instantâneos. Os discos de máquinas virtuais são apoiados por Page Blobs e usam a **Página de Colocação** para escrever dados.

### <a name="recovery"></a>Recuperação

Chamar a operação [Undelete Blob](/rest/api/storageservices/undelete-blob) numa bolha de base suave e apagada restaura-a e todas as imagens suaves e macias associadas como ativas. Chamar a `Undelete Blob` operação numa bolha de base ativa restaura todos os instantâneos apagados associados como ativos. Quando as imagens são restauradas como ativas, parecem instantâneos gerados pelo utilizador; não sobreporem a bolha base.

Para restaurar uma bolha a um instantâneo suave e suave específico, pode chamar `Undelete Blob` na bolha base. Depois, pode copiar o instantâneo sobre a bolha agora ativa. Também pode copiar o instantâneo para uma nova bolha.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Os dados eliminados suaves são cinzentos, enquanto os dados ativos são azuis. Mais recentemente, os dados escritos aparecem abaixo de dados mais antigos. Aqui, **Undelete Blob** é chamado na blob B, restaurando assim a bolha base, B1, e todos os instantâneos associados, aqui apenas B0, como ativo. No segundo passo, B0 é copiado sobre a bolha base. Esta operação de cópia gera uma imagem suave apagada de B1.*

Para ver bolhas suaves apagadas e imagens de bolhas, pode optar por incluir dados eliminados nas **Blobs lista**. Pode optar por ver apenas bolhas de base apagadas suaves ou incluir também imagens de bolhas apagadas suaves. Para todos os dados eliminados suaves, pode visualizar o momento em que os dados foram eliminados, bem como o número de dias antes de os dados serem permanentemente expirados.

### <a name="example"></a>Exemplo

Segue-se a saída da consola de um script .NET que carrega, substitui, snapshots, elimina e restaura uma bolha chamada *HelloWorld* quando a eliminação suave é ligada:

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

Consulte a secção [de passos Seguintes](#next-steps) para obter um ponteiro para a aplicação que produziu esta saída.

## <a name="pricing-and-billing"></a>Preços e faturação

Todos os dados eliminados suaves são faturados ao mesmo ritmo que os dados ativos. Não será cobrado por dados que sejam permanentemente eliminados após o período de retenção configurado. Para um mergulho mais profundo em instantâneos e como acumulam encargos, por favor, veja como as [imagens acumulam encargos](storage-blob-snapshots.md).

Não será cobrado para as transações relacionadas com a geração automática de instantâneos. Será cobrado para transações **Blob Não Apagadas** à taxa de operações de escrita.

Para mais detalhes sobre os preços do Armazenamento De Blob Azure em geral, consulte a página de preços de [armazenamento de Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Quando inicialmente liga o soft delete, recomendamos que utilize um pequeno período de retenção para entender melhor como a funcionalidade irá afetar a sua conta.

## <a name="get-started"></a>Introdução

Os seguintes passos mostram como começar com soft delete.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ativar a eliminação suave de bolhas na sua conta de armazenamento utilizando o portal Azure:

1. No [portal Azure,](https://portal.azure.com/)selecione a sua conta de armazenamento. 

2. Navegue para a opção **de Proteção** de Dados no **âmbito do Serviço Blob**.

3. Clique **ativado** sob **a eliminação suave** de Blob

4. Insira o número de dias que pretende *manter ao* abrigo das políticas de **retenção**

5. Escolha o botão **Guardar** para confirmar as definições de Proteção de Dados

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Para ver bolhas apagadas suaves, selecione a caixa de verificação de **blobs eliminada supérre.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Para ver imagens apagadas suaves para uma dada bolha, selecione a bolha e, em seguida, clique em **Ver snapshots**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Certifique-se de que a caixa de verificação de **instantâneos eliminado seleções** do Show foi selecionada.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Quando clicar numa bolha ou instantâneo suavemente apagado, repare nas novas propriedades blob. Indicam quando o objeto foi apagado e quantos dias faltam até que a bolha ou a imagem blob estejam permanentemente expiradas. Se o objeto apagado macio não for instantâneo, também terá a opção de o desapagar.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Lembre-se que não desabater uma bolha também irá desapagar todos os instantâneos associados. Para desapagar as imagens apagadas macias para uma bolha ativa, clique na bolha e **selecione Undelete todos os instantâneos**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Uma vez que não apague as imagens de uma bolha, pode clicar em **Promover** para copiar uma imagem sobre a bolha da raiz, restaurando assim a bolha para o instantâneo.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob. O exemplo que se segue permite eliminar suavemente para um subconjunto de contas numa subscrição:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Pode verificar se a eliminação suave foi ativada utilizando o seguinte comando:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Para recuperar bolhas que foram acidentalmente apagadas, ligue para undelete nessas bolhas. Lembre-se que chamar **Undelete Blob**, tanto em bolhas apagadas ativas como suaves, irá restaurar todos os instantâneos apagados associados como ativos. O exemplo seguinte chama Undelete em todas as bolhas macias apagadas e ativas num recipiente:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Para encontrar a atual política de retenção soft delete, utilize o seguinte comando:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Para verificar se a eliminação suave está ligada, utilize o seguinte comando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[python](#tab/python)

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob:

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

# <a name="net"></a>[.NET](#tab/net)

Para permitir a eliminação suave, atualize as propriedades de serviço de um cliente blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Para recuperar bolhas que foram acidentalmente apagadas, ligue para undelete nessas bolhas. Lembre-se que chamar **Undelete Blob**, tanto em bolhas apagadas ativas como suaves, irá restaurar todos os instantâneos apagados associados como ativos. O exemplo seguinte chama Undelete em todas as bolhas macias apagadas e ativas num recipiente:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para recuperar para uma versão blob específica, primeiro ligue para Undelete em uma bolha e, em seguida, copie o instantâneo desejado sobre a bolha. O exemplo seguinte recupera uma bolha de bloco para o seu instantâneo mais recentemente gerado:

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

---

## <a name="special-considerations"></a>Considerações especiais

Se houver a possibilidade de os seus dados forem acidentalmente modificados ou eliminados por uma aplicação ou outro utilizador de conta de armazenamento, recomenda-se a visualização do soft delete. Permitir a eliminação suave de dados frequentemente sobreescritos pode resultar num aumento dos encargos de capacidade de armazenamento e no aumento da latência na listagem de bolhas. Pode atenuar este custo adicional e latência armazenando os dados frequentemente sobreescritos numa conta de armazenamento separada onde a eliminação suave é desativada. 

## <a name="faq"></a>FAQ

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Para que serviços de armazenamento posso usar soft delete?

Atualmente, o soft delete só está disponível para armazenamento blob (objeto).

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>A eliminação suave está disponível para todos os tipos de conta de armazenamento?

Sim, o soft delete está disponível para contas de armazenamento Blob, bem como para blobs em contas de armazenamento de fins gerais (gPv1 e GPv2). Os tipos de conta standard e premium são suportados. A eliminação suave está disponível para discos não geridos, que são bolhas de página sob as capas. O soft delete não está disponível para discos geridos.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>A eliminação suave está disponível para todos os níveis de armazenamento?

Sim, o soft delete está disponível para todos os níveis de armazenamento, incluindo quente, fresco e arquivo. No entanto, a eliminação suave não oferece proteção de sobreescrita para bolhas no nível de arquivo.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Posso usar a API de nível de set blob para nidificar bolhas com imagens apagadas macias?

Sim. As imagens suaves apagadas permanecerão no nível original, mas a bolha base passará para o novo nível. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>As contas de armazenamento premium têm um limite de instantâneo por bolha de 100. As imagens apagadas suaves contam para este limite?

Não, as imagens apagadas suaves não contam para este limite.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Posso ligar a eliminação suave para as contas de armazenamento existentes?

Sim, o soft delete é configurável tanto para contas de armazenamento existentes como novas.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Se eu apagar uma conta inteira ou recipiente com eliminação suave ligada, todas as bolhas associadas serão guardadas?

Não, se apagar uma conta ou recipiente inteiro, todas as bolhas associadas serão permanentemente eliminadas. Para obter mais informações sobre a proteção de uma conta de armazenamento de eliminações acidentais, consulte [os Recursos de Bloqueio para Evitar Alterações Inesperadas](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Posso ver métricas de capacidade para dados eliminados?

Os dados eliminados suaves são incluídos como parte da sua capacidade total de conta de armazenamento. Para obter mais informações sobre a capacidade de rastreio e monitorização da capacidade de armazenamento, consulte [Storage Analytics](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Se eu desligar suavemente, ainda poderei aceder a dados apagados suaves?

Sim, ainda poderá aceder e recuperar dados apagados não expirados quando o soft delete for desligado.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Posso ler e copiar fotos apagadas suaves da minha bolha?  

Sim, mas primeiro deves ligar para o Undelete on the Blob.

### <a name="is-soft-delete-available-for-all-blob-types"></a>A eliminação suave está disponível para todos os tipos de blob?

Sim, o soft delete está disponível para bolhas de bloco, bolhas de apêndice e bolhas de página.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>A eliminação suave está disponível para discos de máquinas virtuais?  

O soft delete está disponível para discos não geridos premium e standard, que são bolhas de página sob as capas. A eliminação suave só o ajudará a recuperar os dados eliminados por **Delete Blob**, **Put Blob,** **Put Block List,** **Put Block e** **Copy Blob.** Os dados sobressados por uma chamada para **Put Page** não são recuperáveis.

Uma máquina virtual Azure escreve para um disco não gerido usando chamadas para **Put Page**, por isso usar soft delete para desfazer escreve para um disco não gerido de um VM Azure não é um cenário suportado.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Preciso de alterar as minhas aplicações existentes para usar a eliminação suave?

É possível tirar partido da eliminação suave independentemente da versão API que está a utilizar. No entanto, para enumerar e recuperar bolhas e instantâneos suaves apagados, terá de utilizar a versão 2017-07-29 dos Serviços de [Armazenamento REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) ou mais. A Microsoft recomenda sempre a utilização da versão mais recente da API de Armazenamento Azure.

## <a name="next-steps"></a>Passos seguintes

* [Código da amostra .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Blob Serviço REST API](/rest/api/storageservices/blob-service-rest-api)
* [Replicação de armazenamento azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Conceber Aplicações Altamente Disponíveis utilizando RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Falha na conta de recuperação e armazenamento de desastres (pré-visualização) no Armazenamento Azure](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
