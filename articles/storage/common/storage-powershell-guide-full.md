---
title: Utilização da Azure PowerShell com armazenamento Azure [ Microsoft Docs
description: Aprenda a utilizar os cmdlets Azure PowerShell para armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d2404ee58f5f44fbe5625f267e6d1c504d0bd237
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465102"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Utilizar o Azure PowerShell com o Storage do Azure

O Azure PowerShell é utilizado para criar e gerir os recursos Azure a partir da linha de comando PowerShell ou em scripts. Para o Armazenamento Azure, estes cmdlets enquadram-se em duas categorias: o plano de controlo e o plano de dados. Os cmdlets de plano de controlo são usados para gerir a conta de armazenamento - para criar contas de armazenamento, definir propriedades, apagar contas de armazenamento, rodar as chaves de acesso, e assim por diante. Os cmdlets de plano de dados são utilizados para gerir os dados armazenados *na* conta de armazenamento. Por exemplo, carregar bolhas, criar partilhas de ficheiros e adicionar mensagens a uma fila.

Este artigo de como fazer abrange operações comuns utilizando os cmdlets de plano de gestão para gerir contas de armazenamento. Saiba como:

> [!div class="checklist"]
> * Lista de contas de armazenamento
> * Obtenha uma referência a uma conta de armazenamento existente
> * Criar uma conta do Storage
> * Definir propriedades da conta de armazenamento
> * Recuperar e regenerar as teclas de acesso
> * Proteja o acesso à sua conta de armazenamento
> * Ativar a análise de armazenamento

Este artigo fornece links para vários outros artigos da PowerShell para armazenamento, tais como como ativar e aceder ao Storage Analytics, como usar os cmdlets de avião de dados, e como aceder às nuvens independentes azure como China Cloud, German Cloud e Government A nuvem.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este exercício requer o módulo Azure PowerShell Az versão 0.7 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

Para este exercício, pode escrever os comandos numa janela regular do PowerShell, ou pode utilizar o Ambiente integrado de [Scripts (ISE)](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise) do Windows PowerShell e digitar os comandos num editor e, em seguida, testar um ou mais comandos de cada vez que passar pelos exemplos. Pode destacar as linhas que pretende executar e clicar em Executar Selecionado para executar esses comandos.

Para obter mais informações sobre contas de armazenamento, consulte [Introdução às](storage-introduction.md) contas de armazenamento e armazenamento [sobre azure](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Enumerar as contas de armazenamento na subscrição

Executar o [cmdlet Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) para recuperar a lista de contas de armazenamento na subscrição atual.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Obtenha uma referência a uma conta de armazenamento

Em seguida, precisa de uma referência a uma conta de armazenamento. Pode criar uma nova conta de armazenamento ou obter uma referência a uma conta de armazenamento existente. A secção seguinte mostra ambos os métodos.

### <a name="use-an-existing-storage-account"></a>Utilize uma conta de armazenamento existente

Para recuperar uma conta de armazenamento existente, precisa do nome do grupo de recursos e do nome da conta de armazenamento. Delineie as variáveis para esses dois campos e, em seguida, use o cmdlet [Get-AzStorageAccount.](/powershell/module/az.storage/Get-azStorageAccount)

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Agora tem $storageAccount, o que aponta para uma conta de armazenamento existente.

### <a name="create-a-storage-account"></a>Criar uma conta do Storage

O seguinte script mostra como criar uma conta de armazenamento de uso geral usando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Depois de criar a conta, recupere o seu contexto, que pode ser usado em comandos subsequentes em vez de especificar a autenticação com cada chamada.

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

O script utiliza os seguintes cmdlets PowerShell:

*   [Get-AzLocation-](/powershell/module/az.resources/get-azlocation) recupera uma lista dos locais válidos. O exemplo `eastus` usa para a localização.

*   [O New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cria um novo grupo de recursos. Um grupo de recursos é um recipiente lógico no qual os seus recursos Azure são implantados e geridos. O nosso `teststoragerg`chama-se.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cria a conta de armazenamento. O exemplo `testpshstorage`usa.

O nome SKU indica o tipo de replicação para a conta de armazenamento, como LRS (Armazenamento Localmente Redundante). Para obter mais informações sobre a replicação, consulte [a Replicação de Armazenamento De Azure](storage-redundancy.md).

> [!IMPORTANT]
> O nome da sua conta de armazenamento deve ser único dentro do Azure e deve ser minúsculo. Para nomear convenções e restrições, consulte [Contentores de Nomeação e Referência, Blobs e Metadados.](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
>

Agora tem uma nova conta de armazenamento e uma referência a ela.

## <a name="manage-the-storage-account"></a>Gerir a conta de armazenamento

Agora que tem uma referência a uma nova conta de armazenamento ou a uma conta de armazenamento existente, a seguinte secção mostra alguns dos comandos que pode utilizar para gerir a sua conta de armazenamento.

### <a name="storage-account-properties"></a>Propriedades da conta de armazenamento

Para alterar as definições para uma conta de armazenamento, utilize a [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Embora não possa alterar a localização de uma conta de armazenamento, ou o grupo de recursos em que reside, pode alterar muitas das outras propriedades. As seguintes listas algumas das propriedades que pode alterar usando powerShell.

* O **domínio personalizado** atribuído à conta de armazenamento.

* As **etiquetas atribuídas** à conta de armazenamento. As etiquetas são frequentemente usadas para categorizar recursos para efeitos de faturação.

* O **SKU** é o cenário de replicação para a conta de armazenamento, como LRS para Armazenamento Localmente Redundante. Por exemplo, pode mudar\_de LRS Standard para GRS standard\_ou RAGRS standard.\_ Note que não pode\_alterar Os ZRS\_Standard,\_Standard GZRS,\_Standard RAGZRS ou Premium LRS para outros SKUs, ou alterar outras SKUs para estes.

* O **nível de acesso** às contas de armazenamento blob. O valor para o nível de acesso está definido para **quente** ou **fresco**, e permite-lhe minimizar o seu custo selecionando o nível de acesso que se alinha com a forma como utiliza a conta de armazenamento. Para mais informações, consulte [hot, cool e archive storage tiers](../blobs/storage-blob-storage-tiers.md).

* Só permita o tráfego HTTPS.

### <a name="manage-the-access-keys"></a>Gerir as chaves de acesso

Uma conta azure storage vem com duas chaves de conta. Para recuperar as teclas, utilize [o Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Este exemplo recupera a primeira chave. Para recuperar o outro, `Value[1]` `Value[0]`use em vez de .

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Para regenerar a tecla, utilize [new-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Para regenerar a outra `key2` tecla, use `key1`como nome-chave em vez de .

Regenerar uma das suas chaves e depois recuperá-la novamente para ver o novo valor.

> [!NOTE]
> Deve realizar um planeamento cuidadoso antes de regenerar a chave para uma conta de armazenamento de produção. Regenerar uma ou ambas as teclas invalidará o acesso a qualquer aplicação utilizando a chave que foi regenerada. Para mais informações, consulte Gerir as chaves de [acesso à conta](storage-account-keys-manage.md)de armazenamento .


### <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

Para eliminar uma conta de armazenamento, utilize [a Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Ao eliminar uma conta de armazenamento, todos os ativos armazenados na conta também são eliminados. Se apagar uma conta acidentalmente, ligue imediatamente para o Suporte e abra um bilhete para restaurar a conta de armazenamento. A recuperação dos seus dados não está garantida, mas às vezes funciona. Não crie uma nova conta de armazenamento com o mesmo nome que a antiga até que o bilhete de apoio esteja resolvido.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Proteja a sua conta de armazenamento utilizando VNets e firewalls

Por padrão, todas as contas de armazenamento são acessíveis por qualquer rede que tenha acesso à internet. No entanto, pode configurar regras de rede apenas para permitir que aplicações de redes virtuais específicas acedam a uma conta de armazenamento. Para mais informações, consulte [Configure Azure Storage Firewalls e Redes Virtuais](storage-network-security.md).

O artigo mostra como gerir estas definições utilizando os seguintes cmdlets PowerShell:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remover-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Use análises de armazenamento

[A Azure Storage Analytics](storage-analytics.md) é composta por [Métricas de Análise](/rest/api/storageservices/about-storage-analytics-metrics) de Armazenamento e Registo de Armazenamento [analytics.](/rest/api/storageservices/about-storage-analytics-logging)

**Armazenamento Analytics Metrics** é usado para recolher métricas para as suas contas de armazenamento Azure que você pode usar para monitorizar a saúde de uma conta de armazenamento. As métricas podem ser ativadas para bolhas, ficheiros, tabelas e filas.

**Armazenamento Analytics Logging** acontece do lado do servidor e permite-lhe gravar detalhes tanto para pedidos bem sucedidos como falhados na sua conta de armazenamento. Estes registos permitem-lhe ver detalhes de leitura, escrita e eliminação de operações contra as suas mesas, filas e bolhas, bem como as razões dos pedidos falhados. O registo de registo saca do registo não disponível para Ficheiros Azure.

Pode configurar a monitorização utilizando o [portal Azure,](https://portal.azure.com)PowerShell ou utilizando programáticamente a biblioteca do cliente de armazenamento.

> [!NOTE]
> Pode ativar análises minúsculas utilizando o PowerShell. Esta capacidade não está disponível no portal.
>

* Para aprender a ativar e visualizar dados de Métricas de Armazenamento utilizando o PowerShell, consulte [as métricas](storage-analytics-metrics.md)de análise de armazenamento .

* Para aprender como ativar e recuperar dados de registo de armazenamento utilizando powerShell, consulte [como ativar o Registo de Armazenamento utilizando o PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) e [encontrando](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)os dados de registo de registo de registo de armazenamento .

* Para obter informações detalhadas sobre a utilização de Métricas de Armazenamento e Registo de Armazenamento para resolver problemas de armazenamento, consulte [Monitorização, Diagnóstico e Resolução](storage-monitoring-diagnosing-troubleshooting.md)de Problemas do Armazenamento Microsoft Azure .

## <a name="manage-the-data-in-the-storage-account"></a>Gerir os dados na conta de armazenamento

Agora que compreende como gerir a sua conta de armazenamento com o PowerShell, pode utilizar os seguintes artigos para aprender a aceder aos objetos de dados na conta de armazenamento.

* [Como gerir bolhas com powerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Como gerir ficheiros com o PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Como gerir as filas com a PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Execute operações de armazenamento de mesa azure com powerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

A Azure Cosmos DB Table API fornece funcionalidades premium para armazenamento de mesa, tais como distribuição global chave na mão, leituras e escritos de baixa latência, indexação secundária automática e produção dedicada.

* Para mais informações, consulte a Tabela API da [Azure Cosmos DB](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Implantações independentes em nuvem de Azure

A maioria das pessoas usa a Nuvem Pública Azure para a sua implantação global do Azure. Existem também algumas implementações independentes do Microsoft Azure por razões de soberania e assim por diante. Estas implantações independentes são referidas como "ambientes". Estes são os ambientes disponíveis:

* [Nuvem do Governo de Azure](https://azure.microsoft.com/features/gov/)
* [Nuvem Azure China 21Vianet operada pela 21Vianet na China](http://www.windowsazure.cn/)
* [Nuvem Alemã Azul](../../germany/germany-welcome.md)

Para obter informações sobre como aceder a estas nuvens e ao seu armazenamento com powerShell, consulte [managing storage nas nuvens independentes Azure usando powerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se criou um novo grupo de recursos e uma conta de armazenamento para este exercício, pode remover todos os ativos que criou removendo o grupo de recursos. Isto também elimina todos os recursos contidos no grupo. Neste caso, remove a conta de armazenamento criada e o próprio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Passos seguintes

Este artigo de como fazer abrange operações comuns utilizando os cmdlets de plano de gestão para gerir contas de armazenamento. Aprendeu a:

> [!div class="checklist"]
> * Lista de contas de armazenamento
> * Obtenha uma referência a uma conta de armazenamento existente
> * Criar uma conta do Storage
> * Definir propriedades da conta de armazenamento
> * Recuperar e regenerar as teclas de acesso
> * Proteja o acesso à sua conta de armazenamento
> * Ativar a análise de armazenamento

Este artigo também forneceu referências a vários outros artigos, tais como como gerir os objetos de dados, como permitir o Armazenamento Analytics, e como aceder às nuvens independentes do Azure, como China Cloud, German Cloud e Government Cloud. Aqui estão alguns artigos e recursos mais relacionados para referência:

* [Avião de controlo de armazenamento Azure PowerShell cmdlets](/powershell/module/az.storage/)
* [Avião de dados de armazenamento Azure PowerShell cmdlets](/powershell/module/azure.storage/)
* [Windows PowerShell Reference (Referências do Windows PowerShell)](/powershell/scripting/overview)
