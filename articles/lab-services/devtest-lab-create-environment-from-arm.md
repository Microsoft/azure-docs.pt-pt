---
title: Criar ambientes multi-VM e recursos PaaS com modelos Azure Resource Manager | Documentos da Microsoft
description: Saiba como criar ambientes multi-VM e recursos de PaaS no Azure DevTest Labs a partir de um modelo Azure Resource Manager
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: ebe5c65f701c0a1c7c02182800a35bfbeed5b0be
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181389"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Criar ambientes multi-VM e recursos PaaS com modelos Azure Resource Manager

O [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040) permite-lhe facilmente [adicionar uma VM ao mesmo tempo a um laboratório](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). No entanto, se o ambiente contém várias VMs, cada VM tem de ser individualmente criada. Para cenários como uma aplicação Web de várias camada ou um farm do SharePoint, é necessário um mecanismo para permitir a criação de várias VMs num único passo. Ao utilizar modelos Azure Resource Manager, pode agora definir a infraestrutura e a configuração da sua solução do Azure e implementar repetidamente a várias VMs num estado consistente. Esta funcionalidade fornece as seguintes vantagens:

- Modelos Azure Resource Manager são carregados diretamente a partir do seu repositório de controle de origem (GitHub ou Git de serviços de DevOps do Azure).
- Depois de configurada, os seus utilizadores podem criar um ambiente escolhendo um modelo do Azure Resource Manager do portal do Azure, como fazem outros tipos de [bases de VM](./devtest-lab-comparing-vm-base-image-types.md).
- Recursos de PaaS do Azure podem ser aprovisionados num ambiente a partir de um modelo Azure Resource Manager, além de VMs de IaaS.
- O custo de ambientes pode ser controlado no laboratório, além de VMs individuais criados por outros tipos de bases.
- Recursos de PaaS são criados e serão apresentados no custo de controlo; No entanto, o encerramento automático da VM não é aplicável a recursos de PaaS.

Saiba mais sobre os muitos [vantagens da utilização de modelos do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) para implementar, atualizar ou eliminar todos os seus recursos do laboratório numa única operação.

> [!NOTE]
> Quando utiliza um modelo do Resource Manager como base para criar o laboratório de mais VMs, existem algumas diferenças a ter em conta se estiver a criar várias VMs ou VMs único. [Utilizar o modelo do Azure Resource Manager de uma máquina virtual](devtest-lab-use-resource-manager-template.md) explica essas diferenças em mais detalhes.
>

