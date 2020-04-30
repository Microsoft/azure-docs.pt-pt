---
title: Criar um Windows VM com Azure Image Builder (pré-visualização)
description: Crie um VM Windows com o Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 269b2f4674f2c99fc438c1a7be65e5660ca58d08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869501"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Pré-visualização: Criar um VM Windows com o Azure Image Builder

Este artigo é para lhe mostrar como pode criar uma imagem personalizada do Windows utilizando o Azure VM Image Builder. O exemplo neste artigo utiliza [personalizadores](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) para personalizar a imagem:
- PowerShell (ScriptUri) - descarregue e execute um [script PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Windows Restart - reinicia o VM.
- PowerShell (inline) - executar um comando específico. Neste exemplo, cria um diretório sobre `mkdir c:\\buildActions`o VM utilizando .
- File - copie um ficheiro do GitHub para o VM. Este exemplo copia [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) `c:\buildArtifacts\index.html` no VM.

Também pode especificar `buildTimeoutInMinutes`um . O padrão é de 240 minutos, e você pode aumentar um tempo de construção para permitir construções mais longas.

Vamos usar um modelo de amostra .json para configurar a imagem. O ficheiro .json que estamos a usar está aqui: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .


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

## <a name="set-variables"></a>Definir variáveis

Vamos usar algumas peças de informação repetidamente, por isso vamos criar algumas variáveis para armazenar essa informação.


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

Crie uma variável para o seu ID de subscrição. Pode obter isto `az account show | grep id`usando.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Este grupo de recursos é usado para armazenar o artefacto do modelo de configuração de imagem e a imagem.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Definir permissões no grupo de recursos

Dê permissão ao Image Builder 'colaborador' para criar a imagem no grupo de recursos. Sem isso, a construção da imagem falhará. 

O `--assignee` valor é o ID de registo de aplicações para o serviço Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Descarregue o exemplo do modelo de configuração da imagem

Foi criado um modelo de configuração de imagem parametrizado para que possa experimentar. Descarregue o exemplo .json file e configure-o com as variáveis que definiu anteriormente.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Pode modificar este exemplo, no terminal utilizando `vi`um editor de texto como .

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Para a imagem de origem, deve especificar `latest`sempre [uma versão,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)não pode utilizar .
> Se adicionar ou alterar o grupo de recursos para onde a imagem é distribuída, deve fazer com que as [permissões sejam definidas](#set-permissions-on-the-resource-group) no grupo de recursos.
 
## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o serviço VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Quando estiver concluído, isto devolverá uma mensagem de `Image Builder Configuration Template` sucesso `$imageResourceGroup`à consola e criará uma no . Pode ver este recurso no grupo de recursos no portal Azure, se ativar 'Mostrar tipos ocultos'.

Em segundo plano, o Image Builder também criará um grupo de recursos de encenação na sua subscrição. Este grupo de recursos é usado para a construção de imagem. Será neste formato:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Não deve excluir diretamente o grupo de recursos de encenação. Primeiro, elimine o artefacto do modelo de imagem, isto fará com que o grupo de recursos de encenação seja eliminado.

Se o serviço reportar uma falha durante a submissão do modelo de configuração da imagem:
-  Reveja estes passos de [resolução](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) de problemas. 
- Terá de eliminar o modelo, utilizando o seguinte corte, antes de voltar a tentar a submissão.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Iniciar a construção da imagem
Inicie o processo de construção de imagem utilizando a [ação de invocação do recurso Az](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Espere até que a construção esteja completa. Isto pode levar cerca de 15 minutos.

Se encontrar algum erro, por favor reveja estes passos de [resolução](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) de problemas.


## <a name="create-the-vm"></a>Crie a VM

Crie o VM utilizando a imagem que construiu. Substitua * \<a palavra-passe* `aibuser`>com a sua própria senha para o VM.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Verifique a personalização

Crie uma ligação Remote Desktop ao VM utilizando o nome de utilizador e a palavra-passe que definiu quando criou o VM. No interior do VM, abra um pedido cmd e escreva:

```console
dir c:\
```

Você deve ver estes dois diretórios criados durante a personalização da imagem:
- buildActions
- construirArtefactos

## <a name="clean-up"></a>Limpeza

Quando terminar, apague os recursos.

### <a name="delete-the-image-builder-template"></a>Eliminar o modelo de construtor de imagem

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Eliminar o grupo de recursos de imagem

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os componentes do ficheiro .json utilizado neste artigo, consulte a referência do modelo do construtor de [imagem](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
