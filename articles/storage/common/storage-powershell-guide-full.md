---
title: Usando o Azure PowerShell com o armazenamento do Azure | Microsoft Docs
description: Saiba como usar os cmdlets Azure PowerShell para o armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 40fb44857126c3562e01585c3131afec87f01e42
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430058"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Utilizar o Azure PowerShell com o Armazenamento do Azure

Azure PowerShell é usado para criar e gerenciar recursos do Azure na linha de comando do PowerShell ou em scripts. Para o armazenamento do Azure, esses cmdlets se enquadram em duas categorias: o plano de controle e o plano de dados. Os cmdlets do plano de controle são usados para gerenciar a conta de armazenamento – para criar contas de armazenamento, definir propriedades, excluir contas de armazenamento, girar as chaves de acesso e assim por diante. Os cmdlets do plano de dados são usados para gerenciar os dados armazenados *na* conta de armazenamento. Por exemplo, carregar BLOBs, criar compartilhamentos de arquivos e adicionar mensagens a uma fila.

Este artigo de instruções abrange operações comuns usando os cmdlets do plano de gerenciamento para gerenciar contas de armazenamento. Saiba como:

> [!div class="checklist"]
> * Listar contas de armazenamento
> * Obter uma referência a uma conta de armazenamento existente
> * Create a storage account
> * Definir propriedades da conta de armazenamento
> * Recuperar e regenerar as chaves de acesso
> * Proteger o acesso à sua conta de armazenamento
> * Habilitar Análise de Armazenamento

Este artigo fornece links para vários outros artigos do PowerShell para armazenamento, como, por exemplo, como habilitar e acessar o Análise de Armazenamento, como usar os cmdlets do plano de dados e como acessar as nuvens independentes do Azure, como a nuvem da China, a nuvem alemã e o governo Nuvem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este exercício requer o módulo Azure PowerShell AZ versão 0,7 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

Para este exercício, você pode digitar os comandos em uma janela normal do PowerShell ou pode usar o [ambiente de script integrado do Windows PowerShell (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) e digitar os comandos em um editor e, em seguida, testar um ou mais comandos de cada vez enquanto percorre o disso. Você pode realçar as linhas que deseja executar e clicar em executar selecionado para executar apenas esses comandos.

Para obter mais informações sobre contas de armazenamento, consulte [introdução ao armazenamento](storage-introduction.md) e [sobre contas de armazenamento do Azure](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Listar as contas de armazenamento na assinatura

Execute o cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) para recuperar a lista de contas de armazenamento na assinatura atual.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Obter uma referência a uma conta de armazenamento

Em seguida, você precisa de uma referência a uma conta de armazenamento. Você pode criar uma nova conta de armazenamento ou obter uma referência a uma conta de armazenamento existente. A seção a seguir mostra os dois métodos.

### <a name="use-an-existing-storage-account"></a>Usar uma conta de armazenamento existente

Para recuperar uma conta de armazenamento existente, você precisa do nome do grupo de recursos e do nome da conta de armazenamento. Defina as variáveis para esses dois campos e, em seguida, use o cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) .

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Agora você tem $storageAccount, que aponta para uma conta de armazenamento existente.

### <a name="create-a-storage-account"></a>Create a storage account

O script a seguir mostra como criar uma conta de armazenamento de uso geral usando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Depois de criar a conta, recupere seu contexto, que pode ser usado em comandos subsequentes em vez de especificar a autenticação com cada chamada.

```powershell
# Get list of locations and select one.
Get-AzLocation | select Location
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzResourceGroup -Name $resourceGroup -Location $location

# Set the name of the storage account and the SKU name.
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context.
$ctx = $storageAccount.Context
```

O script usa os seguintes cmdlets do PowerShell:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) --recupera uma lista de locais válidos. O exemplo usa `eastus` para o local.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) --cria um novo grupo de recursos. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Nossa é chamada de `teststoragerg`.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) --cria a conta de armazenamento. O exemplo usa `testpshstorage`.

O nome do SKU indica o tipo de replicação para a conta de armazenamento, como LRS (armazenamento com redundância local). Para obter mais informações sobre replicação, consulte [replicação do armazenamento do Azure](storage-redundancy.md).

