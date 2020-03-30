---
title: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente usando o Azure Image Builder (pré-visualização)
description: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente usando o Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 5766e91dc6a17d50c46d396dd8a68d17081e0926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246811"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Pré-visualização: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente usando o Azure Image Builder

Este artigo mostra-lhe como pegar numa versão de imagem existente numa Galeria de [Imagem Partilhada,](shared-image-galleries.md)atualizá-la e publicá-la como uma nova versão de imagem para a galeria.

Vamos usar um modelo de amostra .json para configurar a imagem. O ficheiro .json que estamos a usar está aqui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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


## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões

Se usou [Criar uma imagem e distribuir para uma Galeria](image-builder-gallery.md) de Imagem Partilhada para criar a sua Galeria de Imagem Partilhada, já criou algumas das variáveis que precisamos. Caso contrário, por favor, configurar algumas variáveis a utilizar para este exemplo.

Para pré-visualização, o construtor de imagens só apoiará a criação de imagens personalizadas no mesmo Grupo de Recursos que a imagem gerida pela fonte. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos que a sua fonte gerida imagem.


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

Crie uma variável para o seu ID de subscrição. Pode obter isto `az account show | grep id`usando.

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


Se já tem a sua própria Galeria de Imagem Partilhada, e não seguiu o exemplo anterior, terá de atribuir permissões ao Image Builder para aceder ao Grupo de Recursos, para que possa aceder à galeria.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Modificar o exemplo helloImage
Pode rever o exemplo que estamos prestes a usar abrindo o ficheiro .json aqui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) juntamente com a referência do [modelo de construtor](image-builder-json.md)de imagem . 


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
```

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o Serviço de Construtor de Imagens VM.

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

Aguarde até que a imagem tenha sido construída e replicação antes de passar para o próximo passo.


## <a name="create-the-vm"></a>Crie a VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Crie uma ligação SSH ao VM utilizando o endereço IP público do VM.

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

Para saber mais sobre os componentes do ficheiro .json utilizado neste artigo, consulte a referência do modelo do construtor de [imagem](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).