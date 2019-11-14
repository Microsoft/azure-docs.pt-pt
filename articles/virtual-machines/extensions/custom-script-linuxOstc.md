---
title: Executar scripts personalizados em VMs do Linux no Azure
description: Automatizar tarefas de configuração de VM do Linux usando a extensão de script personalizado v1
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
ms.openlocfilehash: b7dbabf5be8b1f223f6e39f294b9d7022b83c4f8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073171"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Usar a extensão de script personalizado do Azure versão 1 com máquinas virtuais do Linux

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

A extensão de script personalizado versão 1 baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração após a implantação, instalação de software ou qualquer outra tarefa de configuração/gerenciamento. Você pode baixar scripts do armazenamento do Azure ou outro local de Internet acessível, ou pode fornecê-los ao tempo de execução de extensão.

A extensão de script personalizado se integra com modelos de Azure Resource Manager. Você também pode executá-lo usando CLI do Azure, PowerShell, o portal do Azure ou a API REST de máquinas virtuais do Azure.

Este artigo fornece detalhes sobre como usar a extensão de script personalizado de CLI do Azure e como executar a extensão usando um modelo de Azure Resource Manager. Este artigo também fornece etapas de solução de problemas para sistemas Linux.

Há duas extensões de script personalizado do Linux:

* Versão 1-Microsoft. OSTCExtensions. às customscriptforlinux

* Versão 2-Microsoft. Azure. Extensions. CustomScript

Altere as implantações novas e existentes para usar a nova versão ([Microsoft. Azure. Extensions. CustomScript](custom-script-linux.md)) em vez disso. A nova versão destina-se a ser uma substituição completa. Assim sendo, a migração é tão fácil como alterar o nome e a versão. Não precisa de alterar a configuração da extensão.

### <a name="operating-system"></a>Sistema Operativo

Distribuições do Linux com suporte:

* CentOS 6,5 e superior
* Debian 8 e superior
  * O Debian 8,7 não é fornecido com Python2 nas imagens mais recentes, que quebram o às customscriptforlinux.
* FreeBSD
* OpenSUSE 13,1 e superior
* Oracle Linux 6,4 e superior
* SUSE Linux Enterprise Server 11 SP3 e superior
* Ubuntu 12, 4 e superior

### <a name="script-location"></a>Local do script

