---
title: Introdução ao PowerShell – Azure Batch | Documentos da Microsoft
description: Introdução rápida aos cmdlets do Azure PowerShell que pode utilizar para gerir os recursos do Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 01/15/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 10d8683724622f164299016a801e1960e0a868c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770055"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Gerir os recursos do Batch com os cmdlets do PowerShell

Com os cmdlets do Azure Batch PowerShell, pode efetuar e encriptar muitas das tarefas que desempenha com as APIs do Batch, o portal do Azure e a Interface de Linha de Comandos do Azure (CLI). Isto é uma introdução rápida aos cmdlets que pode utilizar para gerir as contas do Batch e trabalhar com os recursos do Batch, como conjuntos e tarefas.

Para obter uma lista completa de cmdlets do Batch e a sintaxe detalhada dos cmdlets, veja a [Referência de cmdlets do Azure Batch](/powershell/module/az.batch).

Este artigo baseia-se nos cmdlets do módulo de Az Batch 1.0.0. Recomendamos que atualize frequentemente os módulos do Azure PowerShell para tirar partido das atualizações e melhoramentos do serviço.

## <a name="prerequisites"></a>Pré-requisitos

* [Instale e configure o módulo do Azure PowerShell](/powershell/azure/overview). Para instalar um módulo específico do Azure Batch, como um módulo de pré-lançamento, veja a [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az.Batch/1.0.0).

* Executar o **Connect-AzAccount** cmdlet para ligar à sua subscrição (o Azure Batch cmdlets enviados no módulo do Azure Resource Manager):

  ```PowerShell
  Connect-AzAccount
  ```

* **Registe no espaço de nomes do fornecedor do Batch**. Apenas tem de efetuar esta operação **uma vez por subscrição**.
  
  ```PowerShell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Gerir contas e chaves do Batch

### <a name="create-a-batch-account"></a>Criar uma conta do Batch

**Novo AzBatchAccount** cria uma conta do Batch num grupo de recursos especificado. Se ainda não tiver um grupo de recursos, crie um ao executar o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet. Especifique uma das regiões do Azure no parâmetro **Localização**, por exemplo, “EUA Central”. Por exemplo:

```PowerShell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Em seguida, crie uma conta do Batch no grupo de recursos. Especifique um nome para a conta no <*nome_conta*> e a localização e o nome do seu grupo de recursos. Criar a conta do Batch pode demorar algum tempo a concluir. Por exemplo:

```PowerShell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> O nome da conta do Batch tem de ser exclusivo para a região do Azure do grupo de recursos, conter entre 3 e 24 carateres e utilizar apenas letras minúsculas e números.

### <a name="get-account-access-keys"></a>Obter chaves de acesso à conta

**Get-AzBatchAccountKeys** mostra as chaves de acesso associadas uma conta do Azure Batch. Por exemplo, execute o seguinte para obter as chaves primária e secundária da conta que criou.

 ```PowerShell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso

**Novo AzBatchAccountKey** gera uma nova chave de conta primária ou secundária para uma conta do Azure Batch. Por exemplo, para gerar uma nova chave primária para a conta do Batch, escreva:

```PowerShell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Para gerar uma nova chave secundária, especifique "Secondary" para o parâmetro **KeyType**. Tem de regenerar as chaves primária e secundária em separado.

### <a name="delete-a-batch-account"></a>Eliminar uma conta do Batch

**Remove-AzBatchAccount** elimina uma conta do Batch. Por exemplo:

```PowerShell
Remove-AzBatchAccount -AccountName <account_name>
```

Quando lhe for perguntado, confirme que pretende remover a conta. A remoção da conta pode demorar algum tempo a concluir.

## <a name="create-a-batchaccountcontext-object"></a>Criar um objeto BatchAccountContext

Pode autenticar para gerir os recursos do Batch com a autenticação de chave partilhada ou a autenticação do Azure Active Directory. Para autenticar com os cmdlets do PowerShell do Batch, crie primeiro um objeto BatchAccountContext para armazenar as credenciais ou a identidade da conta. O objeto BatchAccountContext é transmitido aos cmdlets que utilizam o parâmetro **BatchContext**.

### <a name="shared-key-authentication"></a>Autenticação de chave partilhada

```PowerShell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Por predefinição, a chave primária da conta é utilizada na autenticação, mas pode selecionar explicitamente a chave a utilizar ao alterar a propriedade **KeyInUse** do objeto BatchAccountContext: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

