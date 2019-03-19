---
title: Extensão de Script personalizado do Azure para Windows | Documentos da Microsoft
description: Automatizar tarefas de configuração de VM do Windows ao utilizar a extensão de Script personalizado
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2018
ms.author: roiyz
ms.openlocfilehash: 520ff1dfeefc8cca66710745012ee54b550a19a0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097928"
---
# <a name="custom-script-extension-for-windows"></a>Extensão de Script personalizado para Windows

A extensão de Script personalizado transfere e executa scripts em máquinas virtuais do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão. A extensão de Script Personalizado é integrada em modelos do Azure Resource Manager, podendo também ser executada ao utilizar a CLI do Azure, PowerShell, portal do Azure ou API REST de Máquinas Virtuais do Azure.

Este documento fornece detalhes sobre como utilizar a extensão de Script personalizado com o módulo Azure PowerShell, modelos Azure Resource Manager e detalhes de resolução de problemas de etapas em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]  
> Utilize a extensão de Script personalizado para executar o cmdlet Update-AzVM com a mesma VM como seu parâmetro, uma vez que ele irá esperar em si mesmo.  
>   
> 

### <a name="operating-system"></a>Sistema Operativo

A extensão de Script personalizado para Linux será executado na extensão de extensão suportada OSs, para obter mais informações, veja isso [artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Localização do script

Pode utilizar a extensão para utilizar as suas credenciais do armazenamento de Blobs do Azure, para aceder ao armazenamento de Blobs do Azure. Em alternativa, a localização do script pode ser em qualquer lugar, desde que a VM pode encaminhar para esse ponto de extremidade, como o GitHub, o servidor de ficheiros interna etc.


### <a name="internet-connectivity"></a>Conectividade com a Internet
Se precisar de transferir um script externamente, como o GitHub ou o armazenamento do Azure, em seguida, rede/firewall adicional grupo de segurança portas tem de ser aberto. Por exemplo, se o script está localizado no armazenamento do Azure, pode permitir acesso utilizando as etiquetas de serviço do Azure NSG para [armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Se o script num servidor local, então ainda pode ser necessário adicionais firewall/redes segurança portas de grupo tem de ser aberto.

### <a name="tips-and-tricks"></a>Sugestões e Truques
* A mais alta taxa de falhas para esta extensão é devido a erros de sintaxe no script, teste o script é executado sem erros e também coloca no registo para o script para torná-lo mais fácil encontrar onde falha adicional.
* Escrever scripts que são idempotentes, para que se obtenha execute novamente mais do que uma vez acidentalmente, não irá causar as alterações do sistema.
* Certifique-se de que os scripts não exigem intervenção do usuário quando eles são executados.
* Há 90 minutos permitidos para o script a executar, nada mais tempo irá resultar numa falha ao aprovisionar da extensão.
* Não coloque reinicializações dentro do script, essa ação causará problemas com outras extensões que estão a ser instalados. Reinício de postagem, a extensão não continuar após o reinício. 
* Se tiver um script que fará com que um reinício, em seguida, instalar aplicativos e executar scripts, etc. Pode agendar o reinício através de uma tarefa agendada do Windows ou com ferramentas como o DSC ou Chef, extensões de Puppet.
* A extensão apenas será executado um script de uma vez, se quiser executar um script em cada inicialização, em seguida, tem de utilizar a extensão para criar uma tarefa agendada do Windows.
* Se quiser agendar quando um script é executado, deve utilizar a extensão para criar uma tarefa agendada do Windows. 
* Quando o script é executado, verá apenas um "transição" Estado da extensão do portal do Azure ou da CLI. Se pretender que as atualizações mais freqüentes de estado de um script em execução, terá de criar sua própria solução.
* Extensão de Script personalizado não suporta nativamente servidores proxy, no entanto, pode usar uma ferramenta de transferência de ficheiros que suporta servidores de proxy no seu script, como *Curl* 
* Lembre-se de não-padrão localizações do que seus scripts ou comandos possam depender, ter uma lógica para lidar com essa situação.


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
**Tenha em atenção** -apenas uma versão de uma extensão pode ser instalada numa VM num ponto no tempo, especificando o script personalizado duas vezes no mesmo modelo do Resource Manager para a mesma VM falhará. 

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Compute | string |
| tipo | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| Timestamp (por exemplo) | 123456789 | número inteiro de 32 bits |
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

Definições de públicas são enviadas em texto não criptografado para a VM em que o script será executado.  Definições protegidas são encriptadas com uma chave só conhecida o Azure e a VM. As definições são guardadas para a VM à medida que eles sejam encaminhados, ou seja, se as definições foram encriptadas que são guardados encriptados na VM. O certificado utilizado para desencriptar os valores criptografados é armazenado na VM e utilizado para desencriptar as definições (se necessário) em tempo de execução.

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON, que se encontra detalhado na secção anterior pode ser utilizado num modelo do Azure Resource Manager para executar a extensão de Script personalizado durante uma implementação de modelo do Azure Resource Manager. Os exemplos seguintes mostram como utilizar a extensão de Script personalizado:

* [Tutorial: Implementar extensões de máquina virtual com modelos Azure Resource Manager](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Implementar a aplicação de duas camadas no Windows e BD SQL do Azure](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Implementação do PowerShell

O `Set-AzVMCustomScriptExtension` comando pode ser utilizado para adicionar a extensão de Script personalizado para uma máquina virtual existente. Para obter mais informações, consulte [Set-AzVMCustomScriptExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Mais exemplos

### <a name="using-multiple-script"></a>Usando vários Script
Neste exemplo, tem três scripts que são utilizados para criar o seu servidor, as chamadas de "commandToExecute" o primeiro script, em seguida, tem opções sobre como os outros são chamados, por exemplo, pode ter um script principal que controla a execução, com o erro certo manipulação, o registo e o gerenciamento de estado.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Executar scripts a partir de uma partilha local
Neste exemplo, pode querer utilizar um servidor do SMB local para a sua localização do script, tenha em atenção de que não precisa passar em outro outras definições, exceto *commandToExecute*.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Como executar o script personalizado mais de uma vez com a CLI
Se quiser executar a extensão de script personalizado mais de uma vez, apenas pode efetuar esta ação sob estas condições:
1. O parâmetro de "Name" de extensão é o mesmo que a implementação anterior da extensão.
2. Tem de atualizar a configuração, caso contrário, que o comando não será novamente executado. Pode adicionar uma propriedade dinâmica para o comando, como um carimbo.

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Resultado da execução de extensão é registado para o arquivo foi encontrado no diretório de seguinte na máquina de virtual de destino.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os ficheiros especificados são transferidos para o diretório seguinte na máquina de virtual de destino.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
onde `<n>` é um número inteiro decimal, o que pode ser alteradas por execuções da extensão.  O `1.*` valor corresponde ao atual real, `typeHandlerVersion` valor da extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o `commandToExecute` comando, a extensão define este diretório (por exemplo, `...\Downloads\2`) como o atual diretório de trabalho. Este processo permite a utilização de caminhos relativos para localizar os arquivos baixados via o `fileURIs` propriedade. Consulte a tabela abaixo para obter exemplos.

Uma vez que o caminho de transferência absoluto pode variar ao longo do tempo, é melhor otimizado para caminhos de script/ficheiro relativo no `commandToExecute` string, sempre que possível. Por exemplo:
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informações de caminho após o primeiro segmento URI é mantido para arquivos baixados via o `fileUris` lista de propriedades.  Conforme mostrado na tabela abaixo, os ficheiros transferidos são mapeados em subdiretórios de download para refletir a estrutura do `fileUris` valores.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de ficheiros transferidos

| URI no fileUris | Localização transferida relativa | Absoluto transferido localização * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Como acima, os caminhos de diretório absoluto mudar ao longo do tempo de vida da VM, mas não numa única execução da extensão CustomScript.

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Também pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
