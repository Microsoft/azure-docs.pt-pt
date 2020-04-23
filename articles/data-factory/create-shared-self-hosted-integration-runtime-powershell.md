---
title: Crie um tempo de execução de integração auto-hospedada com a PowerShell
description: Aprenda a criar um tempo de funcionação de integração auto-hospedado partilhado na Azure Data Factory, para que várias fábricas de dados possam aceder ao tempo de funcionação da integração.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 10/31/2018
ms.openlocfilehash: 0f018d6b94d1c5b9d9002a767b3ebceb6c9c746c
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106632"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Criar um tempo de integração auto-hospedado partilhado na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este guia mostra-lhe como criar um tempo de funcionação de integração auto-hospedado partilhado na Azure Data Factory. Depois, pode utilizar o tempo de execução de integração auto-hospedado partilhado noutra fábrica de dados.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Criar um IR auto-hospedado partilhado utilizando a Azure Data Factory UI

Para criar um IR auto-hospedado partilhado utilizando a Azure Data Factory UI, pode tomar as seguintes etapas:

1. No IR auto-hospedado para ser partilhado, conceda permissão à fábrica de dados em que pretende criar o IR ligado.
      
    ![Botão para concessão de permissão no separador Partilha](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)
      
    ![Seleções para atribuição de permissões](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)     
    
2. Note a identificação do recurso do IR auto-hospedado para ser partilhado.
      
   ![Localização do ID do recurso](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)
    
3. Na fábrica de dados a que foram concedidas as permissões, crie um novo IR auto-hospedado (ligado) e introduza o ID de recurso.
      
   ![Botão para criar um tempo de execução de integração auto-hospedado ligado](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)
      
    ![Caixas para identificação de nome e recursos](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Crie um IR auto-hospedado partilhado usando o Azure PowerShell

Para criar um IR auto-hospedado partilhado utilizando o Azure PowerShell, pode tomar as seguintes etapas: 
1. Criar uma fábrica de dados. 
1. Criar um integration runtime autoalojado.
1. Partilhe o tempo de execução da integração auto-hospedada com outras fábricas de dados.
1. Criar um tempo de execução de integração ligado.
1. Revogar a partilha.

### <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura Azure.** Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

- **Azure PowerShell.** Siga as instruções em [Instalação Azure PowerShell no Windows com PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps). Usa o PowerShell para executar um script para criar um tempo de integração auto-hospedado que pode ser partilhado com outras fábricas de dados. 

> [!NOTE]  
> Para uma lista das regiões do Azure em que a Data Factory está atualmente disponível, selecione as regiões que lhe interessam em [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie o Windows PowerShell Integrated Scripting Environment (ISE).

1. Criar variáveis. Copiar e colar o seguinte script. Substitua as variáveis, tais como Nome de **Subscrição** e **Nome do Grupo de Recursos,** por valores reais: 

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

1. Iniciar sessão e selecionar uma subscrição. Adicione o seguinte código ao script para iniciar sessão e selecione a sua subscrição Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Criar um grupo de recursos e uma fábrica de dados.

    > [!NOTE]  
    > Este passo é opcional. Se já tem uma fábrica de dados, ignore este passo. 

    Crie um grupo de [recursos Azure](../azure-resource-manager/management/overview.md) utilizando o comando [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria `myResourceGroup` um grupo de recursos nomeado na localização da Europa Ocidental: 

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
> Este passo é opcional. Se já tem o tempo de execução de integração auto-hospedado que pretende partilhar com outras fábricas de dados, ignore este passo.

Executar o seguinte comando para criar um tempo de execução de integração auto-hospedado:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Obtenha a chave de autenticação de tempo de execução de integração e registe um nó

Executar o seguinte comando para obter a chave de autenticação para o tempo de execução de integração auto-hospedado:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

A resposta contém a chave de autenticação para este tempo de execução de integração auto-hospedado. Usa esta tecla quando regista o nó de tempo de integração.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instale e registe o tempo de execução da integração auto-hospedado

1. Descarregue o instalador de tempo de integração auto-hospedado a partir do Tempo de [Integração da Fábrica](https://aka.ms/dmg)de Dados azure .

2. Executar o instalador para instalar a integração auto-hospedada num computador local.

3. Registe a nova integração auto-hospedada com a chave de autenticação que recuperou num passo anterior.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Partilhe o tempo de execução da integração auto-hospedada com outra fábrica de dados

#### <a name="create-another-data-factory"></a>Criar outra fábrica de dados

> [!NOTE]  
> Este passo é opcional. Se já tem a fábrica de dados com a que pretende partilhar, ignore este passo.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Concessão de autorização

Conceda permissão à fábrica de dados que precisa de aceder ao tempo de integração auto-hospedado que criou e registou.

> [!IMPORTANT]  
> Não salte este passo!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Criar um tempo de execução de integração auto-hospedado ligado

Executar o seguinte comando para criar um tempo de execução de integração auto-hospedado ligado:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Agora pode utilizar este tempo de execução de integração ligado em qualquer serviço ligado. O tempo de execução de integração ligado utiliza o tempo de execução da integração partilhada para executar atividades.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Revogar a partilha de tempo de integração a partir de uma fábrica de dados

Para revogar o acesso de uma fábrica de dados do tempo de execução de integração partilhada, executar o seguinte comando:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Para remover o tempo de execução de integração ligado existente, executar o seguinte comando contra o tempo de execução da integração partilhada:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Passos seguintes

- Rever conceitos de tempo de [execução de integração na Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)

- Aprenda a [criar um tempo de funcionação de integração auto-hospedado no portal Azure.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
