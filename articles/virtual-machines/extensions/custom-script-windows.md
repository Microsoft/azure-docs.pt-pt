---
title: Extensão do script personalizado azure para windows
description: Automatizar as tarefas de configuração do Windows VM utilizando a extensão do Script Personalizado
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: bf4c7e9fc623ad7dc74b6da943232d5c558d43a4
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920268"
---
# <a name="custom-script-extension-for-windows"></a>Extensão personalizada do script para windows

A extensão personalizada do script descarrega e executa scripts em máquinas virtuais Azure. Esta extensão é útil para a configuração de implementação de posts, instalação de software ou qualquer outra configuração ou tarefas de gestão. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. A extensão personalizada do script integra-se com os modelos do Gestor de Recursos Azure, e pode ser executada utilizando o Portal Azure CLI, PowerShell, Azure ou a API de REST de Máquina Virtual Azure.

Este documento detalha como utilizar a extensão do script personalizado utilizando o módulo PowerShell Azure, os modelos do Gestor de Recursos Azure e detalha passos de resolução de problemas nos sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]  
> Não utilize a extensão personalizada do script para executar o Update-AzVM com o mesmo VM que o seu parâmetro, uma vez que irá esperar por si próprio.  

### <a name="operating-system"></a>Sistema Operativo

A extensão personalizada do script para windows será executada no OSs de extensão suportado por extensão, para mais informações, ver este sistema operativo suportado por [extensão Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Localização do script

Pode configurar a extensão para utilizar as suas credenciais de armazenamento Azure Blob para aceder ao armazenamento Azure Blob. A localização do script pode estar em qualquer lugar, desde que o VM possa direcionar para esse ponto final, como gitHub ou um servidor de ficheirointerno interno.

### <a name="internet-connectivity"></a>Conectividade da Internet

Se necessitar de descarregar um script externamente, como o GitHub ou o Azure Storage, então as portas adicionais de firewall e Network Security Group precisam de ser abertas. Por exemplo, se o seu script estiver localizado no Armazenamento Azure, pode permitir o acesso utilizando etiquetas de serviço Azure NSG para [armazenamento](../../virtual-network/security-overview.md#service-tags).

Se o seu script estiver num servidor local, poderá ainda necessitar de firewall adicional e as portas do Grupo de Segurança da Rede precisam de ser abertas.

### <a name="tips-and-tricks"></a>Dicas e Truques

* A maior taxa de falha para esta extensão deve-se a erros de sintaxe no script, testar o script corre sem erros, e também colocar em registo adicional no script para facilitar a descoberta de onde falhou.
* Escreva guiões que sejam idempotentes. Isto garante que, se voltarem a funcionar acidentalmente, não provocará alterações no sistema.
* Certifique-se de que os scripts não requerem dados do utilizador quando são executados.
* Existe um período de 90 minutos permitido para a execução do script. Um período mais longo resultará numa falha de aprovisionamento da extensão.
* Não coloque reinícios no script. Essa ação causará problemas com outras extensões que sejam instaladas. Após o reinício, a extensão não continuará.
* Se tiver um script que irá causar um reboot, em seguida, instalar aplicações e executar scripts, pode agendar o reboot usando uma Tarefa Agendada do Windows, ou usar ferramentas como extensões DSC, Chef ou Puppet.
* A extensão apenas executará um script de uma vez. Se quiser executar um script a cada arranque, tem de utilizar a extensão para criar uma Tarefa Agendada do Windows.
* Se quiser agendar a execução de um script, deve utilizar a extensão para criar uma Tarefa Agendada do Windows.
* Quando o script estiver em execução, verá apenas um estado de extensão "em transição" no portal ou na CLI do Azure. Se quiser atualizações de estado mais frequentes de um script em execução, terá de criar a sua própria solução.
* A extensão do Script personalizado não suporta de forma nativa servidores proxy, no entanto pode utilizar uma ferramenta de transferência de ficheiros que suporta servidores proxy dentro do seu script, como *o Curl*
* Tenha em atenção as localizações de diretório não predefinidas de que os scripts ou comandos possam depender. Tenha uma lógica para lidar com esta situação.
* Extensão de script personalizada será executada sob a Conta LocalSystem

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica coisas como a localização do script e o comando a ser executado. Pode armazenar esta configuração em ficheiros de configuração, especificá-la na linha de comando ou especificá-la num modelo de Gestor de Recursos Azure.

Pode armazenar dados sensíveis numa configuração protegida, encriptada e apenas desencriptada dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos como uma palavra-passe.

Estes itens devem ser tratados como dados sensíveis e especificados na configuração de definição protegida das extensões. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino.

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
> propriedade de identidade gerida **não deve** ser usada em conjunto com propriedades storageAccountName ou storageAccountKey

> [!NOTE]
> Apenas uma versão de uma extensão pode ser instalada num VM num momento, especificando o script personalizado duas vezes no mesmo modelo de Gestor de Recursos para o mesmo VM falhará.

> [!NOTE]
> Podemos usar este esquema dentro do recurso VirtualMachine ou como recurso autónomo. O nome do recurso tem de estar neste formato "virtualMachineName/extensionName", se esta extensão for utilizada como recurso autónomo no modelo ARM. 

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Compute | string |
| tipo | CustomScriptExtension | string |
| typeHandlerVersion | 1.10 | int |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (por exemplo) | 123456789 | Inteiro de 32 bits |
| comandoToExecutar (por exemplo) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| armazenamentoAccountName (por exemplo) | exemplostorageacct | string |
| armazenagemAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| identidade gerida (por exemplo) | { ou { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } ou { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json objeto |

>[!NOTE]
>Estes nomes de propriedade são sensíveis a casos. Para evitar problemas de implantação, use os nomes como mostrado aqui.

#### <a name="property-value-details"></a>Detalhes do valor da propriedade

* `commandToExecute`: **(obrigatório,** corda) o guião do ponto de entrada a executar. Utilize este campo se o seu comando contiver segredos como palavras-passe ou se os seus ficheirosUris forem sensíveis.
* `fileUris`: (opcional, string array) os URLs para ficheiros ou ficheiros a serem descarregados.
* `timestamp` (opcional, inteiro de 32 bits) usam este campo apenas para desencadear uma reexecução do script alterando o valor deste campo.  Qualquer valor inteiro é aceitável; só deve ser diferente do valor anterior.
* `storageAccountName`: (opcional, corda) o nome da conta de armazenamento. Se especificar credenciais de armazenamento, todos os `fileUris` devem ser URLs para Blobs Azure.
* `storageAccountKey`: (opcional, corda) a chave de acesso da conta de armazenamento
* `managedIdentity`: (opcional, objeto json) a [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para o descarregamento de ficheiros(s)
  * `clientId`: (opcional, corda) a identificação do cliente da identidade gerida
  * `objectId`: (opcional, corda) a identificação do objeto da identidade gerida

Os seguintes valores podem ser definidos em configurações públicas ou protegidas, a extensão rejeitará qualquer configuração em que os valores abaixo sejam definidos em configurações públicas e protegidas.

* `commandToExecute`

Utilizar configurações públicas talvez úteis para depuração, mas é recomendado que utilize configurações protegidas.

As configurações públicas são enviadas em texto claro para o VM onde o script será executado.  As definições protegidas são encriptadas utilizando uma chave conhecida apenas pelo Azure e pelo VM. As definições são guardadas para o VM à medida que foram enviadas, isto é, se as definições foram encriptadas, são guardadas encriptadas no VM. O certificado utilizado para desencriptar os valores encriptados é armazenado no VM e utilizado para desencriptar as definições (se necessário) no prazo de execução.

####  <a name="property-managedidentity"></a>Propriedade: identidade gerida

O CustomScript (versão 1.10 em diante) suporta a [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para descarregar ficheiros(s) de URLs fornecidos na definição "fileUris". Permite ao CustomScript aceder a blobs ou contentores privados do Azure Storage sem que o utilizador tenha de passar segredos como fichas SAS ou chaves de conta de armazenamento.

Para utilizar esta funcionalidade, o utilizador deve adicionar uma identidade atribuída ao [sistema](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-system-assigned-identity) ou atribuída ao [VM](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-user-assigned-identity) ou VMSS onde se espera que o CustomScript seja executado, e conceder o acesso de [identidade gerido ao recipiente ou blob](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access)do Armazenamento Azure .

Para utilizar a identidade atribuída ao sistema no alvo VM/VMSS, detete o campo "managedidentity" para um objeto json vazio. 

> Exemplo:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Para utilizar a identidade atribuída ao utilizador no alvo VM/VMSS, configure o campo "managedidentity" com o ID do cliente ou o ID do objeto da identidade gerida.

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
> propriedade de identidade gerida **não deve** ser usada em conjunto com propriedades storageAccountName ou storageAccountKey

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON, que é detalhado na secção anterior, pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão do script personalizado durante a implementação. As seguintes amostras mostram como utilizar a extensão do Script Personalizado:

* [Tutorial: Implementar extensões de máquinavirtual com modelos de Gestor de Recursos Azure](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Implementar aplicação de dois níveis no Windows e Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Implementação powerShell

O comando `Set-AzVMCustomScriptExtension` pode ser usado para adicionar a extensão do Script Personalizado a uma máquina virtual existente. Para mais informações, consulte [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

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

Neste exemplo, tem três scripts que são usados para construir o seu servidor. O **comandoToExecute** chama o primeiro script, então você tem opções sobre como os outros são chamados. Por exemplo, pode ter um guião principal que controla a execução, com o manuseamento de erros, registos e gestão do Estado. Os scripts são descarregados para a máquina local para correr. Por exemplo, em `1_Add_Tools.ps1` chamaria de `2_Add_Features.ps1` adicionando `.\2_Add_Features.ps1` ao guião e repetindo este processo para os outros scripts que define em `$settings`.

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

### <a name="running-scripts-from-a-local-share"></a>Executando scripts de uma parte local

Neste exemplo, pode querer utilizar um servidor SMB local para a sua localização de script. Ao fazê-lo, não precisa de fornecer outras definições, exceto **comandoSToExecute**.

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

Se quiser executar a extensão do script personalizada mais de uma vez, só pode fazer esta ação nestas condições:

* O **parâmetro** de nome de extensão é o mesmo que a implementação anterior da extensão.
* Atualize a configuração caso contrário o comando não será reexecutado. Pode adicionar uma propriedade dinâmica ao comando, como uma marca de tempo.

Em alternativa, pode definir a propriedade [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) **como verdadeira**.

### <a name="using-invoke-webrequest"></a>Utilização de Invocação-WebRequest

Se estiver a utilizar o [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) no seu script, deve especificar o parâmetro `-UseBasicParsing` ou receberá o seguinte erro ao verificar o estado detalhado:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais

Para implementar a extensão do script personalizado num conjunto de escala, consulte [Add-AzVmssExtension](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>VMs clássicas

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Para implementar a extensão do script personalizado em VMs clássicos, pode utilizar o portal Azure ou os cmdlets Classic Azure PowerShell.

### <a name="azure-portal"></a>Portal do Azure

Navegue para o seu recurso VM clássico. **Selecione extensões** em **Definições**.

Clique **+ Adicione** e na lista de recursos escolha **extensão de script personalizado**.

Na página de **extensão Instalar,** selecione o ficheiro PowerShell local e preencha quaisquer argumentos e clique em **Ok**.

### <a name="powershell"></a>PowerShell

Utilize o [cmdlet set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) pode ser usado para adicionar a extensão do Script Personalizado a uma máquina virtual existente.

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

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das implementações de extensões podem ser recuperados a partir do portal Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implantação das extensões para um dado VM, executar o seguinte comando:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A saída de extensão é registada nos ficheiros encontrados na seguinte pasta na máquina virtual alvo.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os ficheiros especificados são descarregados na seguinte pasta na máquina virtual alvo.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

quando `<n>` é um inteiro decimal, que pode mudar entre execuções da extensão.  O valor `1.*` corresponde ao valor real e atual `typeHandlerVersion` da extensão.  Por exemplo, o diretório real poderia ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o comando `commandToExecute`, a extensão define este diretório (por exemplo, `...\Downloads\2`) como o atual diretório de trabalho. Este processo permite a utilização de caminhos relativos para localizar os ficheiros descarregados através da propriedade `fileURIs`. Veja a tabela abaixo, por exemplo.

Uma vez que o caminho de descarregamento absoluto pode variar ao longo do tempo, é melhor optar por caminhos relativos de script/ficheiro na cadeia `commandToExecute`, sempre que possível. Por exemplo:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informações de percurso após o primeiro segmento URI é mantida para ficheiros descarregados através da lista de propriedades `fileUris`.  Como mostrado na tabela abaixo, os ficheiros descarregados são mapeados em subdiretórios de descarregamento para refletir a estrutura dos valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de Ficheiros Descarregados

| URI em fileUris | Localização relativa descarregada | Localização absolutamente descarregada <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Os caminhos absolutos do diretório mudam ao longo da vida útil do VM, mas não dentro de uma única execução da extensão CustomScript.

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Também pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
