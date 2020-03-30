---
title: Criar uma partilha de ficheiros do Azure
titleSuffix: Azure Files
description: Como criar uma partilha de ficheiros Azure utilizando o portal Azure, PowerShell ou o Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ed6abbac7c5953eaec4fa4584248d0d98b49ba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596926"
---
# <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Para criar uma partilha de ficheiros Azure, precisa responder a três perguntas sobre como irá usá-lo:

- **Quais são os requisitos de desempenho para a sua partilha de ficheiros Azure?**  
    O Azure Files oferece ações de ficheiros padrão, que são alojadas em hardware baseado em discos rígidos (baseados em HDD) e ações de ficheiros premium, que estão alojadas em hardware baseado em discos sólidos (baseadoem em SSD).

- **De que tamanho precisa?**  
    As ações de ficheiros standard podem abranger até 100 TiB, no entanto esta funcionalidade não está ativada por defeito; se precisar de uma partilha de ficheiros superior a 5 TiB, terá de ativar a funcionalidade de partilha de ficheiros para a sua conta de armazenamento. As ações de ficheiropremium podem abranger até 100 TiB sem qualquer configuração especial, no entanto, as ações de ficheiropremium são provisionadas, em vez de pagar em vez de pagar em vez de ir como ações de ficheiro padrão. Isto significa que o fornecimento de um ficheiro partilha muito maior do que o necessário aumentará o custo total de armazenamento.

