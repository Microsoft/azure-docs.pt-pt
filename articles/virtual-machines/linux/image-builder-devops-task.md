---
title: Tarefa de devOps de devops de serviço de construtor de imagem Azure
description: A azure DevOps tarefa de injetar artefactos de construção em uma imagem VM para que você possa instalar e configurar a sua aplicação e SO.
author: danielsollondon
ms.author: danis
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: d02a5c6bc194009d459647721dab16be0dcade84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670476"
---
# <a name="azure-image-builder-service-devops-task"></a>Tarefa de devOps de devops de serviço de construtor de imagem Azure

Este artigo mostra-lhe como usar uma tarefa Azure DevOps para injetar artefactos de construção numa imagem VM para que possa instalar e configurar a sua aplicação e o SEU.

## <a name="devops-task-versions"></a>Versões de tarefa de DevOps
Existem duas tarefas de Construtor de Imagem Azure VM (AIB):

* [Tarefa AIB 'estável',](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)esta é a mais recente construção estável que foi testada, e a telemetria não mostra problemas. 


* [Tarefa AIB 'instável',](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary)isto permite-nos colocar as últimas atualizações e funcionalidades, permitir que os clientes as testem, antes de a promovermos para a tarefa 'estável'. Se não houver problemas relatados, e a nossa telemetria não apresentar problemas, aproximadamente uma semana depois, promoveremos o código de tarefa para "estável". 

## <a name="prerequisites"></a>Pré-requisitos

