---
title: Crie um tempo de integração independente partilhado com o PowerShell
description: Aprenda a criar um tempo de integração auto-hospedado partilhado na Azure Data Factory, para que várias fábricas de dados possam aceder ao tempo de integração.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 16feeb124859fa6199303d9d590fa0a286033ef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389438"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Crie um tempo de integração independente partilhado na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este guia mostra-lhe como criar um tempo de integração independente partilhado na Azure Data Factory. Em seguida, você pode usar o tempo de integração auto-hospedado compartilhado em outra fábrica de dados.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Crie um tempo de integração independente partilhado na Azure Data Factory

Você pode reutilizar uma infraestrutura de tempo de integração auto-hospedada existente que você já estabeleceu em uma fábrica de dados. Esta reutilização permite criar um tempo de integração auto-hospedado ligado numa fábrica de dados diferente, referindo-se a um IR partilhado existente.

Para ver uma introdução e demonstração desta funcionalidade, veja o vídeo seguinte de 12 minutos:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **IR compartilhado**: Um IR original auto-hospedado que funciona em uma infraestrutura física.  
- **Linked IR**: Um IR que faz referência a outro IR partilhado. O IR ligado é um IR lógico e utiliza a infraestrutura de outro IR partilhado auto-hospedado.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Criar um IR partilhado auto-hospedado usando Azure Data Factory UI

Para criar um IR partilhado auto-hospedado utilizando a UI da Azure Data Factory, pode tomar os seguintes passos:

