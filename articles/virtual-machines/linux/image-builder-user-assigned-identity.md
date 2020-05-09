---
title: Crie uma imagem Virtual Machine e utilize uma identidade gerida atribuída pelo utilizador para aceder a ficheiros no Armazenamento Azure (pré-visualização)
description: Crie uma imagem de máquina virtual utilizando o Azure Image Builder, que pode aceder a ficheiros armazenados no Armazenamento Azure utilizando a identidade gerida atribuída pelo utilizador.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0c0e688c628d553c8b732081f1a8b8debff8846e
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930663"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Crie uma imagem e utilize uma identidade gerida atribuída ao utilizador para aceder a ficheiros no Armazenamento Azure 

O Azure Image Builder suporta a utilização de scripts, ou a cópia de ficheiros de vários locais, tais como o armazenamento GitHub e Azure, etc. Para usá-las, devem ter sido acessíveis externamente ao Azure Image Builder, mas pode proteger as bolhas de armazenamento Azure usando Tokens SAS.

Este artigo mostra como criar uma imagem personalizada utilizando o Azure VM Image Builder, onde o serviço utilizará uma [Identidade Gerida atribuída](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pelo Utilizador para aceder a ficheiros no armazenamento do Azure para a personalização da imagem, sem que tenha de tornar os ficheiros acessíveis ao público ou configurar fichas SAS.

No exemplo abaixo, você vai criar dois grupos de recursos, um será usado para a imagem personalizada, e o outro irá hospedar uma Conta de Armazenamento Azure, que contém um ficheiro script. Isto simula um cenário de vida real, onde pode ter construído artefactos, ou ficheiros de imagem em diferentes contas de armazenamento, fora do Image Builder. Criará uma identidade atribuída ao utilizador e, em seguida, concederá que leia permissões no ficheiro script, mas não definirá qualquer acesso público a esse ficheiro. Em seguida, utilizará o personalizador Shell para descarregar e executar esse script a partir da conta de armazenamento.


> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="register-the-features"></a>Registe as características
Para utilizar o Azure Image Builder durante a pré-visualização, é necessário registar a nova funcionalidade.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verifique o estado do registo da funcionalidade.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verifique o seu registo.


```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
```

Se não disserem registado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Vamos usar algumas peças de informação repetidamente, por isso vamos criar algumas variáveis para armazenar essa informação.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Crie uma variável para o seu ID de subscrição. Pode obter isto `az account show | grep id`usando.

```console
subscriptionID=<Your subscription ID>
```

Crie os grupos de recursos tanto para a imagem como para o armazenamento do script.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

Crie uma identidade atribuída ao utilizador e detete permissões no grupo de recursos.

O Image Builder utilizará a [identidade de utilizador](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity) fornecida para injetar a imagem no grupo de recursos. Neste exemplo, irá criar uma definição de função Azure que tem as ações granulares para realizar a distribuição da imagem. A definição de função será então atribuída à identidade do utilizador.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

Crie o armazenamento e copie o script da amostra a partir do GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

Dê permissão ao Image Builder para criar recursos no grupo de recursos de imagem. O `--assignee` valor é o ID de identidade de utilizador.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>Modificar o exemplo

Descarregue o exemplo .json file e configure-o com as variáveis que criou.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o serviço Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Inicie a construção da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Espere que a construção termine. Isto pode levar cerca de 15 minutos.

## <a name="create-a-vm"></a>Criar uma VM

Crie um VM a partir da imagem. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Depois de criado o VM, inicie uma sessão de SSH com o VM.

```console
ssh aibuser@<publicIp>
```

Deve ver que a imagem foi personalizada com uma Mensagem do Dia assim que a sua ligação SSH estiver estabelecida!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Limpeza

Quando terminar, pode apagar os recursos se já não forem necessários.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Se tiver algum problema em trabalhar com o Azure Image Builder, consulte [a Resolução de Problemas.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)
