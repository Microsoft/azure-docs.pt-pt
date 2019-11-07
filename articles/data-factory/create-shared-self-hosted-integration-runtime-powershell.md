---
title: Criar um tempo de execução de integração auto-hospedado compartilhado no Azure Data Factory
description: Saiba como criar um tempo de execução de integração hospedado internamente no Azure Data Factory, para que várias fábricas de dados possam acessar o Integration Runtime.
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
ms.openlocfilehash: fcda60863f78dd338bbfc64c1679561262c554a9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677062"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Criar um tempo de execução de integração auto-hospedado compartilhado no Azure Data Factory

Este guia mostra como criar um tempo de execução de integração auto-hospedado compartilhado no Azure Data Factory. Em seguida, você pode usar o tempo de execução de integração auto-hospedado compartilhado em outra data factory.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Criar um IR compartilhado auto-hospedado usando a interface do usuário do Azure Data Factory

Para criar um IR compartilhado auto-hospedado usando Azure Data Factory interface do usuário, você pode executar as seguintes etapas:

1. No IR auto-hospedado a ser compartilhado, Conceda permissão para o data factory no qual você deseja criar o IR vinculado.
      
    ![Botão para conceder permissão na guia compartilhamento](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)
      
    ![Seleções para atribuir permissões](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)     
    
2. Observe a ID de recurso do IR auto-hospedado a ser compartilhado.
      
   ![Local da ID do recurso](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)
    
3. No data factory ao qual as permissões foram concedidas, crie um novo IR via hospedagem interna (vinculado) e insira a ID do recurso.
      
   ![Botão para criar um Integration Runtime vinculado de hospedagem interna](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)
      
    ![Caixas para ID de nome e recurso](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Criar um IR compartilhado auto-hospedado usando Azure PowerShell

Para criar um IR compartilhado auto-hospedado usando Azure PowerShell, você pode executar as seguintes etapas: 
1. Criar uma fábrica de dados. 
1. Criar um integration runtime autoalojado.
1. Compartilhe o Integration Runtime de hospedagem interna com outras fábricas de dados.
1. Criar um tempo de execução de integração vinculado.
1. Revogue o compartilhamento.

### <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

- **Azure PowerShell**. Siga as instruções em [instalar Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps). Use o PowerShell para executar um script para criar um tempo de execução de integração auto-hospedado que pode ser compartilhado com outras fábricas de dados. 

> [!NOTE]  
> Para obter uma lista de regiões do Azure nas quais Data Factory está disponível no momento, selecione as regiões que lhe interessam em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie o Windows PowerShell Integrated Scripting Environment (ISE).

1. Criar variáveis. Copie e cole o script a seguir. Substitua as variáveis, como **subscriptionname** e **ResourceGroupName**, pelos valores reais: 

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

1. Entre e selecione uma assinatura. Adicione o código a seguir ao script para entrar e selecionar sua assinatura do Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Crie um grupo de recursos e um data factory.

    > [!NOTE]  
    > Este passo é opcional. Se você já tiver um data factory, ignore esta etapa. 

    Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) . Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo a seguir cria um grupo de recursos chamado `myResourceGroup` no local WestEurope: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Execute o comando seguinte para criar uma fábrica de dados: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Criar um integration runtime autoalojado

> [!NOTE]  
> Este passo é opcional. Se você já tiver o Integration Runtime de hospedagem interna que deseja compartilhar com outras fábricas de dados, ignore esta etapa.

Execute o seguinte comando para criar um tempo de execução de integração auto-hospedado:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Obter a chave de autenticação do Integration Runtime e registrar um nó

Execute o seguinte comando para obter a chave de autenticação para o tempo de execução de integração auto-hospedado:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

A resposta contém a chave de autenticação para esse tempo de execução de integração auto-hospedado. Você usa essa chave ao registrar o nó do Integration Runtime.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instalar e registrar o tempo de execução de integração auto-hospedado

1. Baixe o instalador do Integration Runtime auto-hospedado do [Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Execute o instalador para instalar a integração auto-hospedada em um computador local.

3. Registre a nova integração de hospedagem interna com a chave de autenticação que você recuperou em uma etapa anterior.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Compartilhar o tempo de execução de integração auto-hospedado com outro data factory

#### <a name="create-another-data-factory"></a>Criar outro data factory

> [!NOTE]  
> Este passo é opcional. Se você já tiver o data factory que deseja compartilhar com, ignore esta etapa.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Conceder permissão

Conceda permissão para o data factory que precisa acessar o tempo de execução de integração auto-hospedado que você criou e registrou.

> [!IMPORTANT]  
> Não ignore esta etapa!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Criar um Integration Runtime vinculado de hospedagem interna

Execute o seguinte comando para criar um Integration Runtime vinculado de hospedagem interna:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Agora você pode usar esse Integration Runtime vinculado em qualquer serviço vinculado. O tempo de execução de integração vinculado usa o tempo de execução de integração compartilhado para executar atividades.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Revogar o compartilhamento do Integration Runtime de um data factory

Para revogar o acesso de um data factory do Integration Runtime compartilhado, execute o seguinte comando:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Para remover o tempo de execução de integração vinculado existente, execute o seguinte comando no Integration Runtime compartilhado:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Passos seguintes

- Examine os [conceitos do Integration Runtime em Azure data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Saiba como [criar um tempo de execução de integração auto-hospedado no portal do Azure](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).
