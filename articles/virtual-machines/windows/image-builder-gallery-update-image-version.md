---
title: Criar uma nova versão de imagem de uma versão de imagem existente usando o construtor de imagens do Azure (visualização)
description: Crie uma nova versão de imagem de VM com base em uma versão de imagem existente usando o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 160de4521f4035ba3abd01137955cafc27071a05
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976099"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Visualização: criar uma nova versão de imagem de VM de uma versão de imagem existente usando o construtor de imagens do Azure

Este artigo mostra como usar uma versão de imagem existente em uma [Galeria de imagens compartilhadas](shared-image-galleries.md), atualizá-la e publicá-la como uma nova versão de imagem na galeria.

Usaremos um modelo. JSON de exemplo para configurar a imagem. O arquivo. JSON que estamos usando está aqui: [helloImageTemplateforSIGfromWinSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

> [!IMPORTANT]
> O construtor de imagem do Azure está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
az provider show -n Microsoft.Compute | grep registrationState
```

Se não disser registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões

Se você usou [criar uma imagem e distribuir para uma galeria de imagens compartilhadas](image-builder-gallery.md) para criar sua galeria de imagens compartilhadas, você já criou as variáveis que precisamos. Caso contrário, configure algumas variáveis a serem usadas para este exemplo.

Para visualização, o Image Builder dará suporte apenas à criação de imagens personalizadas no mesmo grupo de recursos que a imagem gerenciada de origem. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos que a imagem gerenciada de origem.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Crie uma variável para sua ID de assinatura. Você pode obter isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Obtenha a versão da imagem que você deseja atualizar.

```azurecli-interactive
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
Você pode examinar o exemplo que estamos prestes a usar abrindo o arquivo. JSON aqui: [helloImageTemplateforSIGfromSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) juntamente com a [referência de modelo do Image Builder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


Baixe o exemplo. JSON e configure-o com suas variáveis. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o serviço do construtor de imagem de VM.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Inicie a compilação da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Aguarde até que a imagem tenha sido criada e replicação antes de passar para a próxima etapa.


## <a name="create-the-vm"></a>Crie a VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Verificar a personalização
Crie uma conexão de Área de Trabalho Remota com a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt cmd e digite:

```console
dir c:\
```

Agora você deve ver dois diretórios:
- `buildActions` que foi criado na primeira versão da imagem.
- `buildActions2` que foi criado como parte acima, atualizando a primeira versão da imagem para criar a segunda versão da imagem.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os componentes do arquivo. JSON usado neste artigo, consulte referência de [modelo do Image Builder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).