> [!IMPORTANT]
> O nome da sua conta de armazenamento deve ser exclusivo no Azure e deve estar em minúsculas. Para obter as convenções de nomenclatura e restrições, consulte [nomenclatura e referência de contêineres, BLOBs e metadados](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Agora você tem uma nova conta de armazenamento e uma referência a ela.

## <a name="manage-the-storage-account"></a>Gerenciar a conta de armazenamento

Agora que você tem uma referência a uma nova conta de armazenamento ou a uma conta de armazenamento existente, a seção a seguir mostra alguns dos comandos que você pode usar para gerenciar sua conta de armazenamento.

### <a name="storage-account-properties"></a>Propriedades da conta de armazenamento

Para alterar as configurações de uma conta de armazenamento, use [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Embora não seja possível alterar o local de uma conta de armazenamento ou o grupo de recursos no qual ele reside, você pode alterar muitas das outras propriedades. A seguir, estão listadas algumas das propriedades que podem ser alteradas usando o PowerShell.

* O **domínio personalizado** atribuído à conta de armazenamento.

* As **marcas** atribuídas à conta de armazenamento. As marcas geralmente são usadas para categorizar recursos para fins de cobrança.

* A **SKU** é a configuração de replicação para a conta de armazenamento, como LRS para armazenamento com redundância local. Por exemplo, você pode alterar de @ no__t-0LRS Standard para Standard @ no__t-1GRS ou Standard @ no__t-2RAGRS. Observe que você não pode alterar o padrão @ no__t-0ZRS, Standard @ no__t-1GZRS, Standard @ no__t-2RAGZRS ou Premium @ no__t-3LRS para outras SKUs, ou então alterar outros SKUs para eles.

* A **camada de acesso** para contas de armazenamento de BLOBs. O valor da **camada de acesso é definido como frequente** ou **frio**e permite que você minimize seu custo selecionando a camada de acesso que se alinha com o modo como você usa a conta de armazenamento. Para obter mais informações, consulte [camadas de armazenamento quentes, frias e de arquivo morto](../blobs/storage-blob-storage-tiers.md).

* Permitir somente o tráfego HTTPS.

### <a name="manage-the-access-keys"></a>Gerenciar as chaves de acesso

Uma conta de armazenamento do Azure vem com duas chaves de conta. Para recuperar as chaves, use [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Este exemplo recupera a primeira chave. Para recuperar o outro, use `Value[1]` em vez de `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Para regenerar a chave, use [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Para regenerar a outra chave, use `key2` como o nome da chave em vez de `key1`.

Gere novamente uma de suas chaves e recupere-a novamente para ver o novo valor.

> [!NOTE]
> Você deve executar um planejamento cuidadoso antes de regenerar a chave para uma conta de armazenamento de produção. A regeneração de uma ou de ambas as chaves invalidará o acesso de qualquer aplicativo usando a chave que foi regenerada. Para obter mais informações, veja [Chaves de acesso](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

Para excluir uma conta de armazenamento, use [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Quando você exclui uma conta de armazenamento, todos os ativos armazenados na conta também são excluídos. Se você excluir uma conta acidentalmente, chame o suporte imediatamente e abra um tíquete para restaurar a conta de armazenamento. A recuperação de seus dados não é garantida, mas às vezes ele funciona. Não crie uma nova conta de armazenamento com o mesmo nome que a antiga até que o tíquete de suporte seja resolvido.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Proteger sua conta de armazenamento usando VNets e firewalls

Por padrão, todas as contas de armazenamento podem ser acessadas por qualquer rede que tenha acesso à Internet. No entanto, você pode configurar regras de rede para permitir que somente aplicativos de redes virtuais específicas acessem uma conta de armazenamento. Para obter mais informações, consulte [configurar redes virtuais e firewalls de armazenamento do Azure](storage-network-security.md).

O artigo mostra como gerenciar essas configurações usando os seguintes cmdlets do PowerShell:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Usar a análise de armazenamento  

[Análise de armazenamento do Azure](storage-analytics.md) consiste em [métricas de análise de armazenamento](/rest/api/storageservices/about-storage-analytics-metrics) e [log de análise de armazenamento](/rest/api/storageservices/about-storage-analytics-logging).

**Análise de armazenamento métricas** são usadas para coletar métricas para suas contas de armazenamento do Azure que você pode usar para monitorar a integridade de uma conta de armazenamento. As métricas podem ser habilitadas para BLOBs, arquivos, tabelas e filas.

**Análise de armazenamento log** ocorre no lado do servidor e permite que você registre detalhes de solicitações bem-sucedidas e com falha em sua conta de armazenamento. Esses logs permitem que você veja os detalhes das operações de leitura, gravação e exclusão em suas tabelas, filas e BLOBs, bem como os motivos para solicitações com falha. O registro em log não está disponível para os arquivos do Azure.

Você pode configurar o monitoramento usando o [portal do Azure](https://portal.azure.com), o PowerShell ou programaticamente usando a biblioteca de cliente de armazenamento.

> [!NOTE]
> Você pode habilitar a análise de minutos usando o PowerShell. Esse recurso não está disponível no Portal.
>

* Para saber como habilitar e exibir dados de métricas de armazenamento usando o PowerShell, consulte [métricas de análise de armazenamento](storage-analytics-metrics.md).

* Para saber como habilitar e recuperar dados de log de armazenamento usando o PowerShell, consulte [como habilitar o log de armazenamento usando o PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) e [localizando seus dados de log de log de armazenamento](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Para obter informações detalhadas sobre como usar as métricas de armazenamento e o log de armazenamento para solucionar problemas de armazenamento, consulte [monitoramento, diagnóstico e solução de problemas armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Gerenciar os dados na conta de armazenamento

Agora que você entende como gerenciar sua conta de armazenamento com o PowerShell, você pode usar os artigos a seguir para saber como acessar os objetos de dados na conta de armazenamento.

* [Como gerenciar BLOBs com o PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Como gerenciar arquivos com o PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Como gerenciar filas com o PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Executar operações de armazenamento de tabelas do Azure com o PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB API de Tabela fornece recursos premium para armazenamento de tabelas, como distribuição global pronta, leituras e gravações de baixa latência, indexação secundária automática e taxa de transferência dedicada.

* Para obter mais informações, consulte [Azure Cosmos DB API de tabela](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Implantações de nuvem independentes do Azure

A maioria das pessoas usa a nuvem pública do Azure para sua implantação global do Azure. Há também algumas implantações independentes de Microsoft Azure por razões da soberania e assim por diante. Essas implantações independentes são chamadas de "ambientes". Estes são os ambientes disponíveis:

* [Nuvem do Azure governamental](https://azure.microsoft.com/features/gov/)
* [Nuvem do Azure China 21Vianet operada pela 21Vianet na China](http://www.windowsazure.cn/)
* [Nuvem alemã do Azure](../../germany/germany-welcome.md)

Para obter informações sobre como acessar essas nuvens e seu armazenamento com o PowerShell, confira [Gerenciando o armazenamento nas nuvens independentes do Azure usando o PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você criou um novo grupo de recursos e uma conta de armazenamento para este exercício, o você pode remover todos os ativos que você criou removendo o grupo de recursos. Isto também elimina todos os recursos contidos no grupo. Nesse caso, ele remove a conta de armazenamento criada e o próprio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Passos seguintes

Este artigo de instruções abrange operações comuns usando os cmdlets do plano de gerenciamento para gerenciar contas de armazenamento. Aprendeu a:

> [!div class="checklist"]
> * Listar contas de armazenamento
> * Obter uma referência a uma conta de armazenamento existente
> * Create a storage account
> * Definir propriedades da conta de armazenamento
> * Recuperar e regenerar as chaves de acesso
> * Proteger o acesso à sua conta de armazenamento
> * Habilitar Análise de Armazenamento

Este artigo também forneceu referências a vários outros artigos, como como gerenciar os objetos de dados, como habilitar o Análise de Armazenamento e como acessar as nuvens independentes do Azure, como a nuvem da China, a nuvem alemã e a nuvem governamental. Aqui estão alguns artigos e recursos relacionados para referência:

* [Cmdlets do PowerShell do plano de controle de armazenamento do Azure](/powershell/module/az.storage/)
* [Cmdlets do PowerShell do plano de dados do armazenamento do Azure](/powershell/module/azure.storage/)
* [Referência do Windows PowerShell](/powershell/scripting/developer/windows-powershell)