- **Quais são os seus requisitos de despedimento para a sua parte de ficheiroS Azure?**  
    As ações de ficheiros standard oferecem armazenamento localmente redundante (LRS), zona redundante (ZRS), armazenamento geo-redundante (GRS) ou geozona redundante (GZRS), no entanto a grande funcionalidade de partilha de ficheiros só é suportada em ações de ficheiros redundantes localmente redundantes e redundantes da zona. As ações de ficheiros premium não suportam qualquer forma de georedundância.

    As ações de ficheiros premium estão disponíveis com redundância local na maioria das regiões que oferecem contas de armazenamento e com redundância de zona num subconjunto menor de regiões. Para saber se as ações de ficheiropremium estão atualmente disponíveis na sua região, consulte os [produtos disponíveis por página da região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para o Azure. Para obter informações sobre regiões que apoiam o ZRS, consulte o [despedimento do Armazenamento Azure.](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Para obter mais informações sobre estas três opções, consulte Planeamento para uma implementação de [Ficheiros Azure](storage-files-planning.md).

## <a name="prerequisites"></a>Pré-requisitos
- Este artigo assume que já criou uma subscrição azure. Se ainda não tem uma subscrição, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Se pretende utilizar o Azure PowerShell, [instale a versão mais recente.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Se pretender utilizar o Azure CLI, [instale a versão mais recente.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-storage-account"></a>Criar uma conta do Storage
As ações de ficheiros Azure são implantadas em contas de armazenamento, que são *objetos*de alto nível que representam um conjunto partilhado de armazenamento. Este conjunto de armazenamento pode ser usado para implementar várias partilhas de ficheiros. 

O Azure suporta vários tipos de contas de armazenamento para diferentes cenários de armazenamento que os clientes podem ter, mas existem dois tipos principais de contas de armazenamento para ficheiros Azure. Que tipo de conta de armazenamento precisa criar depende se pretende criar uma partilha de ficheiros padrão ou uma partilha de ficheiro premium: 

- Contas de armazenamento da **versão 2 (GPv2)** de propósito geral : As contas de armazenamento GPv2 permitem-lhe implementar ações de ficheiroS Azure em hardware baseado em disco standard/rígido (baseado em HDD). Além de armazenar ações de ficheiros Azure, as contas de armazenamento Da GPv2 podem armazenar outros recursos de armazenamento, tais como contentores blob, filas ou mesas. 

- Contas de armazenamento de **ficheiros**: As contas de armazenamento de ficheiros permitem-lhe implementar ações de ficheiroS Azure em hardware baseado em discos premium/sólidos (baseados em SSD). As contas de FileStorage só podem ser utilizadas para armazenar ações de ficheiros Azure; nenhum outro recursos de armazenamento (recipientes de blob, filas, mesas, etc.) pode ser implantado numa conta fileStorage.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta de armazenamento através do portal Azure, selecione **+ Crie um recurso** a partir do painel de instrumentos. Na janela de pesquisa do Azure Marketplace resultante, procure uma conta de **armazenamento** e selecione o resultado de pesquisa resultante. Isto levará a uma página geral para contas de armazenamento; selecione **Criar** para prosseguir com o assistente de criação da conta de armazenamento.

![Uma imagem da conta de armazenamento rapidamente criar opção em um navegador](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>A secção Básica
A primeira secção a concluir para criar uma conta de armazenamento está rotulada de **Básicos**. Isto contém todos os campos necessários para criar uma conta de armazenamento. Para criar uma conta de armazenamento GPv2, certifique-se de que o botão de rádio **Performance** está definido para *Standard* e a lista de drop-down **tipo Conta** é selecionada para *StorageV2 (finalidade geral v2)*.

![Uma imagem do botão de rádio Performance com standard selecionado e tipo de conta com StorageV2 selecionado](media/storage-how-to-create-file-share/create-storage-account-1.png)

Para criar uma conta de armazenamento de FileStorage, certifique-se de que o botão de rádio **Performance** está definido para *Premium* e a lista de drop-down **tipo Conta** é selecionada para *FileStorage*.

![Uma imagem do botão de rádio Performance com Premium selecionado e tipo de conta com FileStorage selecionado](media/storage-how-to-create-file-share/create-storage-account-2.png)

Os outros campos básicos são independentes da escolha da conta de armazenamento:
- **Subscrição**: A subscrição da conta de armazenamento a ser implantada. 
- **Grupo de recursos**: O grupo de recursos para a conta de armazenamento a ser implantado. Pode criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente.
- **Nome**da conta de armazenamento : O nome do recurso da conta de armazenamento a criar. Este nome deve ser globalmente único, mas de outra forma pode qualquer nome que deseje. O nome da conta de armazenamento será usado como nome do servidor quando montar uma partilha de ficheiros Azure via SMB.
- **Localização**: A região para a conta de armazenamento a ser implantada. Esta pode ser a região associada ao grupo de recursos, ou qualquer outra região disponível.
- **Replicação**: Embora esta esteja rotulada de replicação, este campo significa, na verdade, **redundância;** este é o nível de despedimento desejado: redundância local (LRS), redundância da zona (ZRS), geo-redundância (GRS) e redundância geozona. Esta lista de abandono também contém geo-redundância de acesso à leitura (RA-GRS) e redundância geo-zona de acesso de leitura (RA-GZRS), que não se aplicam às ações de ficheiros Azure; qualquer parte de ficheiro criada numa conta de armazenamento com estas selecionadas será, na verdade, geo-redundante ou geo-zona redundante, respectivamente. Dependendo do tipo de conta de armazenamento da sua região ou selecionada, algumas opções de despedimento podem não ser permitidas.
- **Nível de acesso:** Este campo não se aplica aos Ficheiros Azure, pelo que pode escolher qualquer um dos botões de rádio.

#### <a name="the-networking-blade"></a>A lâmina de rede
A secção de networking permite-lhe configurar opções de networking. Estas configurações são opcionais para a criação da conta de armazenamento e podem ser configuradas posteriormente, se desejar. Para obter mais informações sobre estas opções, consulte as considerações de [networking do Azure Files.](storage-files-networking-overview.md)

#### <a name="the-advanced-blade"></a>A lâmina avançada
A secção avançada contém várias definições importantes para as partilhas de ficheiros Azure:

- **Transferência segura necessária**: Este campo indica se a conta de armazenamento requer encriptação em trânsito para comunicação à conta de armazenamento. No entanto, recomendamos que fique ativado, se necessitar de suporte SMB 2.1, deve desativar este. Recomendamos que desative a encriptação que limita o acesso da sua conta de armazenamento a uma rede virtual com pontos finais de serviço e/ou pontos finais privados.
- **Grandes partilhas de ficheiros:** Este campo permite a conta de armazenamento de ações de ficheiros até 100 TiB. Ativar esta funcionalidade limitará a sua conta de armazenamento a apenas opções de armazenamento redundantes localmente e redundantes da zona. Uma vez ativada uma conta de armazenamento GPv2 para grandes ações de ficheiros, não é possível desativar a grande capacidade de partilha de ficheiros. As contas de armazenamento de filestorage (contas de armazenamento de ações de ficheiropremium) não têm esta opção, uma vez que todas as ações de ficheiropremium podem escalar até 100 TiB. 

![Uma imagem das definições avançadas importantes que se aplicam aos Ficheiros Azure](media/storage-how-to-create-file-share/create-storage-account-3.png)

As outras definições disponíveis no separador avançado (blob soft-delete, espaço hierárquico de armazenamento do Lago de Dados Azure gen 2, e NFSv3 para armazenamento de bolhas) não se aplicam aos Ficheiros Azure.

#### <a name="tags"></a>Etiquetas
As etiquetas são pares de nome/valor que lhe permitem categorizar recursos e visualizar a faturação consolidada aplicando a mesma etiqueta a múltiplos recursos e grupos de recursos. Estes são opcionais e podem ser aplicados após a criação de conta de armazenamento.

#### <a name="review--create"></a>Rever + criar
O passo final para criar a conta de armazenamento é selecionar o botão **Criar** no separador **Rever + criar.** Este botão não estará disponível se todos os campos necessários para uma conta de armazenamento não estiverem preenchidos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar uma conta de armazenamento utilizando `New-AzStorageAccount` o PowerShell, usaremos o cmdlet. Este cmdlet tem muitas opções; apenas são mostradas as opções necessárias. Para saber mais sobre opções avançadas, consulte a [ `New-AzStorageAccount` documentação do cmdlet.](/powershell/module/az.storage/new-azstorageaccount)

Para simplificar a criação da conta de armazenamento e posterior partilha de ficheiros, armazenaremos vários parâmetros em variáveis. Pode substituir o conteúdo variável por quaisquer valores que deseje, no entanto note que o nome da conta de armazenamento deve ser globalmente único.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Para criar uma conta de armazenamento capaz de armazenar ações padrão de ficheiros Azure, usaremos o seguinte comando. O `-SkuName` parâmetro diz respeito ao tipo de despedimento desejado; se desejar uma conta de armazenamento georedundant ou geo-zona `-EnableLargeFileShare` redundante, também deve remover o parâmetro.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Para criar uma conta de armazenamento capaz de armazenar ações de ficheiros Premium Azure, usaremos o seguinte comando. Note que `-SkuName` o parâmetro mudou `Premium` para incluir ambos e o`LRS`nível de despedimento desejado localmente redundante ( ). O `-Kind` parâmetro `FileStorage` é `StorageV2` em vez de porque as ações de ficheiro premium devem ser criadas numa conta de armazenamento fileStorage em vez de uma conta de armazenamento GPv2.

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Para criar uma conta de armazenamento utilizando o Azure CLI, usaremos a conta de armazenamento az criar comando. Este comando tem muitas opções; apenas são mostradas as opções necessárias. Para saber mais sobre as opções avançadas, consulte a [ `az storage account create` documentação](/cli/azure/storage/account)do comando.

Para simplificar a criação da conta de armazenamento e posterior partilha de ficheiros, armazenaremos vários parâmetros em variáveis. Pode substituir o conteúdo variável por quaisquer valores que deseje, no entanto note que o nome da conta de armazenamento deve ser globalmente único.

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Para criar uma conta de armazenamento capaz de armazenar ações padrão de ficheiros Azure, usaremos o seguinte comando. O `--sku` parâmetro diz respeito ao tipo de despedimento desejado; se desejar uma conta de armazenamento georedundant ou geo-zona `--enable-large-file-share` redundante, também deve remover o parâmetro.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Para criar uma conta de armazenamento capaz de armazenar ações de ficheiros Premium Azure, usaremos o seguinte comando. Note que `--sku` o parâmetro mudou `Premium` para incluir ambos e o`LRS`nível de despedimento desejado localmente redundante ( ). O `--kind` parâmetro `FileStorage` é `StorageV2` em vez de porque as ações de ficheiro premium devem ser criadas numa conta de armazenamento fileStorage em vez de uma conta de armazenamento GPv2.

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Criar a partilha de ficheiros
Depois de ter criado a sua conta de armazenamento, tudo o que resta é criar a sua parte de ficheiro. Este processo é maioritariamente o mesmo, independentemente de estar a usar uma parte de ficheiro premium ou uma partilha de ficheiro padrão. A principal diferença é a **quota** e o que ela representa.

Para as partilhas de ficheiros padrão, é um limite superior da partilha de ficheiros Azure, para além do qual os utilizadores finais não podem ir. O principal objetivo da quota para uma quota-parte de ficheiro padrão é orçamental: "Não quero que esta parte do ficheiro cresça para além deste ponto". Se uma quota não for especificada, a quota-padrão de ficheiro pode abranger até 100 TiB (ou 5 TiB se a propriedade de ações de ficheiros grandes não estiver definida para uma conta de armazenamento).

No caso das ações de ficheiros premium, a quota está sobrecarregada para a **dimensão média prevista**. O tamanho previsto é o valor que você será cobrado, independentemente do uso real. Ao fornecer uma quota de ficheiro premium, pretende considerar dois fatores: 1) o crescimento futuro da parte a partir de uma perspetiva de utilização do espaço e 2) os IOPS necessários para a sua carga de trabalho. Todos os GiB provisionados lhe dá direito a IOPS reservados e rebentados adicionais. Para obter mais informações sobre como planear uma participação de ficheiro premium, consulte o fornecimento de ações de [ficheiros premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Se acabou de criar a sua conta de armazenamento, pode navegar para ela a partir do ecrã de implementação selecionando **Ir para recurso**. Se já criou a conta de armazenamento, pode navegar através do grupo de recursos que a contém. Uma vez na conta de armazenamento, selecione as **partilhas** de ficheiros com etiqueta de azulejo (também pode navegar para **as ações do Arquivo** através da tabela de conteúdos para a conta de armazenamento).

![Uma imagem do Arquivo partilha azulejo](media/storage-how-to-create-file-share/create-file-share-1.png)

Na listagem de ações de ficheiros, deve ver quaisquer ações de ficheiro que tenha criado anteriormente nesta conta de armazenamento; uma mesa vazia se nenhuma parte de arquivo foi criada ainda. Selecione **+ Partilha de ficheiros** para criar uma nova partilha de ficheiros.

A nova lâmina de partilha de ficheiros deve aparecer no ecrã. Complete os campos na nova lâmina de partilha de ficheiros para criar uma partilha de ficheiros:

- **Nome**: o nome da parte do ficheiro a criar.
- **Quota**: A quota da parte de ficheiro para as ações de ficheiros-tipo; a dimensão prevista da parte do ficheiro para ações de ficheiros premium.

Selecione **Criar** para terminar criando a nova parte. Note que se a sua conta de armazenamento estiver numa rede virtual, não será capaz de criar com sucesso uma partilha de ficheiros Azure, a menos que o seu cliente também esteja na rede virtual. Também pode contornar esta limitação pontual utilizando o cmdlet Azure PowerShell. `New-AzRmStorageShare`

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pode criar a partilha de [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) ficheiros Azure com o cmdlet. Os seguintes comandos PowerShell assumem `$resourceGroupName` `$storageAccountName` que definiu as variáveis e, tal como definido acima na criação de uma conta de armazenamento com a secção Azure PowerShell. 

> [!Important]  
> Para ações de `-QuotaGiB` ficheiropremium, o parâmetro refere-se à dimensão prevista da parte do ficheiro. O tamanho previsto da parte do ficheiro é o valor que você será cobrado, independentemente da utilização. As ações de ficheiros padrão são faturadas com base no uso e não no tamanho provisionado.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre a nomeação de partilhas e ficheiros de ficheiros, consulte [nomear e referenciar ações, diretórios, ficheiros e metadados.](https://msdn.microsoft.com/library/azure/dn167011.aspx)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Antes de podermos criar uma partilha de ficheiros Azure com o Azure CLI, tem de obter uma chave de conta de armazenamento para autorizar a operação de criação de partilha de ficheiros. Isto pode ser [`az storage account keys list`](/cli/azure/storage/account/keys) feito com o comando:

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

Assim que tiver a chave da conta de armazenamento, [`az storage share create`](/cli/azure/storage/share) pode criar a partilha de ficheiros Azure com o comando. 

> [!Important]  
> Para ações de `--quota` ficheiropremium, o parâmetro refere-se à dimensão prevista da parte do ficheiro. O tamanho previsto da parte do ficheiro é o valor que você será cobrado, independentemente da utilização. As ações de ficheiros padrão são faturadas com base no uso e não no tamanho provisionado.

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

Este comando falhará se a conta de armazenamento estiver contida numa rede virtual e o computador de onde está a invocar este comando não faz parte da rede virtual. Pode contornar esta limitação pontual utilizando o cmdlet `New-AzRmStorageShare` Azure PowerShell como descrito acima, ou executando o Azure CLI a partir de um computador que faz parte da rede virtual, incluindo através de uma ligação VPN.

---

> [!Note]  
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre a nomeação de partilhas e ficheiros de ficheiros, consulte [nomear e referenciar ações, diretórios, ficheiros e metadados.](https://msdn.microsoft.com/library/azure/dn167011.aspx)

## <a name="next-steps"></a>Passos seguintes
- [Plano para a implantação de Ficheiros Azure](storage-files-planning.md) ou [Plano para uma implementação do Azure File Sync](storage-sync-files-planning.md). 
- [Visão geral de networking.](storage-files-networking-overview.md)
- Conecte e monte uma partilha de ficheiros no [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)e [Linux](storage-how-to-use-files-linux.md).