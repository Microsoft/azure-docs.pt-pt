---
title: Criar uma nova versão de imagem VM a partir de uma versão de imagem existente usando Azure Image Builder (pré-visualização)
description: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente utilizando o Azure Image Builder em Linux.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: danis
ms.openlocfilehash: 13cdb444046fd8d3138e1c33ed65495e9bfe5e98
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685073"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>Pré-visualização: Criar uma nova versão de imagem VM a partir de uma versão de imagem existente usando Azure Image Builder em Linux

Este artigo mostra-lhe como pegar uma versão de imagem existente numa [Galeria de Imagens Partilhadas,](../shared-image-galleries.md)atualizá-la e publicá-la como uma nova versão de imagem para a galeria.

Vamos usar um modelo de amostra .json para configurar a imagem. O ficheiro .json que estamos a usar está aqui: [helloImageTemplateforSIGfromSIG.jsem](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Registar as funcionalidades
Para utilizar o Azure Image Builder durante a pré-visualização, tem de registar a nova funcionalidade.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verifique o estado do registo de funcionalidades.

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

Se não disserem registados, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões

Se usou [Criar uma imagem e distribuir para uma Galeria de Imagens Partilhadas](image-builder-gallery.md) para criar a sua Galeria de Imagens Partilhadas, já criou algumas das variáveis de que precisamos. Caso contrário, por favor, confiem algumas variáveis a utilizar para este exemplo.


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Crie uma variável para o seu ID de subscrição. Pode usar `az account show | grep id` isto.

```console
subscriptionID=<Subscription ID>
```

Obtenha a versão de imagem que pretende atualizar.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Criar uma identidade atribuída ao utilizador e definir permissões no grupo de recursos
Como tinha definido a identidade do utilizador no exemplo anterior, basta obter o ID de recurso do mesmo, este será então anexado ao modelo.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Se já tem a sua própria Galeria de Imagens Partilhadas, e não seguiu o exemplo anterior, terá de atribuir permissões para o Image Builder aceder ao Grupo de Recursos, para que possa aceder à galeria. Por favor, reveja os passos na [Imagem Criar uma imagem e distribua para um exemplo de Galeria de Imagens Partilhada.](image-builder-gallery.md)


## <a name="modify-helloimage-example"></a>Modifique o exemplo helloImage
Pode rever o exemplo que estamos prestes a usar abrindo o ficheiro .json aqui: [helloImageTemplateforSIGfromSIG.js](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) juntamente com a referência do [modelo do Image Builder](image-builder-json.md). 


Descarregue o exemplo .json e configure-o com as suas variáveis. 

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Criar a imagem

Submeta a configuração de imagem para o Serviço de Construtor de Imagem VM.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Inicie a construção da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Aguarde até que a imagem tenha sido construída e replicada antes de passar para o passo seguinte.


## <a name="create-the-vm"></a>Criar a VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Criar uma ligação SSH ao VM utilizando o endereço IP público do VM.

```console
ssh azureuser@<pubIp>
```

Deve ver que a imagem foi personalizada com uma "Mensagem do Dia" assim que a sua ligação SSH estiver estabelecida.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Digite `exit` para fechar a ligação SSH.

Também pode listar as versões de imagem que já estão disponíveis na sua galeria.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os componentes do ficheiro .json utilizado neste artigo, consulte a [referência do modelo do construtor de imagem](../linux/image-builder-json.md).