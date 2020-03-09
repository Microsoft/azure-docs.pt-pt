---
title: Executar scripts personalizados em VMs Linux em Azure
description: Automatizar tarefas de configuração VM Linux utilizando a extensão de script personalizada v2
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
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
ms.openlocfilehash: 2190bfd1a260d7b866fedc1f7c699faef2431a93
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380981"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Utilize a versão 2 de extensão personalizada do script azure com máquinas virtuais Linux
A versão 2 de extensão de script personalizado descarrega e executa scripts em máquinas virtuais Azure. Esta extensão é útil para configuração pós-implementação, instalação de software ou qualquer outra tarefa de configuração/gestão. Pode descarregar scripts a partir do Azure Storage ou de outro local acessível à Internet, ou pode fornecê-los ao tempo de execução da extensão. 

A extensão personalizada do script integra-se com os modelos do Gestor de Recursos Azure. Também pode executá-lo utilizando o Azure CLI, PowerShell ou o Azure Virtual Machines REST API.

Este artigo detalha como usar a extensão personalizada do script do Azure CLI e como executar a extensão utilizando um modelo de Gestor de Recursos Azure. Este artigo também fornece passos de resolução de problemas para sistemas Linux.


Existem duas extensões de script personalizadas Linux:
* Versão 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Versão 2 - Microsoft.Azure.Extensions.CustomScript

Por favor, troque as implementações novas e existentes para utilizar a nova versão 2. A nova versão destina-se a ser uma substituição completa. Assim sendo, a migração é tão fácil como alterar o nome e a versão. Não precisa de alterar a configuração da extensão.


### <a name="operating-system"></a>Sistema Operativo

A extensão personalizada do script para linux será executada na extensão suportada os os's, para mais informações, ver este [artigo](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Localização do script

Pode utilizar a extensão para utilizar as suas credenciais de armazenamento Azure Blob, para aceder ao armazenamento Azure Blob. Em alternativa, a localização do script pode ser qualquer um onde, desde que o VM possa encaminhar-se para esse ponto final, como gitHub, servidor de ficheiros interno, etc.

