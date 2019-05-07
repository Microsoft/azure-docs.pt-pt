---
title: Utilizar o construtor de imagens do Azure com uma galeria de imagens para máquinas virtuais do Windows (pré-visualização)
description: Crie imagens do Windows com o construtor de imagens do Azure e Galeria de imagens de partilhado.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-widows
manager: jeconnoc
ms.openlocfilehash: 2453d37720bcf48b95b428cf78c6186de40b31aa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160114"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Pré-visualização: Criar uma imagem do Windows e distribuí-la a uma galeria de imagem partilhada 

Este artigo é mostrar como pode usar o construtor de imagens do Azure para criar uma versão de imagem num [Galeria de imagens de partilhado](shared-image-galleries.md), em seguida, distribuir a imagem globalmente.

Usaremos um modelo. JSON para configurar a imagem. O ficheiro. JSON, estamos usando aqui é: [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Para distribuir a imagem para uma galeria de imagens de partilhado, o modelo utiliza [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) como o valor para o `distribute` secção do modelo.

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
az provider show -n Microsoft.Compute | grep registrationState
```

Se elas não dizem registados, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Definir as variáveis e permissões 

Usaremos algumas partes de informações repetidamente, portanto, vamos criar algumas variáveis para armazenar tais informações. Substitua os valores das variáveis, como `username` e `vmpassword`, com as suas próprias informações.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

Criar uma variável para o seu ID de subscrição. Pode obter, utilize `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
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
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Transferir e configurar o. JSON

Transferir o modelo. JSON e configurá-lo com suas variáveis.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>Criar a versão da imagem

Esta parte seguinte irá criar a versão da imagem na galeria. 

Submeta a configuração de imagem para o serviço de construtor de imagens do Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Inicie a compilação de imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Criar a imagem e replicá-los para ambas as regiões, podem demorar algum tempo. Aguarde até que esta parte é concluída antes de passar para criar uma VM.


## <a name="create-the-vm"></a>Crie a VM

Crie uma VM a partir da versão de imagem que foi criada pelo construtor de imagens do Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Certifique-se a personalização
Crie uma ligação de ambiente de trabalho remoto à VM com o nome de utilizador e palavra-passe definida quando criou a VM. Dentro da VM, abra uma linha de comandos e escreva:

```console
dir c:\
```

Deverá ver um diretório chamado `buildActions` que foi criado durante a personalização de imagem.


## <a name="clean-up-resources"></a>Limpar recursos
Se quiser experimentar agora voltar personalizando a versão da imagem para criar uma nova versão da imagem, mesmo **ignore este passo** e vá para [utilização construtor de imagens do Azure para criar outra versão de imagem](image-builder-gallery-update-image-version.md).


Esta ação irá eliminar a imagem que foi criada, juntamente com todos os outros arquivos de recursos. Certifique-se de que tiver concluído esta implementação antes de eliminar os recursos.

Quando a eliminação de recursos da Galeria de imagem, tem de eliminar todas as versões de imagem antes de poder eliminar a definição de imagem utilizada para criá-los. Para eliminar uma galeria, primeiro tem de ter eliminado todas as definições de imagem na galeria.

Elimine o modelo de construtor de imagem.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
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

## <a name="next-steps"></a>Próximos Passos

Para saber como atualizar a versão da imagem que criou, veja [utilização construtor de imagens do Azure para criar outra versão de imagem](image-builder-gallery-update-image-version.md).