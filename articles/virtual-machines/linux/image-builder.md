---
title: Crie um Linux VM com Azure Image Builder (pré-visualização)
description: Crie um Linux VM com o Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: b50b2a9bfca99e1868c083759cce26bb176789f4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792416"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Pré-visualização: Crie um VM Linux com o Azure Image Builder

Este artigo mostra-lhe como pode criar uma imagem linux personalizada usando o Azure Image Builder e o Azure CLI. O exemplo neste artigo utiliza três [personalizadores diferentes](image-builder-json.md#properties-customize) para personalizar a imagem:

- Shell (ScriptUri) - descarrega e executa um [script de concha](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - executa comandos específicos. Neste exemplo, os comandos inline incluem a criação de um diretório e a atualização do SISTEMA.
- File - copia um [ficheiro do GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) para um diretório no VM.

Também pode especificar `buildTimeoutInMinutes`um . O padrão é de 240 minutos, e você pode aumentar um tempo de construção para permitir construções mais longas.

Vamos usar um modelo de amostra .json para configurar a imagem. O ficheiro .json que estamos a usar está aqui: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Configuração de variáveis exemplo

Vamos usar algumas peças de informação repetidamente, por isso vamos criar algumas variáveis para armazenar essa informação.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Crie uma variável para o seu ID de subscrição. Pode obter isto `az account show | grep id`usando.

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Crie o grupo de recursos.
Isto é usado para armazenar o artefacto do modelo de configuração da imagem e a imagem.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Definir permissões no grupo de recursos
Dê permissão ao Image Builder 'colaborador' para criar a imagem no grupo de recursos. Sem as permissões adequadas, a construção da imagem falhará. 

O `--assignee` valor é o ID de registo de aplicações para o serviço Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Descarregue o exemplo do modelo

Foi criado um modelo de configuração de imagem de amostra parametrizado para que possa utilizar. Descarregue o ficheiro .json da amostra e configure-o com as variáveis que definiu anteriormente.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Pode modificar este exemplo .json conforme necessário. Por exemplo, pode aumentar `buildTimeoutInMinutes` o valor de permitir construções mais longas. Pode editar o ficheiro na Cloud Shell `vi`usando um editor de texto como .

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Para a imagem de origem, deve especificar `latest`sempre [uma versão,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)não pode utilizar .
>
> Se adicionar ou alterar o grupo de recursos onde a imagem está a ser distribuída, tem de se certificar de que as [permissões estão definidas para o grupo de recursos](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Submeter a configuração da imagem
Envie a configuração de imagem para o serviço VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Se completar com sucesso, devolverá uma mensagem de sucesso e criará um artefacto de modelo de configuração de construtor de imagem no $imageResourceGroup. Pode ver o grupo de recursos no portal se ativar 'Mostrar tipos ocultos'.

Além disso, em segundo plano, o Image Builder cria um grupo de recursos de encenação na sua subscrição. O Image Builder utiliza o grupo de recursos de encenação para a construção da imagem. O nome do grupo de recursos `IT_<DestinationResourceGroup>_<TemplateName>`será neste formato: .

> [!IMPORTANT]
> Não elimine diretamente o grupo de recursos de encenação. Se eliminar o artefacto do modelo de imagem, eliminará automaticamente o grupo de recursos de encenação. Para mais informações, consulte a secção [Limpar](#clean-up) no final deste artigo.

Se o serviço reportar uma falha durante a submissão do modelo de configuração da imagem, consulte os passos de [resolução](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) de problemas. Também terá de apagar o modelo antes de voltar a submeter a construção. Para eliminar o modelo:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Iniciar a construção da imagem

Inicie a construção da imagem.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Espere até que a construção esteja completa, para este exemplo, pode levar 10-15 minutos.

Se encontrar algum erro, por favor reveja estes passos de [resolução](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) de problemas.


## <a name="create-the-vm"></a>Crie a VM

Crie o VM utilizando a imagem que construiu.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Obtenha o endereço IP a partir da saída de criação do VM e use-o para SSH para o VM.

```bash
ssh azureuser@<pubIp>
```

Deve ver que a imagem foi personalizada com uma Mensagem do Dia assim que a sua ligação SSH estiver estabelecida!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Digite `exit` quando estiver feito para fechar a ligação SSH.

## <a name="check-the-source"></a>Verifique a fonte

No Modelo de Construtor de Imagem, nas 'Propriedades', você verá a imagem de origem, o script de personalização que executa, e onde é distribuído.

```bash
cat helloImageTemplateLinux.json
```

Para obter informações mais detalhadas sobre este ficheiro .json, consulte a referência do [modelo do construtor](image-builder-json.md) de imagem

## <a name="clean-up"></a>Limpeza

Quando terminar, pode apagar os recursos.

Elimine o modelo de construtor de imagens.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Elimine o grupo de recursos de imagem.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os componentes do ficheiro .json utilizado neste artigo, consulte a referência do [modelo do Construtor](image-builder-json.md)de Imagem .
