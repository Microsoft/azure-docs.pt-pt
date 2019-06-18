---
title: Extensão de Script personalizado do Azure para Windows | Documentos da Microsoft
description: Automatizar tarefas de configuração de VM do Windows ao utilizar a extensão de Script personalizado
services: virtual-machines-windows
manager: carmonm
author: georgewallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: gwallace
ms.openlocfilehash: b71ba69bcf4965ea607e097c392573e77aab6865
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65408279"
---
# <a name="custom-script-extension-for-windows"></a>Extensão de Script personalizado para Windows

A extensão de Script personalizado transfere e executa scripts em máquinas virtuais do Azure. Esta extensão é útil para configuração pós-implementação, a instalação de software, ou qualquer outro configuração ou tarefas de gestão. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. A extensão de Script personalizado é integrada em modelos do Azure Resource Manager e pode ser executada com a CLI do Azure, PowerShell, portal do Azure ou a API de REST de máquinas virtuais do Azure.

Este documento fornece detalhes sobre como utilizar a extensão de Script personalizado com o módulo Azure PowerShell, modelos Azure Resource Manager e detalhes de resolução de problemas de etapas em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]  
> Utilize a extensão de Script personalizado para executar o cmdlet Update-AzVM com a mesma VM como seu parâmetro, uma vez que ele irá esperar em si mesmo.  

### <a name="operating-system"></a>Sistema operativo

