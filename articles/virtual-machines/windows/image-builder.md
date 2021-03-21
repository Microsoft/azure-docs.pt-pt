---
title: Criar um Windows VM com Azure Image Builder (pré-visualização)
description: Crie um Windows VM com o Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 918cee723bfde69d08532aee6fe4f395dbddb4ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695452"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Pré-visualização: Criar um Windows VM com Azure Image Builder

Este artigo é para mostrar como pode criar uma imagem personalizada do Windows utilizando o Azure VM Image Builder. O exemplo neste artigo utiliza [personalizadores](../linux/image-builder-json.md#properties-customize) para personalizar a imagem:
- PowerShell (ScriptUri) - descarregue e execute um [script PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Windows Restart - reinicia o VM.
- PowerShell (inline) - executar um comando específico. Neste exemplo, cria-se um diretório sobre o VM utilizando `mkdir c:\\buildActions` .
- File - copie um ficheiro do GitHub para o VM. Este exemplo copia [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) `c:\buildArtifacts\index.html` no VM.
- buildTimeoutInMinutes - Aumente o tempo de construção para permitir construções mais longas, o padrão é de 240 minutos, e pode aumentar um tempo de construção para permitir construções mais longas.
- vmProfile - especificando as propriedades vmSize e Network
- osDiskSizeGB - pode aumentar o tamanho da imagem
- identidade - fornecendo uma identidade para Azure Image Builder usar durante a construção


Também pode especificar um `buildTimeoutInMinutes` . O padrão é de 240 minutos, e pode aumentar o tempo de construção para permitir construções mais longas.

Vamos usar um modelo de amostra .json para configurar a imagem. O ficheiro .json que estamos a usar está aqui: [helloImageTemplateWin.jsem](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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


## <a name="set-variables"></a>Definir variáveis

Vamos usar algumas informações repetidamente, por isso vamos criar algumas variáveis para armazenar essa informação.


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

Crie uma variável para o seu ID de subscrição. Pode usar `az account show | grep id` isto.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Este grupo de recursos é utilizado para armazenar o artefacto do modelo de configuração de imagem e a imagem.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Criar uma identidade atribuída ao utilizador e definir permissões no grupo de recursos
O Image Builder utilizará a [identidade do utilizador](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) fornecida para injetar a imagem no grupo de recursos. Neste exemplo, irá criar uma definição de papel Azure que tem as ações granulares para executar a distribuição da imagem. A definição de função será então atribuída à identidade do utilizador.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Criar identidade gerida atribuída ao utilizador e conceder permissões 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Descarregue o exemplo do modelo de configuração de imagem

Foi criado um modelo de configuração de imagem para tentar. Descarregue o ficheiro exemplo .json e configure-o com as variáveis que definiu anteriormente.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Pode modificar este exemplo, no terminal utilizando um editor de texto como `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> Para a imagem de origem, deve sempre [especificar uma versão,](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)não pode utilizar `latest` .
> Se adicionar ou alterar o grupo de recursos para onde a imagem é distribuída, deve escoar as [permissões no](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) grupo de recursos.
 
## <a name="create-the-image"></a>Criar a imagem

Submeta a configuração de imagem para o serviço VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Quando estiver concluído, isto devolverá uma mensagem de sucesso à consola e criará uma `Image Builder Configuration Template` na `$imageResourceGroup` . Pode ver este recurso no grupo de recursos no portal Azure, se ativar 'Mostrar tipos ocultos'.

Em segundo plano, o Image Builder também criará um grupo de recursos de encenação na sua subscrição. Este grupo de recursos é utilizado para a construção de imagens. Será neste formato: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Não deve eliminar diretamente o grupo de recursos de encenação. Primeiro, elimine o artefacto do modelo de imagem, isto fará com que o grupo de recursos de encenação seja eliminado.

Se o serviço reportar uma falha durante a submissão do modelo de configuração de imagem:
-  Reveja estes [passos de resolução de problemas.](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) 
- Terá de eliminar o modelo, utilizando o seguinte corte, antes de voltar a tentar a submissão.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Inicie a construção de imagem
Inicie o processo de construção de imagens utilizando [recurso az invocação de ação](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Espere até que a construção esteja completa. Isto pode levar cerca de 15 minutos.

Se encontrar algum erro, por favor reveja estes passos [de resolução de problemas.](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors)


## <a name="create-the-vm"></a>Criar a VM

Crie o VM utilizando a imagem que construiu. *\<password>* Substitua-a pela sua própria palavra-passe para `aibuser` o VM.

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

Crie uma ligação de ambiente de trabalho remoto ao VM utilizando o nome de utilizador e a palavra-passe que definiu quando criou o VM. Dentro do VM, abra um pedido de cmd e escreva:

```console
dir c:\
```

Você deve ver estes dois diretórios criados durante a personalização de imagem:
- buildActions
- buildArfacts

## <a name="clean-up"></a>Limpeza

Quando terminar, apague os recursos.

### <a name="delete-the-image-builder-template"></a>Elimine o modelo de construtor de imagens

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Eliminar a atribuição de funções, definição de função e identidade de utilizador.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>Eliminar o grupo de recursos de imagem

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os componentes do ficheiro .json utilizado neste artigo, consulte a [referência do modelo do construtor de imagem](../linux/image-builder-json.md).
