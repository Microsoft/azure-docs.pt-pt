---
title: Executar scripts personalizados em VMs do Linux no Azure | Documentos da Microsoft
description: Automatizar tarefas de configuração de VM do Linux ao utilizar a v1 de extensão de Script personalizado
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: 8638b788762a56813c622c0abffe2a8eae3c70c2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437110"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Utilizar a versão 1 do Azure Custom Script extensão com máquinas virtuais do Linux

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

A versão 1 do Custom Script extensão transfere e executa scripts em máquinas virtuais do Azure. Esta extensão é útil para configuração de pós-implementação, instalação de software ou qualquer outra tarefa de gestão de configuração /. Pode baixar scripts a partir do armazenamento do Azure ou noutra localização acessível da internet ou pode fornecê-los para o tempo de execução de extensão.

A extensão de Script personalizado integra-se com modelos Azure Resource Manager. Também pode executá-lo ao utilizar a CLI do Azure, PowerShell, portal do Azure ou a API de REST de máquinas virtuais do Azure.

Este artigo fornece detalhes sobre como utilizar a extensão de Script personalizado da CLI do Azure e como executar a extensão com um modelo Azure Resource Manager. Este artigo também fornece passos de resolução de problemas para sistemas Linux.

Existem duas extensões de Script de personalizada de Linux:

* Versão 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* Versão 2 - Microsoft.Azure.Extensions.CustomScript

Mude as implementações novas e existentes para utilizar a nova versão ([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md)) em vez disso. A nova versão se destina a ser uma substituição completa. Portanto, a migração é tão fácil como alterar o nome e versão, não é necessário alterar a configuração de extensão.

### <a name="operating-system"></a>Sistema Operativo

Distribuições Linux suportadas:

* CentOS 6.5 e versões posteriores
* Debian 8 e superior
  * Debian 8,7 não é fornecido com Python2 nas imagens do mais recente, que quebra CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13.1 e superior
* Oracle Linux 6.4 e superior
* SUSE Linux Enterprise Server 11 SP3 e superior
* Ubuntu 12.04 e superior

### <a name="script-location"></a>Localização do script

