---
title: Extensão de script personalizada Azure para windows
description: Automatizar as tarefas de configuração do Windows VM utilizando a extensão de Script Personalizado
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/31/2020
ms.author: robreed
ms.openlocfilehash: b0502fb05043a54d81d768a7809d19b108cc6248
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976848"
---
# <a name="custom-script-extension-for-windows"></a>Extensão de Script Personalizado para o Windows

A Extensão de Script Personalizado descarrega e executa scripts em máquinas virtuais Azure. Esta extensão é útil para configuração de implementação de posts, instalação de software ou qualquer outra configuração ou tarefas de gestão. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. A Extensão de Script Personalizado integra-se com os modelos do Gestor de Recursos Azure, e pode ser executada usando o Azure CLI, PowerShell, Portal Azure ou a API da Máquina Virtual Azure.

Este documento detalha como utilizar a extensão de script personalizada utilizando o módulo Azure PowerShell, os modelos do Gestor de Recursos Azure e detalhes sobre os passos de resolução de problemas nos sistemas windows.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]  
> Não utilize a Extensão de Script Personalizado para executar Update-AzVM com o mesmo VM do seu parâmetro, uma vez que irá aguardar por si mesmo.  

### <a name="operating-system"></a>Sistema Operativo

A extensão de script personalizada para windows será executada na extensão de extensão suportada OSs;
### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Núcleo do Windows Server 2016
* Windows Server 2019
* Núcleo do Windows Server 2019

### <a name="script-location"></a>Localização do script

Pode configurar a extensão para utilizar as suas credenciais de armazenamento Azure Blob para aceder ao armazenamento do Azure Blob. A localização do script pode ser em qualquer lugar, desde que o VM possa ir até esse ponto final, como o GitHub ou um servidor de ficheiros interno.

### <a name="internet-connectivity"></a>Conectividade da Internet

