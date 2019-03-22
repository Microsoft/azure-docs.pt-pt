---
title: Utilizar o Azure PowerShell com o armazenamento do Azure | Documentos da Microsoft
description: Saiba como utilizar os cmdlets do PowerShell do Azure para armazenamento do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 78eeb986be5380d9e34a42872cbbdeee37654cc0
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317024"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Utilizar o Azure PowerShell com o Storage do Azure

O Azure PowerShell é utilizado para criar e gerir recursos do Azure da linha de comando do PowerShell ou em scripts. Armazenamento do Azure, estes cmdlets abrangem duas categorias, o plano de controlo e o plano de dados. Os cmdlets de plano de controle são utilizados para gerir a conta de armazenamento- - para criar contas de armazenamento, definir as propriedades, eliminar as contas de armazenamento, rodar as chaves de acesso e assim por diante. Os cmdlets do plano de dados são utilizados para gerir os dados armazenados *em* a conta de armazenamento. Por exemplo, carregar blobs, criar partilhas de ficheiros e adicionar mensagens a uma fila.

Este artigo que mostra como abrange operações comuns utilizando os cmdlets do plano de gestão para gerir contas de armazenamento. Saiba como:

> [!div class="checklist"]
> * Lista as contas de armazenamento
> * Obter uma referência a uma conta de armazenamento existente
> * Criar uma conta de armazenamento
> * Definir as propriedades de conta de armazenamento
> * Obter e voltar a gerar as chaves de acesso
> * Proteger o acesso à sua conta de armazenamento
> * Ativar a análise de armazenamento

Este artigo fornece ligações para vários outros artigos sobre o PowerShell para o armazenamento, por exemplo, como ativar e a análise de armazenamento de acesso, como utilizar os cmdlets do plano de dados e como aceder as clouds independentes do Azure, como a cloud na China, nuvem da Alemanha e Governo Na cloud.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Neste exercício requer o módulo Azure PowerShell Az versão 0,7 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

