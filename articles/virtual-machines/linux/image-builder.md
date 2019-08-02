---
title: Criar uma VM do Linux com o construtor de imagens do Azure (visualização)
description: Crie uma VM do Linux com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 1bac04bbb67c7472de92c6da322121bafc20a560
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695441"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Pré-visualização: Criar uma VM do Linux com o construtor de imagens do Azure

Este artigo mostra como você pode criar uma imagem personalizada do Linux usando o construtor de imagens do Azure e o CLI do Azure. O exemplo neste artigo usa três personalizadores [](image-builder-json.md#properties-customize) diferentes para personalizar a imagem:

- Shell (ScriptUri) – baixa e executa um [script de shell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (embutido) – executa comandos específicos. Neste exemplo, os comandos embutidos incluem a criação de um diretório e a atualização do sistema operacional.
- Arquivo – copia um [arquivo do GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) para um diretório na VM.

Você também pode especificar um `buildTimeoutInMinutes`. O padrão é de 240 minutos, e você pode aumentar um tempo de compilação para permitir compilações mais longas.

Usaremos um modelo. JSON de exemplo para configurar a imagem. O arquivo. JSON que estamos usando está aqui: [helloImageTemplateLinux. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Variáveis de exemplo de configuração

Usaremos algumas informações repetidamente, portanto, criaremos algumas variáveis para armazenar essas informações.


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

Crie uma variável para sua ID de assinatura. Você pode obter isso usando `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Crie o grupo de recursos.
Isso é usado para armazenar o artefato do modelo de configuração de imagem e a imagem.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Definir permissões no grupo de recursos
Conceda à permissão ' colaborador ' do construtor de imagens para criar a imagem no grupo de recursos. Sem as permissões adequadas, a criação da imagem falhará. 

O `--assignee` valor é a ID de registro do aplicativo para o serviço do construtor de imagem. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Baixar o exemplo de modelo

Um modelo de configuração de imagem de exemplo com parâmetros foi criado para uso. Baixe o arquivo Sample. JSON e configure-o com as variáveis que você definiu anteriormente.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Você pode modificar este example. JSON conforme necessário. Por exemplo, você pode aumentar o valor de `buildTimeoutInMinutes` para permitir mais compilações em execução. Você pode editar o arquivo em Cloud Shell usando um editor de texto `vi`como.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Para a imagem de origem, você sempre deve [especificar uma versão](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), não `latest`pode usar.
>
> Se você adicionar ou alterar o grupo de recursos onde a imagem está sendo distribuída, precisará certificar-se de que as [permissões estão definidas para o grupo de recursos](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Enviar a configuração de imagem
Enviar a configuração de imagem para o serviço do construtor de imagens de VM

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Se ele for concluído com êxito, ele retornará uma mensagem de êxito e criará um artefato do modelo de configuração do Image Builder no $imageResourceGroup. Você poderá ver o grupo de recursos no portal se habilitar ' Mostrar tipos ocultos '.

Além disso, em segundo plano, o Image Builder cria um grupo de recursos de preparo em sua assinatura. O Image Builder usa o grupo de recursos de preparo para a compilação da imagem. O nome do grupo de recursos estará neste formato: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Não exclua o grupo de recursos de preparo diretamente. Se você excluir o artefato do modelo de imagem, ele excluirá automaticamente o grupo de recursos de preparo. Para obter mais informações, consulte a seção [limpar](#clean-up) no final deste artigo.

Se o serviço relatar uma falha durante o envio do modelo de configuração de imagem, consulte as etapas de [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) . Você também precisará excluir o modelo antes de tentar enviar a compilação novamente. Para excluir o modelo:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Iniciar a compilação da imagem

Inicie a compilação da imagem.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Aguarde até que a compilação seja concluída, para este exemplo, pode levar de 10-15 minutos.

Se você encontrar algum erro, leia essas etapas de [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) .


## <a name="create-the-vm"></a>Crie a VM

Crie a VM usando a imagem que você criou.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Obtenha o endereço IP da saída da criação da VM e use-a para SSH para a VM.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Você deve ver que a imagem foi personalizada com uma mensagem do dia assim que sua conexão SSH é estabelecida!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Digite `exit` quando terminar de fechar a conexão SSH.

## <a name="check-the-source"></a>Verificar a origem

No modelo do Image Builder, em ' Propriedades ', você verá a imagem de origem, o script de personalização que ele executa e onde ele é distribuído.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Para obter informações mais detalhadas sobre esse arquivo. JSON, consulte [referência de modelo do Image Builder](image-builder-json.md)

## <a name="clean-up"></a>Limpeza

Quando terminar, você poderá excluir os recursos.

Exclua o modelo do Image Builder.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Exclua o grupo de recursos de imagem.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre os componentes do arquivo. JSON usado neste artigo, consulte referência de [modelo do Image Builder](image-builder-json.md).
