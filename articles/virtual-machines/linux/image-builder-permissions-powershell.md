---
title: Configure permissões do Serviço de Construtor de Imagem Azure usando PowerShell
description: Configure requisitos para o Azure VM Image Builder Service, incluindo permissões e privilégios usando o PowerShell
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 9f8793b6ea0ba454b66c525c2d53c1de2197d539
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102440212"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>Configure permissões do Serviço de Construtor de Imagem Azure usando PowerShell

Quando se regista para o (AIB), este concede ao Serviço AIB permissão para criar, gerir e eliminar um grupo de recursos de encenação (IT_*), e tem direitos de adicionar recursos a ele, que são necessários para a construção de imagem. Isto é feito por um Nome Principal de Serviço AIB (SPN) sendo disponibilizado na sua subscrição durante uma inscrição bem sucedida.

Para permitir que o Azure VM Image Builder distribua imagens para as imagens geridas ou para uma Galeria de Imagens Partilhadas, terá de criar uma identidade atribuída ao utilizador Azure que tenha permissões para ler e escrever imagens. Se estiver a aceder ao armazenamento do Azure, então isto necessitará de permissões para ler recipientes privados ou públicos.

Deve configurar permissões e privilégios antes de construir uma imagem. As secções seguintes detalham como configurar possíveis cenários utilizando o PowerShell.

> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>Registar as funcionalidades

Em primeiro lugar, tem de se inscrever no Serviço de Construtores de Imagem Azure. O registo concede a permissão de serviço para criar, gerir e eliminar um grupo de recursos de encenação. O serviço também tem o direito de adicionar recursos ao grupo que são necessários para a construção de imagem.

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador Azure

O Azure Image Builder requer que crie uma [identidade gerida atribuída pelo utilizador Azure.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) O Azure Image Builder utiliza a identidade gerida atribuída pelo utilizador para ler imagens, escrever imagens e aceder às contas de armazenamento do Azure. Concede a permissão de identidade para fazer ações específicas na sua subscrição.

> [!NOTE]
> Anteriormente, o Azure Image Builder usou o nome principal do serviço Azure Image Builder (SPN) para conceder permissões aos grupos de recursos de imagem. A utilização do SPN será depreciada. Em vez disso, utilize uma identidade gerida atribuída pelo utilizador.

O exemplo a seguir mostra-lhe como criar uma identidade gerida atribuída pelo utilizador Azure. Substitua as definições do espaço reservado para definir as suas variáveis.

| Definições | Descrição |
|---------|-------------|
| \<Resource group\> | Grupo de recursos onde criar a identidade gerida atribuída pelo utilizador. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

Para obter mais informações sobre as identidades atribuídas pelo utilizador Azure, consulte a documentação [de identidade gerida atribuída pelo utilizador Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) sobre como criar uma identidade.

## <a name="allow-image-builder-to-distribute-images"></a>Permitir que o Construtor de Imagem distribua imagens

Para que o Azure Image Builder distribua imagens (Imagens Geridas / Galeria de Imagens Partilhadas), o serviço Azure Image Builder deve ser autorizado a injetar as imagens nestes grupos de recursos. Para conceder as permissões necessárias, é necessário criar uma identidade gerida atribuída ao utilizador e conceder-lhe direitos sobre o grupo de recursos onde a imagem é construída. O Azure Image Builder **não tem** permissão para aceder a recursos em outros grupos de recursos na subscrição. Precisa de tomar ações explícitas para permitir o acesso para evitar que as suas construções falhem.

Não é necessário conceder aos contribuintes de identidade geridos atribuídos pelo utilizador no grupo de recursos para distribuir imagens. No entanto, a identidade gerida atribuída pelo utilizador necessita das seguintes permissões Azure `Actions` no grupo de recursos de distribuição:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Se distribuir para uma galeria de imagens partilhada, também precisa:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Permissão para personalizar imagens existentes

Para que o Azure Image Builder construa imagens a partir de imagens personalizadas de origem (Imagens Geridas / Galeria de Imagens Partilhadas), o serviço Azure Image Builder deve ser autorizado a ler as imagens nestes grupos de recursos. Para conceder as permissões necessárias, é necessário criar uma identidade gerida atribuída ao utilizador e conceder-lhe direitos sobre o grupo de recursos onde a imagem está localizada.

Construir a partir de uma imagem personalizada existente:

```Actions
Microsoft.Compute/galleries/read
```

Construir a partir de uma versão existente da Galeria de Imagens Partilhadas:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Permissão para personalizar imagens nos seus VNETs

O Azure Image Builder tem a capacidade de implementar e utilizar um VNET existente na sua subscrição, permitindo assim o acesso às personalizações a recursos conectados.

Não é necessário conceder aos contribuintes de identidade geridos atribuídos pelo utilizador no grupo de recursos para implantar um VM num VNET existente. No entanto, a identidade gerida atribuída pelo utilizador necessita das seguintes permissões Azure `Actions` no grupo de recursos VNET:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Criar uma definição de papel Azure

Os exemplos a seguir criam uma definição de papel Azure a partir das ações descritas nas secções anteriores. Os exemplos são aplicados ao nível do grupo de recursos. Avalie e teste se os exemplos são granulares o suficiente para as suas necessidades. Para o seu cenário, poderá ser necessário aussi-lo para uma galeria de imagens partilhada específica.

As ações de imagem permitem ler e escrever. Decida o que é apropriado para o seu ambiente. Por exemplo, criar uma função para permitir que o Azure Image Builder leia imagens do grupo de recursos *exemplo-rg-1* e escreva imagens para o grupo de recursos *exemplo-rg-2*.

### <a name="custom-image-azure-role-example"></a>Exemplo de papel de Azure de imagem personalizada

O exemplo a seguir cria um papel Azure para usar e distribuir uma imagem personalizada de origem. Em seguida, concede o papel personalizado à identidade gerida atribuída pelo utilizador para O Azure Image Builder.

Para simplificar a substituição de valores no exemplo, dei primeiro primeiro as seguintes variáveis. Substitua as definições do espaço reservado para definir as suas variáveis.

| Definições | Descrição |
|---------|-------------|
| \<Subscription ID\> | O seu ID de assinatura Azure |
| \<Resource group\> | Grupo de recursos para imagem personalizada |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>Exemplo de papel do VNET Azure existente

O exemplo a seguir cria um papel Azure para utilizar e distribuir uma imagem VNET existente. Em seguida, concede o papel personalizado à identidade gerida atribuída pelo utilizador para O Azure Image Builder.

Para simplificar a substituição de valores no exemplo, dei primeiro primeiro as seguintes variáveis. Substitua as definições do espaço reservado para definir as suas variáveis.

| Definições | Descrição |
|---------|-------------|
| \<Subscription ID\> | O seu ID de assinatura Azure |
| \<Resource group\> | Grupo de recursos VNET |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [a visão geral do Azure Image Builder](../image-builder-overview.md).
