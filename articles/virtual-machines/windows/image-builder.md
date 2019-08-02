---
title: Criar uma VM do Windows com o construtor de imagens do Azure (visualização)
description: Crie uma VM do Windows com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 608338c628232f7f67ea6f6b7ba15e6bb1c3b315
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698666"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Pré-visualização: Criar uma VM do Windows com o construtor de imagens do Azure

Este artigo mostra como você pode criar uma imagem personalizada do Windows usando o construtor de imagem de VM do Azure. O exemplo neste artigo usa os [personalizadores](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) para personalizar a imagem:
- PowerShell (ScriptUri) – Baixe e execute um [script do PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Reinicialização do Windows – reinicia a VM.
- PowerShell (embutido) – executar um comando específico. Neste exemplo, ele cria um diretório na VM usando `mkdir c:\\buildActions`.
- Arquivo – Copie um arquivo do GitHub para a VM. Este exemplo copia [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) para `c:\buildArtifacts\index.html` na VM.

Você também pode especificar um `buildTimeoutInMinutes`. O padrão é de 240 minutos, e você pode aumentar um tempo de compilação para permitir compilações mais longas.

Usaremos um modelo. JSON de exemplo para configurar a imagem. O arquivo. JSON que estamos usando está aqui: [helloImageTemplateWin. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="set-variables"></a>Definir variáveis

Usaremos algumas informações repetidamente, portanto, criaremos algumas variáveis para armazenar essas informações.


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

Crie uma variável para sua ID de assinatura. Você pode obter isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Esse grupo de recursos é usado para armazenar o artefato do modelo de configuração de imagem e a imagem.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Definir permissões no grupo de recursos

Conceda à permissão ' colaborador ' do construtor de imagens para criar a imagem no grupo de recursos. Sem isso, a criação da imagem falhará. 

O `--assignee` valor é a ID de registro do aplicativo para o serviço do construtor de imagem. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Baixar o exemplo de modelo de configuração de imagem

Um modelo de configuração de imagem com parâmetros foi criado para você tentar. Baixe o arquivo example. JSON e configure-o com as variáveis que você definiu anteriormente.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Você pode modificar este exemplo, no terminal usando um editor de texto como `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Para a imagem de origem, você sempre deve [especificar uma versão](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), não pode `latest`usar.
> Se você adicionar ou alterar o grupo de recursos para o qual a imagem é distribuída, deverá fazer com que as [permissões sejam definidas](#set-permissions-on-the-resource-group) no grupo de recursos.
 
## <a name="create-the-image"></a>Criar a imagem

Enviar a configuração de imagem para o serviço do construtor de imagens de VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Ao concluir, isso retornará uma mensagem de êxito de volta ao console e criará um `Image Builder Configuration Template` `$imageResourceGroup`no. Você pode ver esse recurso no grupo de recursos na portal do Azure, se você habilitar ' Mostrar tipos ocultos '.

Em segundo plano, o Image Builder também criará um grupo de recursos de preparo em sua assinatura. Esse grupo de recursos é usado para a compilação da imagem. Ele estará neste formato:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Você não deve excluir o grupo de recursos de preparo diretamente. Primeiro, exclua o artefato do modelo de imagem. isso fará com que o grupo de recursos de preparo seja excluído.

Se o serviço relatar uma falha durante o envio do modelo de configuração de imagem:
-  Examine essas etapas de [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) . 
- Você precisará excluir o modelo, usando o trecho a seguir, antes de tentar enviar novamente.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Iniciar a compilação da imagem
Inicie o processo de criação de imagem usando [AZ Resource Invoke-Action](/cli/azure/resourceaz-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Aguarde até que a compilação seja concluída. Isso pode levar cerca de 15 minutos.

Se você encontrar algum erro, leia essas etapas de [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) .


## <a name="create-the-vm"></a>Crie a VM

Crie a VM usando a imagem que você criou. `aibuser` Substitua  *\<a senha >* pela sua própria senha para o na VM.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Verificar a personalização

Crie uma conexão de Área de Trabalho Remota com a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt cmd e digite:

```console
dir c:\
```

Você deve ver esses dois diretórios criados durante a personalização da imagem:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Limpeza

Quando terminar, exclua os recursos.

### <a name="delete-the-image-builder-template"></a>Excluir o modelo do construtor de imagem
```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Excluir o grupo de recursos de imagem
```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os componentes do arquivo. JSON usado neste artigo, consulte referência de [modelo do Image Builder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
