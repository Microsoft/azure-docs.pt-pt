---
title: Realizar operações de armazenamento de mesa azure com a PowerShell Microsoft Docs
description: Aprenda a executar tarefas comuns como criar, consultar, eliminar dados da conta de armazenamento da Tabela Azure utilizando o PowerShell.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e643a7ce5ccf4aa5107df1e505d90a0767517350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070416"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Realizar operações de armazenamento de mesa Azure com a Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

O armazenamento de mesas Azure é uma loja de dados NoSQL que pode usar para armazenar e consultar enormes conjuntos de dados estruturados e não relacionais. Os principais componentes do serviço são as tabelas, entidades e propriedades. Uma mesa é uma coleção de entidades. Uma entidade é um conjunto de propriedades. Cada entidade pode ter até 252 propriedades, que são todos pares de valor-nome. Este artigo pressupõe que já está familiarizado com os conceitos do Serviço de Armazenamento de MesaS Azure. Para obter informações detalhadas, consulte [compreender o modelo de dados do serviço de tabela](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) e começar com o armazenamento da tabela [Azure usando .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

Este artigo abrange operações comuns de armazenamento da tabela Azure. Saiba como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Recuperar uma mesa
> * Adicionar entidades de tabela
> * Consulta de uma mesa
> * Excluir entidades de tabelas
> * Eliminar uma tabela

Este artigo mostra-lhe como criar uma nova conta de Armazenamento Azure num novo grupo de recursos para que possa removê-la facilmente quando terminar. Se preferir usar uma conta de Armazenamento existente, pode fazê-lo em vez disso.

Os exemplos requerem módulos Az PowerShell `Az.Storage (1.1.0 or greater)` e `Az.Resources (1.2.0 or greater)` . Numa janela PowerShell, corra `Get-Module -ListAvailable Az*` para encontrar a versão. Se nada for apresentado ou precisar de atualizar, consulte [o módulo Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> A utilização desta função Azure da PowerShell requer que tenha o `Az` módulo instalado. A versão atual `AzTable` de não é compatível com o módulo AzureRM mais antigo.
> Siga as [instruções de instalação mais recentes para instalar o módulo Az,](/powershell/azure/install-az-ps) se necessário.

Depois de o Azure PowerShell ser instalado ou atualizado, tem de instalar o módulo **AzTable,** que tem os comandos para gerir as entidades. Para instalar este módulo, executar o PowerShell como administrador e utilizar o comando **Do Módulo de Instalação.**

> [!IMPORTANT]
> Por razões de compatibilidade do nome do módulo, ainda estamos a publicar este mesmo módulo com o nome antigo `AzureRmStorageTables` na PowerShell Gallery. Este documento apenas referenciará o novo nome.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Add-AzAccount` e siga as instruções no ecrã.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperar lista de locais

Se não souber qual a localização que quer utilizar, pode listar as localizações disponíveis. Depois de a lista ser apresentada, localize a que quer utilizar. Estes exemplos usam **eastus.** Guarde este valor na **localização** variável para utilização futura.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Criar um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) 

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Guarde o nome do grupo de recursos numa variável para utilização futura. Neste exemplo, um grupo de recursos chamado *pshtablesrg* é criado na região *leste.*

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Criar conta de armazenamento

Crie uma conta de armazenamento de uso geral padrão com armazenamento localmente redundante (LRS) utilizando [o New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Certifique-se de especificar um nome único da conta de armazenamento. Em seguida, obtenha o contexto que representa a conta de armazenamento. Ao agir numa conta de armazenamento, pode referenciar o contexto em vez de fornecer repetidamente as suas credenciais.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Criar uma nova tabela

Para criar uma mesa, utilize o [cmdlet New-AzStorageTable.](/powershell/module/az.storage/New-AzStorageTable) Neste exemplo, a tabela chama-se `pshtesttable` .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Recupere uma lista de tabelas na conta de armazenamento

Recupere uma lista de tabelas na conta de armazenamento utilizando o [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Recupere uma referência a uma tabela específica

Para realizar operações numa mesa, precisa de uma referência à tabela específica. Obtenha uma referência utilizando [o Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Propriedade CloudTable de referência de uma tabela específica

> [!IMPORTANT]
> O uso do CloudTable é obrigatório quando se trabalha com o módulo **AzTable** PowerShell. Ligue para o comando **Get-AzStorageTable** para obter a referência a este objeto. Este comando também cria a tabela se já não existir.

Para realizar operações numa mesa utilizando **o AzTable,** precisa de uma referência à propriedade CloudTable de uma tabela específica.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Eliminar uma tabela

Para eliminar uma tabela, utilize [remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Este cmdlet remove a tabela, incluindo todos os seus dados.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se criou um novo grupo de recursos e uma conta de armazenamento no início deste como fazer, pode remover todos os ativos que criou neste exercício removendo o grupo de recursos. Este comando elimina todos os recursos contidos no grupo, bem como o próprio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, você aprendeu sobre operações comuns de armazenamento da Tabela Azure com a PowerShell, incluindo como: 

> [!div class="checklist"]
> * Criar uma tabela
> * Recuperar uma mesa
> * Adicionar entidades de tabela
> * Consulta de uma mesa
> * Excluir entidades de tabelas
> * Eliminar uma tabela

Para mais informações, consulte os seguintes artigos

* [Cmdlets do Armazenamento do PowerShell](/powershell/module/az.storage#storage)

* [Trabalhar com tabelas Azure da PowerShell - AzureRmStorageTable/Módulo PS AzTable v2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
