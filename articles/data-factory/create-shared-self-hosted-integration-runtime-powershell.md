---
title: Criar um runtime de integração autoalojado partilhado na fábrica de dados do Azure com o PowerShell | Documentos da Microsoft
description: Saiba como criar um runtime de integração autoalojado partilhado na fábrica de dados do Azure, para que várias fábricas de dados podem acessar o runtime de integração.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: cc1a0905c97e76c481283363f095087b5fdcba3f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455858"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>Criar um runtime de integração autoalojado partilhado na fábrica de dados do Azure com o PowerShell

Este guia passo a passo mostra-lhe como criar um runtime de integração autoalojado partilhado na fábrica de dados do Azure com o Azure PowerShell. Em seguida, pode utilizar o runtime de integração autoalojado partilhado na fábrica de dados de outro. Neste tutorial, siga os passos seguintes: 

1. Criar uma fábrica de dados. 
1. Criar um integration runtime autoalojado.
1. Partilhe o runtime de integração autoalojado com outras fábricas de dados.
1. Crie um runtime de integração ligado.
1. Revogar o compartilhamento.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

- **Azure PowerShell**. Siga as instruções em [instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps). Utilizar o PowerShell para executar um script para criar um runtime de integração autoalojado que pode ser partilhado com outras fábricas de dados. 

> [!NOTE]  
> Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que interessam a no [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie o Windows PowerShell Integrated Scripting Environment (ISE).

1. Crie variáveis. Copie e cole o seguinte script. Substitua as variáveis, como **SubscriptionName** e **ResourceGroupName**, com valores reais: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Inicie sessão e selecione uma subscrição. Adicione o seguinte código ao script para iniciar sessão e selecione a sua subscrição do Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Crie um grupo de recursos e uma fábrica de dados.

    > [!NOTE]  
    > Este passo é opcional. Se já tiver uma fábrica de dados, ignore este passo. 

    Criar uma [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) utilizando o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azurermps-6.11.0) comando. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização WestEurope: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Execute o comando seguinte para criar uma fábrica de dados: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>Criar um integration runtime autoalojado

> [!NOTE]  
> Este passo é opcional. Se já tiver o runtime de integração autoalojado que pretende partilhar com outras fábricas de dados, ignore este passo.

Execute o seguinte comando para criar um runtime de integração autoalojado:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Obtenha a chave de autenticação do runtime de integração e registe-se um nó

Execute o seguinte comando para obter a chave de autenticação para o runtime de integração autoalojado:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

A resposta contém a chave de autenticação para este integration runtime autoalojado. Utilize esta chave quando registar o nó do integration runtime.

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instalar e registar o runtime de integração autoalojado

1. Transferir o instalador do runtime de integração autoalojado partir [do Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Execute o instalador para instalar a integração de autoalojado num computador local.

3. Registre-se a nova integração de autoalojado com a chave de autenticação que obteve no passo anterior.

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Partilhar o runtime de integração autoalojado com outro fábrica de dados

### <a name="create-another-data-factory"></a>Criar fábrica de dados de outro

> [!NOTE]  
> Este passo é opcional. Se já tiver a fábrica de dados que pretende partilhar com, ignore este passo.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>Conceder permissão

Conceder permissão à fábrica de dados que precisa acessar o runtime de integração autoalojado que criou e registado.

> [!IMPORTANT]  
> Não ignore este passo!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>Criar um runtime de integração autoalojado ligado

Execute o seguinte comando para criar um runtime de integração autoalojado ligado:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Agora, pode utilizar este runtime de integração ligado em qualquer serviço ligado. O runtime de integração ligado utiliza o runtime de integração partilhado para executar atividades.

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Revogar o runtime de integração de partilha de uma fábrica de dados

Para revogar o acesso de uma fábrica de dados do Runtime de integração partilhada, execute o seguinte comando:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Para remover o runtime de integração ligado existente, execute o seguinte comando contra o runtime de integração partilhado:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>Passos Seguintes

- Revisão [conceitos de runtime de integração no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Saiba como [criar um runtime de integração autoalojado no portal do Azure](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).