```PowerShell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Criar e modificar recursos do Batch

Utilize cmdlets, como **New-AzBatchPool**, **New-AzBatchJob**, e **New-AzBatchTask** para criar recursos na conta do Batch. Existem cmdlets **Get-** e **Set-** correspondentes para atualizar as propriedades dos recursos existentes e cmdlets **Remove-** para remover recursos numa conta do Batch.

Quando utiliza muitos destes cmdlets, além de transmitir um objeto de BatchContext, terá de criar ou passar objetos que contêm definições detalhadas de recursos, conforme mostrado no exemplo seguinte. Consulte a ajuda detalhada de cada cmdlet para obter exemplos adicionais.

### <a name="create-a-batch-pool"></a>Criar um conjunto do Batch

Quando criar ou atualizar um conjunto do Batch, selecione a configuração de serviços cloud ou a configuração de máquina virtual para o sistema operativo nos nós de computação (veja [Descrição geral da funcionalidade do Batch](batch-api-basics.md#pool)). Se especificar a configuração de serviços cloud, a imagem dos nós de computação é feita com uma das [versões de SO Convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Se optar pela configuração de máquina virtual, pode especificar uma das imagens de VM do Linux ou Windows suportadas que estão disponíveis no [Markteplace das Máquinas Virtuais do Azure][vm_marketplace] ou fornecer uma imagem personalizada que tenha preparado.

Quando executa **New-AzBatchPool**, passar as definições de sistema operativo num objeto PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Por exemplo, o fragmento seguinte cria um lote em conjunto com o tamanho Standard_A1 nós de computação em configuração da máquina virtual, instalados com 18.04-LTS do Ubuntu Server. Neste caso, o parâmetro **VirtualMachineConfiguration** especifica a variável *$configuration* como o objeto PSVirtualMachineConfiguration. O parâmetro **BatchContext** especifica uma variável *$context* definida anteriormente como objeto BatchAccountContext.

```PowerShell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04.0-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

O número de destino de nós de computação do novo conjunto é calculado por uma fórmula de dimensionamento automático. Neste caso, a fórmula é simplesmente **$TargetDedicated=4**, que indica que o número de nós de computação no conjunto é, no máximo, 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Consulta para conjuntos, tarefas e outros detalhes

Utilize cmdlets, como **Get-AzBatchPool**, **Get-AzBatchJob**, e **Get-AzBatchTask** para consultar entidades criadas sob uma conta do Batch.

### <a name="query-for-data"></a>Consulta de dados

Por exemplo, utilize **Get-AzBatchPools** para localizar os seus agrupamentos. Por predefinição, isto consulta todos os agrupamentos na sua conta, assumindo que já armazenou o objeto BatchAccountContext em *$context*:

```PowerShell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Utilizar um filtro OData

Pode fornecer um filtro OData utilizando o parâmetro **Filter** para localizar apenas os objetos que lhe interessam. Por exemplo, pode encontrar todos os agrupamentos com IDs começados por "myPool":

```PowerShell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Este método não é tão flexível como se utilizasse "Where-Object" num pipeline local. No entanto, a consulta é enviada diretamente para o serviço Batch para que todas as filtragens ocorram no lado do servidor, poupando a largura de banda da Internet.

### <a name="use-the-id-parameter"></a>Utilizar o parâmetro Id

Uma alternativa a um filtro OData é a utilização do parâmetro **Id**. Para consultar um agrupamento específico com o id "myPool":