Para este exercício, pode digitar os comandos numa janela do PowerShell regular, ou pode utilizar o [Windows PowerShell Integrated Scripting Environment (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) e escreva os comandos num editor, em seguida, testar um ou mais comandos num momento como percorrer os exemplos. É possível realçar as linhas que pretende executar e clique em executar seleção para apenas executar esses comandos.

Para obter mais informações sobre contas de armazenamento, consulte [introdução ao armazenamento](storage-introduction.md) e [sobre as contas de armazenamento](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Listar as contas de armazenamento na subscrição

Executar o [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) cmdlet para obter a lista de contas de armazenamento na subscrição atual.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Obter uma referência a uma conta de armazenamento

Em seguida, terá uma referência a uma conta de armazenamento. Pode criar uma nova conta de armazenamento ou obter uma referência a uma conta de armazenamento existente. A seção a seguir mostra os dois métodos.

### <a name="use-an-existing-storage-account"></a>Utilizar uma conta de armazenamento existente

Para obter uma conta de armazenamento existente, tem o nome do grupo de recursos e o nome da conta de armazenamento. Defina as variáveis para esses dois campos, em seguida, utilize o [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) cmdlet.

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Agora tem $storageAccount, que aponta para uma conta de armazenamento existente.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O script seguinte mostra como criar uma conta de armazenamento para fins gerais com [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Depois de criar a conta, obter seu contexto, que pode ser utilizado nos comandos subsequentes em vez de especificar a autenticação com cada chamada.

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

O script utiliza os seguintes cmdlets do PowerShell:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) – recupera uma lista das localizações válidas. O exemplo utiliza `eastus` para localização.

*   [Novo AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) – cria um novo grupo de recursos. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. As nossas é chamada `teststoragerg`.

*   [Novo AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) – cria a conta de armazenamento. O exemplo utiliza `testpshstorage`.

O nome do SKU indica o tipo de replicação para a conta de armazenamento, como o LRS (armazenamento localmente redundante). Para obter mais informações sobre a replicação, consulte [replicação de armazenamento do Azure](storage-redundancy.md).

> [!IMPORTANT]
> O nome da conta de armazenamento tem de ser exclusivo no Azure e tem de estar em minúsculo. Para as convenções de nomenclatura e restrições, consulte [nomenclatura e referenciação de contentores, Blobs e metadados](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Agora tem uma nova conta de armazenamento e uma referência a ele.

## <a name="manage-the-storage-account"></a>Gerir a conta de armazenamento

Agora que tem uma referência a uma nova conta de armazenamento ou uma conta de armazenamento existente, a seção a seguir mostra alguns dos comandos que pode utilizar para gerir a sua conta de armazenamento.

### <a name="storage-account-properties"></a>Propriedades da conta de armazenamento

Para alterar as definições para uma conta de armazenamento, utilize [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Embora não é possível alterar a localização de uma conta de armazenamento ou o grupo de recursos onde esta reside, pode alterar muitas das outras propriedades. Segue-se algumas das propriedades que pode alterar com o PowerShell.

* O **domínio personalizado** atribuído à conta de armazenamento.

* O **etiquetas** atribuído à conta de armazenamento. As etiquetas são, muitas vezes, utilizadas para categorizar recursos para efeitos de faturação.

* O **SKU** é a definição de replicação para a conta de armazenamento, como o LRS para o armazenamento localmente redundante. Por exemplo, pode passar de Standard\_LRS Standard\_GRS ou Standard\_RAGRS. Tenha em atenção que não é possível alterar o padrão\_ZRS ou Premium\_LRS para outras SKUs, ou alterar outras SKUs para cada uma delas.

* O **camada de acesso** para contas de armazenamento de Blobs. O valor para o escalão de acesso é definido como **frequente** ou **esporádico**, e permite-lhe minimizar o custo ao selecionar a camada de acesso que se alinha com como utilizar a conta de armazenamento. Para obter mais informações, consulte [frequente, esporádico e de camadas de armazenamento de arquivo](../blobs/storage-blob-storage-tiers.md).

* Permitir apenas tráfego HTTPS.

### <a name="manage-the-access-keys"></a>Gerir as chaves de acesso

Uma conta de armazenamento do Azure inclui duas chaves de conta. Para obter as chaves, utilize [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Este exemplo obtém a primeira chave. Para obter outros aquele, utilize `Value[1]` em vez de `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Para voltar a gerar a chave, utilize [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Para voltar a gerar a outra chave, utilize `key2` como o nome da chave em vez de `key1`.

Regenerar uma das suas chaves e, em seguida, recuperá-lo novamente para ver o novo valor.

> [!NOTE]
> Deve executar um planejamento cuidadoso antes de voltar a gerar a chave para uma conta de armazenamento de produção. Regenerar chaves de um ou ambos os irá invalidar o acesso a qualquer aplicação que utilize a chave que foi regenerada. Para obter mais informações, veja [Chaves de acesso](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

Para eliminar uma conta de armazenamento, utilize [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Quando elimina uma conta de armazenamento, todos os ativos armazenados na conta são eliminados também. Se eliminar acidentalmente uma conta, contacte o suporte imediatamente e abrir um pedido para restaurar a conta de armazenamento. Não é garantida a recuperação dos seus dados, mas, às vezes, funciona. Não crie uma nova conta de armazenamento com o mesmo nome que o antigo até que o pedido de suporte foi resolvido.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Proteger a sua conta de armazenamento através de VNets e firewalls

Por predefinição, todas as contas de armazenamento estão acessíveis por qualquer rede que tenha acesso à internet. No entanto, pode configurar regras de rede para permitir apenas aplicações de redes virtuais específicas aceder a uma conta de armazenamento. Para obter mais informações, consulte [configurar Firewalls de armazenamento do Azure e redes virtuais](storage-network-security.md).

O artigo mostra como gerenciar essas configurações usando os seguintes cmdlets do PowerShell:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Utilizar a análise de armazenamento  

[Análise de armazenamento do Azure](storage-analytics.md) consiste [Storage Analytics Metrics](/rest/api/storageservices/about-storage-analytics-metrics) e [registo de análise de armazenamento](/rest/api/storageservices/about-storage-analytics-logging).

**Métricas da análise de armazenamento** é utilizada para recolher métricas para as suas contas de armazenamento do Azure que pode utilizar para monitorizar o estado de funcionamento de uma conta de armazenamento. Métricas podem ser ativadas para blobs, ficheiros, tabelas e filas.

**O registo de análise de armazenamento** acontece no servidor e permite-lhe registar detalhes de pedidos com êxito ou falhados à sua conta de armazenamento. Estes registos permitem-lhe ver os detalhes de ler, escrever e eliminar operações de acordo com as suas tabelas, filas e blobs, bem como os motivos dos pedidos com falhas. O registo não está disponível para ficheiros do Azure.

Pode configurar a monitorização utilizando o [portal do Azure](https://portal.azure.com), PowerShell, ou programaticamente usando a biblioteca de cliente de armazenamento.

> [!NOTE]
> Pode ativar a análise de minuto com o PowerShell. Esta capacidade não está disponível no portal.
>

* Para saber como ativar e ver os dados de métricas de armazenamento com o PowerShell, veja [métricas da análise de armazenamento](storage-analytics-metrics.md).

* Para saber como ativar e recuperar dados do registo de armazenamento com o PowerShell, veja [como ativar o registo de armazenamento com o PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) e [localizar os seus dados de registo do registo de armazenamento](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Para obter informações detalhadas sobre como utilizar as métricas de armazenamento e o registo de armazenamento para resolver problemas de armazenamento, consulte [monitorização e resolução de problemas do armazenamento do Microsoft Azure diagnosticando](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Gerir os dados na conta de armazenamento

Agora que compreende como gerir a sua conta de armazenamento com o PowerShell, pode utilizar os seguintes artigos para saber como acessar os objetos de dados na conta de armazenamento.

* [Como gerir blobs com o PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Como gerir ficheiros com o PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Como gerir as filas com o PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Efetuar operações de armazenamento de tabelas do Azure com o PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

API de tabela do Azure Cosmos DB fornece as funcionalidades premium para armazenamento de tabelas, tais como distribuição global chave na mão, leituras de latência baixa e escritas, a indexação secundária automática e débito dedicado.

* Para obter mais informações, consulte [API de tabela do Azure Cosmos DB](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Implementações independentes de cloud do Azure

A maioria das pessoas utilizar a Cloud pública do Azure para a sua implementação do Azure global. Existem também algumas implementações independentes do Microsoft Azure por motivos de soberania de dados e assim por diante. Estas implementações independentes são referidas como "ambientes". Estes são os ambientes disponíveis:

* [O Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [China 21Vianet na Cloud do Azure operada pela 21Vianet na China](http://www.windowsazure.cn/)
* [O Azure Cloud da Alemanha](../../germany/germany-welcome.md)

Para obter informações sobre como aceder a estas clouds e o respetivo armazenamento com o PowerShell, veja [gerir o armazenamento em clouds independentes do Azure, com o PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver criado um novo grupo de recursos e uma conta de armazenamento para este exercício, pode remover yous todos os ativos que criou ao remover o grupo de recursos. Isto também elimina todos os recursos contidos no grupo. Neste caso, remove a conta de armazenamento que criou e o grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Passos Seguintes

Este artigo que mostra como abrange operações comuns utilizando os cmdlets do plano de gestão para gerir contas de armazenamento. Aprendeu a:

> [!div class="checklist"]
> * Lista as contas de armazenamento
> * Obter uma referência a uma conta de armazenamento existente
> * Criar uma conta de armazenamento
> * Definir as propriedades de conta de armazenamento
> * Obter e voltar a gerar as chaves de acesso
> * Proteger o acesso à sua conta de armazenamento
> * Ativar a análise de armazenamento

Este artigo também fornecido referências a outros artigos, como como gerir os objetos de dados, como ativar a análise de armazenamento e como aceder as clouds independentes do Azure, como a cloud na China, nuvem da Alemanha e na Cloud do Governo. Aqui estão alguns artigos e recursos para referência mais relacionados:

* [Cmdlets do PowerShell de plano de controlo do Azure Storage](/powershell/module/az.storage/)
* [Cmdlets do PowerShell do plano de dados de armazenamento do Azure](/powershell/module/azure.storage/)
* [Referência do Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)
