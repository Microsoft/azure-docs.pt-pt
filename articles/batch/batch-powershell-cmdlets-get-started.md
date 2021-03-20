---
title: Introdução ao PowerShell
description: Introdução rápida aos cmdlets do Azure PowerShell que pode utilizar para gerir os recursos do Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 2b51a2a7852df82625fb342bbbbc4a3a1cbf72a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685515"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Gerir os recursos do Batch com os cmdlets do PowerShell

Com os cmdlets Azure Batch PowerShell, pode executar e escrever muitas tarefas comuns do Lote. Isto é uma introdução rápida aos cmdlets que pode utilizar para gerir as contas do Batch e trabalhar com os recursos do Batch, como conjuntos e tarefas.

Para obter uma lista completa de cmdlets do Batch e a sintaxe detalhada dos cmdlets, veja a [Referência de cmdlets do Azure Batch](/powershell/module/az.batch).

Recomendamos que atualize frequentemente os módulos do Azure PowerShell para tirar partido das atualizações e melhoramentos do serviço.

## <a name="prerequisites"></a>Pré-requisitos

- [Instale e configure o módulo do Azure PowerShell](/powershell/azure/). Para instalar um módulo específico do Azure Batch, como um módulo de pré-lançamento, veja a [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az.Batch/).

- Executar o cmdlet **Connect-AzAccount** para ligar à sua subscrição (o navio cmdlets Azure Batch no módulo Azure Resource Manager):

  ```powershell
  Connect-AzAccount
  ```

- **Registe no espaço de nomes do fornecedor do Batch**. Apenas tem de efetuar esta operação **uma vez por subscrição**.
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Gerir contas e chaves do Batch

### <a name="create-a-batch-account"></a>Criar uma conta do Batch

**O New-AzBatchAccount** cria uma conta Batch num grupo de recursos especificado. Se ainda não tiver um grupo de recursos, crie um executando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Especifique uma das regiões do Azure no parâmetro **Localização**, por exemplo, “E.U.A. Central”. Por exemplo:

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Em seguida, crie uma conta Batch no grupo de recursos. Especifique um nome para a conta em <*account_name*> e a localização e nome do seu grupo de recursos. Criar a conta do Batch pode demorar algum tempo a concluir. Por exemplo:

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> O nome da conta do Batch tem de ser exclusivo para a região do Azure do grupo de recursos, conter entre 3 e 24 carateres e utilizar apenas letras minúsculas e números.

### <a name="get-account-access-keys"></a>Obter chaves de acesso à conta

**O Get-AzBatchAccountKeys** mostra as chaves de acesso associadas a uma conta Azure Batch. Por exemplo, execute o seguinte para obter as chaves primária e secundária da conta que criou.

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso

**O New-AzBatchAccountKey** gera uma nova chave de conta primária ou secundária para uma conta Azure Batch. Por exemplo, para gerar uma nova chave primária para a conta do Batch, escreva:

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Para gerar uma nova chave secundária, especifique "Secondary" para o parâmetro **KeyType**. Tem de regenerar as chaves primária e secundária em separado.

### <a name="delete-a-batch-account"></a>Eliminar uma conta do Batch

**Remove-AzBatchAccount** elimina uma conta Batch. Por exemplo:

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

Quando lhe for perguntado, confirme que pretende remover a conta. A remoção da conta pode demorar algum tempo a concluir.

## <a name="create-a-batchaccountcontext-object"></a>Criar um objeto BatchAccountContext

Pode autenticar para gerir os recursos do Batch com a autenticação de chave partilhada ou a autenticação do Azure Active Directory. Para autenticar com os cmdlets do PowerShell do Batch, crie primeiro um objeto BatchAccountContext para armazenar as credenciais ou a identidade da conta. O objeto BatchAccountContext é transmitido aos cmdlets que utilizam o parâmetro **BatchContext**.

### <a name="shared-key-authentication"></a>Autenticação de chave partilhada

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Por predefinição, a chave primária da conta é utilizada na autenticação, mas pode selecionar explicitamente a chave a utilizar ao alterar a propriedade **KeyInUse** do objeto BatchAccountContext: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Criar e modificar recursos do Batch

Utilize cmdlets como **New-AzBatchPool,** **New-AzBatchJob** e **New-AzBatchTask** para criar recursos sob uma conta Batch. Existem cmdlets **Get-** e **Set-** correspondentes para atualizar as propriedades dos recursos existentes e cmdlets **Remove-** para remover recursos numa conta do Batch.

Quando utiliza muitos destes cmdlets, além de transmitir um objeto de BatchContext, terá de criar ou passar objetos que contêm definições detalhadas de recursos, conforme mostrado no exemplo seguinte. Consulte a ajuda detalhada de cada cmdlet para obter exemplos adicionais.

### <a name="create-a-batch-pool"></a>Criar um conjunto do Batch

