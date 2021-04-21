---
title: Criar ambientes multi-VM e recursos PaaS com modelos
description: Saiba como criar ambientes multi-VM e recursos PaaS em Azure DevTest Labs a partir de um modelo de Gestor de Recursos Azure
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: f285acffe642a85fa27792ee51ea67a57f6d35a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790118"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager

Os ambientes Azure DevTest Labs permitem aos utilizadores implementar facilmente infraestruturas complexas de forma consistente dentro dos limites do laboratório. Pode utilizar [modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) para criar ambientes com conjuntos de recursos em DevTest Labs. Estes ambientes podem conter quaisquer recursos Azure que os modelos do Gestor de Recursos possam criar.

Pode adicionar facilmente [uma máquina virtual (VM)](devtest-lab-add-vm.md) de cada vez a um laboratório utilizando o [portal Azure](https://portal.azure.com). No entanto, cenários como aplicações web multi-camadas ou uma fazenda SharePoint precisam de um mecanismo para criar vários VMs num único passo. Ao utilizar os modelos do Azure Resource Manager, pode definir a infraestrutura e configuração da sua solução Azure e implementar repetidamente vários VMs num estado consistente.

Os modelos do Gestor de Recursos Azure também fornecem os seguintes benefícios:

- Os modelos do Gestor de Recursos Azure são carregados diretamente do seu repositório de controlo de fontes GitHub ou Azure Repos.
- Os seus utilizadores podem criar um ambiente escolhendo um modelo de Gestor de Recursos Azure configurado a partir do portal Azure, tal como fazem com outros tipos de [bases VM](devtest-lab-comparing-vm-base-image-types.md).
- Você pode fornecendo recursos Azure PaaS, bem como IaaS VMs em um ambiente a partir de um modelo de Gestor de Recursos Azure.
- Você pode rastrear o custo de ambientes no laboratório, além de VMs individuais criados por outros tipos de bases. Os recursos paaS são criados e aparecerão no rastreio de custos. No entanto, a paragem automática da VM não se aplica aos recursos paaS.

Para saber mais sobre os benefícios de usar modelos de Gestor de Recursos para implementar, atualizar ou eliminar muitos recursos de laboratório numa única operação, consulte [benefícios de usar modelos de Gestor de Recursos.](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)

> [!NOTE]
> Quando utiliza um modelo de Gestor de Recursos como base para criar VMs de laboratório, existem algumas diferenças entre a criação de múltiplos VMs ou um único VM. Para obter mais informações, consulte [utilize o modelo de Gestor de Recursos Azure de uma máquina virtual.](devtest-lab-use-resource-manager-template.md)
>

## <a name="use-devtest-labs-public-environments"></a>Use ambientes públicos de Laboratórios DevTest
A Azure DevTest Labs tem um [repositório público de modelos do Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que você pode usar para criar ambientes sem ter que se conectar a uma fonte externa do GitHub. Este repositório público é semelhante ao repositório público de artefactos que está disponível no portal Azure para todos os laboratórios que cria. O repositório de ambiente permite-lhe começar rapidamente com modelos de ambiente pré-autoria que têm poucos parâmetros de entrada. Estes modelos proporcionam-lhe uma experiência de início suave para os recursos paaS dentro de laboratórios.

No repositório público, a equipa da DevTest Labs e outros criaram e partilharam modelos frequentemente utilizados como Azure Web Apps, Service Fabric Cluster e um ambiente de desenvolvimento SharePoint Farm. Pode usar estes modelos diretamente ou personalizá-los de acordo com as suas necessidades. Para obter mais informações, consulte [Configure e utilize ambientes públicos em DevTest Labs](devtest-lab-configure-use-public-environments.md). Depois de criar os seus próprios modelos, pode armazená-los neste repositório para partilhá-los com outros, ou configurar o seu próprio repositório Git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Crie os seus próprios repositórios de modelo

Como uma das melhores práticas com infraestruturas como código e configuração como código, você deve gerir modelos de ambiente no controlo de fontes. A Azure DevTest Labs segue esta prática, e carrega todos os modelos do Azure Resource Manager diretamente dos seus repositórios GitHub ou Azure Repos. Como resultado, pode utilizar modelos de Gestor de Recursos em todo o ciclo de libertação, desde o ambiente de teste até ao ambiente de produção.

Existem várias regras a seguir para organizar os seus modelos de Gestor de Recursos Azure num repositório:

- Você deve nomear o ficheiro de modelo principal *azuredeploy.jsem*.

- Se pretender utilizar os valores de parâmetro definidos num ficheiro de parâmetros, o ficheiro de parâmetros deve ser nomeado *azuredeploy.parameters.jsem*.

  Pode utilizar os parâmetros `_artifactsLocation` e `_artifactsLocationSasToken` construir os parâmetrosLink URI value, permitindo que a DevTest Labs gere automaticamente modelos aninhados. Para obter mais informações, consulte [implementar modelos aninhados do Gestor de Recursos Azure para testar ambientes.](deploy-nested-template-environments.md)

- Pode definir metadados para especificar o nome e descrição do modelo num ficheiro nomeado *metadata.jsem*, da seguinte forma:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Principais ficheiros de modelo do Gestor de Recursos Azure](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Adicione repositórios de modelo ao laboratório

Depois de criar e configurar o seu repositório, pode adicioná-lo ao seu laboratório utilizando o portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o laboratório que quiser.
1. No painel de **visão geral** do laboratório, selecione **Configuração e políticas**.

   ![Configuração e políticas](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. A partir da lista **de configurações e definições** de políticas, selecione **Repositórios**. O repositório **de artefactos públicos** é gerado automaticamente para todos os laboratórios, e conecta-se ao [repositório público gitHub do DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Para adicionar o seu repositório de modelo de Gestor de Recursos Azure, **selecione Adicionar**.

   ![Repo público](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. No painel **de repositórios,** insira as seguintes informações:

   - **Nome**: Introduza um nome de repositório para utilizar no laboratório.
   - **URL do clone git**: Introduza o URL do clone git HTTPS do GitHub ou Azure Repos.
   - **Ramo** (opcional): Introduza o nome do ramo para aceder às definições do seu modelo de Gestor de Recursos Azure.
   - **Ficha de acesso pessoal**: Introduza o token de acesso pessoal que é usado para aceder de forma segura ao seu repositório.
     - Para obter o seu token da Azure Repos, no seu perfil, selecione **Configurações de** Acesso pessoal de Segurança  >    >  **do** Utilizador .
     - Para obter o seu token do GitHub, no seu perfil, selecione **Definições de**  >  **Definições de Programação**  >  **Fichas de acesso pessoal**.
   - **Caminhos de pastas**: Introduza o caminho da pasta que é relativo ao seu URI clone Git para definições de artefactos ou definições do seu modelo de Gestor de Recursos Azure.

1. Selecione **Guardar**.

   ![Adicione novo repositório](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Uma vez que adicione um modelo de Gestor de Recursos Azure ao laboratório, os seus utilizadores de laboratório podem criar ambientes usando o modelo.

## <a name="configure-access-rights-for-lab-users"></a>Configure direitos de acesso para utilizadores de laboratório

Os utilizadores de laboratório têm o papel **de Reader** por padrão, por isso não podem alterar os recursos num grupo de recursos ambientais. Por exemplo, não podem parar ou iniciar os seus recursos.

Para dar aos seus utilizadores de laboratório **função contributiva** para que possam editar os recursos nos seus ambientes, siga estes passos:

1. No [portal Azure](https://portal.azure.com), no painel **de visão geral** do seu laboratório, selecione **Configuração e políticas**, e, em seguida, selecione as **definições de Laboratório**.

1. No painel de **definições** do Laboratório, selecione **Contributor**, e, em seguida, selecione **Save** para conceder permissões de escrita aos utilizadores de laboratório.

   ![Configure direitos de acesso ao utilizador do laboratório](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

A secção seguinte passa pela criação de ambientes a partir de um modelo de Gestor de Recursos Azure.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Criar ambientes a partir de modelos no portal Azure

Uma vez que adicione um modelo de Gestor de Recursos Azure ao laboratório, os seus utilizadores de laboratório podem criar ambientes no portal Azure seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório que quiser.

1. Na página do laboratório, selecione **Add**.

1. O **painel de base Escolha um** painel de base exibe as imagens base que pode utilizar, com os modelos do Gestor de Recursos Azure listados primeiro. Selecione o modelo de Gestor de Recursos Azure que pretende.

   ![Escolher uma base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. No painel **Add,** introduza um valor **de nome Ambiente** para mostrar aos utilizadores do ambiente.

   O modelo Azure Resource Manager define o resto dos campos de entrada. Se oazuredeploy.parameter.jsdo modelo *no* ficheiro definir valores predefinidos, os campos de entrada mostram esses valores.

   Para parâmetros de *tipo de corda segura,* pode utilizar segredos do seu Cofre de Chaves Azure. Para aprender sobre guardar segredos num cofre chave e usá-los ao criar recursos de laboratório, consulte os segredos da [Loja no Cofre da Chave Azure.](devtest-lab-store-secrets-in-key-vault.md)  

   ![Adicione painel](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Os seguintes valores de parâmetro não aparecem nos campos de entrada, mesmo que o modelo os especifique. Em vez disso, o formulário mostra campos de entrada em branco onde os utilizadores de laboratório devem introduzir valores ao criar o ambiente.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. **Selecione Adicionar** para criar o ambiente.

   O ambiente começa a provisões imediatamente, com o estado de exibição na lista **de máquinas virtuais My.** O laboratório cria automaticamente um novo grupo de recursos para a provisionar todos os recursos definidos no modelo Azure Resource Manager.

1. Assim que o ambiente for criado, selecione o ambiente na lista **my virtual machines** para abrir o painel de grupo de recursos e navegar em todos os recursos que o ambiente aprovisionado.

   ![Recursos ambientais](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Também pode expandir o ambiente para ver apenas a lista de VMs do ambiente aprovisionado.

   ![A minha lista de máquinas virtuais](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Selecione qualquer um dos ambientes para visualizar as ações disponíveis, tais como a aplicação de artefactos, a anexação de discos de dados, a alteração do tempo de paragem automática, e muito mais.

   ![Ações ambientais](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatizar a criação de ambiente com a PowerShell

É possível usar o portal Azure para adicionar um único ambiente a um laboratório, mas quando um cenário de desenvolvimento ou teste deve criar vários ambientes, a implementação automatizada é uma melhor experiência.

Antes de prosseguir, certifique-se de que tem um modelo de Gestor de Recursos Azure que define os recursos para criar. [Adicione e configuure o modelo num repositório de Git](#configure-your-own-template-repositories)e [adicione o repositório ao laboratório.](#add-template-repositories-to-the-lab)

O seguinte guião de amostra cria um ambiente no seu laboratório. Os comentários ajudam-no a compreender melhor o guião.

1. Guarde o seguinte script PowerShell para o seu disco rígido à medida *quedeployenv.ps1*.

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
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
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

1. Execute o script da seguinte forma, utilizando os seus valores específicos para SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (pasta no repo Git) e EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Também pode usar o Azure CLI para implementar recursos com modelos de Gestor de Recursos. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Apenas um utilizador com permissões de proprietário de laboratório pode criar VMs a partir de um modelo de Gestor de Recursos utilizando a Azure PowerShell. Se pretender automatizar a criação de VM utilizando um modelo de Gestor de Recursos e tiver apenas permissões de utilizador, pode utilizar o comando CLI [az lab vm criar](/cli/azure/lab/vm#az_lab_vm_create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Limitações do modelo do Gestor de Recursos em Laboratórios DevTest

Considere estas limitações ao utilizar modelos de Gestor de Recursos em Laboratórios DevTest:

- Não é possível criar fórmulas ou imagens personalizadas a partir de VMs de laboratório que foram criados a partir de um modelo de Gestor de Recursos.

- A maioria das políticas não são avaliadas quando implementa modelos de Gestor de Recursos.

Por exemplo, pode ter uma política de laboratório que um utilizador pode criar apenas cinco VMs. No entanto, um utilizador pode implementar um modelo de Gestor de Recursos que cria dezenas de VMs. As políticas que não são avaliadas incluem:

  - Número de VMs por utilizador

  - Número de VMs premium por utilizador de laboratório

  - Número de discos premium por utilizador de laboratório

## <a name="next-steps"></a>Passos seguintes
- Uma vez criado um VM, pode ligar-se ao VM selecionando **Connect** no painel de gestão do VM.
- Veja e gere recursos num ambiente selecionando o ambiente na lista **das minhas máquinas virtuais** no seu laboratório.
- Explore os [modelos do Gestor de Recursos Azure da galeria de modelos Azure Quickstart.](https://github.com/Azure/azure-quickstart-templates)
