---
title: Configure permissões do Serviço de Construtores de Imagem Azure usando O Azure CLI
description: Configure requisitos para O Serviço de Construtor de Imagem Azure VM, incluindo permissões e privilégios usando O Azure CLI
author: cynthn
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: accefe6644f8959bb9426a154d4f33e0834fc06c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674883"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Configure permissões do Serviço de Construtores de Imagem Azure usando O Azure CLI

O Azure Image Builder Service requer a configuração de permissões e privilégios antes de construir uma imagem. As secções seguintes detalham como configurar possíveis cenários utilizando o Azure CLI.

> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registar as funcionalidades

Em primeiro lugar, tem de se inscrever no Serviço de Construtores de Imagem Azure. O registo concede a permissão de serviço para criar, gerir e eliminar um grupo de recursos de encenação. O serviço também tem o direito de adicionar recursos ao grupo que são necessários para a construção de imagem.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador Azure

O Azure Image Builder requer que crie uma [identidade gerida atribuída pelo utilizador Azure.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) O Azure Image Builder utiliza a identidade gerida atribuída pelo utilizador para ler imagens, escrever imagens e aceder às contas de armazenamento do Azure. Concede a permissão de identidade para fazer ações específicas na sua subscrição.

> [!NOTE]
> Anteriormente, o Azure Image Builder usou o nome principal do serviço Azure Image Builder (SPN) para conceder permissões aos grupos de recursos de imagem. A utilização do SPN será depreciada. Em vez disso, utilize uma identidade gerida atribuída pelo utilizador.

O exemplo a seguir mostra-lhe como criar uma identidade gerida atribuída pelo utilizador Azure. Substitua as definições do espaço reservado para definir as suas variáveis.

| Definições | Descrição |
|---------|-------------|
| \<Resource group\> | Grupo de recursos onde criar a identidade gerida atribuída pelo utilizador. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
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

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Exemplo de papel do VNET Azure existente

O exemplo a seguir cria um papel Azure para utilizar e distribuir uma imagem VNET existente. Em seguida, concede o papel personalizado à identidade gerida atribuída pelo utilizador para O Azure Image Builder.

Para simplificar a substituição de valores no exemplo, dei primeiro primeiro as seguintes variáveis. Substitua as definições do espaço reservado para definir as suas variáveis.

| Definições | Descrição |
|---------|-------------|
| \<Subscription ID\> | O seu ID de assinatura Azure |
| \<Resource group\> | Grupo de recursos VNET |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Utilização de identidade gerida para acesso ao armazenamento Azure

Se pretender autenticar-se de forma irracional com o Azure Storage e utilizar recipientes privados, o Azure Image Builder an Azure necessita de uma identidade gerida atribuída ao utilizador. O Azure Image Builder utiliza a identidade para autenticar com o Azure Storage.

> [!NOTE]
> O Azure Image Builder utiliza apenas a identidade no tempo de submissão do modelo de imagem. A construção VM não tem acesso à identidade durante a construção de imagens.

Utilize o CLI Azure para criar a identidade gerida atribuída ao utilizador.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

No modelo de Image Builder, é necessário fornecer a identidade gerida atribuída pelo utilizador.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Substitua as seguintes definições de espaço reservado:

| Definições | Descrição |
|---------|-------------|
| \<Region\> | Região do modelo |
| \<Resource group\> | Grupo de recursos |
| \<Storage account container\> | Nome do contentor da conta de armazenamento |
| \<Subscription ID\> | Subscrição do Azure |

Para obter mais informações utilizando uma identidade gerida atribuída ao utilizador, consulte a [Criação de uma Imagem Personalizada que utilizará um Azure User-Assigned Identidade Gerida para aceder de forma aparente aos ficheiros Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage#create-a-custom-image-that-will-use-an-azure-user-assigned-managed-identity-to-seemlessly-access-files-azure-storage). O quickstart percorre como criar e configurar a identidade gerida atribuída pelo utilizador para aceder a uma conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [a visão geral do Azure Image Builder](../image-builder-overview.md).