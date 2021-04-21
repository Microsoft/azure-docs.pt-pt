---
title: Criar uma partilha de ficheiros do Azure
titleSuffix: Azure Files
description: Como criar uma partilha de ficheiros Azure utilizando o portal Azure, PowerShell ou o Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 9caabb8dc7f09e4ef3852d9269d178c086744779
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789812"
---
# <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Para criar uma partilha de ficheiros Azure, precisa responder a três perguntas sobre como irá usá-lo:

- **Quais são os requisitos de desempenho para a sua partilha de ficheiros Azure?**  
    A Azure Files oferece ações de ficheiros padrão que são hospedadas em hardware baseado em discos rígidos (baseado em HDD) e ações de ficheiros premium, que estão hospedadas em hardware baseado em disco de estado sólido (baseado em SSD).

- **Quais são os seus requisitos de despedimento para a sua partilha de ficheiros Azure?**  
    As ações de ficheiros standard oferecem armazenamento localmente redundante (LRS), zona redundante (ZRS), geo-redundante (GRS) ou armazenamento de geo-zonas redundantes (GZRS), no entanto a grande funcionalidade de partilha de ficheiros só é suportada em ações de ficheiros redundantes locais e de zonas redundantes. As ações de ficheiros premium não suportam qualquer forma de geo-redundância.

    As ações de ficheiros premium estão disponíveis com redundância local e redundância de zona num subconjunto de regiões. Para saber se as ações de ficheiros premium estão atualmente disponíveis na sua região, consulte os [produtos disponíveis por página da região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para a Azure. Para obter informações sobre as regiões que suportam o ZRS, consulte [a redundância do Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

- **De que tamanho precisa?**  
    Nas contas de armazenamento redundantes locais e de zona, as ações de ficheiros Azure podem abranger até 100 TiB, no entanto, em contas de armazenamento redundantes geo-e geo-zona, as ações de ficheiros Azure podem abranger apenas até 5 TiB. 

Para obter mais informações sobre estas três opções, consulte [Planeamento para uma implementação de Ficheiros Azure](storage-files-planning.md).

## <a name="prerequisites"></a>Pré-requisitos
- Este artigo pressupõe que já criou uma subscrição do Azure. Se ainda não tem uma subscrição, então crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Se pretender utilizar o Azure PowerShell, [instale a versão mais recente](/powershell/azure/install-az-ps).
- Se pretender utilizar o Azure CLI, [instale a versão mais recente](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
As ações de ficheiros Azure são implantadas em contas de armazenamento, que são *objetos* de alto nível que representam um conjunto partilhado de armazenamento. Este conjunto de armazenamento pode ser usado para implantar várias partilhas de ficheiros. 

O Azure suporta vários tipos de contas de armazenamento para diferentes cenários de armazenamento que os clientes podem ter, mas existem dois tipos principais de contas de armazenamento para ficheiros Azure. Que tipo de conta de armazenamento precisa de criar depende se pretende criar uma parte de ficheiro padrão ou uma partilha de ficheiros premium: 

- **Contas de armazenamento versão geral 2 (GPv2): As** contas de armazenamento GPv2 permitem-lhe implantar ações de ficheiros Azure em hardware standard/hard disk (baseado em HDD). Além de armazenar ações de ficheiros Azure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, tais como recipientes blob, filas ou mesas. As ações de ficheiro podem ser implantadas nos níveis otimizados (predefinidos), quentes ou frescos.

- **Contas de armazenamento de filestorage**: As contas de armazenamento de filestorage permitem-lhe implantar ações de ficheiros Azure em hardware baseado em discos de estado premium/sólido (baseado em SSD). As contas de arquiteação de ficheiros só podem ser utilizadas para armazenar ações de ficheiros Azure; nenhum outros recursos de armazenamento (recipientes blob, filas, mesas, etc.) podem ser implantados numa conta de arquiteta.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta de armazenamento através do portal Azure, selecione **+ Crie um recurso** a partir do painel de instrumentos. Na janela de pesquisa Azure Marketplace resultante, procure a **conta de armazenamento** e selecione o resultado da pesquisa resultante. Isto levará a uma página geral para contas de armazenamento; **selecione Criar** para prosseguir com o assistente de criação de conta de armazenamento.

![Uma imagem da conta de armazenamento cria opção rápida num browser](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Noções básicas
A primeira secção a completar para criar uma conta de armazenamento é rotulada **Basics**. Isto contém todos os campos necessários para criar uma conta de armazenamento. Para criar uma conta de armazenamento GPv2, certifique-se de que o botão de rádio **Performance** está definido para *Standard* e a lista de drop-down **tipo conta** é selecionada para *StorageV2 (finalidade geral v2)*.

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-standard.png" alt-text="Uma imagem do botão de rádio de desempenho com o tipo de conta selecionado e tipo de conta com o storagev2 selecionado.":::

Para criar uma conta de armazenamento FileStorage, certifique-se de que o botão de rádio **Performance** está definido para *Premium* e as partilhas de ficheiros são **selecionadas** na lista de down-down **do tipo de conta Premium.**

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Uma imagem do botão de rádio de desempenho com o tipo de conta e o tipo de conta premium selecionados com ficheiros selecionados.":::

Os outros campos básicos são independentes da escolha da conta de armazenamento:
- **Nome da conta de** armazenamento : O nome do recurso da conta de armazenamento a criar. Este nome deve ser globalmente único, mas de outra forma pode qualquer nome que deseje. O nome da conta de armazenamento será usado como o nome do servidor quando montar uma partilha de ficheiroS Azure via SMB.
- **Localização**: A região para a conta de armazenamento a ser implantada. Esta pode ser a região associada ao grupo de recursos, ou qualquer outra região disponível.
- **Replicação**: Embora esta seja a replicação rotulada, este campo significa, na verdade, **redundância;** este é o nível de redundância desejado: redundância local (LRS), redundância de zona (ZRS), geo-redundância (GRS) e geo-redundância (GZRS). Esta lista de abandonos contém também a geo-redundância de acesso à leitura (RA-GRS) e a redundância de geo-zona de acesso à leitura (RA-GZRS), que não se aplicam às ações de ficheiros Azure; qualquer ação de ficheiro criada numa conta de armazenamento com estes selecionados será, na verdade, geo-redundante ou geo-zona redundante, respectivamente. 

#### <a name="networking"></a>Rede
A secção de networking permite-lhe configurar opções de networking. Estas configurações são opcionais para a criação da conta de armazenamento e podem ser configuradas posteriormente, se desejar. Para obter mais informações sobre estas opções, consulte [considerações de networking de ficheiros Azure](storage-files-networking-overview.md).

#### <a name="data-protection"></a>Proteção de dados
A secção de proteção de dados permite-lhe configurar a política de eliminação suave das ações de ficheiros Azure na sua conta de armazenamento. Outras definições relacionadas com a eliminação suave para bolhas, recipientes, restauro pontual para recipientes, versões e alimentos para mudanças aplicam-se apenas ao armazenamento do Azure Blob.

#### <a name="advanced"></a>Avançado
A secção avançada contém várias definições importantes para as ações de ficheiros Azure:

- **Transferência segura necessária**: Este campo indica se a conta de armazenamento requer encriptação em trânsito para comunicação à conta de armazenamento. Se necessitar de suporte SMB 2.1, deve desativá-lo.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-secure-transfer.png" alt-text="Uma imagem de transferência segura ativada nas definições avançadas para a conta de armazenamento.":::

- **Grandes ações de ficheiros**: Este campo permite a conta de armazenamento de ações de ficheiros que podem ir até 100 TiB. Ativar esta funcionalidade limitará a sua conta de armazenamento apenas a opções de armazenamento redundantes localmente e zonas redundantes. Uma vez que uma conta de armazenamento GPv2 tenha sido ativada para grandes ações de ficheiros, não é possível desativar a grande capacidade de partilha de ficheiros. As contas de armazenamento de filestorage (contas de armazenamento de ações de ficheiros premium) não têm esta opção, uma vez que todas as ações de ficheiros premium podem escalar até 100 TiB. 

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-large-file-shares.png" alt-text="Uma imagem da grande definição de partilha de ficheiros na lâmina avançada da conta de armazenamento.":::

As outras definições que estão disponíveis no separador avançado (espaço hierárquico para a azure Data Lake de armazenamento gen 2, nível blob padrão, NFSv3 para armazenamento de bolhas, etc.) não se aplicam aos Ficheiros Azure.

> [!Important]  
> A seleção do nível de acesso blob não afeta o nível da partilha de ficheiros.

#### <a name="tags"></a>Etiquetas
As etiquetas são pares de nome/valor que lhe permitem categorizar recursos e visualizar a faturação consolidada aplicando a mesma etiqueta a múltiplos recursos e grupos de recursos. Estes são opcionais e podem ser aplicados após a criação de conta de armazenamento.

#### <a name="review--create"></a>Rever + criar
O passo final para criar a conta de armazenamento é selecionar o botão **Criar** no **separador 'Revisão +' criar.** Este botão não estará disponível se todos os campos necessários para uma conta de armazenamento não estiverem preenchidos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar uma conta de armazenamento utilizando o PowerShell, usaremos o `New-AzStorageAccount` cmdlet. Este cmdlet tem muitas opções; apenas são apresentadas as opções necessárias. Para saber mais sobre opções avançadas, consulte a documentação do [ `New-AzStorageAccount` cmdlet](/powershell/module/az.storage/new-azstorageaccount).

Para simplificar a criação da conta de armazenamento e posterior partilha de ficheiros, armazenaremos vários parâmetros em variáveis. Pode substituir o conteúdo variável por quaisquer valores que desejar, no entanto note que o nome da conta de armazenamento deve ser globalmente único.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Para criar uma conta de armazenamento capaz de armazenar ações de ficheiros Standard Azure, usaremos o seguinte comando. O `-SkuName` parâmetro diz respeito ao tipo de redundância desejada; se desejar uma conta de armazenamento geo-redundante ou de geo-zona redundante, deve também remover o `-EnableLargeFileShare` parâmetro.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Para criar uma conta de armazenamento capaz de armazenar ações de ficheiros Premium Azure, usaremos o seguinte comando. Note que o `-SkuName` parâmetro mudou para incluir ambos `Premium` e o nível de redundância desejado de redundante localmente ( `LRS` ). O `-Kind` parâmetro é em vez de porque as `FileStorage` ações de ficheiro premium devem ser `StorageV2` criadas numa conta de armazenamento FileStorage em vez de uma conta de armazenamento GPv2.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar uma conta de armazenamento utilizando o CLI Azure, usaremos a conta de armazenamento az criar comando. Este comando tem muitas opções; apenas são apresentadas as opções necessárias. Para saber mais sobre as opções avançadas, consulte a documentação do [ `az storage account create` comando.](/cli/azure/storage/account)

Para simplificar a criação da conta de armazenamento e posterior partilha de ficheiros, armazenaremos vários parâmetros em variáveis. Pode substituir o conteúdo variável por quaisquer valores que desejar, no entanto note que o nome da conta de armazenamento deve ser globalmente único.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Para criar uma conta de armazenamento capaz de armazenar ações de ficheiros Standard Azure, usaremos o seguinte comando. O `--sku` parâmetro diz respeito ao tipo de redundância desejada; se desejar uma conta de armazenamento geo-redundante ou de geo-zona redundante, deve também remover o `--enable-large-file-share` parâmetro.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Para criar uma conta de armazenamento capaz de armazenar ações de ficheiros Premium Azure, usaremos o seguinte comando. Note que o `--sku` parâmetro mudou para incluir ambos `Premium` e o nível de redundância desejado de redundante localmente ( `LRS` ). O `--kind` parâmetro é em vez de porque as `FileStorage` ações de ficheiro premium devem ser `StorageV2` criadas numa conta de armazenamento FileStorage em vez de uma conta de armazenamento GPv2.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros
Uma vez criada a sua conta de armazenamento, tudo o que resta é criar a sua parte de ficheiro. Este processo é maioritariamente o mesmo, independentemente de estar a utilizar uma parte de ficheiro premium ou uma parte de ficheiro padrão. Devia considerar as seguintes diferenças.

As ações de ficheiros standard podem ser implantadas num dos níveis padrão: transação otimizada (padrão), quente ou fria. Este é um nível de partilha por ficheiro que não é afetado pelo nível de **acesso blob** da conta de armazenamento (esta propriedade apenas diz respeito ao armazenamento Azure Blob - não tem nada a ver com ficheiros Azure). Pode alterar o nível da partilha a qualquer momento depois de ter sido implantada. As ações de ficheiros premium não podem ser convertidas diretamente para qualquer nível padrão.

> [!Important]  
> Pode mover ações de ficheiros entre níveis dentro dos tipos de conta de armazenamento GPv2 (transação otimizada, quente e fria). Os movimentos de ações entre os níveis incorrem em transações: passar de um nível mais quente para um nível mais frio incorrerá na taxa de transação de escrita do nível mais frio para cada ficheiro da ação, enquanto uma mudança de um nível mais frio para um nível mais quente incorrerá na taxa de transação de leitura do nível cool para cada ficheiro da ação.

A propriedade **de quota** significa algo ligeiramente diferente entre as ações de arquivo premium e standard:

- Para ações de ficheiros padrão, é um limite superior da partilha de ficheiros Azure, para além da qual os utilizadores finais não podem ir. Se uma quota não for especificada, a quota-padrão pode abranger até 100 TiB (ou 5 TiB se a propriedade de grandes ações não estiver definida para uma conta de armazenamento).

- Para as ações de ficheiros premium, a quota significa **tamanho provisionado**. O tamanho previsto é o valor que será cobrado, independentemente do uso real. Para obter mais informações sobre como planear uma partilha de ficheiros premium, consulte [a provisionar ações de ficheiros premium](understanding-billing.md#provisioned-model).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Se acabou de criar a sua conta de armazenamento, pode navegar para ela a partir do ecrã de implementação selecionando **Go para o recurso**. Uma vez na conta de armazenamento, selecione **as ações do Ficheiro** na tabela de conteúdos para a conta de armazenamento.

Na listagem de ações de ficheiros, deverá ver quaisquer ações de ficheiro que tenha criado anteriormente nesta conta de armazenamento; uma mesa vazia se ainda não foram criadas ações de ficheiros. Selecione **+ Partilha de ficheiros** para criar uma nova partilha de ficheiros.

A nova lâmina de partilha de ficheiros deve aparecer no ecrã. Complete os campos na nova lâmina de partilha de ficheiros para criar uma partilha de ficheiros:

- **Nome:** o nome da partilha de ficheiros a criar.
- **Quota**: quota da ação de ficheiros para ações de ficheiros padrão; a dimensão prevista da parte do ficheiro para ações de ficheiros premium.
- **Níveis**: o nível selecionado para uma partilha de ficheiros. Este campo só está disponível numa **conta de armazenamento para fins gerais (GPv2).** Pode escolher transações otimizadas, quentes ou legais. O nível da ação pode ser alterado a qualquer momento. Recomendamos escolher o nível mais quente possível durante uma migração, para minimizar as despesas de transação e, em seguida, mudar para um nível mais baixo, se desejar após a migração estar completa.

Selecione **Criar** para terminar criando a nova partilha.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pode criar uma partilha de ficheiros Azure com o [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet. Os seguintes comandos PowerShell assumem que definiu as variáveis `$resourceGroupName` e como definido acima na `$storageAccountName` criação de uma conta de armazenamento com a secção Azure PowerShell. 

O exemplo a seguir mostra a criação de uma partilha de ficheiros com um nível explícito utilizando o `-AccessTier` parâmetro. Isto requer a utilização do módulo Az.Storage de pré-visualização, conforme indicado no exemplo. Se não for especificado um nível, quer porque está a utilizar o módulo GA Az.Storage, quer porque não incluiu este comando, o nível padrão para ações de ficheiros padrão é otimizado para a transação.

> [!Important]  
> Para ações de ficheiros premium, o `-QuotaGiB` parâmetro refere-se ao tamanho previsto da parte do ficheiro. O tamanho previsto da parte do ficheiro é o valor pelo qual será cobrado, independentemente da sua utilização. As ações de ficheiros padrão são faturadas com base no uso e não no tamanho previsto.

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Pode criar uma partilha de ficheiros Azure com o [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) comando. Os seguintes comandos Azure CLI assumem que definiu as variáveis `$resourceGroupName` e como definido acima na `$storageAccountName` criação de uma conta de armazenamento com a secção Azure CLI.

> [!Important]  
> Para ações de ficheiros premium, o `--quota` parâmetro refere-se ao tamanho previsto da parte do ficheiro. O tamanho previsto da parte do ficheiro é o valor pelo qual será cobrado, independentemente da sua utilização. As ações de ficheiros padrão são faturadas com base no uso e não no tamanho previsto.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre ações e ficheiros de ficheiros de [nomeação, consulte naming e referências de ações, diretórios, ficheiros e metadados](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

### <a name="change-the-tier-of-an-azure-file-share"></a>Alterar o nível de uma partilha de ficheiros Azure
As ações de ficheiros implantadas na **conta de armazenamento v2 (GPv2)** podem estar nos níveis otimizados, quentes ou frescos da transação. Pode alterar o nível da ação de ficheiro Azure a qualquer momento, sujeito a custos de transação como descrito acima.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Na página principal da conta de armazenamento, selecione **as ações de ficheiro**  selecione as ações de **ficheiros** etiquetadas de azulejos (também pode navegar para **arquivar ações** através da tabela de conteúdos para a conta de armazenamento).

:::image type="content" source="media/storage-files-quick-create-use-windows/click-files.png" alt-text="Screenshot da lâmina da conta de armazenamento, partilhas de ficheiros selecionadas.":::

Na lista de ações de tabela, selecione a partilha de ficheiros para a qual pretende alterar o nível. Na página de visão geral da partilha de ficheiros, selecione Alterar o **nível** do menu.

![Uma imagem da página geral da partilha de ficheiros com o botão de nível de alteração realçado](media/storage-how-to-create-file-share/change-tier-0.png)

No diálogo resultante, selecione o nível pretendido: transação otimizada, quente ou fria.

![Uma imagem do diálogo de nível de mudança](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O cmdlet PowerShell seguintes pressupõe que definiu as `$resourceGroupName` `$storageAccountName` variáveis , como `$shareName` descrito nas secções anteriores deste documento.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
O seguinte comando Azure CLI pressupõe que definiu o `$resourceGroupName` `$storageAccountName` , e `$shareName` variáveis como descrito nas secções anteriores deste documento.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Passos seguintes
- [Plano para a implementação de Ficheiros Azure](storage-files-planning.md) ou [Plano para uma implantação do Azure File Sync](../file-sync/file-sync-planning.md). 
- [Visão geral de ligação em rede](storage-files-networking-overview.md).
- Ligue e monte uma partilha de ficheiros no [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md).