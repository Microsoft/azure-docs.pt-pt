---
title: Criar um modelo de construtor de imagens do Azure (pré-visualização)
description: Saiba como criar um modelo para utilizar com o construtor de imagens do Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b4646879eb7eeecf41852baab7ab64e4053b05e1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159604"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Pré-visualização: Criar um modelo de construtor de imagens do Azure 

Construtor de imagens do Azure utiliza um ficheiro. JSON para passar informações para o serviço de construtor de imagens. Neste artigo vamos abordará as secções do ficheiro json, para que possa criar seu próprio. Para ver exemplos de ficheiros. JSON completo, consulte a [GitHub de construtor de imagem do Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Este é o formato de modelo básico:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "<build timeout in minutes>": {}, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Versão de tipo e a API

O `type` é o tipo de recurso, que tem de ser `"Microsoft.VirtualMachineImages/imageTemplates"`. O `apiVersion` irão mudar ao longo do tempo à medida que as alterações de API, mas deve ser `"2019-05-01-preview"` para pré-visualização.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

A localização é a região onde a imagem personalizada será criada. Para a pré-visualização do construtor de imagens, são suportadas as seguintes regiões:

- EUA Leste
- EUA Leste 2
- EUA Centro-Oeste
- EUA Oeste
- EUA Oeste 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Depende de (opcional)

Esta secção opcional pode ser utilizada para garantir que as dependências são concluídas antes de continuar. 

```json
    "dependsOn": [],
```

Para obter mais informações, consulte [definir as dependências de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identidade
Por predefinição, suporta de construtor de imagens utilizando scripts ou copiar ficheiros a partir de várias localizações, como o armazenamento do GitHub e o Azure. Para as utilizar, têm de ser acessíveis publicamente.

Também pode utilizar uma identidade gerida de Azure User-Assigned, definida por si, para permitir o acesso de construtor de imagens armazenamento do Azure, desde que a identidade recebeu um mínimo de "Leitor de dados de Blob de armazenamento" na conta de armazenamento do Azure. Isso significa que não é necessário tornar os blobs de armazenamento acessíveis externamente ou Tokens de SAS de configuração.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Para obter um exemplo completo, consulte [ utilizar uma identidade gerida de Azure User-Assigned para aceder aos ficheiros no armazenamento do Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Suporte de construtor para uma identidade de User-Assigned de imagem: • suporta uma identidade única só • não suporta nomes de domínio personalizados

Para obter mais informações, consulte [o que há de identidades geridas para recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Para obter mais informações sobre como implementar esta funcionalidade, consulte [configurar geridos identidades para recursos do Azure na VM do Azure com a CLI do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriedades: origem

O `source` secção contém informações sobre a imagem de origem que será utilizada pelo construtor de imagens.

A API requer um "SourceType" que define a origem para a compilação de imagem, atualmente, existem três tipos:
- ISO - Utilize esta opção quando a origem é uma ISO RHEL.
- PlatformImage - indicado na imagem de origem é uma imagem do Marketplace.
- ManagedImage - Utilize esta opção quando a partir de uma imagem gerida regular.
- SharedImageVersion - isto é utilizado quando estiver a utilizar uma versão de imagem na Galeria de imagens partilhado como a origem.

### <a name="iso-source"></a>Origem ISO

Construtor de imagens do Azure só suporta a utilização publicados Red Hat Enterprise Linux 7.x binário DVD ISOs, para pré-visualização. Suporta o construtor de imagens:
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Para obter o `sourceURI` e `sha256Checksum` valores, acedas à `https://access.redhat.com/downloads` , em seguida, selecione o produto **Red Hat Enterprise Linux**e uma versão suportada. 

Na lista de **instaladores e imagens de Red Hat Enterprise Linux Server**, precisa copiar o link para o DVD de binários do Red Hat Enterprise Linux 7.x e a soma de verificação.

> [!NOTE]
> Os tokens de acesso das ligações são atualizados em intervalos frequentes, para que toda vez que pretende submeter um modelo, tem de verificar se o RH vincular o endereço foi alterado.
 
### <a name="platformimage-source"></a>Origem de PlatformImage 
Construtor de imagens do Azure suporta as seguintes imagens do Azure Marketplace:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


As propriedades aqui são os mesmos que são utilizados para criar VMS, com a CLI de AZ, execute o abaixo para obter as propriedades: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Versão não pode ser "mais recente", tem de utilizar o comando acima para obter um número de versão. 

### <a name="managedimage-source"></a>Origem de ManagedImage

Define a imagem de origem como uma imagem gerida existente de um VHD ou VM generalizada. A imagem gerida de origem tem de ser de um SO suportado e estar na mesma região que o modelo de construtor de imagens do Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

O `imageId` deve ser o ResourceId da imagem gerida. Utilize `az image list` para listar as imagens disponíveis.


### <a name="sharedimageversion-source"></a>Origem de SharedImageVersion
Define uma versão de imagem existente numa galeria de imagens partilhado a imagem de origem. A versão da imagem tem de ser de um SO suportado e a imagem deve ser replicada para a mesma região que o modelo de construtor de imagens do Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

O `imageVersionId` deve ser o ResourceId da versão da imagem. Uso [lista de versão da imagem de sig az](/cli/azure/sig/image-version#az-sig-image-version-list) para versões de imagem de lista.

## <a name="properties-customize"></a>Propriedades: Personalizar


Construtor de imagens suporta vários "personalizadores'. Personalizadores são funções que são utilizadas para personalizar a imagem, por exemplo, a execução de scripts ou do reinício dos servidores. 

Quando utilizar `customize`: 
- Pode usar vários personalizadores, mas tem de ter um exclusivo `name`.
- Executar personalizadores na ordem especificada no modelo.
- Se um personalizador falhar, em seguida, o componente de personalização todo falharão e relatar um erro.
- Considere quanto tempo sua compilação de imagem requerem e ajustar a propriedade 'buildTimeoutInMinutes' para permitir tempo suficiente concluir o construtor de imagens.
- É vivamente recomendado que testar o script cuidadosamente antes de o utilizar num modelo. Depurar o script na sua própria VM será mais fácil.
- Não coloque dados confidenciais nos scripts. 
- As localizações de script tem de estar acessível publicamente, exceto se estiver a utilizar [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
A seção de personalizar é uma matriz. Construtor de imagens do Azure será executada por meio de personalizadores ordem sequencial. Qualquer falha em qualquer personalizador irá falhar o processo de compilação. 
 
 
### <a name="shell-customizer"></a>Personalizador de shell

O suporte de personalizador de shell execução de scripts de shell, estes têm de ser publicamente acessíveis para o IB acessá-los.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Suporte do sistema operacional: Linux 
 
Personalize propriedades:

- **tipo de** – Shell 
- **nome** – nome para a personalização de controlo 
- **scriptUri** -URI para a localização do ficheiro 
- **inline** -matriz de comandos da shell, separados por vírgulas.
 
> [!NOTE]
> Ao executar o personalizador de shell com a origem do ISO do RHEL, terá de garantir que seus identificadores de shell de personalização primeira registar um servidor de elegibilidade do Red Hat, antes de ocorre qualquer personalização. Após a conclusão da personalização, o script deve anular o registo com o servidor de elegibilidade.

### <a name="windows-restart-customizer"></a>Personalizador de reinício do Windows 
O personalizador de reinício permite-lhe reiniciar uma VM do Windows e aguardar que esta fique novamente online, isto permite-lhe instalar o software que requeira um reinício.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Suporte do sistema operacional: Windows
 
Personalize propriedades:
- **Tipo de**: WindowsRestart
- **restartCommand** -comando a executar o reinício (opcional). A predefinição é `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – comando para verificar se o reinício com êxito (opcional). 
- **restartTimeout** -reiniciar o tempo limite especificado como uma cadeia de magnitude e a unidade. Por exemplo, `5m` (5 minutos) ou `2h` (2 horas). A predefinição é: '5m'


### <a name="powershell-customizer"></a>Personalizador de PowerShell 
O suporte de personalizador de shell que executam scripts do PowerShell e o comando inline, os scripts tem de ser publicamente acessíveis para o IB acessá-los.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Suporte do sistema operacional: Windows e Linux

Personalize propriedades:

- **tipo de** – PowerShell.
- **scriptUri** -URI para a localização do ficheiro de script do PowerShell. 
- **inline** – Inline de comandos para ser executado, separados por vírgulas.
- **valid_exit_codes** – opcional, códigos de válido que podem ser retornados de script/inline de comando, isso evitará comunicada falha do comando de script/inline.

### <a name="file-customizer"></a>Personalizador de ficheiro

O personalizador de arquivo permite que o construtor de imagens, transferir um ficheiro a partir de um GitHub ou o armazenamento do Azure. Se tiver um pipeline de compilação de imagem que se baseia nos artefactos de compilação, em seguida, pode definir o personalizador de ficheiros para transferir a partir do compartilhamento de compilação e mover os artefactos para a imagem.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Suporte do sistema operacional: Linux e Windows 

Propriedades de personalizador de ficheiro:

- **sourceUri** -um ponto de extremidade de armazenamento acessível, isto pode ser armazenamento GitHub ou do Azure. Só pode transferir um ficheiro, não é um diretório inteiro. Se precisar de transferir um diretório, utilize um ficheiro comprimido e Descompacte-o com os personalizadores Shell ou do PowerShell. 
- **destino** – este é o nome de ficheiro e caminho completo de destino. Qualquer caminho referenciado e subdiretórios tem de existir, use os personalizadores Shell ou do PowerShell para configurar estes com antecedência. Pode utilizar os personalizadores de script para criar o caminho. 

Isto é suportado pelo diretórios do Windows e Linux caminhos, mas existem algumas diferenças: 
- SO Linux – o único caminho de imagem builder pode escrever é /tmp.
- Windows – sem restrição de caminho, mas o caminho tem de existir.
 
 
Se houver um erro a tentar transferir o ficheiro ou colocá-lo num diretório especificado, o passo de personalizar falhará e terá o customization.log.

Ficheiros no personalizador de ficheiro podem ser baixados do armazenamento do Azure utilizando [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalizar 
Por predefinição, o construtor de imagens do Azure também será executado "desaprovisionar" código no final de cada fase de personalização de imagem, para "generalize" a imagem. A generalizar a é um processo em que a imagem é configurada para que possa ser reutilizado para criar várias VMs. Para VMs do Windows, o construtor de imagens do Azure utiliza Sysprep. Para o Linux, o construtor de imagens do Azure é executado "waagent-desaprovisionamento '. 

Os comandos de utilizadores do construtor de imagens para generalizar podem não ser adequados para cada situação, para que o construtor de imagens do Azure permite-lhe personalizar este comando, se necessário. 

Se estiver a migrar personalização existente e estiver a utilizar os comandos de Sysprep/waagent diferentes, pode utilizar os comandos de genéricos do construtor de imagens e, se a criação de VM falhar, utilize os seus próprios comandos do Sysprep ou waagent.

Se o construtor de imagens do Azure cria uma imagem personalizada do Windows com êxito e criar uma VM a partir, em seguida, ver que a criação de VM falha ou não for concluída com êxito, terá de rever a documentação do Sysprep do Windows Server ou faça um pedido de suporte com o Equipe de suporte de serviços do cliente de Sysprep do Windows Server, que pode resolver problemas e aconselhá-lo sobre a utilização correta de Sysprep.


#### <a name="default-sysprep-command"></a>Comando de Sysprep padrão
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Comando de desaprovisionamento de Linux predefinida

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Substituir os comandos
Para substituir os comandos, utilize provisioners de script do PowerShell ou a Shell para criar os ficheiros de comando com o nome de ficheiro exata e colocá-los nos diretórios corretos:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Construtor de imagens irá ler estes comandos, estes são escritos nos registos de AIB 'customization.log'. Ver [resolução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) sobre como recolher registos.
 
## <a name="properties-distribute"></a>Propriedades: distribuir

Construtor de imagens do Azure suporta três destinos de distribuição: 

- **managedImage** - gerido imagem.
- **sharedImage** -partilhado Galeria de imagens.
- **VHD** -VHD numa conta de armazenamento.

Pode distribuir uma imagem por ambos os tipos de destino na mesma configuração, consulte [exemplos](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Como pode ter mais do que um destino para distribuir pelos, o construtor de imagens mantém um Estado para cada destino de distribuição que pode ser acedido através da consulta o `runOutputName`.  O `runOutputName` é um objeto pode consultar publicar distribuição para obter informações sobre essa distribuição. Por exemplo, pode consultar a localização do VHD ou regiões em que a versão da imagem foi replicada para. Esta é uma propriedade de cada destino de distribuição. O `runOutputName` tem de ser exclusivo para cada destino de distribuição.
 
### <a name="distribute-managedimage"></a>Distribute: managedImage

O resultado de imagem será um recurso de imagem gerida.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribua as propriedades:
- **tipo de** – managedImage 
- **imageId** – o ID de recurso de imagem de destino, era esperado o formato: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **localização** -localização da imagem do gerido.  
- **runOutputName** – exclusivo nome para identificar a distribuição.  
- **artifactTags** -especificadas pelo utilizador opcional e etiquetas de par chave-valor.
 
 
> [!NOTE]
> O grupo de recursos de destino tem de existir.
> Se pretender que a imagem distribuída para uma região diferente, irá aumentar o tempo de implementação. 

### <a name="distribute-sharedimage"></a>Distribute: sharedImage 
Galeria de imagens de partilhado do Azure é um novo serviço de gerenciamento de imagens, que permite a gestão de replicação de região de imagem, controle de versão e a partilha de imagens personalizadas. Construtor de imagens do Azure suporta a distribuição com este serviço, portanto, pode distribuir imagens de regiões suportadas pelo galerias de imagem partilhada. 
 
Uma galeria de imagens partilhado é constituída por: 
 
- Galeria de – contêiner para várias imagens partilhadas. Uma galeria é implementada numa única região.
- Definições de imagem - um agrupamento conceitual para imagens. 
- Versões de imagem, este é um tipo de imagem utilizado para implementar um conjunto VM ou de escala. Versões de imagem podem ser replicadas para outras regiões em que as VMs têm de ser implementado.
 
Antes de pode distribuir para a Galeria de imagens, tem de criar uma galeria e uma definição de imagem, consulte [partilhados imagens](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Distribua as propriedades de galerias de imagem partilhada:

- **type** - sharedImage  
- **galleryImageId** – ID da Galeria de imagem partilhada. O formato é: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** – exclusivo nome para identificar a distribuição.  
- **artifactTags** -especificadas pelo utilizador opcional e etiquetas de par chave-valor.
- **replicationRegions** -matriz de regiões para replicação. Uma das regiões do tem de ser a região em que a galeria é implementada.
 
> [!NOTE]
> Pode usar o construtor de imagens do Azure numa região diferente para a galeria, mas o serviço de construtor de imagens do Azure, terá de transferir a imagem entre os datacenters e esta ação irá demorar mais tempo. Construtor de imagens serão automaticamente versão a imagem, com base num número inteiro monotónica, não é possível especificar atualmente. 

### <a name="distribute-vhd"></a>Distribua: VHD  
O utilizador pode apresentar a um VHD. Pode, em seguida, copie o VHD e utilizá-lo para publicar no Azure MarketPlace ou utilize com o Azure Stack.  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
Suporte do sistema operacional: Windows e Linux

Distribua os parâmetros VHD:

- **tipo de** -VHD.
- **runOutputName** – exclusivo nome para identificar a distribuição.  
- **etiquetas** -especificadas pelo utilizador opcional e etiquetas de par chave-valor.
 
Construtor de imagens do Azure não permite ao usuário para especificar uma localização de conta de armazenamento, mas pode consultar o estado do `runOutputs` para obter a localização.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Assim que tiver sido criado o VHD, copie-o para uma localização diferente, logo que possível. O VHD é armazenado numa conta de armazenamento no grupo de recursos temporário, criado quando o modelo de imagem foi submetido para o serviço de construtor de imagens do Azure. Se eliminar o modelo de imagem, em seguida, irá perder o VHD. 
 
## <a name="next-steps"></a>Passos Seguintes

Existem ficheiros de. JSON de exemplo para diferentes cenários no [GitHub de construtor de imagem do Azure](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