O Custom Script extensão para o Windows irá executar a extensão de extensão suportada OSs, para obter mais informações, consulte este [sistemas operativos suportados de extensão do Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Localização do script

Pode configurar a extensão para utilizar as credenciais do armazenamento de Blobs do Azure para aceder ao armazenamento de Blobs do Azure. A localização de script pode ser qualquer lugar, desde que a VM pode encaminhar para esse ponto de extremidade, como o GitHub ou um servidor de ficheiros internos.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Se precisar de transferir um script externamente, tal como a partir do GitHub ou o armazenamento do Azure, em seguida, firewall adicionais e portas do grupo de segurança de rede tem de ser aberto. Por exemplo, se o script está localizado no armazenamento do Azure, pode permitir o acesso utilizando as etiquetas de serviço do Azure NSG para [armazenamento](../../virtual-network/security-overview.md#service-tags).

Se o script estiver num servidor local, em seguida, ainda pode ser necessário firewall adicionais e portas do grupo de segurança de rede tem de ser aberto.

### <a name="tips-and-tricks"></a>Sugestões e Truques

* A mais alta taxa de falhas para esta extensão é devido a erros de sintaxe no script, teste o script é executado sem erros e também coloca no registo para o script para torná-lo mais fácil encontrar onde falha adicional.
* Escreva scripts que são idempotent. Isto garante que se novamente acidentalmente execute, ele não fará com que as alterações do sistema.
* Certifique-se de que os scripts não requerem dados do utilizador quando são executados.
* Existe um período de 90 minutos permitido para a execução do script. Um período mais longo resultará numa falha de aprovisionamento da extensão.
* Não coloque reinícios no script. Essa ação causará problemas com outras extensões que sejam instaladas. Após o reinício, a extensão não continuará.
* Se tiver um script que irá forçar a reinicialização, em seguida, instalar aplicativos e executar scripts, pode agendar o reinício através de uma tarefa agendada do Windows, ou usar ferramentas como extensões de DSC, Chef ou Puppet.
* A extensão apenas executará um script de uma vez. Se quiser executar um script a cada arranque, tem de utilizar a extensão para criar uma Tarefa Agendada do Windows.
* Se quiser agendar a execução de um script, deve utilizar a extensão para criar uma Tarefa Agendada do Windows.
* Quando o script estiver em execução, verá apenas um estado de extensão "em transição" no portal ou na CLI do Azure. Se quiser atualizações de estado mais frequentes de um script em execução, terá de criar a sua própria solução.
* Extensão de Script personalizado não suporta nativamente servidores proxy, no entanto, pode usar uma ferramenta de transferência de ficheiros que suporta servidores de proxy no seu script, como *Curl*
* Tenha em atenção as localizações de diretório não predefinidas de que os scripts ou comandos possam depender. Tenha uma lógica para lidar com esta situação.

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de Script personalizado especifica coisas como a localização de script e o comando a ser executado. Pode armazenar esta configuração nos arquivos de configuração, pode especificá-la na linha de comando ou especificá-lo num modelo Azure Resource Manager.

Pode armazenar dados confidenciais numa configuração protegida, que é encriptada e descriptografada apenas dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma palavra-passe.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegido extensões. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino.

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
> Apenas uma versão de uma extensão pode ser instalada numa VM num ponto no tempo, especificando um script personalizado duas vezes no mesmo modelo do Resource Manager para a mesma VM falhará.

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (por exemplo) | 123456789 | número inteiro de 32 bits |
| commandToExecute (por exemplo) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Estes nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implementação, utilize os nomes, conforme mostrado aqui.

#### <a name="property-value-details"></a>Detalhes de valor de propriedade

* `commandToExecute`: (**necessário**, cadeia de caracteres) o script do ponto de entrada para executar. Utilize este campo em vez disso, se o comando contém segredos como palavras-passe, ou seu fileUris são confidenciais.
* `fileUris`: (opcional, matriz de cadeia) as URLs para o ficheiro (s) a serem baixados.
* `timestamp` Este campo só para acionar uma volte a executar o script alterando o valor deste campo utilização de (inteiro opcional, 32 bits).  Qualquer valor inteiro é aceitável; só tem de ser diferente do valor anterior.
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se especificar credenciais de armazenamento, todas as `fileUris` tem de ser URLs para os Blobs do Azure.
* `storageAccountKey`: (opcional, cadeia) a chave de acesso da conta de armazenamento

Os seguintes valores podem ser definidos nas definições de públicas ou protegidas, a extensão irão rejeitar qualquer configuração onde os valores abaixo são definidos nas definições de públicas e protegidas.

* `commandToExecute`

Utilizar definições públicas pode ser útil para depuração, mas é recomendado que utilize definições protegidas.

Definições de públicas são enviadas em texto não criptografado para a VM em que o script será executado.  Definições protegidas são encriptadas com uma chave só conhecida o Azure e a VM. As definições são guardadas para a VM à medida que eles sejam encaminhados, ou seja, se as definições foram encriptadas vez salvos encriptados na VM. O certificado utilizado para desencriptar os valores criptografados é armazenado na VM e utilizado para desencriptar as definições (se necessário) em tempo de execução.

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON, que se encontra detalhado na secção anterior pode ser utilizado num modelo do Azure Resource Manager para executar a extensão de Script personalizado durante a implementação. Os exemplos seguintes mostram como utilizar a extensão de Script personalizado:

* [Tutorial: Implementar extensões de máquina virtual com modelos Azure Resource Manager](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Implementar a aplicação de duas camadas no Windows e BD SQL do Azure](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Implementação do PowerShell

O `Set-AzVMCustomScriptExtension` comando pode ser utilizado para adicionar a extensão de Script personalizado para uma máquina virtual existente. Para obter mais informações, consulte [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Exemplos adicionais

### <a name="using-multiple-scripts"></a>Utilização de vários scripts

Neste exemplo, tem três scripts que são utilizados para criar o seu servidor. O **commandToExecute** chama o primeiro script, em seguida, tem opções sobre como os outros são chamados. Por exemplo, pode ter um script principal que controla a execução, com o tratamento de erros certo, o registo e o gerenciamento de estado. Os scripts são transferidos para o computador local para execução. Por exemplo, num `1_Add_Tools.ps1` chamaria `2_Add_Features.ps1` adicionando `.\2_Add_Features.ps1` para o script e repita este processo para os outros scripts definidos na `$settings`.

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

### <a name="running-scripts-from-a-local-share"></a>Executar scripts a partir de uma partilha local

Neste exemplo, pode querer utilizar um servidor do SMB local para a sua localização do script. Ao fazer isso, não precisa fornecer quaisquer outras definições, exceto **commandToExecute**.

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Como executar o script personalizado mais de uma vez com a CLI

Se quiser executar a extensão de script personalizado mais de uma vez, apenas pode efetuar esta ação sob estas condições:

* A extensão **nome** parâmetro é o mesmo que a implementação anterior da extensão.
* Atualize a configuração, caso contrário, que o comando não será novamente executado. Pode adicionar uma propriedade dinâmica para o comando, como um carimbo.

Em alternativa, pode definir o [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) propriedade **verdadeiro**.

### <a name="using-invoke-webrequest"></a>Usando o Invoke-WebRequest

Se estiver a utilizar [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) no seu script, tem de especificar o parâmetro `-UseBasicParsing` ou outro receberá o seguinte erro ao verificar o estado detalhado:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>VMs clássicas

Para implementar a extensão de Script personalizado em VMs clássicas, pode utilizar o portal do Azure ou os cmdlets do PowerShell clássico do Azure.

### <a name="azure-portal"></a>Portal do Azure

Navegue para o recurso da VM clássica. Selecione **extensões** sob **definições**.

Clique em **+ adicionar** e na lista de recursos, escolha **extensão de Script personalizado**.

Sobre o **instalar a extensão** página, selecione o ficheiro local do PowerShell e preencha quaisquer argumentos e clique em **Ok**.

### <a name="powershell"></a>PowerShell

Utilize o [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) cmdlet pode ser utilizado para adicionar a extensão de Script personalizado para uma máquina virtual existente.

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

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Saída de extensão é registada para arquivos encontrados na pasta seguinte na máquina de virtual de destino.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os ficheiros especificados são transferidos para a pasta seguinte na máquina de virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

onde `<n>` é um número inteiro decimal, o que pode ser alteradas por execuções da extensão.  O `1.*` valor corresponde ao atual real, `typeHandlerVersion` valor da extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o `commandToExecute` comando, a extensão define este diretório (por exemplo, `...\Downloads\2`) como o atual diretório de trabalho. Este processo permite a utilização de caminhos relativos para localizar os arquivos baixados via o `fileURIs` propriedade. Consulte a tabela abaixo para obter exemplos.

Uma vez que o caminho de transferência absoluto pode variar ao longo do tempo, é melhor otimizado para caminhos de script/ficheiro relativo no `commandToExecute` string, sempre que possível. Por exemplo:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informações de caminho após o primeiro segmento URI é mantido por arquivos baixados via o `fileUris` lista de propriedades.  Conforme mostrado na tabela abaixo, os ficheiros transferidos são mapeados em subdiretórios de download para refletir a estrutura do `fileUris` valores.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de ficheiros transferidos

| URI no fileUris | Localização transferida relativa | Absoluto transferido localização <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> os caminhos de diretório absoluto mudam ao longo do tempo de vida da VM, mas não numa única execução da extensão CustomScript.

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Também pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
