---
title: Criar uma VM do Linux com o construtor de imagens do Azure (pré-visualização)
description: Crie uma VM do Linux com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159514"
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

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

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
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>Criar a imagem
Submeter a configuração de imagem para o serviço de construtor de imagens de VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Inicie a compilação de imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Aguarde até que a compilação estiver concluída. Esta ação pode demorar cerca de 15 minutos.


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

Quando tiver terminado, elimine os recursos.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01

az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os componentes do ficheiro. JSON usados neste artigo, consulte [referência de modelo do construtor de imagens](image-builder-json.md).