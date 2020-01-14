---
title: Executar scripts personalizados em VMs do Linux no Azure
description: Automatizar tarefas de configuração de VM do Linux usando a extensão de script personalizado v2
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: da7ade4b4724f8d155deb1c109587a311d03375c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931019"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Usar a extensão de script personalizado do Azure versão 2 com máquinas virtuais do Linux
A extensão de script personalizado versão 2 baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração após a implantação, instalação de software ou qualquer outra tarefa de configuração/gerenciamento. Você pode baixar scripts do armazenamento do Azure ou outro local de Internet acessível, ou pode fornecê-los ao tempo de execução de extensão. 

A extensão de script personalizado se integra com modelos de Azure Resource Manager. Você também pode executá-lo usando CLI do Azure, PowerShell ou a API REST de máquinas virtuais do Azure.

Este artigo fornece detalhes sobre como usar a extensão de script personalizado de CLI do Azure e como executar a extensão usando um modelo de Azure Resource Manager. Este artigo também fornece etapas de solução de problemas para sistemas Linux.


Há duas extensões de script personalizado do Linux:
* Versão 1-Microsoft. OSTCExtensions. às customscriptforlinux
* Versão 2-Microsoft. Azure. Extensions. CustomScript

Altere as implantações novas e existentes para usar a nova versão 2 em vez disso. A nova versão destina-se a ser uma substituição completa. Assim sendo, a migração é tão fácil como alterar o nome e a versão. Não precisa de alterar a configuração da extensão.


### <a name="operating-system"></a>Sistema Operativo

