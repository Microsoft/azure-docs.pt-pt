---
title: Criar um modelo de construtor de imagem Azure (pré-visualização)
description: Aprenda a criar um modelo para usar com O Azure Image Builder.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: eb02bff77ffedc0a1f2fee0a186d544c39374dbf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693871"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Pré-visualização: Criar um modelo de construtor de imagens Azure 

O Azure Image Builder utiliza um ficheiro .json para passar informações no serviço Image Builder. Neste artigo vamos ver as secções do ficheiro json, para que possa construir a sua própria. Para ver exemplos de ficheiros .json completos, consulte o [Azure Image Builder GitHub](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts).

Este é o formato básico do modelo:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Versão tipo e API

O `type` tipo de recurso, que deve `"Microsoft.VirtualMachineImages/imageTemplates"` ser. A `apiVersion` mudança será alterada ao longo do tempo à medida que a API muda, mas deve ser `"2020-02-14"` para pré-visualização.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Localização

A localização é a região onde a imagem personalizada será criada. Para a pré-visualização do Construtor de Imagens, as seguintes regiões são apoiadas:

- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Centro-Oeste
- E.U.A. Oeste
- E.U.A. Oeste 2
- Europa do Norte
- Europa Ocidental


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Por predefinição, o Image Builder utilizará um VM de construção "Standard_D1_v2", pode sobrepor-se a isto, por exemplo, se quiser personalizar uma Imagem para um VM GPU, precisa de um tamanho VM GPU. Isto é opcional.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Por predefinição, o Image Builder não alterará o tamanho da imagem, utilizará o tamanho a partir da imagem de origem. **Só** é possível aumentar o tamanho do disco OS (Win e Linux), isto é opcional, e um valor de 0 significa deixar o mesmo tamanho que a imagem de origem. Não é possível reduzir o tamanho do disco DE Para menor do que o tamanho da imagem de origem.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Se não especificar quaisquer propriedades VNET, então o Image Builder criará os seus próprios VNET, Public IP e NSG. O IP público é utilizado para o serviço de comunicação com o VM de construção, no entanto, se não quiser um IP público ou quiser que o Image Builder tenha acesso aos recursos VNET existentes, tais como servidores de configuração (DSC, Chef, Puppet, Ansible), partilhas de ficheiros, etc., então pode especificar um VNET. Para mais informações, [reveja a documentação de networking,](image-builder-networking.md)isto é opcional.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Etiquetas

Estes são pares chave/valor que pode especificar para a imagem gerada.

## <a name="depends-on-optional"></a>Depende (opcional)

Esta secção opcional pode ser utilizada para garantir que as dependências sejam completas antes de prosseguir. 

```json
    "dependsOn": [],
```

Para obter mais informações, consulte [Definir as dependências de recursos.](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)

## <a name="identity"></a>Identidade

Obrigatório - Para que o Construtor de Imagem tenha permissões para ler/escrever imagens, leia nos scripts do Azure Storage deve criar um Azure User-Assigned Identidade, que tenha permissões para os recursos individuais. Para mais detalhes sobre como funcionam as permissões do Image Builder e os passos relevantes, por favor reveja a [documentação.](image-builder-user-assigned-identity.md)


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Suporte do Construtor de Imagem para uma User-Assigned Identidade:
* Suporta apenas uma única identidade
* Não suporta nomes de domínio personalizados

