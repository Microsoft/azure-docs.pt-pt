---
title: Executar scripts personalizados em VMs do Linux no Azure | Documentos da Microsoft
description: Automatizar tarefas de configuração de VM do Linux ao utilizar o v2 de extensão de Script personalizado
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: roiyz
ms.openlocfilehash: 19637a1fe49550d0ed7aea7e3a596f1f77f5984b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082046"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Utilizar a versão 2 do Azure Custom Script extensão com máquinas virtuais do Linux
A versão 2 do Custom Script extensão transfere e executa scripts em máquinas virtuais do Azure. Esta extensão é útil para configuração de pós-implementação, instalação de software ou qualquer outra tarefa de gestão de configuração /. Pode baixar scripts a partir do armazenamento do Azure ou noutra localização acessível da internet ou pode fornecê-los para o tempo de execução de extensão. 

A extensão de Script personalizado integra-se com modelos Azure Resource Manager. Também pode executá-lo ao utilizar a CLI do Azure, PowerShell ou a API de REST de máquinas virtuais do Azure.

Este artigo fornece detalhes sobre como utilizar a extensão de Script personalizado da CLI do Azure e como executar a extensão com um modelo Azure Resource Manager. Este artigo também fornece passos de resolução de problemas para sistemas Linux.


Existem duas extensões de Script de personalizada de Linux:
* Versão 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Versão 2 - Microsoft.Azure.Extensions.CustomScript

Mude as implementações novas e existentes para utilizar a nova versão 2 em vez disso. A nova versão se destina a ser uma substituição completa. Portanto, a migração é tão fácil como alterar o nome e versão, não é necessário alterar a configuração de extensão.


### <a name="operating-system"></a>Sistema Operativo

