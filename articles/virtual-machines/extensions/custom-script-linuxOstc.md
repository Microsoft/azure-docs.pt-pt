---
title: Executar scripts personalizados em VMs Linux em Azure
description: Automatizar tarefas de configuração VM Linux utilizando a extensão de script personalizada v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: a3eae08510e57227b91deeeb7a7a608a6652cb4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79535413"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Utilize a versão 1 de extensão personalizada do script azure com máquinas virtuais Linux

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

A versão 1 de extensão de script personalizado descarrega e executa scripts em máquinas virtuais Azure. Esta extensão é útil para configuração pós-implementação, instalação de software ou qualquer outra tarefa de configuração/gestão. Pode descarregar scripts a partir do Azure Storage ou de outro local acessível à Internet, ou pode fornecê-los ao tempo de execução da extensão.

A extensão personalizada do script integra-se com os modelos do Gestor de Recursos Azure. Também pode executá-lo utilizando o Azure CLI, PowerShell, o portal Azure ou a API REST Das Máquinas Virtuais Azure.

Este artigo detalha como usar a extensão personalizada do script do Azure CLI e como executar a extensão utilizando um modelo de Gestor de Recursos Azure. Este artigo também fornece passos de resolução de problemas para sistemas Linux.

Existem duas extensões de script personalizadas Linux:

* Versão 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* Versão 2 - Microsoft.Azure.Extensions.CustomScript

Por favor, altere as implementações novas e existentes para utilizar a nova versão[(Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md)). A nova versão destina-se a ser uma substituição completa. Assim sendo, a migração é tão fácil como alterar o nome e a versão. Não precisa de alterar a configuração da extensão.

### <a name="operating-system"></a>Sistema Operativo

Distribuição linux suportada:

* CentOS 6.5 e superior
* Debiano 8 e superior
  * Debian 8.7 não é enviado com Python2 nas imagens mais recentes, que quebra o CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13.1 e superior
* Oracle Linux 6.4 e superior
* SUSE Linux Enterprise Server 11 SP3 e superior
* Ubuntu 12.04 e mais alto

### <a name="script-location"></a>Localização do script

Pode utilizar a extensão para utilizar as suas credenciais de armazenamento Azure Blob, para aceder ao armazenamento Azure Blob. Em alternativa, a localização do script pode ser qualquer um onde, desde que o VM possa encaminhar-se para esse ponto final, como gitHub, servidor de ficheiros interno, etc.

### <a name="internet-connectivity"></a>Conectividade da Internet