Para saber mais, veja [o que são identidades geridas para os recursos do Azure?](../../active-directory/managed-identities-azure-resources/overview.md)
Para obter mais informações sobre a implementação desta funcionalidade, consulte [as identidades geridas para a Azure num VM Azure utilizando o Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriedades: fonte

A `source` secção contém informações sobre a imagem de origem que será usada pelo Image Builder. Atualmente, o Image Builder apenas suporta de forma nativa a criação de imagens de geração Hyper-V (Gen1) 1 para a Galeria de Imagem Partilhada Azure (SIG) ou Imagem Gerida. Se queres criar imagens da Gen2, então precisas de usar uma imagem de origem da Gen2 e distribuir para VHD. Depois, terá de criar uma Imagem Gerida a partir do VHD e injetá-la no SIG como uma imagem gen2.

A API requer um 'SourceType' que define a fonte para a construção de imagem, atualmente existem três tipos:
- PlatformImage - indicou que a imagem de origem é uma imagem do Marketplace.
- ManagedImage - use isto quando começar a partir de uma imagem gerida regularmente.
- SharedImagesion - isto é usado quando está a usar uma versão de imagem numa Galeria de Imagens Partilhadas como fonte.


> [!NOTE]
> Ao utilizar as imagens personalizadas do Windows existentes, pode executar o comando Sysprep até 8 vezes numa única imagem do Windows, para obter mais informações, consulte a documentação [do sysprep.](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)

### <a name="platformimage-source"></a>Fonte da PlataformaImage 
Azure Image Builder suporta o Windows Server e o cliente, e as imagens linux Azure Marketplace, consulte [aqui](../image-builder-overview.md#os-support) a lista completa. 

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

Pode utilizar o 'mais recente' na versão, a versão é avaliada quando a imagem é construída, não quando o modelo é submetido. Se utilizar esta funcionalidade com o destino Da Galeria de Imagens Partilhadas, pode evitar reenviar o modelo e refazer a construção da imagem em intervalos, para que as suas imagens sejam recriadas a partir das imagens mais recentes.

#### <a name="support-for-market-place-plan-information"></a>Apoio à informação do plano de mercado
Também pode especificar informações do plano, por exemplo:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>Fonte ManagedImage

Define a imagem de origem como uma imagem gerida existente de um VHD ou VM generalizado.

> [!NOTE]
> A imagem gerida pela fonte deve ser de um SISTEMA suportado e a imagem deve ser a mesma região que o seu modelo de Construtor de Imagem Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

O `imageId` deve ser o ResourceId da imagem gerida. Use `az image list` para listar imagens disponíveis.


### <a name="sharedimageversion-source"></a>Fonte partilhada da Imagem
Define a imagem de origem uma versão de imagem existente numa Galeria de Imagens Partilhadas.

> [!NOTE]
> A imagem gerida pela fonte deve ser de um SISTEMA suportado e a imagem deve ser a mesma região que o seu modelo de construtor de imagem Azure, se não, por favor, replique a versão de imagem para a região do Modelo de Construtor de Imagem.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Deve `imageVersionId` ser o ResourceId da versão de imagem. Utilize [a lista de versão de imagem az sig](/cli/azure/sig/image-version#az-sig-image-version-list) para listar versões de imagem.


## <a name="properties-buildtimeoutinminutes"></a>Propriedades: buildTimeoutInMinutes

Por predefinição, o Image Builder funcionará durante 240 minutos. Depois disso, vai parar o tempo e parar, quer a construção da imagem esteja ou não completa. Se o tempo limite for atingido, verá um erro semelhante a este:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Se não especificar um valor buildTimeoutInMinutes, ou o definir para 0, este utilizará o valor predefinido. Pode aumentar ou diminuir o valor, até ao máximo de 960mins (16hrs). Para o Windows, não recomendamos que o ajuste abaixo dos 60 minutos. Se descobrir que está a acertar no tempo limite, reveja os [registos,](image-builder-troubleshoot.md#customization-log)para ver se o passo de personalização está à espera de algo como a entrada do utilizador. 

Se descobrir que precisa de mais tempo para que as personalizações se concretizem, desemote isto no que acha que precisa, com um pouco de sobrecarga. Mas não o coloque demasiado alto porque pode ter de esperar que ele a tempo limite antes de ver um erro. 


## <a name="properties-customize"></a>Propriedades: personalizar

O Image Builder suporta vários 'personalizadores'. Os personalizadores são funções que são usadas para personalizar a sua imagem, como executar scripts ou reiniciar servidores. 

Ao `customize` utilizar: 
- Você pode usar vários personalizadores, mas eles devem ter um único `name` .
- Os personalizadores executam na ordem especificada no modelo.
- Se um personalizador falhar, todo o componente de personalização falhará e reportará um erro.
- É fortemente aconselhável testar o script cuidadosamente antes de usá-lo em um modelo. Depurar o guião no seu próprio VM será mais fácil.
- Não coloque dados sensíveis nos scripts. 
- As localizações do script precisam de ser acessíveis ao público, a menos que esteja a utilizar [o MSI](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-user-assigned-identity).

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

 
A secção de personalização é uma matriz. O Azure Image Builder irá percorrer os personalizadores em ordem sequencial. Qualquer falha em qualquer personalização falhará no processo de construção. 

> [!NOTE]
> Os comandos inline podem ser vistos na definição do modelo de imagem e no Microsoft Support ao ajudar com um caso de suporte. Se tiver informações sensíveis, deverá ser transferida para scripts no Azure Storage, onde o acesso requer autenticação.
 
### <a name="shell-customizer"></a>Personalizador de conchas

O personalizador de concha suporta scripts de conchas, estes devem ser acessíveis ao público para o IB aceder a eles.

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

Suporte os: Linux 
 
Personalize propriedades:

- **tipo** – Concha 
- **nome** - nome para rastrear a personalização 
- **scriptUri** - URI para a localização do ficheiro 
- **inline** - conjunto de comandos de concha, separados por vírgulas.
- **sha256Checksum** - Valor da obra de verificação sha256 do ficheiro, gere-o localmente e, em seguida, o Image Builder verificará e validará.
    * Para gerar o sha256Checksum, utilizando um terminal na execução Mac/Linux: `sha256sum <fileName>`

> [!NOTE]
> Os comandos inline são armazenados como parte da definição do modelo de imagem, pode vê-los quando despeja a definição de imagem, e estes também são visíveis para o Microsoft Support em caso de um caso de suporte para efeitos de resolução de problemas. Se tiver comandos ou valores sensíveis, recomenda-se vivamente que estes sejam movidos para scripts e utilize uma identidade de utilizador para autenticar para o Azure Storage.

#### <a name="super-user-privileges"></a>Privilégios super-utilizadores
Para que os comandos sejam executados com privilégios super-utilizadores, eles devem ser `sudo` pré-fixados, você pode adicionar estes em scripts ou usá-lo em comandos inline, por exemplo:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Exemplo de um script que usa sudo que pode fazer referência usando scriptUri:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Windows reinicia personalizador 
O personalizador Restart permite-lhe reiniciar um VM do Windows e esperar que volte a estar online, o que lhe permite instalar um software que requer um reboot.  

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

Suporte os os: Janelas
 
Personalize propriedades:
- **Tipo**: WindowsRestart
- **reiniciarComeça -** Comando para executar o reinício (opcional). A predefinição é `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **reiniciarCheckCommand** – Comando para verificar se o reinício foi bem sucedido (opcional). 
- **recomeceTimeout** - Reinicie o tempo limite especificado como uma sequência de magnitude e unidade. Por exemplo, `5m` (5 minutos) ou `2h` (2 horas). O padrão é: '5m'

### <a name="linux-restart"></a>Linux reinicia  
No entanto, não existe um personalizador Linux Restart, no entanto, se estiver a instalar controladores, ou componentes que necessitem de um reinício, pode instalá-los e invocar um reinício utilizando o personalizador Shell, existe um tempo limite de 20min SSH para a construção de VM.

### <a name="powershell-customizer"></a>Personalizador PowerShell 
O personalizador de concha suporta scripts PowerShell e comando inline, os scripts devem ser acessíveis ao público para o IB aceder aos mesmos.

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

Suporte oss: Windows e Linux

Personalize propriedades:

- **tipo** – PowerShell.
- **scriptUri** - URI para a localização do ficheiro de script PowerShell. 
- **inline** – Inline comandos a serem executados, separados por vírgulas.
- **válidoExitCodes** – Códigos opcionais e válidos que podem ser devolvidos do comando script/inline, evitará a falha reportada do comando script/inline.
- **runElevated** – Opcional, boolean, suporte para executar comandos e scripts com permissões elevadas.
- **sha256Checksum** - Valor da obra de verificação sha256 do ficheiro, gere-o localmente e, em seguida, o Image Builder verificará e validará.
    * Para gerar o sha256Checksum, utilizando um PowerShell no Windows [Get-Hash](/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Personalizador de ficheiros

O personalizador de ficheiros permite que o construtor de imagens descarregue um ficheiro a partir de um armazenamento GitHub ou Azure. Se tiver um pipeline de construção de imagens que se baseie na construção de artefactos, pode então definir o personalizador de ficheiros para descarregar a partir da partilha de construção e mover os artefactos para a imagem.  

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

Suporte oss: Linux e Windows 

Propriedades do personalizador de arquivos:

- **sourceUri** - um ponto final de armazenamento acessível, este pode ser o armazenamento GitHub ou Azure. Só pode descarregar um ficheiro, não um diretório inteiro. Se precisar de descarregar um diretório, use um ficheiro comprimido e, em seguida, descomprimir-se utilizando os personalizadores Shell ou PowerShell. 

> [!NOTE]
> Se a fonteUri for uma Conta de Armazenamento Azure, independentemente de a bolha ser marcada como pública, concederá às permissões de Identidade de Utilizador Gerida para ler o acesso na bolha. Consulte este [exemplo](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-user-assigned-identity#create-a-resource-group) para definir as permissões de armazenamento.

- **destino** – este é o caminho de destino completo e nome de arquivo. Qualquer caminho e subdireções referenciados devem existir, utilize os personalizadores Shell ou PowerShell para os configurar previamente. Pode utilizar os personalizadores de scripts para criar o caminho. 

Isto é suportado por diretórios Windows e caminhos Linux, mas existem algumas diferenças: 
- Linux OS's – o único caminho a que o construtor de imagem pode escrever é /tmp.
- Janelas – Sem restrições de caminho, mas o caminho deve existir.
 
 
Se houver um erro ao tentar descarregar o ficheiro, ou colocá-lo num diretório especificado, o passo personalizado falhará, e isso estará na personalização.log.

> [!NOTE]
> O personalizador de ficheiros só é adequado para pequenos downloads de ficheiros, < 20MB. Para transferências de ficheiros maiores, utilize um script ou um comando inline, o código de utilização para descarregar ficheiros, tais como, Linux `wget` ou `curl` Windows, `Invoke-WebRequest` .

### <a name="windows-update-customizer"></a>Personalizador de atualização do Windows
Este personalizador é construído na [comunidade Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) para Packer, que é um projeto de código aberto mantido pela comunidade Packer. A Microsoft testa e valida o provisionador com o serviço Image Builder, e irá apoiar a investigação de problemas com o mesmo, e trabalhará para resolver problemas, no entanto o projeto open source não é oficialmente suportado pela Microsoft. Para obter documentação detalhada e ajudar com o Windows Update Provisioner, consulte o repositório do projeto.

```json
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
OS support: Windows
```

Personalize propriedades:
- **tipo**  – WindowsUpdate.
- **searchCriteria** - Opcional, define que tipo de atualizações são instaladas (Recomendado, Importante etc.), BrowseOnly=0 e IsInstalled=0 (Recomendado) é o padrão.
- **filtros** – Opcional, permite especificar um filtro para incluir ou excluir atualizações.
- **updateLimit** – Opcional, define quantas atualizações podem ser instaladas, padrão 1000.
 
> [!NOTE]
> O personalizador do Windows Update pode falhar se houver algum reinício pendente do Windows, ou instalações de aplicações ainda em execução, normalmente pode ver este erro na personalização.log, `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . Aconselhamos vivamente que considere adicionar um Windows Restart e/ou permitir às aplicações tempo suficiente para completar as suas instalações utilizando comandos [de sono] ou de espera( https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/start-sleep?view=powershell-7) nos comandos ou scripts inline antes de executar o Windows Update.

### <a name="generalize"></a>Generalizar 
Por predefinição, o Azure Image Builder também executará o código 'deprovisionamento' no final de cada fase de personalização de imagem, para 'generalizar' a imagem. Generalizar é um processo em que a imagem é configurada para que possa ser reutilizada para criar vários VMs. Para o Windows VMs, o Azure Image Builder utiliza o Sysprep. Para o Linux, o Azure Image Builder corre 'waagent -deprovision". 

Os comandos que os utilizadores do Image Builder generalizarem podem não ser adequados para todas as situações, pelo que o Azure Image Builder permitir-lhe-á personalizar este comando, se necessário. 

Se estiver a migrar a personalização existente e estiver a utilizar diferentes comandos Sysprep/waagent, pode utilizar os comandos genéricos do Image Builder, e se a criação de VM falhar, use os seus próprios comandos Sysprep ou waagent.

Se o Azure Image Builder criar uma imagem personalizada do Windows com sucesso e criar um VM a partir dela, então descobrir que a criação de VM falha ou não completa com sucesso, terá de rever a documentação do Windows Server Sysprep ou levantar um pedido de suporte com a equipa de Suporte ao Cliente do Windows Server Sysprep, que pode resolver problemas e aconselhar sobre a utilização correta do Sysprep.


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
#### <a name="default-linux-deprovision-command"></a>Comando de desprovisionamento linux padrão

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Sobrevando os Comandos
Para anular os comandos, utilize os provisionadores de scripts PowerShell ou Shell para criar os ficheiros de comando com o nome exato do ficheiro e colocá-los nos diretórios corretos:

* Janelas: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

O Image Builder irá ler estes comandos, estes são escritos para os registos do AIB, 'personalização.log'. Consulte [a resolução de problemas](image-builder-troubleshoot.md#customization-log) sobre como recolher registos.
 
## <a name="properties-distribute"></a>Propriedades: distribuir

O Azure Image Builder suporta três alvos de distribuição: 

- **managedImage** - imagem gerida.
- **sharedImage** - Shared Image Gallery.
- **VHD** - VHD numa conta de armazenamento.

Pode distribuir uma imagem a ambos os tipos de destino na mesma configuração.

> [!NOTE]
> O comando Sysprep AIB padrão não inclui "/mode:vm", no entanto, talvez necessário quando criar imagens que terão a função HyperV instalada. Se precisar de adicionar este argumento de comando, tem de anular o comando sysprep.

Como pode ter mais do que um alvo para distribuir, o Image Builder mantém um estado para cada alvo de distribuição que pode ser acedido através da consulta do `runOutputName` .  É `runOutputName` um objeto que pode consultar a distribuição de posts para obter informações sobre essa distribuição. Por exemplo, pode consultar a localização do VHD, ou regiões onde a versão de imagem foi replicada, ou a versão SIG Image criada. Esta é uma propriedade de todos os alvos de distribuição. O `runOutputName` deve ser exclusivo de cada alvo de distribuição. Aqui está um exemplo, isto é consultando uma distribuição de Galeria de Imagens Partilhada:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

Resultado:
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
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
Distribuir propriedades:
- **tipo** – managedImage 
- **imageId** – ID de recursos da imagem de destino, formato esperado: /subscrições/ \<subscriptionId> /recursosGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **localização** - localização da imagem gerida.  
- **runOutputName** – nome único para identificar a distribuição.  
- **artifactTags** - Etiquetas de par de valores de chave específicas do utilizador opcionais.
 
 
> [!NOTE]
> O grupo de recursos de destino deve existir.
> Se quiser que a imagem seja distribuída para uma região diferente, aumentará o tempo de implantação . 

### <a name="distribute-sharedimage"></a>Distribuir: sharedImage 
A Azure Shared Image Gallery é um novo serviço de Gestão de Imagem que permite gerir a replicação da região de imagem, a versão e a partilha de imagens personalizadas. O Azure Image Builder suporta a distribuição com este serviço, para que possa distribuir imagens para regiões apoiadas por Galerias de Imagem Partilhada. 
 
Uma Galeria de Imagens Partilhadas é composta por: 
 
- Galeria - Recipiente para múltiplas imagens partilhadas. Uma galeria é implantada numa região.
- Definições de imagem - um agrupamento conceptual para imagens. 
- Versões de imagem - este é um tipo de imagem usado para implantar um conjunto de VM ou escala. As versões de imagem podem ser replicadas noutras regiões onde os VMs precisam de ser implantados.
 
Antes de poder distribuir pela Galeria de Imagens, tem de criar uma galeria e uma definição de imagem, ver [imagens partilhadas.](../shared-images-cli.md) 

```json
{
    "type": "SharedImage",
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

Distribuir propriedades para galerias de imagem partilhadas:

- **tipo** - sharedImage  
- **galeriaImageId** – ID da galeria de imagens partilhada, esta pode especificar em dois formatos:
    * Versão automática - O Image Builder gerará um número de versão monótona para si, isto é útil para quando pretender manter a reconstrução de imagens a partir do mesmo modelo: O formato é: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Versão explícita - Pode passar no número de versão que pretende que o construtor de imagens utilize. O formato é: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** – nome único para identificar a distribuição.  
- **artifactTags** - Etiquetas de par de valores de chave específicas do utilizador opcionais.
- **replicaçãoRegions** - Conjunto de regiões para replicação. Uma das regiões deve ser a região onde a Galeria está implantada. A adição de regiões significará um aumento do tempo de construção, uma vez que a construção não se completa até que a replicação esteja concluída.
- **excluirFromLatest** (opcional) Isto permite-lhe marcar a versão de imagem que cria não ser usada como a versão mais recente na definição SIG, o padrão é 'falso'.
- **storageAccountType** (opcional) AIB suporta especificar estes tipos de armazenamento para a versão de imagem que vai ser criada:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Se o modelo de imagem e referenciado `image definition` não estiverem no mesmo local, verá tempo adicional para criar imagens. Atualmente, o Image Builder não tem um `location` parâmetro para o recurso da versão de imagem, tiramo-lo do seu `image definition` progenitor. Por exemplo, se uma definição de imagem está em westus e você quer a versão de imagem replicada para eastus, uma bolha é copiada para westus, a partir deste, um recurso de versão de imagem em Westus é criado, e depois replicar-se para leste. Para evitar o tempo adicional de replicação, certifique-se de que o `image definition` modelo e o modelo de imagem estão no mesmo local.


### <a name="distribute-vhd"></a>Distribuir: VHD  
Podes chegar a um VHD. Em seguida, pode copiar o VHD e usá-lo para publicar no Azure MarketPlace, ou usá-lo com Azure Stack.  

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
 
Suporte oss: Windows e Linux

Distribuir parâmetros VHD:

- **tipo** - VHD.
- **runOutputName** – nome único para identificar a distribuição.  
- **tags** - Etiquetas de par de chaves específicas do utilizador opcional.
 
O Azure Image Builder não permite que o utilizador especifique a localização de uma conta de armazenamento, mas pode consultar o estado do `runOutputs` mesmo para obter a localização.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Uma vez criado o VHD, copie-o para um local diferente, o mais rapidamente possível. O VHD é armazenado numa conta de armazenamento no grupo de recursos temporários criado quando o modelo de imagem é submetido ao serviço Azure Image Builder. Se eliminar o modelo de imagem, perderá o VHD. 

## <a name="image-template-operations"></a>Operações do modelo de imagem

### <a name="starting-an-image-build"></a>Iniciar uma construção de imagem
Para iniciar uma construção, é necessário invocar 'Run' no recurso Modelo de Imagem, exemplos de `run` comandos:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Cancelar uma construção de imagem
Se estiver a executar uma construção de imagem que acredita estar incorreta, à espera da entrada do utilizador, ou se sentir que nunca completará com sucesso, então pode cancelar a construção.

A construção pode ser cancelada a qualquer momento. Se a fase de distribuição tiver começado ainda pode cancelar, mas terá de limpar quaisquer imagens que possam não estar concluídas. O comando de cancelamento não espera que o cancelamento esteja concluído, por favor monitorize `lastrunstatus.runstate` para cancelar o progresso, utilizando estes [comandos de](image-builder-troubleshoot.md#customization-log)estado .


Exemplos de `cancel` comandos:

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Passos seguintes

Existem ficheiros de amostra .json para diferentes cenários no [Azure Image Builder GitHub](https://github.com/azure/azvmimagebuilder).
