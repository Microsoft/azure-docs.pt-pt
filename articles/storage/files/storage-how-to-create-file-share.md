---
title: Criar uma partilha de ficheiros do Azure
titleSuffix: Azure Files
description: Como criar uma partilha de ficheiros Azure utilizando o portal Azure, PowerShell ou o Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 705910a9e2f4ebc80a63ab22ac4edecc5ae03cd0
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724804"
---
# <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Para criar uma partilha de ficheiros Azure, precisa responder a três perguntas sobre como irá usá-lo:

- **Quais são os requisitos de desempenho para a sua partilha de ficheiros Azure?**  
    A Azure Files oferece ações de ficheiros padrão (incluindo ações de ficheiros otimizadas, quentes e cool), que são hospedadas em hardware baseado em discos rígidos (baseado em HDD) e ações de ficheiros premium, que estão hospedadas em hardware baseado em disco de estado sólido (baseado em SSD).

- **De que tamanho precisa?**  
    As ações de ficheiros standard podem abranger até 100 TiB, no entanto esta funcionalidade não é ativada por padrão; se precisar de uma partilha de ficheiros superior a 5 TiB, terá de ativar a grande funcionalidade de partilha de ficheiros para a sua conta de armazenamento. As ações de ficheiros premium podem abranger até 100 TiB sem qualquer definição especial, no entanto, as ações de ficheiros premium são a provisionadas, em vez de pagarem à medida que forem como ações de ficheiros padrão. Isto significa que o fornecimento de uma parte de ficheiros muito maior do que o que você precisa aumentará o custo total de armazenamento.

