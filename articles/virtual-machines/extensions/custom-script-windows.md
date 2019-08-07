---
title: Extensão de script personalizado do Azure para Windows | Microsoft Docs
description: Automatizar tarefas de configuração de VM do Windows usando a extensão de script personalizado
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 58b6531a394db8f9d29dcc0fe9b4b40d1725e70a
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774588"
---
# <a name="custom-script-extension-for-windows"></a>Extensão de script personalizado para Windows

A extensão de script personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de pós-implantação, instalação de software ou quaisquer outras tarefas de configuração ou gerenciamento. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. A extensão de script personalizado se integra com modelos de Azure Resource Manager e pode ser executada usando o CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure.

Este documento fornece detalhes sobre como usar a extensão de script personalizado usando o módulo Azure PowerShell, os modelos de Azure Resource Manager e as etapas de solução de problemas de detalhes em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]  
> Não use a extensão de script personalizado para executar Update-AzVM com a mesma VM que seu parâmetro, pois ela aguardará por si mesma.  

### <a name="operating-system"></a>Sistema operativo

A extensão de script personalizado para Windows será executada no OSs de extensão com suporte da extensão, para obter mais informações, consulte os [sistemas operacionais com suporte da extensão do Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Local do script

Você pode configurar a extensão para usar suas credenciais de armazenamento de BLOBs do Azure para acessar o armazenamento de BLOBs do Azure. O local do script pode estar em qualquer lugar, desde que a VM possa ser roteada para esse ponto de extremidade, como o GitHub ou um servidor de arquivos interno.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Se você precisar baixar um script externamente, como do GitHub ou do armazenamento do Azure, as portas de grupo de segurança de rede e firewall adicionais precisam ser abertas. Por exemplo, se o seu script estiver localizado no armazenamento do Azure, você poderá permitir o acesso usando as marcas do serviço NSG do Azure para [armazenamento](../../virtual-network/security-overview.md#service-tags).

Se o seu script estiver em um servidor local, talvez você ainda precise que as portas de grupo de segurança de rede e firewall adicionais precisem ser abertas.

### <a name="tips-and-tricks"></a>Sugestões e Truques

* A taxa de falha mais alta para essa extensão é devido a erros de sintaxe no script, teste o script é executado sem erros e também Coloque em log adicional no script para facilitar a localização de onde ele falhou.
* Grave scripts que são idempotentes. Isso garante que, se eles forem executados novamente acidentalmente, não causarão alterações no sistema.
* Certifique-se de que os scripts não requerem dados do utilizador quando são executados.
* Existe um período de 90 minutos permitido para a execução do script. Um período mais longo resultará numa falha de aprovisionamento da extensão.
* Não coloque reinícios no script. Essa ação causará problemas com outras extensões que sejam instaladas. Após o reinício, a extensão não continuará.
* Se você tiver um script que causará uma reinicialização, instale aplicativos e execute scripts, você poderá agendar a reinicialização usando uma tarefa agendada do Windows ou usar ferramentas como as extensões DSC, chefe ou Puppet.
* A extensão apenas executará um script de uma vez. Se quiser executar um script a cada arranque, tem de utilizar a extensão para criar uma Tarefa Agendada do Windows.
* Se quiser agendar a execução de um script, deve utilizar a extensão para criar uma Tarefa Agendada do Windows.
* Quando o script estiver em execução, verá apenas um estado de extensão "em transição" no portal ou na CLI do Azure. Se quiser atualizações de estado mais frequentes de um script em execução, terá de criar a sua própria solução.
* A extensão de script personalizado não dá suporte nativo a servidores proxy, no entanto, você pode usar uma ferramenta de transferência de arquivo que dá suporte a servidores proxy em seu script, como ondulação
* Tenha em atenção as localizações de diretório não predefinidas de que os scripts ou comandos possam depender. Tenha uma lógica para lidar com esta situação.
* A extensão de script personalizado será executada na conta LocalSystem

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica coisas como o local do script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo de Azure Resource Manager.

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada apenas dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de configurações protegidas por extensões. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
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
        "typeHandlerVersion": "1.9",
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
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> Somente uma versão de uma extensão pode ser instalada em uma VM em um ponto no tempo, especificar o script personalizado duas vezes no mesmo modelo do Resource Manager para a mesma VM falhará.

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | Cadeia de caracteres |
| type | CustomScriptExtension | Cadeia de caracteres |
| typeHandlerVersion | 1.9 | int |
| fileuris (por exemplo,) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (por exemplo) | 123456789 | inteiro de 32 bits |
| commandToExecute (por exemplo,) | PowerShell-ExecutionPolicy Unrestricted-File configure-Music-app. ps1 | Cadeia de caracteres |
| storageAccountName (por exemplo,) | examplestorageacct | cadeia |
| storageAccountKey (por exemplo,) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | cadeia |

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

#### <a name="property-value-details"></a>Detalhes do valor da propriedade

* `commandToExecute`: (**Required**, String) o script de ponto de entrada a ser executado. Use esse campo se o comando contiver segredos, como senhas, ou se os fileuris forem confidenciais.
* `fileUris`: (opcional, matriz de cadeia de caracteres) as URLs para os arquivos a serem baixados.
* `timestamp`(opcional, número inteiro de 32 bits) Use este campo somente para disparar uma nova execução do script alterando o valor desse campo.  Qualquer valor inteiro é aceitável; Ele deve ser diferente do valor anterior.
* `storageAccountName`: (opcional, Cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, `fileUris` todas deverão ser URLs para BLOBs do Azure.
* `storageAccountKey`: (opcional, Cadeia de caracteres) a chave de acesso da conta de armazenamento

Os valores a seguir podem ser definidos em configurações públicas ou protegidas, a extensão rejeitará qualquer configuração na qual os valores abaixo estejam definidos nas configurações pública e protegida.

* `commandToExecute`

O uso de configurações públicas talvez seja útil para depuração, mas é recomendável que você use configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM em que o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida somente para o Azure e a VM. As configurações são salvas na VM conforme elas foram enviadas, ou seja, se as configurações foram criptografadas, elas são salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) em tempo de execução.

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON, que é detalhado na seção anterior, pode ser usado em um modelo de Azure Resource Manager para executar a extensão de script personalizado durante a implantação. Os exemplos a seguir mostram como usar a extensão de script personalizado:

* [Tutorial: Implantar extensões de máquina virtual com modelos de Azure Resource Manager](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Implantar um aplicativo de duas camadas no Windows e no banco de BD SQL do Azure](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Implantação do PowerShell

O `Set-AzVMCustomScriptExtension` comando pode ser usado para adicionar a extensão de script personalizado a uma máquina virtual existente. Para obter mais informações, consulte [set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

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

Neste exemplo, você tem três scripts que são usados para criar seu servidor. O **commandToExecute** chama o primeiro script e, em seguida, você tem opções sobre como os outros são chamados. Por exemplo, você pode ter um script mestre que controla a execução, com o tratamento de erros, o registro em log e o gerenciamento de estado corretos. Os scripts são baixados no computador local para execução. Por exemplo, `1_Add_Tools.ps1` em você chamaria `2_Add_Features.ps1` adicionando `.\2_Add_Features.ps1` ao script e repetiria esse processo para os outros scripts que você definir em `$settings`.

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
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Executando scripts de um compartilhamento local

Neste exemplo, talvez você queira usar um servidor SMB local para o local do script. Ao fazer isso, você não precisa fornecer outras configurações, exceto **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Como executar um script personalizado mais de uma vez com a CLI

Se você quiser executar a extensão de script personalizado mais de uma vez, só poderá executar essa ação sob estas condições:

* O parâmetro de **nome** da extensão é o mesmo que a implantação anterior da extensão.
* Atualize a configuração, caso contrário, o comando não será executado novamente. Você pode adicionar uma propriedade dinâmica no comando, como um carimbo de data/hora.

Como alternativa, você pode definir a propriedade [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) como **true**.

### <a name="using-invoke-webrequest"></a>Usando Invoke-WebRequest

Se você estiver usando [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) em seu script, será necessário especificar o parâmetro `-UseBasicParsing` ou, caso contrário, receberá o seguinte erro ao verificar o status detalhado:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>VMs clássicas

Para implantar a extensão de script personalizado em VMs clássicas, você pode usar o portal do Azure ou os cmdlets Azure PowerShell clássicos.

### <a name="azure-portal"></a>Portal do Azure

Navegue até o recurso da VM clássica. Selecione **extensões** em **configurações**.

Clique em **+ Adicionar** e, na lista de recursos, escolha **extensão de script personalizado**.

Na página **instalar extensão** , selecione o arquivo local do PowerShell e preencha os argumentos e clique em **OK**.

### <a name="powershell"></a>PowerShell

Use o cmdlet [set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) pode ser usado para adicionar a extensão de script personalizado a uma máquina virtual existente.

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

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure e usando o módulo Azure PowerShell. Para ver o estado de implantação das extensões de uma determinada VM, execute o seguinte comando:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A saída da extensão é registrada em arquivos encontrados na seguinte pasta na máquina virtual de destino.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os arquivos especificados são baixados para a seguinte pasta na máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

em `<n>` que é um inteiro decimal, que pode ser alterado entre as execuções da extensão.  O `1.*` valor corresponde ao valor atual `typeHandlerVersion` real da extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o `commandToExecute` comando, a extensão define esse diretório (por exemplo, `...\Downloads\2`) como o diretório de trabalho atual. Esse processo permite o uso de caminhos relativos para localizar os arquivos baixados por `fileURIs` meio da propriedade. Consulte a tabela abaixo para obter exemplos.

Como o caminho de download absoluto pode variar ao longo do tempo, é melhor optar por caminhos de script/arquivo relativos `commandToExecute` na cadeia de caracteres, sempre que possível. Por exemplo:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

As informações de caminho após o primeiro segmento de URI são mantidas para `fileUris` os arquivos baixados por meio da lista de propriedades.  Conforme mostrado na tabela a seguir, os arquivos baixados são mapeados em subdiretórios de download para refletir `fileUris` a estrutura dos valores.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de arquivos baixados

| URI em fileuris | Local baixado relativo | Local baixado absoluto <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> os caminhos de diretório absolutos são alterados durante o tempo de vida da VM, mas não dentro de uma única execução da extensão CustomScript.

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você também pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
