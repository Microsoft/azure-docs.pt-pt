---
title: Criar uma nova versão de imagem de VM de uma versão de imagem existente usando o construtor de imagens do Azure (visualização)
description: Crie uma nova versão de imagem de VM com base em uma versão de imagem existente usando o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: d226a7b31dc9f8cf219c6d0d0f886fb5b21741a6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976337"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Visualização: criar uma nova versão de imagem de VM de uma versão de imagem existente usando o construtor de imagens do Azure

Este artigo mostra como usar uma versão de imagem existente em uma [Galeria de imagens compartilhadas](shared-image-galleries.md), atualizá-la e publicá-la como uma nova versão de imagem na galeria.

Usaremos um modelo. JSON de exemplo para configurar a imagem. O arquivo. JSON que estamos usando está aqui: [helloImageTemplateforSIGfromSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Registrar os recursos
Para usar o construtor de imagens do Azure durante a versão prévia, você precisa registrar o novo recurso.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verifique o status do registro do recurso.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verifique seu registro.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Se não disser registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões

Se você usou [criar uma imagem e distribuir para uma galeria de imagens compartilhadas](image-builder-gallery.md) para criar sua galeria de imagens compartilhadas, você já criou algumas das variáveis que precisamos. Caso contrário, configure algumas variáveis a serem usadas para este exemplo.

Para visualização, o Image Builder dará suporte apenas à criação de imagens personalizadas no mesmo grupo de recursos que a imagem gerenciada de origem. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos que a imagem gerenciada de origem.


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

Crie uma variável para sua ID de assinatura. Você pode obter isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Obtenha a versão da imagem que você deseja atualizar.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Se você já tiver sua própria galeria de imagens compartilhadas e não seguir o exemplo anterior, será necessário atribuir permissões para que o Image Builder acesse o grupo de recursos, para que possa acessar a galeria.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Modificar exemplo de helloImage
Você pode examinar o exemplo que estamos prestes a usar abrindo o arquivo. JSON aqui: [helloImageTemplateforSIGfromSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) juntamente com a [referência de modelo do Image Builder](image-builder-json.md). 


Baixe o exemplo. JSON e configure-o com suas variáveis. 

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

Envie a configuração de imagem para o serviço do construtor de imagem de VM.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Inicie a compilação da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Aguarde até que a imagem tenha sido criada e replicação antes de passar para a próxima etapa.


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

Crie uma conexão SSH com a VM usando o endereço IP público da VM.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Você deve ver que a imagem foi personalizada com uma "mensagem do dia" assim que a conexão SSH é estabelecida.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Digite `exit` para fechar a conexão SSH.

Você também pode listar as versões de imagem que agora estão disponíveis na galeria.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os componentes do arquivo. JSON usado neste artigo, consulte referência de [modelo do Image Builder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).