- **Quais são os seus requisitos de despedimento para a sua partilha de ficheiros Azure?**  
    As ações de ficheiros standard oferecem armazenamento localmente redundante (LRS), zona redundante (ZRS), geo-redundante (GRS) ou armazenamento de geo-zonas redundantes (GZRS), no entanto a grande funcionalidade de partilha de ficheiros só é suportada em ações de ficheiros redundantes locais e de zonas redundantes. As ações de ficheiros premium não suportam qualquer forma de geo-redundância.

    As ações de ficheiros premium estão disponíveis com redundância local na maioria das regiões que oferecem contas de armazenamento e com redundância de zona num subconjunto menor de regiões. Para saber se as ações de ficheiros premium estão atualmente disponíveis na sua região, consulte os [produtos disponíveis por página da região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para a Azure. Para obter informações sobre as regiões que suportam o ZRS, consulte [a redundância do Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Para obter mais informações sobre estas três opções, consulte [Planeamento para uma implementação de Ficheiros Azure](storage-files-planning.md).

## <a name="prerequisites"></a>Pré-requisitos
- Este artigo pressupõe que já criou uma subscrição do Azure. Se ainda não tem uma subscrição, então crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Se pretender utilizar o Azure PowerShell, [instale a versão mais recente](/powershell/azure/install-az-ps).
- Se pretender utilizar o Azure CLI, [instale a versão mais recente](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
As ações de ficheiros Azure são implantadas em contas de armazenamento, que são *objetos* de alto nível que representam um conjunto partilhado de armazenamento. Este conjunto de armazenamento pode ser usado para implantar várias partilhas de ficheiros. 

O Azure suporta vários tipos de contas de armazenamento para diferentes cenários de armazenamento que os clientes podem ter, mas existem dois tipos principais de contas de armazenamento para ficheiros Azure. Que tipo de conta de armazenamento precisa de criar depende se pretende criar uma parte de ficheiro padrão ou uma partilha de ficheiros premium: 

- **Contas de armazenamento versão geral 2 (GPv2): As** contas de armazenamento GPv2 permitem-lhe implantar ações de ficheiros Azure em hardware standard/hard disk (baseado em HDD). Além de armazenar ações de ficheiros Azure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, tais como recipientes blob, filas ou mesas. As ações de ficheiro podem ser implantadas nos níveis otimizados (predefinidos), quentes ou frescos.

- **Contas de armazenamento de filestorage**: As contas de armazenamento de filestorage permitem-lhe implantar ações de ficheiros Azure em hardware baseado em discos de estado premium/sólido (baseado em SSD). As contas de arquiteação de ficheiros só podem ser utilizadas para armazenar ações de ficheiros Azure; nenhum outros recursos de armazenamento (recipientes blob, filas, mesas, etc.) podem ser implantados numa conta de arquiteta.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta de armazenamento através do portal Azure, selecione **+ Crie um recurso** a partir do painel de instrumentos. Na janela de pesquisa Azure Marketplace resultante, procure a **conta de armazenamento** e selecione o resultado da pesquisa resultante. Isto levará a uma página geral para contas de armazenamento; **selecione Criar** para prosseguir com o assistente de criação de conta de armazenamento.

![Uma imagem da conta de armazenamento cria opção rápida num browser](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>A secção De Bases
A primeira secção a completar para criar uma conta de armazenamento é rotulada **Basics**. Isto contém todos os campos necessários para criar uma conta de armazenamento. Para criar uma conta de armazenamento GPv2, certifique-se de que o botão de rádio **Performance** está definido para *Standard* e a lista de drop-down **tipo conta** é selecionada para *StorageV2 (finalidade geral v2)*.

![Uma imagem do botão de rádio Performance com o tipo Standard selecionado e conta com StorageV2 selecionado](media/storage-how-to-create-file-share/create-storage-account-1.png)

Para criar uma conta de armazenamento de fileStorage, certifique-se de que o botão de rádio **Performance** está definido para *Premium* e a lista de drop-down **tipo conta** é selecionada para *o FileStorage*.

![Uma imagem do botão de rádio Performance com premium selecionado e tipo de Conta com FileStorage selecionado](media/storage-how-to-create-file-share/create-storage-account-2.png)

Os outros campos básicos são independentes da escolha da conta de armazenamento:
- **Assinatura**: A subscrição da conta de armazenamento a ser implantada. 
- **Grupo de recursos**: O grupo de recursos para a conta de armazenamento a ser implantado. Pode criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente.
- **Nome da conta de** armazenamento : O nome do recurso da conta de armazenamento a criar. Este nome deve ser globalmente único, mas de outra forma pode qualquer nome que deseje. O nome da conta de armazenamento será usado como o nome do servidor quando montar uma partilha de ficheiroS Azure via SMB.
- **Localização**: A região para a conta de armazenamento a ser implantada. Esta pode ser a região associada ao grupo de recursos, ou qualquer outra região disponível.
- **Replicação**: Embora esta seja a replicação rotulada, este campo significa, na verdade, **redundância;** este é o nível de redundância desejado: redundância local (LRS), redundância de zona (ZRS), geo-redundância (GRS) e geo-redundância. Esta lista de abandonos contém também a geo-redundância de acesso à leitura (RA-GRS) e a redundância de geo-zona de acesso à leitura (RA-GZRS), que não se aplicam às ações de ficheiros Azure; qualquer ação de ficheiro criada numa conta de armazenamento com estes selecionados será, na verdade, geo-redundante ou geo-zona redundante, respectivamente. Dependendo da sua região ou do tipo de conta de armazenamento selecionado, algumas opções de redundância podem não ser permitidas.
- **Nível de acesso blob**: Este campo não se aplica aos Ficheiros Azure, para que possa escolher um dos botões de rádio. 

> [!Important]  
> A seleção do nível de acesso blob não afeta o nível da partilha de ficheiros.

#### <a name="the-networking-blade"></a>A lâmina de networking
A secção de networking permite-lhe configurar opções de networking. Estas configurações são opcionais para a criação da conta de armazenamento e podem ser configuradas posteriormente, se desejar. Para obter mais informações sobre estas opções, consulte [considerações de networking de ficheiros Azure](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>A lâmina avançada
A secção avançada contém várias definições importantes para as ações de ficheiros Azure:

- **Transferência segura necessária**: Este campo indica se a conta de armazenamento requer encriptação em trânsito para comunicação à conta de armazenamento. Recomendamos que isto fique ativado, no entanto, se necessitar de suporte SMB 2.1, deve desativá-lo. Recomendamos que, se desativar a encriptação, conclua o acesso da sua conta de armazenamento a uma rede virtual com pontos finais de serviço e/ou pontos finais privados.
- **Grandes ações de ficheiros**: Este campo permite a conta de armazenamento de ações de ficheiros que podem ir até 100 TiB. Ativar esta funcionalidade limitará a sua conta de armazenamento apenas a opções de armazenamento redundantes localmente e zonas redundantes. Uma vez que uma conta de armazenamento GPv2 tenha sido ativada para grandes ações de ficheiros, não é possível desativar a grande capacidade de partilha de ficheiros. As contas de armazenamento de filestorage (contas de armazenamento de ações de ficheiros premium) não têm esta opção, uma vez que todas as ações de ficheiros premium podem escalar até 100 TiB. 

![Uma imagem das definições avançadas importantes que se aplicam aos Ficheiros Azure](media/storage-how-to-create-file-share/create-storage-account-3.png)

As outras definições que estão disponíveis no separador avançado (blob soft-delete, espaço hierárquico de nomes para Azure Data Lake de armazenamento gen 2, e NFSv3 para armazenamento de bolhas) não se aplicam aos Ficheiros Azure.

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

```bash
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Para criar uma conta de armazenamento capaz de armazenar ações de ficheiros Standard Azure, usaremos o seguinte comando. O `--sku` parâmetro diz respeito ao tipo de redundância desejada; se desejar uma conta de armazenamento geo-redundante ou de geo-zona redundante, deve também remover o `--enable-large-file-share` parâmetro.

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Para criar uma conta de armazenamento capaz de armazenar ações de ficheiros Premium Azure, usaremos o seguinte comando. Note que o `--sku` parâmetro mudou para incluir ambos `Premium` e o nível de redundância desejado de redundante localmente ( `LRS` ). O `--kind` parâmetro é em vez de porque as `FileStorage` ações de ficheiro premium devem ser `StorageV2` criadas numa conta de armazenamento FileStorage em vez de uma conta de armazenamento GPv2.

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Criar a partilha de ficheiros
Uma vez criada a sua conta de armazenamento, tudo o que resta é criar a sua parte de ficheiro. Este processo é maioritariamente o mesmo, independentemente de estar a utilizar uma parte de ficheiro premium ou uma parte de ficheiro padrão. Devia considerar as seguintes diferenças.

As ações de ficheiros standard podem ser implantadas num dos níveis padrão: transação otimizada (padrão), quente ou fria. Este é um nível de partilha por ficheiro que não é afetado pelo nível de **acesso blob** da conta de armazenamento (esta propriedade apenas diz respeito ao armazenamento Azure Blob - não tem nada a ver com ficheiros Azure). Pode alterar o nível da partilha a qualquer momento depois de ter sido implantada. As ações de ficheiro premium não podem ser convertidas diretamente para ações de ficheiros padrão em qualquer nível padrão.

> [!Important]  
> Pode mover ações de ficheiros entre níveis dentro dos tipos de conta de armazenamento GPv2 (transação otimizada, quente e fria). Os movimentos de ações entre os níveis incorrem em transações: passar de um nível mais quente para um nível mais frio incorrerá na taxa de transação de escrita do nível mais frio para cada ficheiro da ação, enquanto uma mudança de um nível mais frio para um nível mais quente incorrerá na taxa de transação de leitura do nível cool para cada ficheiro da ação.

A propriedade **de quota** significa algo ligeiramente diferente entre as ações de arquivo premium e standard:

- Para ações de ficheiros padrão, é um limite superior da partilha de ficheiros Azure, para além da qual os utilizadores finais não podem ir. O principal objetivo da quota para uma quota-padrão é o orçamento: "Não quero que esta parte dos ficheiros cresça para além deste ponto". Se uma quota não for especificada, a quota-padrão pode abranger até 100 TiB (ou 5 TiB se a propriedade de grandes ações não estiver definida para uma conta de armazenamento).

- No caso das ações de ficheiros premium, a quota está sobrecarregada para o **tamanho previsto.** O tamanho previsto é o valor que será cobrado, independentemente do uso real. Ao providenciar uma parte de ficheiro premium, pretende considerar dois fatores: 1) o crescimento futuro da parte numa perspetiva de utilização do espaço e 2) o IOPS necessário para a sua carga de trabalho. Cada GiB abasteci-se a um IOPS adicional reservado e rebentado. Para obter mais informações sobre como planear uma partilha de ficheiros premium, consulte [a provisionar ações de ficheiros premium](understanding-billing.md#provisioned-billing).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Se acabou de criar a sua conta de armazenamento, pode navegar para ela a partir do ecrã de implementação selecionando **Go para o recurso**. Se criou previamente a conta de armazenamento, pode navegar para ela através do grupo de recursos que a contém. Uma vez na conta de armazenamento, selecione as **ações de ficheiro** com rótulo de azulejo (também pode navegar para **arquivar ações** através da tabela de conteúdos para a conta de armazenamento).

![Uma imagem do arquivo partilha azulejo](media/storage-how-to-create-file-share/create-file-share-1.png)

Na listagem de ações de ficheiros, deverá ver quaisquer ações de ficheiro que tenha criado anteriormente nesta conta de armazenamento; uma mesa vazia se ainda não foram criadas ações de ficheiros. Selecione **+ Partilha de ficheiros** para criar uma nova partilha de ficheiros.

A nova lâmina de partilha de ficheiros deve aparecer no ecrã. Complete os campos na nova lâmina de partilha de ficheiros para criar uma partilha de ficheiros:

- **Nome:** o nome da partilha de ficheiros a criar.
- **Quota**: quota da ação de ficheiros para ações de ficheiros padrão; a dimensão prevista da parte do ficheiro para ações de ficheiros premium.
- **Níveis**: o nível selecionado para uma partilha de ficheiros. Este campo só está disponível numa **conta de armazenamento para fins gerais (GPv2).** Pode escolher transações otimizadas, quentes ou legais. O nível da ação pode ser alterado a qualquer momento. Recomendamos escolher o nível mais alto possível durante uma migração, minimizar as despesas de transação e, em seguida, mudar para um nível mais baixo, se desejar que a migração esteja completa.

Selecione **Criar** para terminar criando a nova partilha. Note que se a sua conta de armazenamento estiver numa rede virtual, não será capaz de criar com sucesso uma partilha de ficheiros Azure, a menos que o seu cliente também esteja na rede virtual. Também pode trabalhar em torno desta limitação pontual utilizando o cmdlet Azure `New-AzRmStorageShare` PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pode criar uma partilha de ficheiros Azure com o [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet. Os seguintes comandos PowerShell assumem que definiu as variáveis `$resourceGroupName` e como definido acima na `$storageAccountName` criação de uma conta de armazenamento com a secção Azure PowerShell. 

O exemplo a seguir mostra a criação de uma partilha de ficheiros com um nível explícito utilizando o `-AccessTier` parâmetro. Isto requer a utilização do módulo Az.Storage de pré-visualização, conforme indicado no exemplo. Se não for especificado um nível, quer porque está a utilizar o módulo GA Az.Storage, quer porque não incluiu este comando, o nível padrão para ações de ficheiros padrão é otimizado para a transação.

> [!Important]  
> Para ações de ficheiros premium, o `-QuotaGiB` parâmetro refere-se ao tamanho previsto da parte do ficheiro. O tamanho previsto da parte do ficheiro é o valor pelo qual será cobrado, independentemente da sua utilização. As ações de ficheiros padrão são faturadas com base no uso e não no tamanho previsto.

```powershell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

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

> [!Note]  
> A capacidade de definir e alterar níveis através do PowerShell é fornecida no módulo Az.Storage PowerShell de pré-visualização. Estes cmdlets ou a sua saída podem mudar antes de serem lançados no módulo Az.Storage PowerShell geralmente disponível, por isso crie scripts com isto em mente.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Pode criar uma partilha de ficheiros Azure com o [`az storage share-rm create`](/cli/azure/storage/share-rm?preserve-view=true&view=azure-cli-latest#az_storage_share_rm_create) comando. Os seguintes comandos Azure CLI assumem que definiu as variáveis `$resourceGroupName` e como definido acima na `$storageAccountName` criação de uma conta de armazenamento com a secção Azure CLI.

A funcionalidade para criar ou mover uma partilha de ficheiros para um nível específico está disponível na mais recente atualização do Azure CLI. A atualização do Azure CLI é específica para a distribuição do sistema operativo/Linux que está a utilizar. Para obter instruções sobre como atualizar o Azure CLI no seu sistema, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

> [!Important]  
> Para ações de ficheiros premium, o `--quota` parâmetro refere-se ao tamanho previsto da parte do ficheiro. O tamanho previsto da parte do ficheiro é o valor pelo qual será cobrado, independentemente da sua utilização. As ações de ficheiros padrão são faturadas com base no uso e não no tamanho previsto.

```bash
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

> [!Note]  
> A capacidade de definir um nível com o `--access-tier` parâmetro é fornecida uma pré-visualização no mais recente pacote Azure CLI. Este comando ou a sua saída podem mudar antes de serem marcados como geralmente disponíveis, por isso crie scripts com isso em mente.

---

> [!Note]  
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre ações e ficheiros de ficheiros de [nomeação, consulte naming e referências de ações, diretórios, ficheiros e metadados](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

### <a name="changing-the-tier-of-an-azure-file-share"></a>Alterar o nível de uma partilha de ficheiros Azure
As ações de ficheiros implantadas na **conta de armazenamento v2 (GPv2)** podem estar nos níveis otimizados, quentes ou frescos da transação. Pode alterar o nível da ação de ficheiro Azure a qualquer momento, sujeito a custos de transação como descrito acima.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Na página principal da conta de armazenamento, selecione **as ações de ficheiro**  selecione as ações de **ficheiros** etiquetadas de azulejos (também pode navegar para **arquivar ações** através da tabela de conteúdos para a conta de armazenamento).

![Uma imagem do arquivo partilha azulejo](media/storage-how-to-create-file-share/create-file-share-1.png)

Na lista de ações de tabela, selecione a partilha de ficheiros para a qual pretende alterar o nível. Na página de visão geral da partilha de ficheiros, selecione Alterar o **nível** do menu.

![Uma imagem da página geral da partilha de ficheiros com o botão de nível de alteração realçado](media/storage-how-to-create-file-share/change-tier-0.png)

No diálogo resultante, selecione o nível pretendido: transação otimizada, quente ou fria.

![Uma imagem do diálogo de nível de mudança](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O cmdlet PowerShell seguintes pressupõe que definiu as `$resourceGroupName` `$storageAccountName` variáveis , como `$shareName` descrito nas secções anteriores deste documento.

```PowerShell
# This cmdlet requires Az.Storage version 2.1.1-preview, which is installed
# in the earlier example.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
O seguinte comando Azure CLI pressupõe que definiu o `$resourceGroupName` `$storageAccountName` , e `$shareName` variáveis como descrito nas secções anteriores deste documento.

```bash
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Passos seguintes
- [Plano para a implementação de Ficheiros Azure](storage-files-planning.md) ou [Plano para uma implantação do Azure File Sync](storage-sync-files-planning.md). 
- [Visão geral de ligação em rede](storage-files-networking-overview.md).
- Ligue e monte uma partilha de ficheiros no [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md).