Se precisar de descarregar um script externo, como no GitHub ou no Azure Storage, então é necessário abrir firewall adicional e portas do Grupo de Segurança de Rede. Por exemplo, se o seu script estiver localizado no Azure Storage, pode permitir o acesso usando tags de serviço Azure NSG para [armazenamento.](../../virtual-network/network-security-groups-overview.md#service-tags)

Note que a extensão CustomScript não tem qualquer forma de contornar a validação do certificado. Por isso, se está a descarregar de um local seguro com, por exemplo. um certificado auto-assinado, pode acabar por ter erros como *"O certificado remoto é inválido de acordo com o procedimento de validação".* Certifique-se de que o certificado está corretamente instalado na loja *"Trusted Root Certification Authorities"* na Máquina Virtual.

Se o seu script estiver num servidor local, poderá ainda necessitar de firewall adicional e as portas do Grupo de Segurança de Rede precisam de ser abertas.

### <a name="tips-and-tricks"></a>Dicas e Truques

* A taxa de falha mais alta para esta extensão deve-se a erros de sintaxe no script, testar o script funciona sem erro, e também colocar inserção adicional no script para facilitar a descoberta de onde falhou.
* Escreva scripts que sejam idempotentes. Isto garante que, se voltarem a funcionar acidentalmente, não provocará alterações no sistema.
* Certifique-se de que os scripts não requerem dados do utilizador quando são executados.
* Existe um período de 90 minutos permitido para a execução do script. Um período mais longo resultará numa falha de aprovisionamento da extensão.
* Não coloque reinícios no script. Essa ação causará problemas com outras extensões que sejam instaladas. Após o reinício, a extensão não continuará.
* Se tiver um script que irá causar um reboot, em seguida, instalar aplicações e executar scripts, você pode agendar o reboot usando uma Tarefa Agendada do Windows, ou usar ferramentas como extensões DSC, Chef ou Puppet.
* Não é aconselhável executar um script que irá causar uma paragem ou atualização do Agente VM. Isto pode deixar a extensão num estado de transição, levando a um tempo limite.
* A extensão apenas executará um script de uma vez. Se quiser executar um script a cada arranque, tem de utilizar a extensão para criar uma Tarefa Agendada do Windows.
* Se quiser agendar a execução de um script, deve utilizar a extensão para criar uma Tarefa Agendada do Windows.
* Quando o script estiver em execução, verá apenas um estado de extensão "em transição" no portal ou na CLI do Azure. Se quiser atualizações de estado mais frequentes de um script em execução, terá de criar a sua própria solução.
* A extensão de Script personalizado não suporta de forma nativa servidores proxy, no entanto pode utilizar uma ferramenta de transferência de ficheiros que suporta servidores proxy dentro do seu script, como *Curl*
* Tenha em atenção as localizações de diretório não predefinidas de que os scripts ou comandos possam depender. Tenha uma lógica para lidar com esta situação.
* A extensão do script personalizado será executada sob a conta do Sistema Local
* Se pretender utilizar as propriedades *de ArmazenamentoSeMe* e *armazenamentoCocountKey,* estas propriedades devem ser collocadas em *instalações protegidas*.

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica coisas como a localização do script e o comando a ser executado. Pode armazenar esta configuração em ficheiros de configuração, especi-la na linha de comando ou especi-la num modelo de Gestor de Recursos Azure.

Pode armazenar dados sensíveis numa configuração protegida, que é encriptada e apenas desencriptada dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos como uma palavra-passe.

Estes itens devem ser tratados como dados sensíveis e especificados na configuração de definição protegida das extensões. Os dados de definição protegidos por extensão Azure VM são encriptados e apenas desencriptados na máquina virtual alvo.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> propriedade managedIdentity **não deve** ser usado em conjunto com armazenamentoScome ou armazenamentoScolhostas

> [!NOTE]
> Apenas uma versão de uma extensão pode ser instalada num VM num ponto do tempo, especificando o script personalizado duas vezes no mesmo modelo de Gestor de Recursos para o mesmo VM falhará.

> [!NOTE]
> Podemos usar este esquema dentro do recurso VirtualMachine ou como um recurso autónomo. O nome do recurso deve estar neste formato "virtualMachineName/extensionName", se esta extensão for utilizada como recurso autónomo no modelo ARM.

### <a name="property-values"></a>Valores patrimoniais

| Name | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Compute | string |
| tipo | CustomScriptExtension | string |
| typeHandlerVersion | 1.10 | int |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matriz |
| timetamp (por exemplo) | 123456789 | Inteiro de 32 bits |
| commandToExecute (por exemplo) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| armazenamentoSame de número de armazenamento (por exemplo) | exemplostorageacct | string |
| armazenamentoSColho (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| entidade geridaId (por exemplo) | { } ou { "clientId": "31b403a-c364-4240-a7ff-d85fb6cd7232" } ou {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | objeto json |

>[!NOTE]
>Estes nomes de propriedade são sensíveis a casos. Para evitar problemas de implantação, utilize os nomes como mostrado aqui.

#### <a name="property-value-details"></a>Detalhes do valor da propriedade

* `commandToExecute`:**(obrigatório,** string) o roteiro do ponto de entrada a executar. Utilize este campo em vez disso se o seu comando contiver segredos como palavras-passe ou os seus ficheirosUris são sensíveis.
* `fileUris`: (opcional, matriz de cordas) os URLs para ficheiros a serem descarregados.
* `timestamp` (opcional, inteiro de 32 bits) utilize este campo apenas para desencadear uma repetição do script alterando o valor deste campo.  Qualquer valor inteiro é aceitável; só deve ser diferente do valor anterior.
* `storageAccountName`: (opcional, cadeia) o nome da conta de armazenamento. Se especificar credenciais de armazenamento, todos `fileUris` devem ser URLs para Azure Blobs.
* `storageAccountKey`: (opcional, cadeia) a chave de acesso da conta de armazenamento
* `managedIdentity`: (opcional, objeto json) a [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para descarregar ficheiros
  * `clientId`: (opcional, string) o ID do cliente da identidade gerida
  * `objectId`: (opcional, cadeia) o ID do objeto da identidade gerida

Os seguintes valores podem ser definidos em configurações públicas ou protegidas, a extensão rejeitará qualquer configuração em que os valores abaixo estejam definidos em configurações públicas e protegidas.

* `commandToExecute`

Usando configurações públicas talvez úteis para depurar, mas recomenda-se que use configurações protegidas.

As definições públicas são enviadas em texto claro para o VM onde o script será executado.  As definições protegidas são encriptadas utilizando uma chave conhecida apenas pelo Azure e pelo VM. As definições são guardadas para o VM à medida que foram enviadas, isto é, se as definições foram encriptadas, são guardadas encriptadas no VM. O certificado utilizado para desencriptar os valores encriptados é armazenado no VM e utilizado para desencriptar as definições (se necessário) no tempo de execução.

####  <a name="property-managedidentity"></a>Propriedade: managedIdentity
> [!NOTE]
> Esta propriedade **deve** ser especificada apenas em configurações protegidas.

CustomScript (versão 1.10 em diante) suporta [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para descarregar ficheiros(s) a partir de URLs fornecidos na definição "fileUris". Permite que o CustomScript aceda a blobs ou contentores privados do Azure Storage sem que o utilizador tenha de passar segredos como fichas SAS ou chaves de conta de armazenamento.

Para utilizar esta funcionalidade, o utilizador deve adicionar uma identidade atribuída ao [sistema](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) ou [atribuída](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) ao VM ou VMSS onde se espera que o CustomScript seja executado, e conceder o acesso de identidade gerido ao recipiente ou bolha de [armazenamento Azure](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Para utilizar a identidade atribuída ao sistema no VM/VMSS alvo, dedije o campo "managedidentity" a um objeto json vazio. 

> Exemplo:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Para utilizar a identidade atribuída ao utilizador no VM/VMSS alvo, configuure o campo "managedidentity" com o ID do cliente ou o ID do objeto da identidade gerida.

> Exemplos:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> propriedade managedIdentity **não deve** ser usado em conjunto com armazenamentoScome ou armazenamentoScolhostas

## <a name="template-deployment"></a>Implementação de modelos

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. O esquema JSON, que é detalhado na secção anterior, pode ser usado num modelo de Gestor de Recursos Azure para executar a Extensão de Script Personalizado durante a implementação. As seguintes amostras mostram como usar a extensão do Script Personalizado:

* [Tutorial: Implementar extensões de máquina virtual com modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Implementar aplicação de dois níveis no Windows e Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Implementação do PowerShell

O `Set-AzVMCustomScriptExtension` comando pode ser usado para adicionar a extensão de Script Personalizado a uma máquina virtual existente. Para obter mais informações, consulte [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Exemplos adicionais

### <a name="using-multiple-scripts"></a>Usando vários scripts

Neste exemplo, tem três scripts que são usados para construir o seu servidor. O **comandoToExecute** chama o primeiro script, então você tem opções sobre como os outros são chamados. Por exemplo, pode ter um script principal que controla a execução, com o manuseamento de erros, registo e gestão do estado. Os scripts são descarregados para a máquina local para correr. Por `1_Add_Tools.ps1` exemplo, você chamaria `2_Add_Features.ps1` adicionando  `.\2_Add_Features.ps1` ao script, e repetiria este processo para os outros scripts que define em `$settings` .

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Executando scripts a partir de uma parte local

Neste exemplo, pode querer utilizar um servidor SMB local para a sua localização de script. Ao fazê-lo, não precisa de fornecer quaisquer outras definições, exceto **o commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Como executar script personalizado mais de uma vez com CLI

Se quiser executar a extensão do script personalizado mais de uma vez, só pode fazer esta ação nestas condições:

* O parâmetro de extensão **Nome** é o mesmo que a implantação anterior da extensão.
* Atualize a configuração caso contrário o comando não será re executado. Pode adicionar uma propriedade dinâmica ao comando, como uma hora de pontuação.

Em alternativa, pode definir a propriedade [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) como **verdadeira.**

### <a name="using-invoke-webrequest"></a>Usando Invoke-WebRequest

Se estiver a utilizar [o Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) no seu script, tem de especificar o parâmetro `-UseBasicParsing` ou receberá o seguinte erro ao verificar o estado detalhado:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais

Para implementar a extensão de script personalizada num conjunto de escala, consulte [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>VMs clássicas

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Para implementar a extensão de script personalizada em VMs clássicos, você pode usar o portal Azure ou os cmdlets Classic Azure PowerShell.

### <a name="azure-portal"></a>Portal do Azure

Navegue para o seu recurso VM Clássico. Selecione **extensões** em **Definições**.

Clique **+ Adicionar** e na lista de recursos escolha **extensão de script personalizada.**

Na página **de extensão instalar,** selecione o ficheiro PowerShell local e preencha quaisquer argumentos e clique em **Ok**.

### <a name="powershell"></a>PowerShell

Utilize o [cmdlet set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) pode ser usado para adicionar a extensão de Script Personalizado a uma máquina virtual existente.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A saída de extensão é registada em ficheiros encontrados na seguinte pasta na máquina virtual alvo.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os ficheiros especificados são transferidos para a seguinte pasta na máquina virtual alvo.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

onde `<n>` é um inteiro decimal, que pode mudar entre execuções da extensão.  O `1.*` valor corresponde ao valor atual real da `typeHandlerVersion` extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` .  

Ao executar o `commandToExecute` comando, a extensão define este diretório (por exemplo, `...\Downloads\2` ) como o diretório de trabalho atual. Este processo permite o uso de caminhos relativos para localizar os ficheiros descarregados através da `fileURIs` propriedade. Veja a tabela abaixo, por exemplo.

Uma vez que o caminho de descarregamento absoluto pode variar ao longo do tempo, é melhor optar por caminhos relativos de script/arquivo na `commandToExecute` cadeia, sempre que possível. Por exemplo:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

As informações sobre o percurso após o primeiro segmento URI são guardadas para ficheiros descarregados através da `fileUris` lista de propriedades.  Como mostrado na tabela abaixo, os ficheiros descarregados são mapeados em subdireções de descarregamento para refletir a estrutura dos `fileUris` valores.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de Ficheiros Descarregados

| URI em fileUris | Localização descarregada relativa | Local descarregado absoluto <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Os caminhos absolutos do diretório mudam ao longo da vida útil do VM, mas não dentro de uma única execução da extensão CustomScript.

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Também pode arquivar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