Se necessitar de descarregar um script externamente, como gitHub ou Azure Storage, então as portas adicionais do Grupo de Segurança de Firewall/Network precisam de ser abertas. Por exemplo, se o seu script estiver localizado no Armazenamento Azure, pode permitir o acesso utilizando etiquetas de serviço Azure NSG para [armazenamento](../../virtual-network/security-overview.md#service-tags).

Se o seu script estiver num servidor local, poderá ainda necessitar de portas adicionais de firewall/Network Security Group.

### <a name="tips-and-tricks"></a>Dicas e Truques

* A taxa de falhas mais elevada para esta extensão deve-se a erros de sintaxe no script. Teste a execução do script sem erros e coloque registos adicionais no script para facilitar a deteção da falha.
* Escreva scripts que sejam idempotentes. Assim, se forem executados mais de uma vez acidentalmente, não causam alterações do sistema.
* Certifique-se de que as scripts não requerem entrada do utilizador quando são executadas.
* Há 90 minutos permitidos para o script funcionar, qualquer coisa mais longa resultará numa disposição falhada da extensão.
* Não coloque reboots dentro do script, isto causará problemas com outras extensões que estão a ser instaladas, e após o reinício, a extensão não continuará após o reinício. 
* Se tiver um script que provoque um reboot, instale aplicações e execute scripts, etc. Você deve agendar o reboot usando um trabalho cron, ou usando ferramentas como DSC, ou Chef, extensões de marionetas.
* A extensão só executará um script uma vez, se quiser executar um script em cada bota, então pode usar a imagem de [cloud-init](../linux/using-cloud-init.md) e usar um módulo [Scripts Per Boot.](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) Em alternativa, pode utilizar o script para criar uma unidade de serviço Sistemad.
* Se quiser marcar quando um script será executado, deve usar a extensão para criar um trabalho cron.
* Quando o script estiver em execução, verá apenas um estado de extensão "em transição" no portal ou na CLI do Azure. Se quiser atualizações de estado mais frequentes de um script em execução, terá de criar a sua própria solução.
* A extensão do Script personalizado não suporta de forma nativa servidores proxy, no entanto pode utilizar uma ferramenta de transferência de ficheiros que suporta servidores proxy dentro do seu script, como *curl*.
* Esteja ciente de localizações de diretório sem incumprimento em que os seus scripts ou comandos podem confiar, tem lógica para lidar com isso.

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica coisas como a localização do script e o comando a ser executado. Pode armazenar esta configuração em ficheiros de configuração, especificá-la na linha de comando ou especificá-la num modelo de Gestor de Recursos Azure. 

Pode armazenar dados sensíveis numa configuração protegida, encriptada e apenas desencriptada dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos como uma palavra-passe.

Estes itens devem ser tratados como dados sensíveis e especificados na configuração de definição protegida das extensões. Os dados de definição protegidos por extensão Azure VM são encriptados e apenas desencriptados na máquina virtual alvo.

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

### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.OSTCExtensions | string |
| tipo | CustomScriptForLinux | string |
| typeHandlerVersion | 1.5 | int |
| fileUris (por exemplo) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| comandoToExecutar (por exemplo) | python \<MyPythonScript.py meu-param1\> | string |
| ativarInternalDNSCheck | true | boolean |
| armazenamentoAccountName (por exemplo) | exemplostorageacct | string |
| armazenagemAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQzQj8hg== | string |

### <a name="property-value-details"></a>Detalhes do valor da propriedade

* `fileUris`: (opcional, string array) a lista uri dos scripts
* `enableInternalDNSCheck`: (opcional, bool) a falha é verdadeira, definida para a verificação DNS falsa para desativar.
* `commandToExecute`: (opcional, corda) o roteiro do ponto de entrada para executar
* `storageAccountName`: (opcional, cadeia) o nome da conta de armazenamento
* `storageAccountKey`: (opcional, corda) a chave de acesso da conta de armazenamento

Os seguintes valores podem ser definidos em configurações públicas ou protegidas, não deve ndo ter estes valores abaixo definidos em configurações públicas e protegidas.

* `commandToExecute`

A utilização de configurações públicas talvez seja útil para depuração, mas recomenda-se vivamente que utilize configurações protegidas.

As configurações públicas são enviadas em texto claro para o VM onde o script será executado.  As definições protegidas são encriptadas utilizando uma chave conhecida apenas pelo Azure e pelo VM. As definições são guardadas para o VM à medida que foram enviadas, ou é, se as definições foram encriptadas, são guardadas encriptadas no VM. O certificado utilizado para desencriptar os valores encriptados é armazenado no VM e utilizado para desencriptar as definições (se necessário) no prazo de execução.

## <a name="template-deployment"></a>Implementação de modelos

As extensões VM azure podem ser implantadas com modelos de Gestor de Recursos Azure. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão do script personalizado durante uma implementação do modelo do Gestor de Recursos Azure.

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
>Estes nomes de propriedade são sensíveis a casos. Para evitar problemas de implantação, use os nomes como mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure

Quando estiver a utilizar o Azure CLI para executar a extensão do script personalizado, crie um ficheiro de configuração ou ficheiros. No mínimo, deve ter 'commandToExecute'.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Opcionalmente, pode especificar as definições no comando como uma cadeia formada JSON. Isto permite que a configuração seja especificada durante a execução e sem um ficheiro de configuração separado.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos da CLI do Azure

#### <a name="public-configuration-with-no-script-file"></a>Configuração pública sem ficheiro script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando Azure CLI:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Ficheiros de configuração públicos e protegidos

Utiliza um ficheiro de configuração pública para especificar o ficheiro URI do script. Utilize um ficheiro de configuração protegido para especificar o comando a ser executado.

Ficheiro de configuração pública:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Ficheiro de configuração protegido:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Comando Azure CLI:

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

Quando a extensão do script personalizado é executado, o script é criado ou descarregado para um diretório semelhante ao seguinte exemplo. A saída de comando também é `stdout` `stderr` guardada neste diretório dentro e em ficheiros.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Para resolver problemas, verifique primeiro o Registo do Agente Linux, certifique-se de que a extensão foi decorreu, verifique:

```bash
/var/log/waagent.log
```

Devias procurar a execução da extensão, vai parecer algo como:

```output
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

Alguns pontos a notar:

1. Ativar é quando o comando começa a funcionar.
1. O download diz respeito ao download do pacote de extensão CustomScript do Azure, e não aos ficheiros de script especificados no fileUris.
1. Você também pode ver qual o ficheiro de registo que está escrevendo para`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

O próximo passo é verificar o ficheiro de registo, este é o formato:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Devias procurar a execução individual, vai parecer algo como:

```output
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

* O arranque do comando Enable é este registo
* As definições passaram para a extensão
* O ficheiro de descarregamento de extensão e o resultado disso.
* O comando está a ser executado e o resultado.

Também pode recuperar o estado de execução da extensão do script personalizado utilizando o Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída parece com o seguinte texto:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Passos seguintes

Para ver o código, questões e versões atuais, consulte o repo de [extensão CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