1. No IR auto-hospedado a ser partilhado, selecione **a permissão de Concessão para outra fábrica de Dados** e na página "Integração runtime setup", selecione a fábrica de Dados na qual pretende criar o IR ligado.
      
    ![Botão para conceder permissão no separador Partilhar](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. Note e copie o "ID de recurso" acima referido do IR auto-hospedado a ser partilhado.
         
3. Na fábrica de dados a que foram concedidas as permissões, crie um novo IR auto-hospedado (ligado) e introduza o ID de recursos.
      
    ![Botão para criar um tempo de integração auto-hospedado](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![Botão para criar um tempo de integração auto-hospedado ligado](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Caixas para identificação de nome e recursos](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Crie um IR partilhado auto-hospedado usando a Azure PowerShell

Para criar um IR partilhado e auto-hospedado utilizando a Azure PowerShell, pode tomar os seguintes passos: 
1. Criar uma fábrica de dados. 
1. Criar um integration runtime autoalojado.
1. Partilhe o tempo de integração auto-hospedado com outras fábricas de dados.
1. Crie um tempo de integração ligado.
1. Revogue a partilha.

### <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura Azure**. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

- **Azure PowerShell**. Siga as instruções no [Install Azure PowerShell no Windows com o PowerShellGet](/powershell/azure/install-az-ps). Você usa o PowerShell para executar um script para criar um tempo de integração auto-hospedado que pode ser partilhado com outras fábricas de dados. 

> [!NOTE]  
> Para obter uma lista das regiões do Azure em que a Data Factory está atualmente disponível, selecione as regiões que lhe interessam [os produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados

1. Inicie o Windows PowerShell Integrated Scripting Environment (ISE).

1. Criar variáveis. Copiar e colar o seguinte script. Substitua as variáveis, tais como **Nome de Subscrição** e **Nome de Grupo de Recursos,** por valores reais: 

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

1. Iniciar s.000 e selecionar uma subscrição. Adicione o seguinte código ao script para iniciar sôm e selecione a subscrição do Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Criar um grupo de recursos e uma fábrica de dados.

    > [!NOTE]  
    > Este passo é opcional. Se já tem uma fábrica de dados, ignore este passo. 

    Crie um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) utilizando o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo a seguir cria um grupo de recursos nomeado `myResourceGroup` na localização WestEurope: 

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
> Este passo é opcional. Se já tem o tempo de integração auto-hospedado que pretende partilhar com outras fábricas de dados, ignore este passo.

Executar o seguinte comando para criar um tempo de integração auto-hospedado:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Obtenha a chave de autenticação de tempo de integração e registe um nó

Executar o seguinte comando para obter a chave de autenticação para o tempo de integração auto-hospedado:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

A resposta contém a chave de autenticação para este tempo de integração auto-hospedado. Utilize esta chave quando registar o nó de tempo de execução de integração.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instale e registe o tempo de funcionação da integração auto-hospedada

1. Descarregue o instalador de tempo de execução de integração auto-hospedado a partir do Tempo de Execução da Integração da [Fábrica de Dados Azure](https://aka.ms/dmg).

2. Executar o instalador para instalar a integração auto-hospedada num computador local.

3. Registe a nova integração auto-hospedada com a chave de autenticação que recuperou num passo anterior.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Partilhe o tempo de integração auto-hospedado com outra fábrica de dados

#### <a name="create-another-data-factory"></a>Criar outra fábrica de dados

> [!NOTE]  
> Este passo é opcional. Se já tem a fábrica de dados com a que pretende partilhar, ignore este passo. Mas para adicionar ou remover atribuições de funções a outras fábricas de dados, tem de ter `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, como [o Administrador de Acesso ao Utilizador](../role-based-access-control/built-in-roles.md#user-access-administrator) ou o [Proprietário.](../role-based-access-control/built-in-roles.md#owner)

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Conceder permissão

Conceda permissão à fábrica de dados que precisa de aceder ao tempo de integração auto-hospedado que criou e registou.

> [!IMPORTANT]  
> Não salte este passo!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Criar um tempo de integração auto-hospedado ligado

Executar o seguinte comando para criar um tempo de integração auto-hospedado ligado:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Agora pode utilizar este tempo de integração ligado em qualquer serviço ligado. O tempo de execução da integração ligada utiliza o tempo de execução da integração partilhada para executar atividades.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Revogar a partilha de tempo de execução de integração de uma fábrica de dados

Para revogar o acesso de uma fábrica de dados a partir do tempo de execução da integração partilhada, executar o seguinte comando:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Para remover o tempo de execução da integração ligada existente, executar o seguinte comando contra o tempo de execução da integração partilhada:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="monitoring"></a>Monitorização

#### <a name="shared-ir"></a>IR compartilhado

![Seleções para encontrar um tempo de integração partilhada](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Monitorize um tempo de integração partilhada](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>IR ligado

![Seleções para encontrar um tempo de integração ligado](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Monitorize um tempo de integração ligado](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)


### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitações conhecidas da partilha de IR auto-acolam

* A fábrica de dados em que é criado um IR vinculado deve ter uma [Identidade Gerida.](../active-directory/managed-identities-azure-resources/overview.md) Por padrão, as fábricas de dados criadas no portal Azure ou nos cmdlets PowerShell têm uma Identidade Gerida implicitamente criada. Mas quando uma fábrica de dados é criada através de um modelo de Gestor de Recursos Azure ou SDK, você deve definir a propriedade **Identidade** explicitamente. Esta definição garante que o Gestor de Recursos cria uma fábrica de dados que contém uma Identidade Gerida.

* A Data Factory .NET SDK que suporta esta funcionalidade deve ser a versão 1.1.0 ou posterior.

* Para conceder permissão, precisa da função proprietário ou da função de Proprietário herdado na fábrica de dados onde existe o IR partilhado.

* A funcionalidade de partilha funciona apenas para fábricas de dados dentro do mesmo inquilino AZure AD.

* Para [os utilizadores convidados](../active-directory/governance/manage-guest-access-with-access-reviews.md)da Azure AD, a funcionalidade de pesquisa no UI, que lista todas as fábricas de dados utilizando uma palavra-chave de pesquisa, não funciona. Mas enquanto o utilizador convidado for o proprietário da fábrica de dados, pode partilhar o IR sem a funcionalidade de pesquisa. Para a Identidade Gerida da fábrica de dados que necessita de partilhar o IR, insira essa Identidade Gerida na caixa **de Permissão de Atribuição** e selecione **Adicionar** na UI da Fábrica de Dados.

  > [!NOTE]
  > Esta funcionalidade está disponível apenas na Data Factory V2.


### <a name="next-steps"></a>Passos seguintes

- Rever [conceitos de tempo de execução de integração na Azure Data Factory.](./concepts-integration-runtime.md)

- Saiba como [criar um tempo de integração auto-hospedado no portal Azure](./create-self-hosted-integration-runtime.md).