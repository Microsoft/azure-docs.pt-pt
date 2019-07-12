---
title: Criar uma VM do Linux com o construtor de imagens do Azure (pré-visualização)
description: Crie uma VM do Linux com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667516"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Pré-visualização: Criar uma VM do Linux com o construtor de imagens do Azure

Este artigo mostra-lhe como pode criar uma imagem personalizada do Linux usando o construtor de imagens do Azure e a CLI do Azure. O exemplo neste artigo usa três diferentes [personalizadores](image-builder-json.md#properties-customize) para personalizar a imagem:

- Shell (ScriptUri) - downloads e execuções de um [script de shell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - comandos específicos de execuções. Neste exemplo, os comandos de inline incluem criar um diretório e atualizar o sistema operacional.
- Ficheiro - cópias de um [ficheiro a partir do GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) num diretório na VM.


Usaremos um modelo de. JSON de exemplo para configurar a imagem. O ficheiro. JSON, estamos usando aqui é: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Variáveis de exemplo de configuração

Usaremos algumas partes de informações repetidamente, portanto, vamos criar algumas variáveis para armazenar tais informações.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Criar uma variável para o seu ID de subscrição. Pode obter, utilize `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Crie o grupo de recursos.
Isto é utilizado para armazenar o artefacto de modelo de configuração de imagem e a imagem.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Definir as permissões no grupo de recursos
Conceder permissão de "contribuinte" do construtor de imagens para criar a imagem no grupo de recursos. Sem as permissões adequadas, a criação da imagem falhará. 

O `--assignee` valor é o ID de registo de aplicação para o serviço de construtor de imagens. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Transferir o exemplo de modelo

Um modelo de configuração de imagem de exemplo parametrizado foi criado para ser utilizada. Transfira o ficheiro. JSON de exemplo e configurá-lo com as variáveis que definiu anteriormente.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Pode modificar este. JSON de exemplo conforme necessário. Por exemplo, pode aumentar o valor de `buildTimeoutInMinutes` para permitir mais compilações em execução. Pode editar o ficheiro no Cloud Shell com `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Para a imagem de origem, deve sempre [especifique uma versão](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), não é possível utilizar `latest`.
>
> Se adicionar ou alterar o grupo de recursos onde está a ser distribuída a imagem, tem de certificar-se de que o [permissões estão definidas para o grupo de recursos](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Submeter a configuração de imagem
Submeter a configuração de imagem para o serviço de construtor de imagens de VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Se ele for concluída com êxito, ele irá devolver uma mensagem de êxito e crie um artefato de modelo de configuração de construtor de imagem no $imageResourceGroup. Se ativar a opção "Mostrar tipos ocultos", pode ver o grupo de recursos no portal.

Além disso, em segundo plano, o construtor de imagens cria um grupo de recursos de teste na sua subscrição. Construtor de imagens usa o grupo de recursos de teste para a compilação de imagem. O nome do grupo de recursos vai estar no seguinte formato: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Não elimine o grupo de recursos de teste diretamente. Se eliminar o artefacto de modelo de imagem, irá eliminar automaticamente o grupo de recursos de teste. Para obter mais informações, consulte a [limpar](#clean-up) secção no final deste artigo.

Se o serviço reportar uma falha durante a submissão de modelo de configuração de imagem, consulte a [resolução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) passos. Também terá de eliminar o modelo antes de repetir a submeter a compilação. Para eliminar o modelo:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Iniciar a compilação de imagem

Inicie a compilação de imagem.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Aguarde até que a compilação estiver concluída, para este exemplo, pode demorar 10 a 15 minutos.

Se encontrar algum erro, reveja estes [resolução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) passos.


## <a name="create-the-vm"></a>Crie a VM

Crie a VM com a imagem que criou.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Obter o endereço IP a partir da saída de criar a VM e utilizá-lo para encaminhar o SSH à VM.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Deverá ver que a imagem foi personalizada com uma mensagem do dia assim que a ligação SSH é estabelecida!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Tipo de `exit` quando tiver concluído para fechar a ligação de SSH.

## <a name="check-the-source"></a>Verificar a origem

No modelo de construtor de imagem, em "Propriedades", verá que a imagem de origem, a personalização do script é executado, e onde é distribuída.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Para obter mais informações sobre este ficheiro. JSON, consulte [referência de modelo do construtor de imagens](image-builder-json.md)

## <a name="clean-up"></a>Limpeza

Quando tiver terminado, pode eliminar os recursos.

Elimine o modelo de construtor de imagem.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Elimine o grupo de recursos de imagem.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os componentes do ficheiro. JSON usados neste artigo, consulte [referência de modelo do construtor de imagens](image-builder-json.md).
