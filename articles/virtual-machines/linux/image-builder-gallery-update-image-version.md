---
title: Criar uma nova versão de imagem a partir de uma versão de imagem existente usando o construtor de imagens do Azure (pré-visualização)
description: Crie uma nova versão de imagem a partir de uma versão de imagem existente usando o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 31ef53abcf9b416500ee70e42cc3cbd12cb11f35
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159544"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Pré-visualização: Criar uma nova versão de imagem a partir de uma versão de imagem existente usando o construtor de imagens do Azure

Este artigo mostra-lhe como tirar uma versão de imagem existente [Galeria de imagens de partilhado](shared-image-galleries.md), atualizá-lo e publicá-la como uma nova versão de imagem na galeria.

Usaremos um modelo de. JSON de exemplo para configurar a imagem. O ficheiro. JSON, estamos usando aqui é: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Registe-se os recursos
Para utilizar o construtor de imagens do Azure durante a pré-visualização, terá de registar o novo recurso.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verificar o estado de registo de recurso.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verifique o registo.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Se elas não dizem registados, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Definir as variáveis e permissões

Se utilizou [criar uma imagem e distribua uma galeria de imagens partilhado](image-builder-gallery.md) para criar a sua galeria de imagens de partilhado, que já criou algumas das variáveis que precisamos. Caso contrário, configure o algumas variáveis para serem utilizadas para este exemplo.

Para a pré-visualização, construtor de imagens só irá suportar a criação de imagens personalizadas no mesmo grupo de recursos como a imagem gerida de origem. Atualize o nome do grupo de recursos neste exemplo, para ser o mesmo grupo de recursos como a sua imagem gerida de origem.


```azurecli-interactive
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

Criar uma variável para o seu ID de subscrição. Pode obter, utilize `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Obtenha a versão de imagem que pretende atualizar.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Se já tiver sua própria Galeria de imagens de partilhado e não seguia o exemplo anterior, terá de atribuir permissões para o construtor de imagens acessar o grupo de recursos, para que ele possa acessar a galeria.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Modificar o exemplo de helloImage
Pode rever o exemplo que estamos prestes a utilizar ao abrir o ficheiro. JSON aqui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) juntamente com o [referência de modelo do construtor de imagens](image-builder-json.md). 


Transfira o exemplo. JSON e configurá-lo com suas variáveis. 

```azurecli-interactive
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

Submeta a configuração de imagem para o serviço de construtor de imagens de VM.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Inicie a compilação de imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Aguarde até que a imagem foi criada e a replicação antes de passar para o passo seguinte.


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

Crie uma ligação SSH à VM com o endereço IP público da VM.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Deverá ver que a imagem foi personalizada com uma "mensagem do dia" assim que a ligação SSH é estabelecida.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Tipo de `exit` para fechar a ligação de SSH.

Também pode listar as versões de imagem que estão agora disponíveis na sua galeria.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os componentes do ficheiro. JSON usados neste artigo, consulte [referência de modelo do construtor da imagem](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).