## <a name="devtest-labs-public-environments"></a>Ambientes públicos do DevTest Labs
O Azure DevTest Labs tem um [repositório público dos modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que pode utilizar para criar ambientes sem ter de ligar a uma origem externa do GitHub sozinho. Este repositório inclui modelos usados com freqüência, como aplicações Web do Azure, o Cluster do Service Fabric e o desenvolvimento de ambiente de Farm do SharePoint. Esta funcionalidade é semelhante para o repositório público de artefactos que é incluído para todos os laboratórios que criar. O repositório de ambiente permite-lhe começar a utilizar rapidamente com modelos de ambiente previamente criados com parâmetros de entrada mínimos para lhe fornecer uma experiência de introdução ao obter uniforme para os recursos de PaaS dentro de laboratórios. Para obter mais informações, consulte [configurar e utilizar ambientes públicos no DevTest Labs](devtest-lab-configure-use-public-environments.md).

## <a name="configure-your-own-template-repositories"></a>Configurar os seus próprios repositórios de modelo
Como uma das melhores práticas com a infraestrutura como código e a configuração como código, modelos de ambiente devem ser geridos no controle de origem. O Azure DevTest Labs segue essa prática e carrega todos os modelos do Azure Resource Manager diretamente a partir de seus repositórios do GitHub ou o Git de serviços do Azure DevOps. Como resultado, os modelos do Resource Manager podem ser utilizados no ciclo de lançamento de todo o ambiente de teste para o ambiente de produção.

Veja os modelos criados pela equipe do DevTest Labs no [repositório do GitHub público](https://github.com/Azure/azure-devtestlab/tree/master/Environments). Neste repositório público, pode ver modelos partilhados por outros utilizadores, que pode utilizar diretamente ou personalize-os para satisfazer as suas necessidades. Depois de criar o seu modelo, armazená-lo neste repositório para partilhá-lo com outras pessoas. Também pode configurar seu próprio repositório de Git com modelos que podem ser utilizadas para configurar ambientes na cloud. 

Existem algumas regras a seguir para organizar seus modelos do Azure Resource Manager num repositório:

- O arquivo de modelo global deve ser nomeado `azuredeploy.json`. 

    ![Arquivos de modelo de chave do Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Se pretender utilizar os valores de parâmetros definidos no ficheiro de parâmetros, o ficheiro de parâmetros tem de ser nome `azuredeploy.parameters.json`.
- Pode utilizar os parâmetros `_artifactsLocation` e `_artifactsLocationSasToken` para construir o valor do URI parametersLink, permitindo que o DevTest Labs gerir automaticamente a modelos aninhados. Para obter mais informações, consulte [como o Azure DevTest Labs facilita o Gestor de recursos aninhados implementações de modelo em ambientes de teste](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/).
- Metadados podem ser definidos para especificar o nome a apresentar do modelo e a descrição. Estes metadados tem de ser num arquivo chamado `metadata.json`. O ficheiro de metadados de exemplo seguinte ilustra como especificar o nome a apresentar e a descrição: 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

Os seguintes passos guiá-lo por meio de adicionar um repositório ao seu laboratório com o portal do Azure. 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório pretendido.   
1. O laboratório **descrição geral** painel, selecione **Konfigurace a zásady**.

    ![Configuração e políticas](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Do **Konfigurace a zásady** lista de definições, selecione **repositórios**. O **repositórios** painel lista os repositórios que foram adicionados ao laboratório. Um repositório com o nome `Public Repo` é gerado automaticamente para todos os laboratórios e liga-se para o [repositório do GitHub de laboratórios DevTest](https://github.com/Azure/azure-devtestlab) que contém vários artefactos VM para a sua utilização.

    ![Repositório público](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Selecione **adicionar +** para adicionar o seu repositório de modelos do Azure Resource Manager.
1. Quando a segunda **repositórios** abre painel, introduza as informações necessárias da seguinte forma:
    - **Nome** -introduza o nome do repositório que é utilizado no laboratório.
    - **URL de clone de Git** -introduza o URL de clone de GIT HTTPS a partir do GitHub ou dos serviços de DevOps do Azure.  
    - **Ramo** -introduza o nome do ramo para acessar as definições de modelo do Azure Resource Manager. 
    - **Token de acesso pessoal** -o token de acesso pessoal é utilizado para aceder de forma segura o repositório. Para obter o token de DevOps nos serviços do Azure, selecione  **&lt;YourName >> meu perfil > Segurança > token de acesso público**. Para obter o token a partir do GitHub, selecione seu avatar seguido selecionando **definições > token de acesso público**. 
    - **Caminhos de pastas** -através de um dos dois campos de entrada, introduza o caminho da pasta que começa com uma barra - / - e é relativo ao seu URI klonu GITU a qualquer uma, as definições de artefacto (o primeiro campo de entrada) ou as definições de modelo do Azure Resource Manager .   
    
        ![Repositório público](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. Depois de todos os campos obrigatórios são introduzidos e passam a validação, selecione **guardar**.

A secção seguinte explica como criar ambientes a partir de um modelo Azure Resource Manager.

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Criar um ambiente a partir de um modelo do Resource Manager com o portal do Azure

Depois de configurar um repositório de modelos do Azure Resource Manager no laboratório, os utilizadores de laboratório podem criar um ambiente com o portal do Azure com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione o laboratório pretendido.   
1. No painel do laboratório, selecione **adicionar +**.
1. O **Vyberte bázi** painel apresenta as imagens base, pode utilizar com os modelos Azure Resource Manager listados em primeiro lugar. Selecione o modelo Azure Resource Manager pretendido.

    ![Escolher uma base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Na **Add** painel, introduza o **nome do ambiente** valor. O nome do ambiente é o que é apresentado aos utilizadores no laboratório. Os campos de entrada restantes são definidos no modelo do Azure Resource Manager. Se os valores predefinidos são definidos no modelo ou o `azuredeploy.parameter.json` ficheiro está presente, valores predefinidos são apresentados nesses campos de entrada. Para os parâmetros do tipo *proteger a cadeia de caracteres*, pode usar os segredos armazenados no seu Cofre de chaves do Azure. Para saber mais sobre a guardar segredos num cofre de chaves e utilizá-las durante a criação de recursos do laboratório, consulte [Store segredos no Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

    ![Adicionar o painel](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Existem vários valores de parâmetro que - mesmo que o especificado - são exibidos como valores vazios. Por conseguinte, se os utilizadores atribuir esses valores para parâmetros num modelo Azure Resource Manager, o DevTest Labs não apresenta os valores. Em vez disso, os campos de entrada em branco são apresentados em que os utilizadores de laboratório tem de introduzir um valor ao criar o ambiente.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Selecione **adicionar** para criar o ambiente. O ambiente começa imediatamente o aprovisionamento com a exibição de estado no **minhas máquinas virtuais** lista. Um novo grupo de recursos é criado automaticamente pelo laboratório para aprovisionar todos os recursos definidos no modelo do Azure Resource Manager.
1. Depois de criar o ambiente, selecione o ambiente no **minhas máquinas virtuais** lista para abrir o painel do grupo de recursos e procurar todos os recursos aprovisionados no ambiente.
    
    ![Minha lista de máquinas virtuais](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Também pode expandir o ambiente para ver apenas a lista de VMs que são aprovisionados no ambiente.
    
    ![Minha lista de máquinas virtuais](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Clique em qualquer um dos ambientes para ver as ações disponíveis - como a aplicação de artefactos, anexar discos de dados, alterar o tempo de encerramento automático e muito mais.

    ![Ações de ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="automate-deployment-of-environments"></a>Automatizar a implementação de ambientes
O Azure DevTest Labs fornece a capacidade de utilizar um [modelo Azure Resource Manager de gestão](../azure-resource-manager/resource-group-authoring-templates.md) para criar um ambiente com um conjunto de recursos do laboratório. Estes ambientes podem conter todos os recursos do Azure que podem ser criados com modelos do Resource Manager. Ambientes de laboratórios DevTest permitem aos utilizadores prontamente implementar infraestruturas complexas de forma consistente nos limites do laboratório. Atualmente, a adição de um ambiente a um laboratório no portal do Azure é exequível durante a criação de uma vez, mas num desenvolvimento ou numa situação de teste, onde ocorrem vários criações, uma implementação automatizada permite uma experiência melhorada.

Concluir os passos seguintes na [configurar seus próprios repositórios de modelo](#configure-your-own-template-repositories) secção antes de avançar: 

1. Crie o modelo do Resource Manager que define os recursos a ser criados. 
2. Configure o modelo do Resource Manager no Git um repositório. 
3. Ligar o repositório de Git para o laboratório. 

### <a name="powershell-script-to-deploy-the-resource-manager-template"></a>Script do PowerShell para implementar o modelo do Resource Manager
Guarde o script do PowerShell na secção seguinte no seu disco rígido (por exemplo: deployenv.ps1) e execute o script depois de especificar valores para o SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (pasta) no repositório de Git, EnvironmentName.

```powershell
./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"  
```

#### <a name="sample-script"></a>Script de exemplo
Aqui está o script de exemplo para criar um ambiente no seu laboratório. Os comentários no script de ajudar a compreender melhor o script. 

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

[CmdletBinding()]

param (
# ID of the Azure Subscription where the lab is created.
[string] [Parameter(Mandatory=$true)] $SubscriptionId,

# Name of the lab (existing) in which to create the environment.
[string] [Parameter(Mandatory=$true)] $LabName,

# Name of the connected repository in the lab. 
[string] [Parameter(Mandatory=$true)] $RepositoryName,

# Name of the template (folder name in the Git repository) based on which the environment will be created.
[string] [Parameter(Mandatory=$true)] $TemplateName,

# Name of the environment to be created in the lab.
[string] [Parameter(Mandatory=$true)] $EnvironmentName,

# The parameters to be passed to the template. Each parameter is prefixed with “-param_”. 
# For example, if the template has a parameter named “TestVMName” with a value of “MyVMName”, the string in $Params will have the form: `-param_TestVMName MyVMName`. 
# This convention allows the script to dynamically handle different templates.
[Parameter(ValueFromRemainingArguments=$true)]
    $Params
)

# Save this script as the deployenv.ps1 file
# Run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName
# ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"    

# Comment this statement to completely automate the environment creation.    
# Sign in to Azure. 
Connect-AzureRmAccount

# Select the subscription that has the lab.  
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# Get information about the user, specifically the user ID, which is used later in the script.  
$UserId = $((Get-AzureRmADUser -UserPrincipalName (Get-AzureRmContext).Account).Id.Guid)
        
# Get information about the lab such as lab location. 
$lab = Get-AzureRmResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
    
# Get information about the repository in the lab. 
$repository = Get-AzureRmResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
    -ResourceName $LabName `
    -ApiVersion 2016-05-15 `
    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 

# Get information about the Resource Manager template based on which the environment will be created. 
$template = Get-AzureRmResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
    -ResourceName "$LabName/$($repository.Name)" `
    -ApiVersion 2016-05-15 `
    | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." } 

# Build the template parameters with parameter name and values.     
$parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
$templateParameters = @()

# The custom parameters need to be extracted from $Params and formatted as name/value pairs.
$Params | ForEach-Object {
    if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
        $name = $Matches[1]                
    } elseif ( $name ) {
        $templateParameters += @{ "name" = "$name"; "value" = "$_" }
        $name = $null #reset name variable
    }
}

# Once name/value pairs are isolated, create an object to hold the necessary template properties
$templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; } 

# Now, create or deploy the environment in the lab by using the New-AzureRmResource command. 
New-AzureRmResource -Location $Lab.Location `
    -ResourceGroupName $lab.ResourceGroupName `
    -Properties $templateProperties `
    -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
    -ResourceName "$LabName/$UserId/$EnvironmentName" `
    -ApiVersion '2016-05-15' -Force 
 
Write-Output "Environment $EnvironmentName completed."
```

Também pode utilizar a CLI do Azure para implementar recursos com modelos do Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli).

> [!NOTE]
> Apenas um utilizador com permissões de proprietário de laboratório pode criar VMs a partir de um modelo do Resource Manager com o Azure PowerShell. Se pretender automatizar a criação da VM com um modelo do Resource Manager e tiver apenas permissões de utilizador, pode utilizar o [ **az lab vm crie** comando na CLI](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create).

### <a name="general-limitations"></a>Limitações gerais 

Considere estas limitações ao utilizar um modelo do Resource Manager no DevTest Labs:

- Qualquer modelo do Resource Manager que cria não pode fazer referência aos recursos existentes; só pode fazer referência aos novos recursos. Além disso, se tiver um modelo do Resource Manager existente, que normalmente implementa fora do DevTest Labs e contém referências a recursos existentes, não pode ser utilizado no laboratório.

   A única exceção é que **pode** fazer referência a uma rede virtual existente. 

- Não não possível criar fórmulas do laboratório de VMs que foram criadas a partir de um modelo do Resource Manager. 

- Não não possível criar imagens personalizadas do laboratório de VMs que foram criadas a partir de um modelo do Resource Manager. 

- A maioria das políticas não são avaliadas quando implementar modelos do Resource Manager.

   Por exemplo, pode ter uma política de laboratório, especificando que um utilizador só pode criar cinco VMs. No entanto, um utilizador pode implementar um modelo do Resource Manager que cria dezenas de VMs. Políticas que não são avaliadas incluem:

   - Número de VMs por utilizador
   - Número de VMs do premium por utilizador de laboratório
   - Número de discos premium por utilizador de laboratório


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>Configurar os direitos do acesso de grupo de recursos do ambiente para os utilizadores de laboratório

Os utilizadores de laboratório podem implementar um modelo do Resource Manager. Mas, por padrão, eles têm direitos de acesso de leitor, o que significa que não podem alterar os recursos num grupo de recursos de ambiente. Por exemplo, não é possível parar ou iniciar seus recursos.

Siga estes passos para conceder aos seus utilizadores de laboratório direitos de acesso de contribuinte. Em seguida, quando implementam um modelo do Resource Manager, poderão editar os recursos nesse ambiente. 


1. No seu laboratório **descrição geral** painel, selecione **Konfigurace a zásady**.
1. Selecione **definições de laboratório**.
1. No painel de definições de laboratório, selecione **contribuinte** para conceder permissões de escrita aos utilizadores de laboratório.

    ![Configurar direitos de acesso de utilizador do laboratório](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos Seguintes
* Quando uma VM tiver sido criada, pode ligar à VM selecionando **Connect** no painel de gestão da VM.
* Ver e gerir os recursos num ambiente ao selecionar o ambiente do **minhas máquinas virtuais** lista em seu laboratório. 
* Explore os [modelos do Azure Resource Manager da Galeria de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
