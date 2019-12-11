---
title: Usar o construtor de imagens do Azure com uma galeria de imagens para VMs do Linux (versão prévia)
description: Crie imagens de VM do Linux com o Azure Image Builder e a Galeria de imagens compartilhadas.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 09dceb84a20ef49b3e9d5264b94bb5e74180cd2b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976133"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Visualização: criar uma imagem do Linux e distribuí-la para uma galeria de imagens compartilhadas 

Este artigo mostra como você pode usar o construtor de imagens do Azure para criar uma versão de imagem em uma [Galeria de imagens compartilhada](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)e, em seguida, distribuir a imagem globalmente.


Usaremos um modelo. JSON de exemplo para configurar a imagem. O arquivo. JSON que estamos usando está aqui: [helloImageTemplateforSIG. JSON](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Para distribuir a imagem a uma galeria de imagens compartilhadas, o modelo usa [sharedImage](image-builder-json.md#distribute-sharedimage) como o valor para a seção `distribute` do modelo.

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
```

Se não disser registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões 

Usaremos algumas informações repetidamente, portanto, criaremos algumas variáveis para armazenar essas informações.

Para visualização, o Image Builder dará suporte apenas à criação de imagens personalizadas no mesmo grupo de recursos que a imagem gerenciada de origem. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos que a imagem gerenciada de origem.

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

Crie uma variável para sua ID de assinatura. Você pode obter isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Crie o grupo de recursos.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Dê permissão ao construtor de imagens do Azure para criar recursos nesse grupo de recursos. O valor de `--assignee` é a ID de registro do aplicativo para o serviço do construtor de imagem. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Criar uma definição de imagem e uma galeria

Para usar o construtor de imagem com uma galeria de imagens compartilhada, você precisa ter uma galeria de imagens e uma definição de imagem existentes. O Image Builder não criará a Galeria de imagens e a definição de imagem para você.

Se você ainda não tiver uma definição de imagem e galeria para usar, comece criando-as. Primeiro, crie uma galeria de imagens.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Em seguida, crie uma definição de imagem.

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


## <a name="download-and-configure-the-json"></a>Baixar e configurar o. JSON

Baixe o modelo. JSON e configure-o com suas variáveis.

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

Esta próxima parte criará a versão da imagem na galeria. 

Envie a configuração de imagem para o serviço do construtor de imagem do Azure.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Inicie a compilação da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Criar a imagem e replicá-la para ambas as regiões pode levar algum tempo. Aguarde até que essa parte seja concluída antes de passar para a criação de uma VM.


## <a name="create-the-vm"></a>Crie a VM

Crie uma VM com base na versão de imagem criada pelo construtor de imagens do Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Aceda através de SSH na VM.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Você deve ver que a imagem foi personalizada com uma *mensagem do dia* assim que sua conexão SSH é estabelecida!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser agora tentar repersonalizar a versão da imagem para criar uma nova versão da mesma imagem, pule as próximas etapas e vá para [usar o construtor de imagem do Azure para criar outra versão da imagem](image-builder-gallery-update-image-version.md).


Isso excluirá a imagem que foi criada, junto com todos os outros arquivos de recurso. Verifique se você concluiu essa implantação antes de excluir os recursos.

Ao excluir os recursos da Galeria de imagens, você precisa excluir todas as versões da imagem antes de excluir a definição de imagem usada para criá-las. Para excluir uma galeria, primeiro você precisa excluir todas as definições de imagem na galeria.

Exclua o modelo do Image Builder.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Obter a versão da imagem criada pelo construtor de imagem, que sempre começa com `0.`e, em seguida, exclui a versão da imagem

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


Exclua a definição da imagem.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Exclua a galeria.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Exclua o grupo de recursos.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as [galerias de imagens compartilhadas do Azure](shared-image-galleries.md).