A extensão de Script personalizado para Linux será executado na extensão de extensão suportada do sistema operacional, para obter mais informações, veja isso [artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Localização do script

Pode utilizar a extensão para utilizar as suas credenciais do armazenamento de Blobs do Azure, para aceder ao armazenamento de Blobs do Azure. Em alternativa, a localização do script pode ser qualquer where, desde que a VM pode encaminhar para esse ponto de extremidade, como o GitHub, o servidor de ficheiros interna etc.

### <a name="internet-connectivity"></a>Conectividade com a Internet
Se precisar de transferir um script externamente, como o GitHub ou o armazenamento do Azure, em seguida, rede/firewall adicional grupo de segurança portas tem de ser aberto. Por exemplo, se o script está localizado no armazenamento do Azure, pode permitir acesso utilizando as etiquetas de serviço do Azure NSG para [armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Se o script num servidor local, então ainda pode ser necessário adicionais firewall/redes segurança portas de grupo tem de ser aberto.

### <a name="tips-and-tricks"></a>Sugestões e Truques
* A mais alta taxa de falhas para esta extensão é devido a erros de sintaxe no script, teste o script é executado sem erros e também coloca no registo para o script para torná-lo mais fácil encontrar onde falha adicional.
* Escrever scripts que são idempotentes, para que se obtenha execute novamente mais do que uma vez acidentalmente, não irá causar as alterações do sistema.
* Certifique-se de que os scripts não necessitam de entrada do usuário quando eles são executados.
* Há 90 minutos permitidos para o script a executar, nada mais tempo irá resultar numa falha ao aprovisionar da extensão.
* Não coloque reinicializações dentro do script, isso causará problemas com outras extensões que estão a ser instalados e, após o reinício, a extensão não será continuada após o reinício. 
* Se tiver um script que fará com que um reinício, em seguida, instalar aplicativos e executar scripts, etc. Deve agendar o reinício de utilizar uma tarefa Cron ou utilizar ferramentas como o DSC ou Chef, extensões de Puppet.
* A extensão apenas executarão um script de uma vez, se quiser executar um script em cada inicialização, em seguida, pode utilizar [imagem na cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) e utilizar um [Scripts por inicialização](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) módulo. Em alternativa, pode utilizar o script para criar uma unidade de serviço Systemd.
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
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
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
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Compute.Extensions | string |
| tipo | CustomScript | string |
| typeHandlerVersion | 2.0 | int |
| fileUris (por exemplo) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute (por exemplo) | python MyPythonScript.py <my-param1> | string |
| Script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (por exemplo) | false | boolean |
| Timestamp (por exemplo) | 123456789 | número inteiro de 32 bits |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Detalhes de valor de propriedade
* `skipDos2Unix`: (opcional, booleano) ignorar conversão dos2unix de URLs de ficheiros baseado no script ou script.
* `timestamp` Este campo só para acionar uma volte a executar o script alterando o valor deste campo utilização de (inteiro opcional, 32 bits).  Qualquer valor inteiro é aceitável; só tem de ser diferente do valor anterior.
  * `commandToExecute`: (**necessário** se o script não definido, cadeia de caracteres) o script do ponto de entrada para executar. Utilize este campo em vez disso, se o comando contém segredos como palavras-passe.
* `script`: (**necessário** se commandToExecute não definido, cadeia de caracteres) um codificada em base64 (e, opcionalmente, gzip'ed) script foi executado pelo /bin/sh.
* `fileUris`: (opcional, matriz de cadeia) as URLs para o ficheiro (s) a serem baixados.
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se especificar credenciais de armazenamento, todas as `fileUris` tem de ser URLs para os Blobs do Azure.
* `storageAccountKey`: (opcional, cadeia) a chave de acesso da conta de armazenamento


Os seguintes valores podem ser definidos nas definições de públicas ou protegidas, a extensão irão rejeitar qualquer configuração onde os valores abaixo são definidos nas definições de públicas e protegidas.
* `commandToExecute`
* `script`
* `fileUris`

Utilizar as definições de públicas pode ser útil para depuração, mas ele é altamente recomendável que utilize definições protegidas.

Definições de públicas são enviadas em texto não criptografado para a VM em que o script será executado.  Definições protegidas são encriptadas com uma chave só conhecida o Azure e a VM. As definições são guardadas para a VM à medida que eles sejam encaminhados, ou seja, se as definições foram encriptadas que são guardados encriptados na VM. O certificado utilizado para desencriptar os valores criptografados é armazenado na VM e utilizado para desencriptar as definições (se necessário) em tempo de execução.

#### <a name="property-skipdos2unix"></a>Propriedade: skipDos2Unix

O valor predefinido é false, o que significa que a conversão de dos2unix **é** executado.

A versão anterior do CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, seria converter automaticamente ficheiros aos arquivos do UNIX ao traduzir `\r\n` para `\n`. Essa conversão ainda existe e está ativada por predefinição. Esta conversão é aplicada a todos os ficheiros transferidos a partir fileUris ou a definição de script com base em qualquer um dos seguintes critérios.

* Se a extensão é um dos `.sh`, `.txt`, `.py`, ou `.pl` será convertido. A definição de script sempre corresponderá este critério porque é considerado como um script executado com /bin/sh e é guardada como script.sh na VM.
* Se o ficheiro for iniciado com `#!`.

A conversão de dos2unix pode ser ignorada definindo o skipDos2Unix como true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Propriedade: script

CustomScript suporta a execução de um script definido pelo utilizador. As definições de script para combinar commandToExecute e fileUris numa única definição. Em vez de precisar configurar um ficheiro para transferência a partir do armazenamento do Azure ou a essência do GitHub, pode simplesmente de codificar o script como uma definição. Script pode ser usado para commandToExecute substituído e fileUris.

O script **tem** ser codificados em base64.  O script pode **opcionalmente** ser gzip'ed. A definição de script pode ser utilizada nas definições de públicas ou protegidas. O tamanho máximo de dados do parâmetro de script é 256 KB. Se o script exceder este tamanho que não será executado.

Por exemplo, tendo em conta o seguinte script guardado /script.sh/ o ficheiro.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A definição de script CustomScript correta poderia ser construída, efetuando a saída do comando seguinte.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

O script, opcionalmente, pode ser gzip'ed para reduzir ainda mais o tamanho (na maioria dos casos). (CustomScript Deteta automaticamente a utilização da compressão de gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript usa o seguinte algoritmo para executar um script.

 1. o comprimento do valor do script não exceda 256 KB de declaração.
 1. Base64 descodificar o valor do script
 1. _tentar_ para gunzip a base64 descodificado valor
 1. escrever o valor de descodificada (e, opcionalmente, descomprimido) no disco (/ var/lib/waagent/custom-script/#/script.sh)
 1. Execute o script usando /var/lib/waagent/custom-script/#/script.sh _ / bin/sh - c.


## <a name="template-deployment"></a>Implementação de modelos
Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo do Azure Resource Manager para executar a extensão de Script personalizado durante uma implementação de modelo do Azure Resource Manager. Pode encontrar aqui, um modelo de exemplo que inclui a extensão de Script personalizado [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
>Estes nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implementação, utilize os nomes, conforme mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure
Quando estiver a utilizar o CLI do Azure para executar a extensão de Script personalizado, crie um ficheiro de configuração ou ficheiros. No mínimo, tem de ter 'commandToExecute'.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcionalmente, pode especificar as definições no comando como uma cadeia de caracteres de formato JSON. Isso permite que a configuração seja especificado durante a execução e sem um ficheiro de configuração diferente.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos da CLI do Azure

#### <a name="public-configuration-with-script-file"></a>Configuração pública com o ficheiro de script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Resolução de problemas
Quando a extensão de Script personalizado é executado, o script é criado ou transferido para um diretório que é semelhante ao seguinte exemplo. A saída do comando também é guardada para este diretório na `stdout` e `stderr` ficheiros.

```bash
/var/lib/waagent/custom-script/download/0/
```

Para resolver, primeiro verifique o registo do agente do Linux, certifique-se de que a extensão for executada, verifique:

```bash
/var/log/waagent.log 
```

Deve procurar a execução de extensão, ele será algo parecido com:
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
1. Ativar é quando o comando começa a ser executado.
2. Download está relacionado com o download do pacote de extensão CustomScript do Azure, não os arquivos de script especificado no fileUris.


A extensão de Script do Azure produz um registo, que pode ser encontrado aqui:

```bash
/var/log/azure/custom-script/handler.log
```

Deve procurar a execução individual, ele será algo parecido com:
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
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Passos Seguintes
Para ver o código, problemas atuais e versões, veja [repositório personalizado-script-extensão-linux](https://github.com/Azure/custom-script-extension-linux).