### <a name="internet-connectivity"></a>Conectividade da Internet
Se necessitar de descarregar um script externamente, como gitHub ou Azure Storage, então as portas adicionais do Grupo de Segurança de Firewall/Network precisam de ser abertas. Por exemplo, se o seu script estiver localizado no Armazenamento Azure, pode permitir o acesso utilizando etiquetas de serviço Azure NSG para [armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Se o seu script estiver num servidor local, poderá ainda necessitar de portas adicionais de firewall/Network Security Group.

### <a name="tips-and-tricks"></a>Dicas e Truques
* A taxa de falhas mais elevada para esta extensão deve-se a erros de sintaxe no script. Teste a execução do script sem erros e coloque registos adicionais no script para facilitar a deteção da falha.
* Escreva scripts que sejam idempotentes. Assim, se forem executados mais de uma vez acidentalmente, não causam alterações do sistema.
* Certifique-se de que as scripts não requerem entrada do utilizador quando são executadas.
* Há 90 minutos permitidos para o script funcionar, qualquer coisa mais longa resultará numa disposição falhada da extensão.
* Não coloque reboots dentro do script, isto causará problemas com outras extensões que estão a ser instaladas, e após o reinício, a extensão não continuará após o reinício. 
* Se tiver um script que provoque um reboot, instale aplicações e execute scripts, etc. Você deve agendar o reboot usando um trabalho cron, ou usando ferramentas como DSC, ou Chef, extensões de marionetas.
* A extensão só executará um script uma vez, se quiser executar um script em cada bota, então pode usar a imagem de [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) e usar um módulo [Scripts Per Boot.](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) Em alternativa, pode utilizar o script para criar uma unidade de serviço SystemD.
* Se quiser marcar quando um script será executado, deve usar a extensão para criar um trabalho cron. 
* Quando o script estiver em execução, verá apenas um estado de extensão "em transição" no portal ou na CLI do Azure. Se quiser atualizações de estado mais frequentes de um script em execução, terá de criar a sua própria solução.
* A extensão do Script personalizado não suporta de forma nativa servidores proxy, no entanto pode utilizar uma ferramenta de transferência de ficheiros que suporta servidores proxy dentro do seu script, como *curl*. 
* Esteja ciente de localizações de diretório sem incumprimento em que os seus scripts ou comandos podem confiar, tem lógica para lidar com isso.
*  Ao implementar script personalizado para produzir casos VMSS, é sugerido que implemente através do modelo json e guarde a sua conta de armazenamento de script onde tem controlo sobre o token SAS. 


## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica coisas como a localização do script e o comando a ser executado. Pode armazenar esta configuração em ficheiros de configuração, especificá-la na linha de comando ou especificá-la num modelo de Gestor de Recursos Azure. 

Pode armazenar dados sensíveis numa configuração protegida, encriptada e apenas desencriptada dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos como uma palavra-passe.

Estes itens devem ser tratados como dados sensíveis e especificados na configuração de definição protegida das extensões. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino.

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
    "typeHandlerVersion": "2.1",
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
       "fileUris": ["https://.."],
        "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> propriedade de identidade gerida **não deve** ser usada em conjunto com propriedades storageAccountName ou storageAccountKey

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publicador | Microsoft.Compute.Extensions | string |
| tipo | CustomScript | string |
| typeHandlerVersion | 2.1 | int |
| fileUris (por exemplo) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| comandoToExecutar (por exemplo) | python MyPythonScript.py \<my-param1> | string |
| . | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (por exemplo) | false | boolean |
| timestamp (por exemplo) | 123456789 | Inteiro de 32 bits |
| armazenamentoAccountName (por exemplo) | exemplostorageacct | string |
| armazenagemAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| identidade gerida (por exemplo) | { ou { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } ou { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json objeto |

### <a name="property-value-details"></a>Detalhes do valor da propriedade
* `apiVersion`: A apiVersão mais atualizada pode ser encontrada utilizando o [Resource Explorer](https://resources.azure.com/) ou a partir do Azure CLI utilizando o comando seguinte `az provider list -o json`
* `skipDos2Unix`: (opcional, boolean) saltar conversão dos2unix de URLs de ficheiro seletiva ou script.
* `timestamp` (opcional, inteiro de 32 bits) usam este campo apenas para desencadear uma reexecução do script alterando o valor deste campo.  Qualquer valor inteiro é aceitável; só deve ser diferente do valor anterior.
  * `commandToExecute`: **(necessário** se o script não estiver definido, string) o guião do ponto de entrada a executar. Utilize este campo se o seu comando contiver segredos como senhas.
* `script`: (**necessário** se o comandoExecutar não definido, corda)um script de base64 codificado (e opcionalmente gzip'ed) executado por /bin/sh.
* `fileUris`: (opcional, string array) os URLs para ficheiros ou ficheiros a serem descarregados.
* `storageAccountName`: (opcional, corda) o nome da conta de armazenamento. Se especificar credenciais de armazenamento, todos os `fileUris` devem ser URLs para Blobs Azure.
* `storageAccountKey`: (opcional, corda) a chave de acesso da conta de armazenamento
* `managedIdentity`: (opcional, objeto json) a [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para o descarregamento de ficheiros(s)
  * `clientId`: (opcional, corda) a identificação do cliente da identidade gerida
  * `objectId`: (opcional, corda) a identificação do objeto da identidade gerida


Os seguintes valores podem ser definidos em configurações públicas ou protegidas, a extensão rejeitará qualquer configuração em que os valores abaixo sejam definidos em configurações públicas e protegidas.
* `commandToExecute`
* `script`
* `fileUris`

A utilização de configurações públicas talvez seja útil para depuração, mas recomenda-se vivamente que utilize configurações protegidas.

As configurações públicas são enviadas em texto claro para o VM onde o script será executado.  As definições protegidas são encriptadas utilizando uma chave conhecida apenas pelo Azure e pelo VM. As definições são guardadas para o VM à medida que foram enviadas, ou é, se as definições foram encriptadas, são guardadas encriptadas no VM. O certificado utilizado para desencriptar os valores encriptados é armazenado no VM e utilizado para desencriptar as definições (se necessário) no prazo de execução.

#### <a name="property-skipdos2unix"></a>Propriedade: skipDos2Unix

O valor predefinido é falso, o que significa que a conversão dosdos2unix **é** executada.

A versão anterior do CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, converteria automaticamente ficheiros DOS para ficheiros UNIX traduzindo `\r\n` para `\n`. Esta tradução ainda existe, e está por defeito. Esta conversão é aplicada a todos os ficheiros descarregados a partir de fileUris ou à definição de script com base em qualquer um dos seguintes critérios.

* Se a extensão for uma das `.sh`, `.txt`, `.py`ou `.pl` será convertida. A definição do script corresponderá sempre a estes critérios porque se presume ser um script executado com /bin/sh, e é guardado como script.sh no VM.
* Se o ficheiro começar com `#!`.

A conversão dosdos2unix pode ser ignorada definindo o skipDos2Unix como verdadeiro.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Propriedade: script

CustomScript suporta a execução de um script definido pelo utilizador. As definições do script para combinar comandoSToExecute e fileUris numa única definição. Em vez de ter de configurar um ficheiro para download a partir do armazenamento Do Azure ou gitHub gist, pode simplesmente codificar o script como uma definição. O script pode ser usado para substituir o comandoToExecute e fileUris.

O guião **deve** ser codificado na base64.  O guião pode **opcionalmente** ser gzip'ed. A definição do script pode ser utilizada em configurações públicas ou protegidas. O tamanho máximo dos dados do parâmetro do script é de 256 KB. Se o guião exceder este tamanho, não será executado.

Por exemplo, dado o seguinte script guardado no ficheiro /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A definição correta do script CustomScript seria construída tomando a saída do seguinte comando.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

O script pode opcionalmente ser gzip'ed para reduzir ainda mais o tamanho (na maioria dos casos). (CustomScript deteta automaticamente a utilização de compressão gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript usa o seguinte algoritmo para executar um script.

 1. afirmar que o comprimento do valor do guião não excede 256 KB.
 1. base64 descodificar o valor do script
 1. _tentar_ arçar o valor descodificado base64
 1. escrever o valor descodificado (e opcionalmente descomprimido) para o disco (/var/lib/waagent/custom-script/#/script.sh)
 1. executar o script usando _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.

####  <a name="property-managedidentity"></a>Propriedade: identidade gerida

O CustomScript (versão 2.1) suporta a [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para descarregar ficheiros(s) de URLs fornecidos na definição "fileUris". Permite ao CustomScript aceder a blobs ou contentores privados do Azure Storage sem que o utilizador tenha de passar segredos como fichas SAS ou chaves de conta de armazenamento.

Para utilizar esta funcionalidade, o utilizador deve adicionar uma identidade atribuída ao [sistema](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-system-assigned-identity) ou atribuída ao [VM](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-user-assigned-identity) ou VMSS onde se espera que o CustomScript seja executado, e conceder o acesso de [identidade gerido ao recipiente ou blob](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access)do Armazenamento Azure .

Para utilizar a identidade atribuída ao sistema no alvo VM/VMSS, detete o campo "managedidentity" para um objeto json vazio. 

> Exemplo:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Para utilizar a identidade atribuída ao utilizador no alvo VM/VMSS, configure o campo "managedidentity" com o ID do cliente ou o ID do objeto da identidade gerida.

> Exemplos:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> propriedade de identidade gerida **não deve** ser usada em conjunto com propriedades storageAccountName ou storageAccountKey

## <a name="template-deployment"></a>Implementação de modelos
Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão do script personalizado durante uma implementação do modelo do Gestor de Recursos Azure. Um modelo de amostra que inclui a extensão do script personalizado pode ser encontrado aqui, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
    "typeHandlerVersion": "2.1",
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
>Estes nomes de propriedade são sensíveis a casos. Para evitar problemas de implantação, use os nomes como mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure
Quando estiver a utilizar o Azure CLI para executar a extensão do script personalizado, crie um ficheiro de configuração ou ficheiros. No mínimo, deve ter 'commandToExecute'.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcionalmente, pode especificar as definições no comando como uma cadeia formada JSON. Isto permite que a configuração seja especificada durante a execução e sem um ficheiro de configuração separado.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos da CLI do Azure

#### <a name="public-configuration-with-script-file"></a>Configuração pública com ficheiro script

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Comando Azure CLI:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Resolução de problemas
Quando a extensão do script personalizado é executado, o script é criado ou descarregado para um diretório semelhante ao seguinte exemplo. A saída de comando também é guardada neste diretório em ficheiros `stdout` e `stderr`.

```bash
/var/lib/waagent/custom-script/download/0/
```

Para resolver problemas, verifique primeiro o Registo do Agente Linux, certifique-se de que a extensão foi decorreu, verifique:

```bash
/var/log/waagent.log 
```

Devias procurar a execução da extensão, vai parecer algo como:
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
Alguns pontos a notar:
1. Ativar é quando o comando começa a funcionar.
2. O download diz respeito ao download do pacote de extensão CustomScript do Azure, e não aos ficheiros de script especificados no fileUris.


A extensão do script azure produz um registo, que pode encontrar aqui:

```bash
/var/log/azure/custom-script/handler.log
```

Devias procurar a execução individual, vai parecer algo como:
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
* O arranque do comando Enable é este registo
* As definições passaram para a extensão
* O ficheiro de descarregamento de extensão e o resultado disso.
* O comando está a ser executado e o resultado.

Também pode recuperar o estado de execução da extensão do script personalizado utilizando o Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída parece com o seguinte texto:

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
Para ver o código, questões e versões atuais, consulte o [script-script-extension-linux repo](https://github.com/Azure/custom-script-extension-linux)personalizado .