Você pode usar a extensão para usar suas credenciais de armazenamento de BLOBs do Azure, para acessar o armazenamento de BLOBs do Azure. Como alternativa, o local do script pode ser qualquer lugar, desde que a VM possa ser roteada para esse ponto de extremidade, como GitHub, servidor de arquivos interno, etc.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Se você precisar baixar um script externamente, como o GitHub ou o armazenamento do Azure, as portas de grupo de segurança de rede/firewall adicionais precisam ser abertas. Por exemplo, se o seu script estiver localizado no armazenamento do Azure, você poderá permitir o acesso usando as marcas do serviço NSG do Azure para [armazenamento](../../virtual-network/security-overview.md#service-tags).

Se o seu script estiver em um servidor local, talvez ainda seja necessário abrir portas de grupo de segurança de rede/firewall adicionais.

### <a name="tips-and-tricks"></a>Sugestões e Truques

* A taxa de falhas mais elevada para esta extensão deve-se a erros de sintaxe no script. Teste a execução do script sem erros e coloque registos adicionais no script para facilitar a deteção da falha.
* Escreva scripts que sejam idempotentes. Assim, se forem executados mais de uma vez acidentalmente, não causam alterações do sistema.
* Verifique se os scripts não exigem entrada do usuário quando eles são executados.
* Há 90 minutos permitidos para o script ser executado; qualquer coisa resultará em um provisionamento com falha da extensão.
* Não coloque reinicializações dentro do script, isso causará problemas com outras extensões que estão sendo instaladas e após a reinicialização, a extensão não continuará após a reinicialização. 
* Se você tiver um script que causará uma reinicialização, instale aplicativos e execute scripts, etc. Você deve agendar a reinicialização usando um trabalho cron ou usando ferramentas como DSC, ou chefe, Puppet Extensions.
* A extensão só executará um script uma vez, se você quiser executar um script em cada inicialização, poderá usar a [imagem de inicialização de nuvem](../linux/using-cloud-init.md) e usar um módulo de [scripts por inicialização](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) . Como alternativa, você pode usar o script para criar uma unidade de serviço do sistema.
* Se você quiser agendar quando um script será executado, use a extensão para criar um trabalho cron.
* Quando o script estiver em execução, verá apenas um estado de extensão "em transição" no portal ou na CLI do Azure. Se você quiser atualizações de status mais frequentes de um script em execução, será necessário criar sua própria solução.
* A extensão de script personalizado não dá suporte nativo a servidores proxy, no entanto, você pode usar uma ferramenta de transferência de arquivo que dá suporte a servidores proxy em seu script, como *ondulação*.
* Lembre-se de locais de diretório não padrão dos quais seus scripts ou comandos podem depender, têm lógica para lidar com isso.

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica coisas como o local do script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo de Azure Resource Manager. 

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada apenas dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de configurações protegidas por extensões. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino.

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
| publisher | Microsoft. OSTCExtensions | string |
| tipo | CustomScriptForLinux | string |
| typeHandlerVersion | 1.5 | int |
| fileuris (por exemplo,) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (por exemplo,) | Python MyPythonScript.py \<My-param1\> | string |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (por exemplo,) | examplestorageacct | string |
| storageAccountKey (por exemplo,) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Detalhes do valor da propriedade

* `fileUris`: (opcional, matriz de cadeia de caracteres) a lista de URI dos scripts
* `enableInternalDNSCheck`: (opcional, bool) o padrão é true, definido como false para desabilitar a verificação de DNS.
* `commandToExecute`: (opcional, Cadeia de caracteres) o script EntryPoint a ser executado
* `storageAccountName`: (opcional, Cadeia de caracteres) o nome da conta de armazenamento
* `storageAccountKey`: (opcional, Cadeia de caracteres) a chave de acesso da conta de armazenamento

Os valores a seguir podem ser definidos em configurações públicas ou protegidas, você não deve ter esses valores abaixo definidos nas configurações pública e protegida.

* `commandToExecute`

O uso de configurações públicas talvez seja útil para depuração, mas é altamente recomendável que você use as configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM em que o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida somente para o Azure e a VM. As configurações são salvas na VM conforme elas foram enviadas, ou seja, se as configurações foram criptografadas, elas são salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) em tempo de execução.

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo de Azure Resource Manager para executar a extensão de script personalizado durante uma implantação de modelo de Azure Resource Manager.

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
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure

Quando você estiver usando CLI do Azure para executar a extensão de script personalizado, crie um arquivo de configuração ou arquivos. No mínimo, você deve ter ' commandToExecute '.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Opcionalmente, você pode especificar as configurações no comando como uma cadeia de caracteres formatada em JSON. Isso permite que a configuração seja especificada durante a execução e sem um arquivo de configuração separado.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos da CLI do Azure

#### <a name="public-configuration-with-no-script-file"></a>Configuração pública sem arquivo de script

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

CLI do Azure comando:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Arquivos de configuração públicos e protegidos

Você usa um arquivo de configuração pública para especificar o URI do arquivo de script. Você usa um arquivo de configuração protegida para especificar o comando a ser executado.

Arquivo de configuração pública:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Arquivo de configuração protegida:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

CLI do Azure comando:

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

Quando a extensão de script personalizado é executada, o script é criado ou baixado em um diretório semelhante ao exemplo a seguir. A saída do comando também é salva nesse diretório em arquivos `stdout` e `stderr`.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Para solucionar problemas, primeiro verifique o log do agente do Linux, verifique se a extensão foi executada, verifique:

```bash
/var/log/waagent.log
```

Você deve procurar a execução da extensão; ela terá uma aparência semelhante a esta:

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

1. Habilitar é quando o comando começa a ser executado.
1. O download está relacionado ao download do pacote de extensão CustomScript do Azure, não aos arquivos de script especificados em fileuris.
1. Você também pode ver em qual arquivo de log ele está gravando `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

A próxima etapa é verificar o arquivo de log, este é o formato:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Você deve procurar a execução individual; ela terá uma aparência semelhante a:

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

Aqui você pode ver:

* O comando habilitar iniciando é este log
* As configurações passadas para a extensão
* A extensão que baixa o arquivo e o resultado disso.
* O comando que está sendo executado e o resultado.

Você também pode recuperar o estado de execução da extensão de script personalizado usando CLI do Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída é semelhante ao seguinte texto:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Passos seguintes

Para ver o código, os problemas e as versões atuais, consulte [repositório de extensão CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
