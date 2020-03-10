---
title: Crie uma imagem Virtual Machine e utilize uma identidade gerida atribuída pelo utilizador para aceder a ficheiros no Armazenamento Azure (pré-visualização)
description: Crie uma imagem de máquina virtual utilizando o Azure Image Builder, que pode aceder a ficheiros armazenados no Armazenamento Azure utilizando a identidade gerida atribuída pelo utilizador.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: f3990037d75f9f77eaedc7ec4049f14814216d9c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944967"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Crie uma imagem e utilize uma identidade gerida atribuída ao utilizador para aceder a ficheiros no Armazenamento Azure 

O Azure Image Builder suporta a utilização de scripts, ou a cópia de ficheiros de vários locais, tais como o armazenamento GitHub e Azure, etc. Para usá-las, devem ter sido acessíveis externamente ao Azure Image Builder, mas pode proteger as bolhas de armazenamento Azure usando Tokens SAS.

Este artigo mostra como criar uma imagem personalizada utilizando o Azure VM Image Builder, onde o serviço utilizará uma [Identidade Gerida atribuída](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pelo Utilizador para aceder a ficheiros no armazenamento do Azure para a personalização da imagem, sem que tenha de tornar os ficheiros acessíveis ao público ou configurar fichas SAS.

No exemplo abaixo, você vai criar dois grupos de recursos, um será usado para a imagem personalizada, e o outro irá hospedar uma Conta de Armazenamento Azure, que contém um ficheiro script. Isto simula um cenário de vida real, onde pode ter construído artefactos, ou ficheiros de imagem em diferentes contas de armazenamento, fora do Image Builder. Criará uma identidade atribuída ao utilizador e, em seguida, concederá que leia permissões no ficheiro script, mas não definirá qualquer acesso público a esse ficheiro. Em seguida, utilizará o personalizador Shell para descarregar e executar esse script a partir da conta de armazenamento.


> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

az provider show -n Microsoft.Storage | grep registrationState
```

Se não disserem registado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Vamos usar algumas peças de informação repetidamente, por isso vamos criar algumas variáveis para armazenar essa informação.


```azurecli-interactive
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

Crie uma variável para o seu ID de subscrição. Pode obter isto usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Crie os grupos de recursos tanto para a imagem como para o armazenamento do script.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
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



Dê permissão ao Image Builder para criar recursos no grupo de recursos de imagem. O valor `--assignee` é o ID de registo de aplicações para o serviço Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Criar identidade gerida atribuída ao utilizador

Crie a identidade e atribua permissões para a conta de armazenamento do script. Para mais informações, consulte a [Identidade Gerida Atribuída](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)pelo Utilizador .

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Modificar o exemplo

Descarregue o exemplo .json file e configure-o com as variáveis que criou.

```azurecli-interactive
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

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Depois de criado o VM, inicie uma sessão de SSH com o VM.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Deve ver que a imagem foi personalizada com uma Mensagem do Dia assim que a sua ligação SSH estiver estabelecida!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Limpeza

Quando terminar, pode apagar os recursos se já não forem necessários.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Se tiver algum problema em trabalhar com o Azure Image Builder, consulte [a Resolução de Problemas.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)