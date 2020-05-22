---
title: Criar um modelo de construtor de imagem Azure (pré-visualização)
description: Aprenda a criar um modelo para usar com o Azure Image Builder.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: f567114613f484f0765a6e007c3f0ba97480a968
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779335"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Pré-visualização: Crie um modelo de construtor de imagem Azure 

O Azure Image Builder utiliza um ficheiro .json para passar informações para o serviço Image Builder. Neste artigo vamos ver as secções do ficheiro JSON, para que possas construir o teu. Para ver exemplos de ficheiros .json completos, consulte o [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

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
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tipo e versão API

O `type` é do tipo de recurso, que deve ser `"Microsoft.VirtualMachineImages/imageTemplates"` . O irá mudar ao longo do tempo à `apiVersion` medida que a API muda, mas deve ser `"2019-05-01-preview"` para pré-visualização.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Localização

A localização é a região onde será criada a imagem personalizada. Para a pré-visualização do Image Builder, são suportadas as seguintes regiões:

- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Centro-Oeste
- E.U.A. Oeste
- E.U.A.Oeste 2
- Europa do Norte
- Europa ocidental


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmPerfil
Por padrão, o Image Builder utilizará um VM de construção "Standard_D1_v2", pode anular este, por exemplo, se quiser personalizar uma Imagem para um VM GPU, precisa de um tamanho DE GPU VM. Isto é opcional.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Por padrão, o Image Builder não alterará o tamanho da imagem, utilizará o tamanho a partir da imagem de origem. Pode aumentar o tamanho do Disco OS (Win e Linux), este é opcional, e um valor de 0 meios deixar o mesmo tamanho que a imagem de origem. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Se não especificar quaisquer propriedades VNET, então o Image Builder criará o seu próprio VNET, PUBLIC IP e NSG. O IP Público é utilizado para que o serviço se comunique com o VM de construção, no entanto, se não quiser um IP público ou quiser que o Image Builder tenha acesso aos seus recursos VNET existentes, tais como servidores de configuração (DSC, Chef, Marioneta, Ansible), partilhas de ficheiros etc., então pode especificar um VNET. Para mais informações, reveja a documentação em [rede,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)isto é opcional.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Etiquetas

Estes são os pares chave/valor que pode especificar para a imagem que é gerada.

## <a name="depends-on-optional"></a>Depende (opcional)

Esta secção opcional pode ser utilizada para garantir que as dependências sejam concluídas antes de prosseguir. 

```json
    "dependsOn": [],
```

Para mais informações, consulte [Definir dependências de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identidade
Por padrão, o Image Builder suporta a utilização de scripts ou a cópia de ficheiros de vários locais, tais como o armazenamento GitHub e Azure. Para usá-los, devem ser acessíveis ao público.

Também pode utilizar uma Identidade Gerida Atribuída ao Utilizador Azure, definida por si, para permitir o acesso do Construtor de Imagem ao Armazenamento Azure, desde que a identidade tenha sido concedida um mínimo de 'Storage Blob Data Reader' na conta de armazenamento Azure. Isto significa que não precisa de tornar as bolhas de armazenamento externamente acessíveis, nem configurar tokens SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Para um exemplo completo, consulte [Utilize uma Identidade Gerida Atribuída ao Utilizador Azure para aceder a ficheiros no Armazenamento Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Suporte do Image Builder para uma identidade atribuída ao utilizador: • Suporta uma única identidade apenas • Não suporta nomes de domínio personalizados

Para saber mais, veja [o que é gerida identidades para os recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
Para obter mais informações sobre a implementação desta funcionalidade, consulte [as identidades geridas pela Configure para os recursos Do Azure num Azure VM utilizando o Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriedades: fonte

A `source` secção contém informações sobre a imagem de origem que serão utilizadas pelo Image Builder.

A API requer um 'SourceType' que define a fonte para a construção da imagem, atualmente existem três tipos:
- PlatformImage - indicou que a imagem de origem é uma imagem do Marketplace.
- ManagedImage - use isto quando começar de uma imagem gerida regularmente.
- SharedImageVersion - isto é usado quando se está a usar uma versão de imagem numa Galeria de Imagem Partilhada como fonte.

### <a name="iso-source"></a>Fonte ISO
Estamos a depreciar esta funcionalidade do construtor de imagens, uma vez que existem agora [imagens RHEL Bring Your Own Subscription,](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)por favor reveja as cronologias abaixo:
    * 31 de março de 2020 - Os modelos de imagem com fontes ISO RHEL serão agora mais aceites pelo fornecedor de recursos.
    * 30 de abril de 2020- Os modelos de imagem que contenham fontes iso rhel não serão mais processados.

### <a name="platformimage-source"></a>Fonte de PlatformImage 
O Azure Image Builder suporta o Windows Server e o cliente, e as imagens do Linux Azure Marketplace, consulte [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) a lista completa. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


As propriedades aqui são as mesmas que são usadas para criar VM's, usando AZ CLI, executar o abaixo para obter as propriedades: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Pode utilizar 'mais recente' na versão, a versão é avaliada quando a construção da imagem ocorre, não quando o modelo é submetido. Se utilizar esta funcionalidade com o destino da Galeria de Imagem Partilhada, pode evitar reenviar o modelo e reexecutar a construção da imagem em intervalos, para que as suas imagens sejam recriadas a partir das imagens mais recentes.

### <a name="managedimage-source"></a>Fonte de Imagem Gerida

Define a imagem de origem como uma imagem gerida existente de um VHD ou VM generalizado. A imagem gerida pela fonte deve ser de um SISTEMA suportado, e estar na mesma região que o seu modelo azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Deve `imageId` ser o ResourceId da imagem gerida. Utilize `az image list` para listar imagens disponíveis.


### <a name="sharedimageversion-source"></a>Fonte de Versão Partilhada ImageVersion
Define a imagem de origem de uma versão de imagem existente numa Galeria de Imagem Partilhada. A versão de imagem deve ser de um SISTEMA suportado, e a imagem deve ser replicada para a mesma região que o seu modelo de Construtor de Imagem Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Deve `imageVersionId` ser o ResourceId da versão de imagem. Utilize a [lista de versão de imagem az sig](/cli/azure/sig/image-version#az-sig-image-version-list) para listar versões de imagem.

## <a name="properties-buildtimeoutinminutes"></a>Propriedades: buildTimeoutInMinutes

Por padrão, o Construtor de Imagem funcionará durante 240 minutos. Depois disso, vai esgotar-se e parar, quer a construção da imagem esteja ou não completa. Se o tempo for atingido, verá um erro semelhante ao seguinte:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Se não especificar um valor buildTimeoutInMinutes, ou defini-lo para 0, este utilizará o valor predefinido. Pode aumentar ou diminuir o valor, até ao máximo de 960mins (16horas). Para windows, não recomendamos que este ajuste abaixo dos 60 minutos. Se descobrir que está a atingir o intervalo, reveja os [registos,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)para ver se o passo de personalização está à espera de algo como a entrada do utilizador. 

Se você descobrir que precisa de mais tempo para as personalizações para completar, coloque isto no que você acha que precisa, com um pouco de sobrecarga. Mas não o coloque muito alto porque pode ter de esperar que fique fora de tempo antes de ver um erro. 


## <a name="properties-customize"></a>Propriedades: personalizar

O Image Builder suporta vários 'personalizadores'. Os personalizadores são funções que são usadas para personalizar a sua imagem, como executar scripts ou reiniciar servidores. 

Ao `customize` utilizar: 
- Pode utilizar vários personalizadores, mas devem ter um `name` exclusivo.
- Os personalizadores executam na ordem especificada no modelo.
- Se um personalizador falhar, todo o componente de personalização falhará e reportará um erro.
- É fortemente aconselhável testar o script cuidadosamente antes de usá-lo num modelo. Depurar o guião por si só será mais fácil.
- Não coloque dados sensíveis nos scripts. 
- Os locais do script precisam de ser acessíveis ao público, a menos que esteja a utilizar [o MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
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

 
A secção personalizada é uma matriz. O Azure Image Builder passará pelos personalizadores por ordem sequencial. Qualquer falha em qualquer personalizista falhará no processo de construção. 
 
 
### <a name="shell-customizer"></a>Personalizador de concha

O personalizador suporta scripts de conchas em execução, estes devem ser acessíveis ao público para que o IB possa aceder aos mesmos.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
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

Suporte sO: Linux 
 
Personalize propriedades:

- **tipo** – Concha 
- **nome** - nome para rastrear a personalização 
- **scriptUri** - URI para a localização do ficheiro 
- **inline** - matriz de comandos de concha, separados por vírgulas.
- **sha256Checksum** - Valor da verificação sha256 do ficheiro, gera-se isto localmente, e então o Image Builder verificará e validará.
    * Para gerar o sha256Checksum, utilizando um terminal em mac/linux executado:`sha256sum <fileName>`


Para que os comandos sejam executados com privilégios de super utilizador, devem ser pré-fixados com `sudo` .

> [!NOTE]
> Ao executar o personalizador de conchas com fonte RHEL ISO, você precisa garantir que as suas primeiras alças de concha de personalização se registem com um servidor de direito red hat antes de qualquer personalização ocorrer. Uma vez concluída a personalização, o script deve desregistar-se com o servidor de direito.

### <a name="windows-restart-customizer"></a>Personalizador de reinício do Windows 
O personalizador Restart permite reiniciar um VM do Windows e esperar que volte a funcionar, isto permite instalar software que requer um reboot.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Suporte sOs: Windows
 
Personalize propriedades:
- **Tipo**: WindowsRestart
- **reiniciar Comando** - Comando para executar o reinício (opcional). A predefinição é `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **reiniciarCheckCommand** – Comando para verificar se o reinício foi bem sucedido (opcional). 
- **reiniciar Timeout** - Tempo de reinício especificado como uma cadeia de magnitude e unidade. Por exemplo, `5m` (5 minutos) ou `2h` (2 horas). O padrão é: '5m'

### <a name="linux-restart"></a>Reinício do Linux  
No entanto, não existe um personalizador Linux Restart, se estiver a instalar controladores ou componentes que exijam um reinício, pode instalá-los e invocar um reinício utilizando o personalizadora Shell, existindo um intervalo de 20min SSH para o VM de construção.

### <a name="powershell-customizer"></a>Personalizador PowerShell 
O personalizador suporta a execução de scripts PowerShell e comando inline, os scripts devem ser acessíveis ao público para que o IB aceda aos mesmos.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Suporte para os SISTEMAS: Windows e Linux

Personalize propriedades:

- **tipo** – PowerShell.
- **scriptUri** - URI para a localização do ficheiro de script PowerShell. 
- **linha** de linha – Comandos inline a executar, separados por vírgulas.
- **validOsCódigosExit** – Códigos opcionais e válidos que podem ser devolvidos a partir do comando script/inline, isto evitará a falha reportada do comando script/inline.
- **runElevated** – Opcional, booleano, suporte para executar comandos e scripts com permissões elevadas.
- **sha256Checksum** - Valor da verificação sha256 do ficheiro, gera-se isto localmente, e então o Image Builder verificará e validará.
    * Para gerar o sha256Checksum, usando uma PowerShell no Windows [Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Personalizador de ficheiros

O personalizador de ficheiros permite ao construtor de imagens descarregar um ficheiro a partir de um armazenamento GitHub ou Azure. Se tiver um pipeline de construção de imagem que dependa de artefactos de construção, pode então definir o personalizador de ficheiros para descarregar a partir da parte de construção, e mover os artefactos para a imagem.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Suporte sO: Linux e Windows 

Propriedades de personalização de ficheiros:

- **sourceUri** - um ponto final de armazenamento acessível, este pode ser o armazenamento GitHub ou Azure. Só pode descarregar um ficheiro, não um diretório inteiro. Se precisar de descarregar um diretório, utilize um ficheiro comprimido e, em seguida, descomprima-o utilizando os personalizadores Shell ou PowerShell. 
- **destino** – este é o caminho de destino completo e nome de arquivo. Qualquer caminho e subdiretórios referenciados devem existir, utilizar os personalizadores Shell ou PowerShell para os configurar previamente. Pode utilizar os personalizadores de script para criar o caminho. 

Isto é suportado por diretórios windows e caminhos linux, mas existem algumas diferenças: 
- Linux OS's – o único caminho para o qual o construtor de imagem pode escrever é /tmp.
- Janelas – Sem restrição de caminho, mas o caminho deve existir.
 
 
Se houver um erro ao tentar descarregar o ficheiro, ou colocá-lo num diretório especificado, o passo personalizado falhará, e isso estará no registo de personalização.log.

> [!NOTE]
> O personalizador de ficheiros só é adequado para pequenos downloads de ficheiros, < de 20MB. Para downloads de ficheiros maiores, utilize um script ou um comando inline, o código de utilização para descarregar ficheiros, tais como, Linux `wget` ou `curl` Windows, `Invoke-WebRequest` .

Os ficheiros do personalizador de ficheiros podem ser descarregados a partir do Armazenamento Azure utilizando [mSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Personalizador de atualização do Windows
Este personalizador é construído sobre a [comunidade Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) para Packer, que é um projeto de código aberto mantido pela comunidade Packer. A Microsoft testa e valida o provisionador com o serviço Image Builder, e irá apoiar a investigação de problemas com ele, e trabalhará para resolver problemas, no entanto o projeto open source não é oficialmente suportado pela Microsoft. Para obter documentação detalhada e ajudar com o Windows Update Provisioner, consulte o repositório do projeto.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Suporte para os SISTEMAS: Windows

Personalize propriedades:
- **tipo** – WindowsUpdate.
- **pesquisaCritérios** - Opcional, define que tipo de atualizações são instaladas (Recomendado, Importante etc.), BrowseOnly=0 e IsInstalled=0 (Recomendado) é o padrão.
- **filtros** – Opcional, permite especificar um filtro para incluir ou excluir atualizações.
- **actualizaçãoLimite** – Opcional, define quantas atualizações podem ser instaladas, padrão 1000.
 
 

### <a name="generalize"></a>Generalizar 
Por padrão, o Azure Image Builder também executará o código de 'desprovisionamento' no final de cada fase de personalização de imagem, para 'generalizar' a imagem. Generalizar é um processo em que a imagem é configurada para que possa ser reutilizada para criar vários VMs. Para VMs Windows, o Azure Image Builder utiliza sysprep. Para linux, o Azure Image Builder executa 'waagent -deprovisionamento'. 

Os comandos Image Builder para generalizar podem não ser adequados para todas as situações, pelo que o Azure Image Builder lhe permitirá personalizar este comando, se necessário. 

Se estiver a migrar a personalização existente, e estiver a utilizar diferentes comandos Sysprep/waagent, pode utilizar os comandos genéricos do Construtor de Imagem, e se a criação de VM falhar, utilize os seus próprios comandos Sysprep ou waagent.

Se o Azure Image Builder criar uma imagem personalizada do Windows com sucesso, e criar um VM a partir dela, então descubra que a criação vM falha ou não se completa com sucesso, terá de rever a documentação de Sysprep do Windows Server ou levantar um pedido de suporte com a equipa de suporte ao serviço de apoio ao cliente do Windows Server, que pode resolver problemas e aconselhar sobre o uso correto do Sysprep.


#### <a name="default-sysprep-command"></a>Comando Sysprep padrão
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>Comando de deprovisionamento Deprovision Linux padrão

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Sobrepondo os Comandos
Para anular os comandos, utilize os provisionadores de scriptPowerShell ou Shell para criar os ficheiros de comando com o nome exato do ficheiro e colocá-los nos diretórios corretos:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

O Image Builder irá ler estes comandos, estes estão escritos para os registos AIB, 'customization.log'. Consulte a [resolução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) sobre como recolher registos.
 
## <a name="properties-distribute"></a>Propriedades: distribuir

O Azure Image Builder suporta três alvos de distribuição: 

- **managedImage** - imagem gerida.
- **sharedImage** - Galeria de Imagem Partilhada.
- **VHD** - VHD numa conta de armazenamento.

Pode distribuir uma imagem para ambos os tipos de alvo na mesma configuração, por favor, consulte [exemplos](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Como pode ter mais do que um alvo para distribuir, o Image Builder mantém um estado para cada alvo de distribuição que pode ser acedido consultando o `runOutputName` .  O é um objeto que pode consultar a `runOutputName` distribuição de posts para obter informações sobre essa distribuição. Por exemplo, pode consultar a localização do VHD, ou regiões para as quais a versão de imagem foi replicada, ou a versão SIG Image criada. Esta é uma propriedade de todos os alvos de distribuição. O `runOutputName` deve ser único em cada alvo de distribuição. Aqui está um exemplo, isto é consultar uma distribuição da Galeria de Imagem Partilhada:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Saída:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribuir: managedImage

A saída de imagem será um recurso de imagem gerido.

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
 
Distribuir propriedades:
- **tipo** – managedImage 
- **imageId** – Id de recurso da imagem de destino, formato esperado: /subscrições/ \< subscriçãoId>/resourceGroups/ \< destinationResourceGroupName>/providers/Microsoft.Compute/images/imageName \<>
- **localização** - localização da imagem gerida.  
- **runOutputName** – nome único para identificar a distribuição.  
- **artefactoSTags** - Utilizador opcional especificado etiquetas de par de valor chave.
 
 
> [!NOTE]
> O grupo de recursos de destino deve existir.
> Se quiser que a imagem seja distribuída para outra região, aumentará o tempo de implantação. 

### <a name="distribute-sharedimage"></a>Distribuir: sharedImage 
A Azure Shared Image Gallery é um novo serviço de Gestão de Imagem que permite gerir a replicação da região de imagem, versonizar e partilhar imagens personalizadas. O Azure Image Builder suporta a distribuição com este serviço, para que possa distribuir imagens para regiões apoiadas por Galerias de Imagem Partilhada. 
 
Uma Galeria de Imagem Partilhada é composta por: 
 
- Galeria - Recipiente para múltiplas imagens partilhadas. Uma galeria é implantada numa região.
- Definições de imagem - um agrupamento conceptual para imagens. 
- Versões de imagem - este é um tipo de imagem usado para implementar um VM ou conjunto de escala. As versões de imagem podem ser replicadas para outras regiões onde os VMs precisam de ser implantados.
 
Antes de poder distribuir para a Galeria de Imagem, tem de criar uma galeria e uma definição de imagem, ver [imagens partilhadas.](shared-images.md) 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Distribua propriedades para galerias de imagem partilhada:

- **tipo** - sharedImage  
- **galleryImageId** – ID da galeria de imagens partilhadas. O formato é: /subscrições/ \< subscriçãoId>/resourceGroups/ \< resourceGroupName>/providers/Microsoft.Compute/galleries/ \< sharedImageGalleryName>/images/ \< imageGalleryName>.
- **runOutputName** – nome único para identificar a distribuição.  
- **artefactoSTags** - Utilizador opcional especificado etiquetas de par de valor chave.
- **replicaçõesRegiões** - Conjunto de regiões para replicação. Uma das regiões deve ser a região onde a Galeria está implantada.
 
> [!NOTE]
> Pode utilizar o Azure Image Builder numa região diferente da galeria, mas o serviço Azure Image Builder terá de transferir a imagem entre os datacenters e isso levará mais tempo. O Image Builder irá automaticamente versonrá a imagem, com base num inteiro monotónico, não pode especificar atualmente. 

### <a name="distribute-vhd"></a>Distribuir: VHD  
Pode ser produzido para um VHD. Em seguida, pode copiar o VHD e usá-lo para publicar no Azure MarketPlace, ou usá-lo com o Azure Stack.  

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
 
Suporte sOs: Windows e Linux

Distribuir parâmetros VHD:

- **tipo** - VHD.
- **runOutputName** – nome único para identificar a distribuição.  
- **tags** - Etiquetas de pares de valor chave especificadas pelo utilizador opcional.
 
O Azure Image Builder não permite que o utilizador especifique a localização da conta de armazenamento, mas pode consultar o estado do `runOutputs` local.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Uma vez criado o VHD, copie-o para um local diferente, o mais rapidamente possível. O VHD é armazenado numa conta de armazenamento no grupo de recursos temporários criado quando o modelo de imagem é submetido ao serviço Azure Image Builder. Se apagar o modelo de imagem, perderá o VHD. 
 
## <a name="next-steps"></a>Passos seguintes

Existem ficheiros de amostra .json para diferentes cenários no [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