```PowerShell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

O **Id** parâmetro suporta apenas a pesquisa de IDs completos; não carateres universais ou filtros de estilo OData.

### <a name="use-the-maxcount-parameter"></a>Utilizar o parâmetro MaxCount

Por predefinição, cada cmdlet devolve um máximo de 1000 objetos. Se atingir este limite, refine o filtro para obter menos objetos ou defina explicitamente um limite máximo utilizando o parâmetro **MaxCount**. Por exemplo:

```PowerShell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

### <a name="use-the-powershell-pipeline"></a>Utilizar o pipeline do PowerShell

Cmdlets do batch utilizar o pipeline do PowerShell para enviar dados entre cmdlets. Este procedimento tem o mesmo efeito que especificar um parâmetro, mas facilita o trabalho com várias entidades.

Por exemplo, localizar e apresentar todas as tarefas na sua conta:

```PowerShell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Reinicie todos os nós de computação num conjunto:

```PowerShell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Gestão de pacotes de aplicações

Os pacotes de aplicações proporcionam uma forma simplificada para implementar aplicações nos nós de computação nos seus conjuntos. Com os cmdlets do PowerShell do Batch, pode carregar e gerir pacotes de aplicações na sua conta do Batch e implementar versões de pacotes em nós de computação.

**Criar** uma aplicação:

```PowerShell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**Adicionar** um pacote de aplicações:

```PowerShell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Definir a **versão predefinida** da aplicação:

```PowerShell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Listar** os pacotes de uma aplicação

```PowerShell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**Eliminar** um pacote de aplicação

```PowerShell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**Eliminar** uma aplicação

```PowerShell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Tem de eliminar todas as versões dos pacotes de aplicações de uma aplicação antes de eliminá-la. Se tentar eliminar uma aplicação que possua atualmente pacotes de aplicações, receberá um erro de “Conflito”.

### <a name="deploy-an-application-package"></a>Implementar um pacote de aplicações

Quando cria um conjunto, pode especificar um ou mais pacotes de aplicações para implementação. Quando especifica um pacote durante a criação de conjuntos, é implementado em cada nó no momento em que o nó é associado ao conjunto. Os pacotes também são implementados quando um nó é reiniciado ou recriado.

Especifique a opção `-ApplicationPackageReference` quando criar um conjunto para implementar pacotes de aplicações nos nós do conjunto à medida que são adicionados ao mesmo. Primeiro, crie uma **PSApplicationPackageReference** de objeto e configurá-lo com a versão de ID e o pacote de aplicação que pretende implementar em nós de computação do conjunto:

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Em seguida, crie o conjunto e especifique o objeto de referência do pacote como argumento para a opção `ApplicationPackageReferences`:

```PowerShell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Pode encontrar mais informações sobre pacotes de aplicações em [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).

> [!IMPORTANT]
> Tem de associar uma conta de armazenamento do Azure a sua conta do Batch para utilizar pacotes de aplicações.

### <a name="update-a-pools-application-packages"></a>Atualizar os pacotes de aplicações de um conjunto

Para atualizar as aplicações atribuídas a um conjunto existente, crie primeiro um objeto PSApplicationPackageReference com as propriedades pretendidas (ID e o pacote de versão da aplicação):

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Em seguida, obtenha o conjunto do Batch, retire quaisquer pacotes existentes, adicione a nossa nova referência do pacote e atualize o serviço Batch com as novas definições do conjunto:

```PowerShell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Atualizou as propriedades do conjunto no serviço Batch. Contudo, para implementar realmente o pacote de aplicação novo nos nós de computação no conjunto, tem de reiniciar ou recriar as imagens destes nós. Pode reiniciar todos os nós num conjunto com este comando:

```PowerShell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> Pode implementar vários pacotes de aplicações nos nós de computação num conjunto. Se pretender *adicionar* um pacote de aplicação ao invés de substituir os pacotes implementados atualmente, omita a linha `$pool.ApplicationPackageReferences.Clear()` acima.

## <a name="next-steps"></a>Passos Seguintes

* Para obter exemplos e a sintaxe detalhada dos cmdlets, veja a [Referência de cmdlets do Azure Batch](/powershell/module/az.batch).
* Para obter mais informações sobre as aplicações e os pacotes de aplicações no Batch, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