Ao criar ou atualizar uma piscina de Lote, especifique uma [configuração](nodes-and-pools.md#configurations). As piscinas devem geralmente ser configuradas com configuração de máquina virtual, que permite especificar uma das imagens suportadas do Linux ou Do Windows VM listadas no [Azure Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1), ou fornecer uma imagem personalizada que preparou. Os pools de configuração de serviços de nuvem fornecem apenas nós de computação do Windows e não suportam todas as funcionalidades do Lote.

Quando executar **New-AzBatchPool,** passe as definições do sistema operativo num objeto PSVirtualMachineConfiguration ou PSCloudServiceConfiguration. Por exemplo, o seguinte corte cria uma piscina de Lote com tamanho Standard_A1 nós computacional na configuração da máquina virtual, imagem com Ubuntu Server 18.04-LTS. Neste caso, o parâmetro **VirtualMachineConfiguration** especifica a variável *$configuration* como o objeto PSVirtualMachineConfiguration. O parâmetro **BatchContext** especifica uma variável *$context* definida anteriormente como objeto BatchAccountContext.

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

O número-alvo de nós computacional no novo pool é calculado por uma fórmula de autoscalagem. Neste caso, a fórmula é simplesmente **$TargetDedicated=4**, que indica que o número de nós de computação no conjunto é, no máximo, 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Consulta para conjuntos, tarefas e outros detalhes

Utilize cmdlets como **Get-AzBatchPool,** **Get-AzBatchJob** e **Get-AzBatchTask** para consultar entidades criadas sob uma conta Batch.

### <a name="query-for-data"></a>Consulta de dados

Como exemplo, use **o Get-AzBatchPools** para encontrar as suas piscinas. Por predefinição, isto consulta todos os agrupamentos na sua conta, assumindo que já armazenou o objeto BatchAccountContext em *$context*:

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Utilizar um filtro OData

Pode fornecer um filtro OData utilizando o parâmetro **Filter** para localizar apenas os objetos que lhe interessam. Por exemplo, você pode encontrar todas as piscinas com IDs começando com "myPool":

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Este método não é tão flexível como se utilizasse "Where-Object" num pipeline local. No entanto, a consulta é enviada diretamente para o serviço Batch para que todas as filtragens ocorram no lado do servidor, poupando a largura de banda da Internet.

### <a name="use-the-id-parameter"></a>Utilizar o parâmetro Id

Uma alternativa a um filtro OData é a utilização do parâmetro **Id**. Para consultar um agrupamento específico com o id "myPool":

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

O parâmetro **ID** suporta apenas a pesquisa completa de ID; não wildcards ou filtros estilo OData.

### <a name="use-the-maxcount-parameter"></a>Utilizar o parâmetro MaxCount

Por predefinição, cada cmdlet devolve um máximo de 1000 objetos. Se atingir este limite, refine o filtro para obter menos objetos ou defina explicitamente um limite máximo utilizando o parâmetro **MaxCount**. Por exemplo:

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

### <a name="use-the-powershell-pipeline"></a>Utilizar o pipeline do PowerShell

Os cmdlets do lote utilizam o gasoduto PowerShell para enviar dados entre cmdlets. Este procedimento tem o mesmo efeito que especificar um parâmetro, mas facilita o trabalho com várias entidades.

Por exemplo, localizar e apresentar todas as tarefas na sua conta:

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Reinicie todos os nós de computação num conjunto:

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Gestão de pacotes de aplicações

[Os pacotes](batch-application-packages.md) de aplicações fornecem uma forma simplificada de implementar aplicações nos nós de computação nas suas piscinas. Com os cmdlets do PowerShell do Batch, pode carregar e gerir pacotes de aplicações na sua conta do Batch e implementar versões de pacotes em nós de computação.

> [!IMPORTANT]
> Tem de associar uma conta de armazenamento do Azure à sua conta do Batch para utilizar pacotes de aplicações.

**Criar** uma aplicação:

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**Adicionar** um pacote de aplicações:

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Definir a **versão predefinida** da aplicação:

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Listar** os pacotes de uma aplicação

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**Eliminar** um pacote de aplicação

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**Eliminar** uma aplicação

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Tem de eliminar todas as versões dos pacotes de aplicações de uma aplicação antes de eliminá-la. Se tentar eliminar uma aplicação que possua atualmente pacotes de aplicações, receberá um erro de “Conflito”.

### <a name="deploy-an-application-package"></a>Implementar um pacote de aplicações

Quando cria um conjunto, pode especificar um ou mais pacotes de aplicações para implementação. Quando especifica um pacote durante a criação de conjuntos, é implementado em cada nó no momento em que o nó é associado ao conjunto. Os pacotes também são implementados quando um nó é reiniciado ou recriado.

Especifique a opção `-ApplicationPackageReference` quando criar um conjunto para implementar pacotes de aplicações nos nós do conjunto à medida que são adicionados ao mesmo. Em primeiro lugar, crie um objeto **PSApplicationPackageReference** e configuure-o com o ID da aplicação e a versão do pacote que pretende implementar nos nos nóns de computação da piscina:

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Em seguida, crie o conjunto e especifique o objeto de referência do pacote como argumento para a opção `ApplicationPackageReferences`:

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -VirtualMachineConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Pode encontrar mais informações sobre pacotes de aplicações em [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).

### <a name="update-a-pools-application-packages"></a>Atualizar os pacotes de aplicações de um conjunto

Para atualizar as aplicações atribuídas a um pool existente, primeiro crie um objeto PSApplicationPackageReference com as propriedades desejadas (ID de aplicação e versão pacote):

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Em seguida, obtenha a piscina do Batch, limpe quaisquer pacotes existentes, adicione a nova referência do pacote e atualize o serviço Batch com as novas definições de piscina:

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Atualizou as propriedades do conjunto no serviço Batch. Contudo, para implementar realmente o pacote de aplicação novo nos nós de computação no conjunto, tem de reiniciar ou recriar as imagens destes nós. Pode reiniciar todos os nós num conjunto com este comando:

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> Pode implementar vários pacotes de aplicações nos nós de computação num conjunto. Se pretender adicionar um pacote de aplicação ao invés de substituir os pacotes implementados atualmente, omita a linha `$pool.ApplicationPackageReferences.Clear()` acima.

## <a name="next-steps"></a>Passos seguintes

- Reveja a [referência cmdlet do Azure Batch](/powershell/module/az.batch) para obter sintaxe e exemplos detalhados do cmdlet.
- Saiba como [implementar aplicações para calcular nós com pacotes de aplicações batch](batch-application-packages.md).
