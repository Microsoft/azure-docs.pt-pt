---
title: Criar ambientes multi-VM e recursos PaaS com modelos
description: Saiba como criar ambientes multi-VM e recursos PaaS em Laboratórios Azure DevTest a partir de um modelo de Gestor de Recursos Azure
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169640"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager

Os ambientes da Azure DevTest Labs permitem que os utilizadores implementem facilmente infraestruturas complexas de forma consistente dentro dos limites do laboratório. Você pode usar [modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md) para criar ambientes com conjuntos de recursos em DevTest Labs. Estes ambientes podem conter quaisquer recursos Azure que os modelos do Gestor de Recursos possam criar.

Pode [facilmente adicionar uma máquina virtual (VM)](devtest-lab-add-vm.md) de cada vez a um laboratório utilizando o [portal Azure](https://portal.azure.com). No entanto, cenários como aplicações web de vários níveis ou uma quinta do SharePoint precisam de um mecanismo para criar vários VMs num único passo. Ao utilizar modelos do Gestor de Recursos Azure, pode definir a infraestrutura e configuração da sua solução Azure e implementar repetidamente vários VMs num estado consistente.

Os modelos do Gestor de Recursos Azure também proporcionam os seguintes benefícios:

- Os modelos do Gestor de Recursos Azure são carregados diretamente do seu repositório de controlo de fonte GitHub ou Azure Repos.
- Os seus utilizadores podem criar um ambiente escolhendo um modelo de Gestor de Recursos Azure configurado do portal Azure, tal como fazem com outros tipos de [bases VM](devtest-lab-comparing-vm-base-image-types.md).
- Você pode fornecer recursos Azure PaaS, bem como VMs IaaS em um ambiente a partir de um modelo de Gestor de Recursos Azure.
- Você pode rastrear o custo dos ambientes no laboratório, além de VMs individuais criados por outros tipos de bases. Os recursos paaS são criados e aparecerão no rastreio de custos. No entanto, a paragem automática vM não se aplica aos recursos da PaaS.

Para saber mais sobre os benefícios de usar modelos do Gestor de Recursos para implementar, atualizar ou eliminar muitos recursos de laboratório numa única operação, consulte [Benefícios de usar modelos de Gestor de Recursos](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> Quando você usa um modelo de Gestor de Recursos como base para criar VMs de laboratório, existem algumas diferenças entre criar vários VMs ou um único VM. Para mais informações, consulte Utilize o modelo de [Gestor de Recursos Azure da máquina virtual](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Use ambientes públicos de DevTest Labs
A Azure DevTest Labs tem um [repositório público de modelos](https://github.com/Azure/azure-devtestlab/tree/master/Environments) do Gestor de Recursos Azure que pode usar para criar ambientes sem ter que se ligar a uma fonte externa do GitHub. Este repositório público é semelhante ao repositório público de artefactos que está disponível no portal Azure para cada laboratório que você cria. O repositório ambiental permite-lhe começar rapidamente com modelos ambientais pré-autores que têm poucos parâmetros de entrada. Estes modelos proporcionam-lhe uma experiência de início suave para os recursos paaS dentro dos laboratórios.

No repositório público, a equipa de DevTest Labs e outros criaram e partilharam modelos frequentemente usados como Aplicações Web Azure, Cluster de Tecidos de Serviço e um ambiente de desenvolvimento SharePoint Farm. Pode utilizar estes modelos diretamente, ou personalizá-los de acordo com as suas necessidades. Para mais informações, consulte [Configure e use ambientes públicos em DevTest Labs](devtest-lab-configure-use-public-environments.md). Depois de criar os seus próprios modelos, pode armazená-los neste repositório para partilhá-los com outros, ou configurar o seu próprio repositório Git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Crie os seus próprios repositórios de modelo

Como uma das melhores práticas com infra-estrutura-como código e configuração-como código, você deve gerir os modelos ambientais no controlo de fonte. A Azure DevTest Labs segue esta prática e carrega todos os modelos do Gestor de Recursos Azure diretamente dos seus repositórios GitHub ou Azure Repos. Como resultado, você pode usar modelos de Gestor de Recursos em todo o ciclo de libertação, desde o ambiente de teste ao ambiente de produção.

Existem várias regras a seguir para organizar os seus modelos de Gestor de Recursos Azure num repositório:

- Deve nomear o ficheiro de modelo principal *azuredeploy.json*.

- Se pretender utilizar valores de parâmetros definidos num ficheiro de parâmetros, o ficheiro parâmetro deve ser denominado *azuredeploy.parameters.parson*.

  Pode utilizar os `_artifactsLocation` parâmetros e `_artifactsLocationSasToken` construir o valor dos parâmetrosLink URI, permitindo que os Laboratórios DevTest gerem automaticamente os modelos aninhados. Para mais informações, consulte a implementação de modelos do Gestor de [Recursos Do Azure para testar ambientes](deploy-nested-template-environments.md).

- Pode definir metadados para especificar o nome e descrição do modelo num ficheiro chamado *metadata.json,* da seguinte forma:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Ficheiros de modelo de gestor de recursos key Azure](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Adicione repositórios de modelo ao laboratório

Depois de criar e configurar o seu repositório, pode adicioná-lo ao seu laboratório utilizando o portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o laboratório que quiser.
1. No painel de **visão geral** do laboratório, selecione **Configuração e políticas**.

   ![Configuração e políticas](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. A partir da lista de definições de **Configuração e políticas,** selecione **Repositórios**. O repositório de Repo do **Public Artifact Repo** é gerado automaticamente para todos os laboratórios, e conecta-se ao [repositório público GitHub da DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Para adicionar o seu repositório de modelo de Gestor de Recursos Azure, **selecione Adicionar**.

   ![Repo público](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. No painel **dos Repositórios,** introduza as seguintes informações:

   - **Nome**: Introduza um nome de repositório para usar no laboratório.
   - Url de **clone Git**: Introduza o URL de clone Git HTTPS do GitHub ou Azure Repos.
   - **Ramo** (opcional): Introduza o nome do ramo para aceder às definições do modelo do Gestor de Recursos Do Azure.
   - **Ficha de acesso pessoal**: Introduza o sinal de acesso pessoal que é usado para aceder de forma segura ao seu repositório.
     - Para obter o seu token do Azure Repos, sob o seu perfil, selecione **Definições** > de utilizador**Fichas**de acesso pessoal de**segurança** > .
     - Para obter o seu token do GitHub, sob o seu perfil, selecione **Definições** > de**Definições** > **de Acesso Pessoal**.
   - **Caminhos das pastas**: Introduza o caminho da pasta relativo ao seu clone Git URI para as definições do seu artefacto ou as definições do modelo do Gestor de Recursos Azure.

1. Selecione **Guardar**.

   ![Adicione novo repositório](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Uma vez adicionado um modelo de Gestor de Recursos Azure ao laboratório, os utilizadores do laboratório podem criar ambientes usando o modelo.

## <a name="configure-access-rights-for-lab-users"></a>Configure direitos de acesso para utilizadores de laboratório

Os utilizadores de laboratório têm a função **do Leitor** por padrão, por isso não podem alterar os recursos num grupo de recursos ambientais. Por exemplo, não podem parar ou começar os seus recursos.

Para dar aos seus utilizadores de laboratório o papel de **Contribuinte** para que possam editar os recursos nos seus ambientes, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)no painel de **visão geral** do seu laboratório, selecione **Configuração e políticas,** e, em seguida, selecione **as definições**do Laboratório .

1. No painel de definições do **Laboratório,** selecione **Contributor**, e, em seguida, selecione **Save** para conceder permissões de escrita para utilizadores de laboratório.

   ![Configure direitos de acesso ao utilizador do laboratório](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

A próxima secção passa pela criação de ambientes a partir de um modelo de Gestor de Recursos Azure.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Criar ambientes a partir de modelos no portal Azure

Uma vez adicionado um modelo de Gestor de Recursos Azure ao laboratório, os utilizadores do laboratório podem criar ambientes no portal Azure seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório que quiser.

1. Na página do laboratório, selecione **Adicionar**.

1. O **Painel de Base Escolha** as imagens base que pode utilizar, com os modelos do Gestor de Recursos Azure listados primeiro. Selecione o modelo de Gestor de Recursos Azure que deseja.

   ![Escolher uma base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. No painel **Adicionar,** introduza um valor de **nome ambiente** para exibir aos utilizadores do ambiente.

   O modelo do Gestor de Recursos Azure define o resto dos campos de entrada. Se o ficheiro *de modelo azuredeploy.parameter.json* definir valores predefinidos, os campos de entrada mostram esses valores.

   Para parâmetros de *cadeia segura*de tipo, pode usar segredos do seu Cofre de Chaves Azure. Para aprender sobre guardar segredos num cofre chave e usá-los ao criar recursos de laboratório, consulte [os segredos da Loja no Cofre de Chaves Azure](devtest-lab-store-secrets-in-key-vault.md).  

   ![Adicione o painel](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Os seguintes valores de parâmetro não aparecem nos campos de entrada, mesmo que o modelo os especifique. Em vez disso, o formulário mostra campos de entrada em branco onde os utilizadores de laboratório devem entrar em valores ao criar o ambiente.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Selecione **Adicionar** para criar o ambiente.

   O ambiente começa a ser provisionado imediatamente, com o estado exibido na lista das **minhas máquinas virtuais.** O laboratório cria automaticamente um novo grupo de recursos para fornecer todos os recursos definidos no modelo do Gestor de Recursos Azure.

1. Assim que o ambiente for criado, selecione o ambiente na lista das **Minhas máquinas virtuais** para abrir o painel do grupo de recursos e navegar por todos os recursos que o ambiente aprovisionou.

   ![Recursos ambientais](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Também pode expandir o ambiente para ver apenas a lista de VMs do ambiente provisionado.

   ![A minha lista de máquinas virtuais](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Selecione qualquer um dos ambientes para visualizar as ações disponíveis, tais como a aplicação de artefactos, a fixação de discos de dados, a alteração do tempo de paragem automática e muito mais.

   ![Ações ambientais](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatizar a criação de ambiente com a PowerShell

É possível usar o portal Azure para adicionar um único ambiente a um laboratório, mas quando um cenário de desenvolvimento ou teste deve criar múltiplos ambientes, a implementação automatizada é uma melhor experiência.

Antes de prosseguir, certifique-se de que tem um modelo de Gestor de Recursos Azure que define os recursos para criar. [Adicione e configure o modelo num repositório Git,](#configure-your-own-template-repositories)e [adicione o repositório ao laboratório](#add-template-repositories-to-the-lab).

O seguinte guião de amostra cria um ambiente no seu laboratório. Os comentários ajudam-no a entender melhor o guião.

1. Guarde o seguinte script PowerShell para o seu disco rígido como *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Execute o script da seguinte forma, utilizando os seus valores específicos para SubscriçãoId, LabName, ResourceGroupName, RepositoryName, TemplateName (pasta no repo Git) e EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Também pode utilizar o Azure CLI para implementar recursos com modelos de Gestor de Recursos. Para mais informações, consulte [A implantação de recursos com modelos de Gestor de Recursos e ClI Azure](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Apenas um utilizador com permissões do proprietário do laboratório pode criar VMs a partir de um modelo de Gestor de Recursos usando o Azure PowerShell. Se pretender automatizar a criação de VM utilizando um modelo de Gestor de Recursos e tiver apenas permissões de utilizador, pode utilizar a criação de [vm de comando](/cli/azure/lab/vm#az-lab-vm-create)CLI az lab .

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Limitações de modelo de gestor de recursos em Laboratórios DevTest

Considere estas limitações ao utilizar modelos de Gestor de Recursos em Laboratórios DevTest:

- Os modelos do Gestor de Recursos não podem referir-se à maioria dos recursos existentes. Só podem criar novos recursos. Se tiver modelos de Gestor de Recursos que usa fora dos Laboratórios DevTest que se referem aos recursos existentes, não pode usá-los em Laboratórios DevTest. A única exceção é que pode fazer referência a uma rede virtual existente.

- Não é possível criar fórmulas ou imagens personalizadas a partir de VMs de laboratório que foram criados a partir de um modelo de Gestor de Recursos.

- A maioria das políticas não são avaliadas quando se implementam modelos de Gestor de Recursos.

  Por exemplo, pode ter uma política de laboratório que um utilizador pode criar apenas cinco VMs. No entanto, um utilizador pode implementar um modelo de Gestor de Recursos que cria dezenas de VMs. As políticas que não são avaliadas incluem:

  - Número de VMs por utilizador

  - Número de VMs premium por utilizador de laboratório

  - Número de discos premium por utilizador de laboratório

## <a name="next-steps"></a>Passos seguintes
- Assim que criar um VM, pode ligar-se ao VM selecionando **O Connect** no painel de gestão do VM.
- Veja e gere os recursos num ambiente selecionando o ambiente na lista das **minhas máquinas virtuais** no seu laboratório.
- Explore os modelos do Gestor de [Recursos Azure a partir da galeria de modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).