Pode utilizar a extensão para utilizar as suas credenciais do armazenamento de Blobs do Azure, para aceder ao armazenamento de Blobs do Azure. Em alternativa, a localização do script pode ser qualquer where, desde que a VM pode encaminhar para esse ponto de extremidade, como o GitHub, o servidor de ficheiros interna etc.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Se precisar de transferir um script externamente, como o GitHub ou o armazenamento do Azure, em seguida, rede/firewall adicional grupo de segurança portas tem de ser aberto. Por exemplo, se o script está localizado no armazenamento do Azure, pode permitir acesso utilizando as etiquetas de serviço do Azure NSG para [armazenamento](../../virtual-network/security-overview.md#service-tags).

Se o script num servidor local, então ainda pode ser necessário adicionais firewall/redes segurança portas de grupo tem de ser aberto.

### <a name="tips-and-tricks"></a>Sugestões e Truques

* A mais alta taxa de falhas para esta extensão é devido a erros de sintaxe no script, teste o script é executado sem erros e também coloca no registo para o script para torná-lo mais fácil encontrar onde falha adicional.
* Escrever scripts que são idempotentes, para que se obtenha execute novamente mais do que uma vez acidentalmente, não irá causar as alterações do sistema.
* Certifique-se de que os scripts não necessitam de entrada do usuário quando eles são executados.
* Há 90 minutos permitidos para o script a executar, nada mais tempo irá resultar numa falha ao aprovisionar da extensão.
* Não coloque reinicializações dentro do script, isso causará problemas com outras extensões que estão a ser instalados e, após o reinício, a extensão não será continuada após o reinício. 
* Se tiver um script que fará com que um reinício, em seguida, instalar aplicativos e executar scripts, etc. Deve agendar o reinício de utilizar uma tarefa Cron ou utilizar ferramentas como o DSC ou Chef, extensões de Puppet.
* A extensão apenas executarão um script de uma vez, se quiser executar um script em cada inicialização, em seguida, pode utilizar [imagem na cloud-init](../linux/using-cloud-init.md) e utilizar um [Scripts por inicialização](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) módulo. Em alternativa, pode utilizar o script para criar uma unidade de serviço Systemd.
* Se quiser agendar quando um script é executado, deve utilizar a extensão para criar uma tarefa Cron.
* Quando o script é executado, verá apenas um "transição" Estado da extensão do portal do Azure ou da CLI. Se pretender que as atualizações mais freqüentes de estado de um script em execução, terá de criar sua própria solução.
* Extensão de Script personalizado não suporta nativamente servidores proxy, no entanto, pode usar uma ferramenta de transferência de ficheiros que suporta servidores de proxy no seu script, tal como *Curl*.
* Lembre-se dos locais de diretório não padrão que seus scripts ou comandos podem basear-se, ter uma lógica para lidar com isso.

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de Script personalizado especifica coisas como a localização de script e o comando a ser executado. Pode armazenar esta configuração nos arquivos de configuração, pode especificá-la na linha de comando ou especificá-lo num modelo Azure Resource Manager. 

Pode armazenar dados confidenciais numa configuração protegida, que é encriptada e descriptografada apenas dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma palavra-passe.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegido extensões. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.OSTCExtensions | string |
| tipo | CustomScriptForLinux | string |
| typeHandlerVersion | 1.5 | int |
| fileUris (por exemplo) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (por exemplo) | python MyPythonScript.py \<my-param1\> | string |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Detalhes de valor de propriedade

* `fileUris`: (opcional, matriz de cadeia) a lista de uri dos scripts
* `enableInternalDNSCheck`: (opcional, bool) a predefinição é True, definido como falso para desativar a verificação DNS.
* `commandToExecute`: (opcional, cadeia de caracteres) o script de ponto de entrada para executar
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento
* `storageAccountKey`: (opcional, cadeia) a chave de acesso da conta de armazenamento

Os seguintes valores podem ser definidos nas definições de públicas ou protegidas, não tem de ter estes valores abaixo conjunto nas definições de públicas e protegidas.

* `commandToExecute`

Utilizar as definições de públicas pode ser útil para depuração, mas ele é altamente recomendável que utilize definições protegidas.

Definições de públicas são enviadas em texto não criptografado para a VM em que o script será executado.  Definições protegidas são encriptadas com uma chave só conhecida o Azure e a VM. As definições são guardadas para a VM à medida que eles sejam encaminhados, ou seja, se as definições foram encriptadas que são guardados encriptados na VM. O certificado utilizado para desencriptar os valores criptografados é armazenado na VM e utilizado para desencriptar as definições (se necessário) em tempo de execução.

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo do Azure Resource Manager para executar a extensão de Script personalizado durante uma implementação de modelo do Azure Resource Manager.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Estes nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implementação, utilize os nomes, conforme mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure

Quando estiver a utilizar o CLI do Azure para executar a extensão de Script personalizado, crie um ficheiro de configuração ou ficheiros. No mínimo, tem de ter 'commandToExecute'.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Opcionalmente, pode especificar as definições no comando como uma cadeia de caracteres de formato JSON. Isso permite que a configuração seja especificado durante a execução e sem um ficheiro de configuração diferente.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos da CLI do Azure

#### <a name="public-configuration-with-no-script-file"></a>Configuração pública sem nenhum arquivo de script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Ficheiros de configuração públicos e protegidos

Utilize um ficheiro de configuração pública para especificar o URI do ficheiro de script. Utilize um ficheiro de configuração protegida para especificar o comando a ser executado.

Ficheiro de configuração pública:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Ficheiro de configuração protegida:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Resolução de problemas

Quando a extensão de Script personalizado é executado, o script é criado ou transferido para um diretório que é semelhante ao seguinte exemplo. A saída do comando também é guardada para este diretório na `stdout` e `stderr` ficheiros.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Para resolver, primeiro verifique o registo do agente do Linux, certifique-se de que a extensão for executada, verifique:

```bash
/var/log/waagent.log
```

Deve procurar a execução de extensão, ele será algo parecido com:

```text
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

Alguns pontos a serem observados:

1. Ativar é quando o comando começa a ser executado.
1. Download está relacionado com o download do pacote de extensão CustomScript do Azure, não os arquivos de script especificado no fileUris.
1. Também pode ver o ficheiro de registo que ele está gravando`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
`

Próxima etapa é passar uma verificação do ficheiro de registo, este é o formato:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Deve procurar a execução individual, ele será algo parecido com:

```text
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

Aqui pode ver:

* O início do comando de ativação é este registo
* As definições transmitidas para a extensão
* A extensão de transferir o ficheiro e o resultado disso.
* O comando a ser executado e o resultado.

Também pode obter o estado de execução da extensão de Script personalizado com a CLI do Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída é parecido com o seguinte texto:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Passos Seguintes

Para ver o código, problemas atuais e versões, veja [repositório de extensão CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
