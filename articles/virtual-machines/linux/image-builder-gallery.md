---
title: Utilizar o construtor de imagens do Azure com uma galeria de imagens para máquinas virtuais do Linux (pré-visualização)
description: Crie imagens do Linux com o construtor de imagens do Azure e Galeria de imagens de partilhado.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: d29fa8700cb1f530cfe85f0bdf6852d75ec1613e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65508162"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Pré-visualização: Criar uma imagem do Linux e distribuí-la a uma galeria de imagem partilhada 

Este artigo mostra-lhe como pode utilizar o construtor de imagens do Azure para criar uma versão de imagem num [Galeria de imagens de partilhado](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), em seguida, distribuir a imagem globalmente.


Usaremos um modelo de. JSON de exemplo para configurar a imagem. O ficheiro. JSON, estamos usando aqui é: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Para distribuir a imagem para uma galeria de imagens de partilhado, o modelo utiliza [sharedImage](image-builder-json.md#distribute-sharedimage) como o valor para o `distribute` secção do modelo.

> [!IMPORTANT]
> Construtor de imagens do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Usaremos algumas partes de informações repetidamente, portanto, vamos criar algumas variáveis para armazenar tais informações.

Para a pré-visualização, construtor de imagens só irá suportar a criação de imagens personalizadas no mesmo grupo de recursos como a imagem gerida de origem. Atualize o nome do grupo de recursos neste exemplo, para ser o mesmo grupo de recursos como a sua imagem gerida de origem.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Criar uma variável para o seu ID de subscrição. Pode obter, utilize `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Crie o grupo de recursos.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Conceder permissão de construtor de imagens do Azure para criar recursos nesse grupo de recursos. O `--assignee` valor é o ID de registo de aplicação para o serviço de construtor de imagens. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Criar uma definição de imagem e a Galeria

Crie uma galeria de imagens. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Crie uma definição de imagem.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Transferir e configurar o. JSON

Transferir o modelo. JSON e configurá-lo com suas variáveis.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Criar a versão da imagem

Esta parte seguinte irá criar a versão da imagem na galeria. 

Submeta a configuração de imagem para o serviço de construtor de imagens do Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Inicie a compilação de imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Criar a imagem e replicá-los para ambas as regiões, podem demorar algum tempo. Aguarde até que esta parte é concluída antes de passar para criar uma VM.


## <a name="create-the-vm"></a>Crie a VM

Crie uma VM a partir da versão de imagem que foi criada pelo construtor de imagens do Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

SSH na VM.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Deverá ver a imagem foi personalizada com um *mensagem do dia* assim que a ligação SSH é estabelecida!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser experimentar agora voltar personalizando a versão da imagem para criar uma nova versão da imagem do mesmo, ignore os passos seguintes e ir em frente para [utilização construtor de imagens do Azure para criar outra versão de imagem](image-builder-gallery-update-image-version.md).


Esta ação irá eliminar a imagem que foi criada, juntamente com todos os outros arquivos de recursos. Certifique-se de que tiver concluído esta implementação antes de eliminar os recursos.

Quando a eliminação de recursos da Galeria de imagem, tem de eliminar todas as versões de imagem antes de poder eliminar a definição de imagem utilizada para criá-los. Para eliminar uma galeria, primeiro tem de ter eliminado todas as definições de imagem na galeria.

Elimine o modelo de construtor de imagem.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Obtenha a versão da imagem criada pelo construtor de imagens, isso sempre começa com `0.`e, em seguida, elimine a versão da imagem

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Elimine a definição de imagem.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Elimine galeria.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Elimine o grupo de recursos.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [galerias de imagem do Azure partilhado](shared-image-galleries.md).