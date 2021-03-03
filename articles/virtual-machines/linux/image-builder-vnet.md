---
title: Utilize o Azure Image Builder para VMs Linux permitindo o acesso a um Azure VNET existente (pré-visualização)
description: Criar imagens Linux VM com Azure Image Builder permitindo o acesso a um Azure VNET existente
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: b523168429aa05a148a6e814881d1338a1bb39cf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695622"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Use O Azure Image Builder para VMs Linux permitindo o acesso a um Azure VNET existente

Este artigo mostra-lhe como pode usar o Azure Image Builder para criar uma imagem básica personalizada do Linux que tenha acesso aos recursos existentes num VNET. A construção VM que cria é implantada para um VNET novo ou existente que especifique na sua subscrição. Quando utiliza um Azure VNET existente, o serviço Azure Image Builder não requer conectividade de rede pública.

> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>Registar as funcionalidades

Em primeiro lugar, tem de se inscrever no Serviço de Construtores de Imagem Azure. O registo concede a permissão de serviço para criar, gerir e eliminar um grupo de recursos de encenação. O serviço também tem o direito de adicionar recursos ao grupo que são necessários para a construção de imagem.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões 

Vai usar algumas informações repetidamente. Crie algumas variáveis para armazenar essa informação.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Crie o grupo de recursos.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Configurar as redes

Se não tiver um VNET\Subnet\NSG existente, utilize o seguinte script para criar um.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Adicionar regra do Grupo de Segurança de Rede

Esta regra permite a conectividade do equilibrador de carga Azure Image Builder para o VM proxy. A porta 60001 é para Linux OSs e a porta 60000 é para OSs Windows. O VM proxy liga-se ao VM de construção utilizando a porta 22 para Linux OSs ou a porta 5986 para OSs do Windows.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Desativar a Política de Serviços Privados na sub-rede

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

Para obter mais informações sobre a rede do Image Builder, consulte [as opções de networking do Azure Image Builder Service](image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Modifique o modelo de exemplo e crie papel

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Definir permissões no grupo de recursos

O Image Builder utilizará a [identidade do utilizador](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) fornecida para injetar a imagem na Galeria de Imagens Partilhadas Azure (SIG). Neste exemplo, irá criar uma definição de papel Azure que tem as ações granulares para executar a distribuição da imagem para o SIG. A definição de função será então atribuída à identidade do utilizador.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

Em vez de conceder ao Image Builder uma menor granularidade e um privilégio acrescido, pode criar duas funções. Um dá ao construtor permissões para criar uma imagem, o outro permite-lhe ligar o VM de construção e o equilibrador de carga ao seu VNET.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

Para obter mais informações sobre permissões, consulte [permissões do Serviço de Construtores de Imagem Configure Azure utilizando permissões do Serviço de Construtores de Imagem Azure da Azure ou](image-builder-permissions-cli.md) [Configure Image Builder service utilizando powerShell](image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>Criar a imagem

Submeta a configuração de imagem para o serviço Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Inicie a construção da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

Criar a imagem e replicá-la em ambas as regiões pode demorar algum tempo. Aguarde até que esta peça esteja terminada antes de passar a criar um VM.


## <a name="create-the-vm"></a>Criar a VM

Crie um VM a partir da versão de imagem que foi criada pelo Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

SSH na VM.

```bash
ssh aibuser@<publicIpAddress>
```

Deve ver que a imagem foi personalizada com uma *Mensagem do Dia* assim que a sua ligação SSH estiver estabelecida!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser agora tentar recustomizar a versão de imagem para criar uma nova versão da mesma imagem, salte os próximos passos e continue a utilizar o [Azure Image Builder para criar outra versão de imagem](image-builder-gallery-update-image-version.md).


O seguinte elimina a imagem que foi criada, juntamente com todos os outros ficheiros de recursos. Certifique-se de que terminou com esta implantação antes de apagar os recursos.

Ao eliminar os recursos da galeria de imagens, é necessário eliminar todas as versões de imagem antes de poder eliminar a definição de imagem utilizada para as criar. Para eliminar uma galeria, primeiro tem de eliminar todas as definições de imagem na galeria.

Apague o modelo de construtor de imagens.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

Eliminar permissões atribuições, funções e identidade
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Elimine o grupo de recursos.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Se criou um VNET para este arranque rápido, pode eliminar o VNET se já não estiver a ser utilizado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [galerias de imagem partilhadas Azure.](../shared-image-galleries.md)