* Instale a [Tarefa DevOps Estável do Mercado do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder).
* Você deve ter uma conta VSTS DevOps, e um Pipeline build
* Registar e ativar os requisitos de funcionalidade do Construtor de Imagens na subscrição utilizada pelos gasodutos:
    * [Az PowerShell](../windows/image-builder-powershell.md#register-features)
    * [Az CLI](../windows/image-builder.md#register-the-features)
    
* Crie uma Conta de Armazenamento Standard Azure no Grupo de Recursos de imagem de origem, pode utilizar outras contas de Grupo de Recursos/Armazenamento. A conta de armazenamento é usada para transferir os artefactos de construção da tarefa DevOps para a imagem.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Adicionar tarefa para lançar pipeline

Selecione **Editar pipeline de lançamento**  >  

No Agente utilizador, selecione *+* para adicionar então procure por **Image Builder**. Selecione **Adicionar**.

Definir as seguintes propriedades de tarefa:

### <a name="azure-subscription"></a>Subscrição do Azure

Selecione a partir do menu suspenso que subscrição deseja que o Construtor de Imagens execute. Utilize a mesma subscrição onde estão localizadas as imagens de origem e onde as imagens serão distribuídas. Tem de autorizar o acesso do colaborador do construtor de imagens ao Grupo de Assinatura ou Recursos.

### <a name="resource-group"></a>Grupo de Recursos

Utilize o grupo de recursos onde o artefacto do modelo de imagem temporária será armazenado. Ao criar um artefacto de modelo, é criado um grupo de recursos temporários de Image `IT_<DestinationResourceGroup>_<TemplateName>_guid` Builder. O grupo de recursos temporários armazena os metadados de imagem, como scripts. No final da tarefa, o artefacto do modelo de imagem e o grupo temporário de recursos do Construtor de Imagem são eliminados.
 
### <a name="location"></a>Localização

A localização é a região onde o Construtor de Imagem vai funcionar. Apenas um número definido de regiões são [apoiadas.](../image-builder-overview.md#regions) As imagens de origem devem estar presentes neste local. Por exemplo, se estiver a utilizar a Galeria de Imagens Partilhadas, deve existir uma réplica nessa região.

### <a name="managed-identity-required"></a>Identidade gerida (Requerida)
O Image Builder requer uma Identidade Gerida, que utiliza para ler imagens personalizadas de origem, ligar-se ao Azure Storage e criar imagens personalizadas. Aceda [aqui](../image-builder-overview.md#permissions) para obter mais detalhes.

### <a name="vnet-support"></a>Suporte VNET

Atualmente, a tarefa DevOps não suporta especificar uma sub-rede existente, esta está no roteiro, mas se quiser utilizar um VNET existente, pode utilizar um modelo ARM, com um modelo de Construtor de Imagem aninhado no interior, consulte os exemplos do Construtor de Imagens do Windows sobre como isto é alcançado, ou utilizar alternativamente [o AZ AIB PowerShell](../windows/image-builder-powershell.md).

### <a name="source"></a>Origem

As imagens de origem devem ser do OSs do Construtor de Imagem suportado. Pode escolher imagens personalizadas existentes na mesma região que o Image Builder está a correr:
* Imagem gerida - É necessário passar nos recursosId, por exemplo:
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Galeria de Imagens Partilhadas Azure - É necessário passar nos recursosId da versão de imagem, por exemplo:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    Se precisar de obter a versão mais recente da Shared Image Gallery, pode ter uma tarefa CLI AZ PowerShell ou AZ antes de obter a versão mais recente e definir uma variável DevOps. Utilize a variável na tarefa AZ VM Image Builder DevOps. Para mais informações, consulte os [exemplos.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery)

* (Mercado) Imagem Base Existe uma lista de imagens populares, estas utilizarão sempre a versão 'mais recente' dos OS suportados. 

    Se a imagem base não estiver na lista, pode especificar a imagem exata que utiliza `Publisher:Offer:Sku` .

    Versão base de imagem (opcional) - Pode fornecer a versão da imagem que pretende utilizar, o padrão é `latest` .

### <a name="customize"></a>Personalizar

#### <a name="provisioner"></a>Provisioner

Inicialmente, dois personalizadores são suportados - **Shell** e **PowerShell**. Só a inline é suportada. Se quiser baixar scripts, pode passar comandos inline para o fazer.

Para o seu SISTEMA, selecione PowerShell ou Shell.

#### <a name="windows-update-task"></a>Tarefa de atualização do Windows

Apenas para o Windows, a tarefa executa o Windows Update no final das personalizações. Trata das reinicializações necessárias.

É executada a seguinte configuração de Atualização do Windows:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Instala atualizações importantes e recomendadas do Windows que não são de pré-visualização.

#### <a name="handling-reboots"></a>Manipulação de Reboots
Atualmente, a tarefa DevOps não tem suporte para reiniciar as construções do Windows, se tentar reiniciar com o código PowerShell, a construção falhará. No entanto, pode usar código para reiniciar as construções do Linux.

#### <a name="build-path"></a>Construir Caminho

A tarefa foi concebida para ser capaz de injetar artefactos de libertação de DevOps Build na imagem. Para que isto funcione, é preciso montar um oleoduto de construção. Na configuração do gasoduto de libertação, deve adicionar o repo dos artefactos de construção.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Selecionar adicione um artefacto no gasoduto de libertação.":::

Selecione o botão **'Construir Caminho'** para escolher a pasta de construção que pretende ser colocada na imagem. A tarefa do Construtor de Imagens copia todos os ficheiros e diretórios dentro dela. Quando a imagem está a ser criada, o Image Builder implanta os ficheiros e diretórios em diferentes caminhos, dependendo do SISTEMA.

> [!IMPORTANT]
> Ao adicionar um artefacto de repo, poderá descobrir que o diretório está pré-fixado com um sublinhado *_*. O sublinhado pode causar problemas com os comandos inline. Utilize as cotações apropriadas nos comandos.
> 

O exemplo a seguir explica como isto funciona:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="Uma estrutura de diretório mostrando hierarquia.":::


* Windows - Os ficheiros existem em `C:\` . Um diretório nomeado `buildArtifacts` é criado que inclui o `webapp` diretório.

* Linux - Os ficheiros existem em  `/tmp` . O `webapp` diretório é criado que inclui todos os ficheiros e diretórios. Tens de tirar os ficheiros deste diretório. Caso contrário, serão suprimidas, uma vez que se encontra no diretório temporário.

#### <a name="inline-customization-script"></a>Roteiro de personalização inline

* Windows - Pode introduzir comandos de linha PowerShell separados por vírgulas. Se quiser executar um script no seu diretório de construção, pode usar:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

   Pode fazer referência a vários scripts ou adicionar mais comandos, por exemplo:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    & 'c:\buildArtifacts\webapp\installAgent.ps1'
    ```
* Linux - Nos sistemas Linux os artefactos de construção são colocados no `/tmp` diretório. No entanto, em muitos OSs Linux, num reboot, os conteúdos do diretório /tmp são eliminados. Se quiser que os artefactos existam na imagem, deve criar outro diretório e copiá-los.  Por exemplo:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    Se estiver bem usando o diretório "/tmp", então pode usar o código abaixo para executar o script.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>O que acontece com os artefactos de construção depois da construção da imagem?

> [!NOTE]
> O Image Builder não remove automaticamente os artefactos de construção, sugere-se fortemente que tenha sempre código para remover os artefactos de construção.
> 

* Windows - O construtor de imagens implementa ficheiros no `c:\buildArtifacts` diretório. O diretório é persistido, deve retirar o diretório. Pode removê-lo no guião que executa. Por exemplo:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux - Os artefactos de construção são colocados no `/tmp` diretório. No entanto, em muitos OSs Linux, num reboot, os conteúdos do `/tmp` diretório são eliminados. Sugere-se que tenha código para remover o conteúdo e não depender do SO para remover o conteúdo. Por exemplo:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>Comprimento total da construção de imagem

O comprimento total ainda não pode ser alterado na tarefa do gasoduto DevOps. Usa o padrão de 240 minutos. Se pretender aumentar a [buildTimeoutInMinutes,](./image-builder-json.md#properties-buildtimeoutinminutes)então pode utilizar uma tarefa CLI AZ no Pipeline de Lançamento. Configurar a tarefa de copiar um modelo e submetê-lo. Por exemplo, consulte esta [solução](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)ou utilize a Az PowerShell.


#### <a name="storage-account"></a>Conta de Armazenamento

Selecione a conta de armazenamento que criou nos pré-requisitos. Se não o vir na lista, o Image Builder não tem permissões.

Quando a construção começar, o Image Builder criará um recipiente chamado `imagebuilder-vststask` . O recipiente é onde os artefactos de construção do repo estão armazenados.

> [!NOTE]
> É necessário eliminar manualmente a conta de armazenamento ou o recipiente após cada construção.
>

### <a name="distribute"></a>Distribuir

Existem 3 tipos de distribuição suportados.

#### <a name="managed-image"></a>Imagem gerida

* ResourceID:
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Localizações

#### <a name="azure-shared-image-gallery"></a>Galeria de Imagem Partilhada de Azure

A Galeria de Imagens Partilhadas já **deve** existir.

* ResourceID: 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Regiões: lista de regiões, vírgula separada. Por exemplo, westus, este, central

#### <a name="vhd"></a>VHD

Não é possível passar valores para isto, o Image Builder emitirá o VHD para o grupo temporário de recursos do Construtor de `IT_<DestinationResourceGroup>_<TemplateName>` Imagem, no recipiente *vhds.* Quando inicia a construção do desbloqueio, o construtor de imagens emite registos. Quando terminar, emitirá o URL VHD.

### <a name="optional-settings"></a>Definições opcionais

* [Tamanho VM](image-builder-json.md#vmprofile) - Pode sobrepor-se ao tamanho VM, a partir do padrão de *Standard_D1_v2*. Pode sobrepor-se para reduzir o tempo total de personalização, ou porque pretende criar as imagens que dependem de certos tamanhos VM, como GPU/HPC, etc.

## <a name="how-it-works"></a>Como funciona

Ao criar o desbloqueio, a tarefa cria um recipiente na conta de armazenamento, denominado *imagebuilder-vststask*. Fecha e carrega os seus artefactos de construção e cria um Sas Token para o ficheiro zip.

A tarefa utiliza as propriedades passadas para a tarefa de criar o artefacto do Modelo de Construtor de Imagem. A tarefa faz o seguinte:
* Descarrega o arquivo zip de construção de artefactos e quaisquer outros scripts associados. Os ficheiros são guardados numa conta de armazenamento no grupo de recursos temporários do Construtor de Imagens `IT_<DestinationResourceGroup>_<TemplateName>` .
* Cria um t_ pré-fixado do modelo *e* um número inteiro monotónico de 10 dígitos. O modelo é guardado para o grupo de recursos que selecionou. O modelo existe durante a duração da construção no grupo de recursos. 

Exemplo de saída:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

Quando a construção da imagem começa, o estado de execução é relatado nos registos de libertação:

```text
starting run template...
```

Quando a construção da imagem termina, vê-se uma saída semelhante ao seguinte texto:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

O modelo de imagem e `IT_<DestinationResourceGroup>_<TemplateName>` é eliminado.

Pode pegar na variável VSTS '$(imageUri)' e usá-la na tarefa seguinte ou apenas usar o valor e construir um VM.

## <a name="output-devops-variables"></a>Variáveis de devOps de saída

Pub/oferta/SKU/Versão da imagem do mercado de origem:
* $(pirPublisher)
* $(pirOffer)
* $(pirSku)
* $(pirVersão)

Imagem URI - O ResourceID da imagem distribuída:
* $(imageUri)

## <a name="faq"></a>FAQ

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>Posso usar um modelo de imagem existente que já criei, fora do DevOps?

Atualmente, não neste momento.

### <a name="can-i-specify-the-image-template-name"></a>Posso especificar o nome do modelo de imagem?

N.º Um nome de modelo único é usado e, em seguida, apagado.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>O construtor de imagens falhou. Como posso resolver problemas?

Se houver uma falha de construção, a tarefa DevOps não elimina o grupo de recursos de encenação. Pode aceder ao grupo de recursos de encenação que contém o registo de personalização de construção.

Verá um erro no registo de DevOps para a tarefa VM Image Builder e verá a localização .log personalização. Por exemplo:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Exemplo DevOps erro de tarefa que mostra uma falha.":::

Para obter mais informações sobre a resolução de problemas, consulte [o Serviço de Construtores de Imagem Azure .](image-builder-troubleshoot.md) 

Depois de investigar a falha, pode eliminar o grupo de recursos de encenação. Primeiro, elimine o artefacto do recurso do modelo de imagem. O artefacto é pré-fixado com *t_* e pode ser encontrado no registo de construção de tarefas DevOps:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

O artefacto do recurso do modelo de imagem está no grupo de recursos especificado inicialmente na tarefa. Quando acabares de resolver problemas, apaga o artefacto. Se eliminar utilizando o portal Azure, dentro do grupo de recursos, selecione **Mostrar Tipos Ocultos,** para visualizar o artefacto.


## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [a visão geral do Azure Image Builder](../image-builder-overview.md).
