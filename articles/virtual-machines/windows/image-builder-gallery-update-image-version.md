---
title: Criar uma nova versão de imagem a partir de uma versão de imagem existente usando Azure Image Builder (pré-visualização)
description: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente utilizando o Azure Image Builder no Windows.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 0a53e8de8dd832e793ae12034c96ce9fe634ed7a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694109"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-windows"></a>Pré-visualização: Criar uma nova versão de imagem VM a partir de uma versão de imagem existente utilizando O Azure Image Builder no Windows

Este artigo mostra-lhe como pegar uma versão de imagem existente numa [Galeria de Imagens Partilhadas,](../shared-image-galleries.md)atualizá-la e publicá-la como uma nova versão de imagem para a galeria.

Vamos usar um modelo de amostra .json para configurar a imagem. O ficheiro .json que estamos a usar está aqui: [helloImageTemplateforSIGfromWinSIG.jsem](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
az provider show -n Microsoft.Network | grep registrationState
```

Se não disserem registados, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões

Se usou [Criar uma imagem e distribuir para uma Galeria de Imagens Partilhadas](image-builder-gallery.md) para criar a sua Galeria de Imagens Partilhadas, já criou as variáveis de que precisamos. Caso contrário, por favor, confiem algumas variáveis a utilizar para este exemplo.

Para a pré-visualização, o construtor de imagens apenas irá suportar a criação de imagens personalizadas no mesmo Grupo de Recursos que a imagem gerida pela fonte. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos que a sua imagem gerida pela fonte.

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

Crie uma variável para o seu ID de subscrição. Pode usar `az account show | grep id` isto.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Obtenha a versão de imagem que pretende atualizar.

```azurecli-interactive
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
Pode rever o exemplo que estamos prestes a usar abrindo o ficheiro .json aqui: [helloImageTemplateforSIGfromSIG.js](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) juntamente com a referência do [modelo do Image Builder](../linux/image-builder-json.md). 


Descarregue o exemplo .json e configure-o com as suas variáveis. 

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Criar a imagem

Submeta a configuração de imagem para o Serviço de Construtor de Imagem VM.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Inicie a construção da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Aguarde até que a imagem tenha sido construída e replicada antes de passar para o passo seguinte.


## <a name="create-the-vm"></a>Criar a VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Verifique a personalização
Crie uma ligação de ambiente de trabalho remoto ao VM utilizando o nome de utilizador e a palavra-passe que definiu quando criou o VM. Dentro do VM, abra um pedido de cmd e escreva:

```console
dir c:\
```

Deve agora ver dois diretórios:
- `buildActions` que foi criado na primeira versão de imagem.
- `buildActions2` que foi criado como parte a atualização da primeira versão de imagem para criar a segunda versão de imagem.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os componentes do ficheiro .json utilizado neste artigo, consulte a [referência do modelo do construtor de imagem](../linux/image-builder-json.md).
