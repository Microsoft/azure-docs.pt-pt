---
title: Criar uma VM do Windows com o construtor de imagens do Azure (pré-visualização)
description: Crie um VM do Windows com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: fec6d83870e20b7622f37c52847803d4f03cbba5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722670"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Pré-visualização: Criar um VM do Windows com o construtor de imagens do Azure

Este artigo é mostrar-lhe como pode criar uma imagem personalizada do Windows usando o construtor de imagens de VM do Azure. O exemplo neste artigo usa três diferentes [personalizadores](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) para personalizar a imagem:
- PowerShell (ScriptUri) - Transferir e executar uma [script do PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Reinício do Windows - reinicia a VM.
- PowerShell (inline) - executar um comando específico. Neste exemplo, ele cria um diretório no VM com `mkdir c:\\buildActions`.
- Ficheiro - copiar um ficheiro a partir do GitHub na VM. Neste exemplo copia [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) para `c:\buildArtifacts\index.html` na VM.

Usaremos um modelo de. JSON de exemplo para configurar a imagem. O ficheiro. JSON, estamos usando aqui é: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Usaremos algumas partes de informações repetidamente, portanto, vamos criar algumas variáveis para armazenar tais informações.

```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Criar uma variável para o seu ID de subscrição. Pode obter, utilize `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Crie o grupo de recursos.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Conceder permissão de construtor de imagens para criar recursos nesse grupo de recursos. O `--assignee` valor é o ID de registo de aplicação para o serviço de construtor de imagens. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Transferir o exemplo. JSON

Transfira o ficheiro. JSON de exemplo e configurá-lo com as variáveis que criou.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>Criar a imagem

Submeter a configuração de imagem para o serviço de construtor de imagens de VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Inicie a compilação de imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Aguarde até que a compilação estiver concluída. Esta ação pode demorar cerca de 15 minutos.

## <a name="create-the-vm"></a>Crie a VM

Crie a VM com a imagem que criou. Substitua *<password>* com a sua própria palavra-passe para o `aibuser` na VM.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Certifique-se a personalização

Crie uma ligação de ambiente de trabalho remoto à VM com o nome de utilizador e palavra-passe definida quando criou a VM. Dentro da VM, abra uma linha de comandos e escreva:

```console
dir c:\
```

Deverá ver esses dois diretórios criados durante a personalização de imagem:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Limpeza

Quando tiver terminado, elimine os recursos.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os componentes do ficheiro. JSON usados neste artigo, consulte [referência de modelo do construtor da imagem](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