A extensão de script personalizado para Linux será executada na extensão do so de extensão com suporte, para obter mais informações, consulte este [artigo](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Local do script

Você pode usar a extensão para usar suas credenciais de armazenamento de BLOBs do Azure, para acessar o armazenamento de BLOBs do Azure. Como alternativa, o local do script pode ser qualquer lugar, desde que a VM possa ser roteada para esse ponto de extremidade, como GitHub, servidor de arquivos interno, etc.

### <a name="internet-connectivity"></a>Conectividade com a Internet
Se você precisar baixar um script externamente, como o GitHub ou o armazenamento do Azure, as portas de grupo de segurança de rede/firewall adicionais precisam ser abertas. Por exemplo, se o seu script estiver localizado no armazenamento do Azure, você poderá permitir o acesso usando as marcas do serviço NSG do Azure para [armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Se o seu script estiver em um servidor local, talvez ainda seja necessário abrir portas de grupo de segurança de rede/firewall adicionais.

### <a name="tips-and-tricks"></a>Sugestões e Truques
* A taxa de falhas mais elevada para esta extensão deve-se a erros de sintaxe no script. Teste a execução do script sem erros e coloque registos adicionais no script para facilitar a deteção da falha.
* Escreva scripts que sejam idempotentes. Assim, se forem executados mais de uma vez acidentalmente, não causam alterações do sistema.
* Verifique se os scripts não exigem entrada do usuário quando eles são executados.
* Há 90 minutos permitidos para o script ser executado; qualquer coisa resultará em um provisionamento com falha da extensão.
* Não coloque reinicializações dentro do script, isso causará problemas com outras extensões que estão sendo instaladas e após a reinicialização, a extensão não continuará após a reinicialização. 
* Se você tiver um script que causará uma reinicialização, instale aplicativos e execute scripts, etc. Você deve agendar a reinicialização usando um trabalho cron ou usando ferramentas como DSC, ou chefe, Puppet Extensions.
* A extensão só executará um script uma vez, se você quiser executar um script em cada inicialização, poderá usar a [imagem de inicialização de nuvem](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) e usar um módulo de [scripts por inicialização](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) . Como alternativa, você pode usar o script para criar uma unidade de serviço do sistema.
* Se você quiser agendar quando um script será executado, use a extensão para criar um trabalho cron. 
* Quando o script estiver em execução, verá apenas um estado de extensão "em transição" no portal ou na CLI do Azure. Se você quiser atualizações de status mais frequentes de um script em execução, será necessário criar sua própria solução.
* A extensão de script personalizado não dá suporte nativo a servidores proxy, no entanto, você pode usar uma ferramenta de transferência de arquivo que dá suporte a servidores proxy em seu script, como *ondulação*. 
* Lembre-se de locais de diretório não padrão dos quais seus scripts ou comandos podem depender, têm lógica para lidar com isso.
*  Ao implantar o script personalizado em instâncias de VMSS de produção, é recomendável implantar por meio do modelo JSON e armazenar sua conta de armazenamento de script onde você tem controle sobre o token SAS. 


## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica coisas como o local do script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo de Azure Resource Manager. 

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada apenas dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de configurações protegidas por extensões. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publicador | Microsoft.Compute.Extensions | string |
| tipo | CustomScript | string |
| typeHandlerVersion | 2.0 | int |
| fileuris (por exemplo,) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (por exemplo,) | python MyPythonScript.py \<my param1 > | string |
| . | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (por exemplo,) | false | boolean |
| timestamp (por exemplo) | 123456789 | inteiro de 32 bits |
| storageAccountName (por exemplo,) | examplestorageacct | string |
| storageAccountKey (por exemplo,) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Detalhes do valor da propriedade
* `apiVersion`: a apiVersion mais atualizada pode ser encontrada usando o [Gerenciador de recursos](https://resources.azure.com/) ou de CLI do Azure usando o seguinte comando `az provider list -o json`
* `skipDos2Unix`: (opcional, booliano) ignore a conversão dos2unix de URLs de arquivo baseadas em script ou script.
* `timestamp` (opcional, número inteiro de 32 bits) Use este campo somente para disparar uma nova execução do script alterando o valor desse campo.  Qualquer valor inteiro é aceitável; Ele deve ser diferente do valor anterior.
  * `commandToExecute`: (**necessário** se o script não estiver definido, Cadeia de caracteres) o script de ponto de entrada a ser executado. Use esse campo se o comando contiver segredos como senhas.
* `script`: (**necessário** se commandToExecute não estiver definido, Cadeia de caracteres) um script codificado em Base64 (e, opcionalmente, gzip'ed) executado por/bin/sh.
* `fileUris`: (opcional, matriz de cadeia de caracteres) as URLs para os arquivos a serem baixados.
* `storageAccountName`: (opcional, Cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os `fileUris` devem ser URLs para BLOBs do Azure.
* `storageAccountKey`: (opcional, Cadeia de caracteres) a chave de acesso da conta de armazenamento


Os valores a seguir podem ser definidos em configurações públicas ou protegidas, a extensão rejeitará qualquer configuração na qual os valores abaixo estejam definidos nas configurações pública e protegida.
* `commandToExecute`
* `script`
* `fileUris`

O uso de configurações públicas talvez seja útil para depuração, mas é altamente recomendável que você use as configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM em que o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida somente para o Azure e a VM. As configurações são salvas na VM conforme elas foram enviadas, ou seja, se as configurações foram criptografadas, elas são salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) em tempo de execução.

#### <a name="property-skipdos2unix"></a>Propriedade: skipDos2Unix

O valor padrão é false, o que significa que a conversão de dos2unix **é** executada.

A versão anterior do CustomScript, Microsoft. OSTCExtensions. às customscriptforlinux, converteria automaticamente os arquivos DOS em arquivos UNIX ao traduzir `\r\n` para `\n`. Essa tradução ainda existe e está ativada por padrão. Essa conversão é aplicada a todos os arquivos baixados de fileuris ou da configuração de script com base em qualquer um dos critérios a seguir.

* Se a extensão for uma das `.sh`, `.txt`, `.py`ou `.pl` ela será convertida. A configuração de script sempre corresponderá a esses critérios porque supõe-se que seja um script executado com/bin/sh e é salvo como script.sh na VM.
* Se o arquivo começar com `#!`.

A conversão dos2unix pode ser ignorada definindo skipDos2Unix como true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Propriedade: script

O CustomScript dá suporte à execução de um script definido pelo usuário. As configurações de script para combinar commandToExecute e fileuris em uma única configuração. Em vez de ter que configurar um arquivo para download do armazenamento do Azure ou do Microsoft GitHub, você pode simplesmente codificar o script como uma configuração. O script pode ser usado para substituir commandToExecute e fileuris.

O script **deve** ser codificado em base64.  O script pode **, opcionalmente,** ser gzip'ed. A configuração de script pode ser usada em configurações públicas ou protegidas. O tamanho máximo dos dados do parâmetro de script é 256 KB. Se o script exceder esse tamanho, ele não será executado.

Por exemplo, considerando o script a seguir salvo no arquivo/script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A configuração correta do script CustomScript seria construída por meio da saída do comando a seguir.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

O script pode, opcionalmente, ser gzip'ed para reduzir ainda mais o tamanho (na maioria dos casos). (CustomScript automaticamente detecta o uso da compactação Gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript usa o algoritmo a seguir para executar um script.

 1. Assert o comprimento do valor do script não excede 256 KB.
 1. Base64 decodificar o valor do script
 1. _tentativa_ de gunzip o valor decodificado em base64
 1. gravar o valor decodificado (e, opcionalmente, descompactado) em disco (/var/lib/waagent/Custom-script/#/script.sh)
 1. Execute o script usando _/bin/sh-c/var/lib/waagent/Custom-script/#/script.sh.


## <a name="template-deployment"></a>Implementação de modelos
Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo de Azure Resource Manager para executar a extensão de script personalizado durante uma implantação de modelo de Azure Resource Manager. Um modelo de exemplo que inclui a extensão de script personalizado pode ser encontrado aqui, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure
Quando você estiver usando CLI do Azure para executar a extensão de script personalizado, crie um arquivo de configuração ou arquivos. No mínimo, você deve ter ' commandToExecute '.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcionalmente, você pode especificar as configurações no comando como uma cadeia de caracteres formatada em JSON. Isso permite que a configuração seja especificada durante a execução e sem um arquivo de configuração separado.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos de CLI do Azure

#### <a name="public-configuration-with-script-file"></a>Configuração pública com arquivo de script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

CLI do Azure comando:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Resolução de problemas
Quando a extensão de script personalizado é executada, o script é criado ou baixado em um diretório semelhante ao exemplo a seguir. A saída do comando também é salva nesse diretório em arquivos `stdout` e `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

Para solucionar problemas, primeiro verifique o log do agente do Linux, verifique se a extensão foi executada, verifique:

```bash
/var/log/waagent.log 
```

Você deve procurar a execução da extensão; ela terá uma aparência semelhante a esta:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Alguns pontos a serem observados:
1. Habilitar é quando o comando começa a ser executado.
2. O download está relacionado ao download do pacote de extensão CustomScript do Azure, não aos arquivos de script especificados em fileuris.


A extensão de script do Azure produz um log, que pode ser encontrado aqui:

```bash
/var/log/azure/custom-script/handler.log
```

Você deve procurar a execução individual; ela terá uma aparência semelhante a:
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
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
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Passos seguintes
Para ver o código, os problemas e as versões atuais, consulte o [repositório personalizado-script-Extension-Linux](https://github.com/Azure/custom-script-extension-linux).

