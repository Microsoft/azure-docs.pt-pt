---
title: Executar extensão de script personalizado em VMs Linux em Azure
description: Automatizar as tarefas de configuração do Linux VM utilizando a extensão de script personalizada v2
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: 24d1992db5f1826045fdb47397e44dc2e2fbdaf9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962166"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Utilizar a Versão 2 da Extensão de Script Personalizado do Azure com as máquinas virtuais do Linux
A versão de extensão de scripts personalizada 2 descarrega e executa scripts em máquinas virtuais Azure. Esta extensão é útil para configuração pós-implantação, instalação de software ou qualquer outra tarefa de configuração/gestão. Você pode baixar scripts a partir de Azure Storage ou outro local de internet acessível, ou você pode fornecer-lhes para o tempo de execução da extensão. 

A extensão de script personalizado integra-se com modelos de Gestor de Recursos Azure. Também pode executá-lo utilizando Azure CLI, PowerShell ou a API das Máquinas Virtuais Azure.

Este artigo detalha como usar a extensão de script personalizada a partir de Azure CLI, e como executar a extensão usando um modelo de Gestor de Recursos Azure. Este artigo também fornece etapas de resolução de problemas para sistemas Linux.


Existem duas extensões de script personalizadas Linux:
* Versão 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Versão 2 - Microsoft.Azure.Extensions.CustomScript

Por favor, troque as novas e existentes implementações para utilizar a nova versão 2. A nova versão destina-se a ser uma substituição completa. Assim sendo, a migração é tão fácil como alterar o nome e a versão. Não precisa de alterar a configuração da extensão.


### <a name="operating-system"></a>Sistema Operativo

A extensão de script personalizada para o Linux será executada na extensão suportada os os's, para mais informações, consulte este [artigo](../linux/endorsed-distros.md).

### <a name="script-location"></a>Localização do script

Pode utilizar a extensão para utilizar as suas credenciais de armazenamento Azure Blob, para aceder ao armazenamento do Azure Blob. Em alternativa, a localização do script pode ser qualquer onde, desde que o VM possa ir até esse ponto final, como o GitHub, o servidor interno de ficheiros, etc.

### <a name="internet-connectivity"></a>Conectividade da Internet
Se precisar de descarregar um script externo, como GitHub ou Azure Storage, então devem ser abertas portas adicionais do Grupo de Segurança de Firewall/Rede. Por exemplo, se o seu script estiver localizado no Azure Storage, pode permitir o acesso usando tags de serviço Azure NSG para [armazenamento.](../../virtual-network/network-security-groups-overview.md#service-tags)

Se o seu script estiver num servidor local, poderá ainda necessitar de portas adicionais de firewall/Grupo de Segurança de Rede.

### <a name="tips-and-tricks"></a>Dicas e Truques
* A taxa de falhas mais elevada para esta extensão deve-se a erros de sintaxe no script. Teste a execução do script sem erros e coloque registos adicionais no script para facilitar a deteção da falha.
* Escreva scripts que sejam idempotentes. Assim, se forem executados mais de uma vez acidentalmente, não causam alterações do sistema.
* Certifique-se de que os scripts não requerem a entrada do utilizador quando são executados.
* Há 90 minutos permitidos para o script ser executado, qualquer coisa mais longa resultará numa disposição falhada da extensão.
* Não coloque reboots dentro do script, isto irá causar problemas com outras extensões que estão a ser instaladas, e após o reboot, a extensão não continuará após o reinício. 
* Não é aconselhável executar um script que irá causar uma paragem ou atualização do Agente VM. Isto pode deixar a extensão num estado de transição e levar a um tempo limite.
* Se tiver um script que irá causar um reboot, então instale aplicações e execute scripts, etc. Você deve agendar o reboot usando um trabalho cron, ou usando ferramentas como DSC, ou Chef, extensões de marionetas.
* A extensão só irá executar um script uma vez, se quiser executar um script em cada bota, então você pode usar [a imagem cloud-init](../linux/using-cloud-init.md) e usar um módulo [Scripts Per Boot.](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) Em alternativa, pode utilizar o script para criar uma unidade de serviço SystemD.
* Só é possível ter uma versão de uma extensão aplicada ao VM. Para executar um segundo script personalizado, você precisa remover a extensão de script personalizado e reaplicá-lo novamente com o script atualizado. 
* Se quiser agendar quando um script será executado, deve usar a extensão para criar um trabalho cron. 
* Quando o script estiver em execução, verá apenas um estado de extensão "em transição" no portal ou na CLI do Azure. Se quiser atualizações de estado mais frequentes de um script de execução, terá de criar a sua própria solução.
* A extensão de Script personalizado não suporta de forma nativa servidores proxy, no entanto pode utilizar uma ferramenta de transferência de ficheiros que suporta servidores proxy dentro do seu script, como *o Curl*. 
* Esteja ciente das localizações de diretório não padrão em que os seus scripts ou comandos podem confiar, tenha lógica para lidar com isso.
*  Ao implementar script personalizado para a produção de instâncias VMSS, é sugerido implementar através do modelo json e armazenar a sua conta de armazenamento de scripts onde você tem controlo sobre o token SAS. 


## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica coisas como a localização do script e o comando a ser executado. Pode armazenar esta configuração em ficheiros de configuração, especi-la na linha de comando ou especi-la num modelo de Gestor de Recursos Azure. 

Pode armazenar dados sensíveis numa configuração protegida, que é encriptada e apenas desencriptada dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos como uma palavra-passe.

Estes itens devem ser tratados como dados sensíveis e especificados na configuração de definição protegida das extensões. Os dados de definição protegidos por extensão Azure VM são encriptados e apenas desencriptados na máquina virtual alvo.

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
> propriedade managedIdentity **não deve** ser usado em conjunto com armazenamentoScome ou armazenamentoScolhostas

### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo | Tipo de Dados | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | data |
| publicador | Microsoft.Compute.Extensions | string |
| tipo | PersonalScript | string |
| typeHandlerVersion | 2.1 | int |
| fileUris (por exemplo) | `https://github.com/MyProject/Archive/MyPythonScript.py` | matriz |
| commandToExecute (por exemplo) | MyPythonScript.py pitão \<my-param1> | string |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix (por exemplo) | false | boolean |
| timetamp (por exemplo) | 123456789 | Inteiro de 32 bits |
| armazenamentoSame de número de armazenamento (por exemplo) | exemplostorageacct | string |
| armazenamentoSColho (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| entidade geridaId (por exemplo) | { } ou { "clientId": "31b403a-c364-4240-a7ff-d85fb6cd7232" } ou {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | objeto json |

### <a name="property-value-details"></a>Detalhes do valor da propriedade
* `apiVersion`: A apiversão mais atualizada pode ser encontrada usando [o Explorador de Recursos](https://resources.azure.com/) ou a partir de Azure CLI usando o seguinte comando `az provider list -o json`
* `skipDos2Unix`: (opcional, boolean) saltar a conversão dos2unix de URLs ou scripts baseados em scripts.
* `timestamp` (opcional, inteiro de 32 bits) utilize este campo apenas para desencadear uma reedição do script alterando o valor deste campo.  Qualquer valor inteiro é aceitável; só deve ser diferente do valor anterior.
* `commandToExecute`:**(requerido** se o script não estiver definido, string) o script do ponto de entrada para executar. Utilize este campo em vez disso se o seu comando contiver segredos como palavras-passe.
* `script`:**(requerido** se o comandoToExecute não for definido, string)um script de base64 codificado (e opcionalmente gzip'ed) executado por /bin/sh.
* `fileUris`: (opcional, matriz de cordas) os URLs para ficheiros a serem descarregados.
* `storageAccountName`: (opcional, cadeia) o nome da conta de armazenamento. Se especificar credenciais de armazenamento, todos `fileUris` devem ser URLs para Azure Blobs.
* `storageAccountKey`: (opcional, cadeia) a chave de acesso da conta de armazenamento
* `managedIdentity`: (opcional, objeto json) a [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para descarregar ficheiros
  * `clientId`: (opcional, string) o ID do cliente da identidade gerida
  * `objectId`: (opcional, cadeia) o ID do objeto da identidade gerida


Os seguintes valores podem ser definidos em configurações públicas ou protegidas, a extensão rejeitará qualquer configuração em que os valores abaixo estejam definidos em configurações públicas e protegidas.
* `commandToExecute`
* `script`
* `fileUris`

A utilização de configurações públicas pode ser útil para depurar, mas recomenda-se vivamente que utilize configurações protegidas.

As definições públicas são enviadas em texto claro para o VM onde o script será executado.  As definições protegidas são encriptadas utilizando uma chave conhecida apenas pelo Azure e pelo VM. As definições são guardadas para o VM tal como foram enviadas, ou seja, se as definições foram encriptadas, são guardadas encriptadas no VM. O certificado utilizado para desencriptar os valores encriptados é armazenado no VM e utilizado para desencriptar as definições (se necessário) no tempo de execução.

#### <a name="property-skipdos2unix"></a>Propriedade: skipDos2Unix

O valor predefinido é falso, o que significa que a conversão dos2unix **é** executada.

A versão anterior do CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, converteria automaticamente ficheiros DOS para ficheiros UNIX traduzindo `\r\n` para `\n` . Esta tradução ainda existe, e está por defeito. Esta conversão é aplicada a todos os ficheiros descarregados a partir de fileUris ou na definição de script com base em qualquer um dos seguintes critérios.

* Se a extensão for de `.sh` `.txt` , ou será `.py` `.pl` convertida. A definição do script corresponderá sempre a estes critérios porque se presume ser um script executado com /bin/sh, e é guardado à medida que script.sh no VM.
* Se o ficheiro começar com `#!` .

A conversão dos2unix pode ser ignorada definindo o skipDos2Unix para verdadeiro.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Propriedade: script

O CustomScript suporta a execução de um script definido pelo utilizador. As definições de script para combinar comandoToExecute e fileUris numa única definição. Em vez de ter de configurar um ficheiro para download a partir do armazenamento Azure ou do GitHub, pode simplesmente codificar o script como uma definição. O script pode ser usado para substituir o comandoToExecute e fileUris.

O guião **deve** estar codificado.  O script pode **opcionalmente** ser gzip'ed. A definição do script pode ser usada em configurações públicas ou protegidas. O tamanho máximo dos dados do parâmetro do script é de 256 KB. Se o script exceder este tamanho, não será executado.

Por exemplo, dado o seguinte script guardado no ficheiro /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A definição de script personalizado correta seria construída tomando a saída do seguinte comando.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

O script pode opcionalmente ser gzip'ed para reduzir ainda mais o tamanho (na maioria dos casos). (CustomScript deteta automaticamente a utilização da compressão gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

O CustomScript utiliza o seguinte algoritmo para executar um script.

 1. afirmar que o comprimento do valor do script não excede 256 KB.
 1. base64 descodificar o valor do script
 1. _tentativa_ de gunzip o valor descodificado base64
 1. escrever o valor descodificado (e opcionalmente descomprimido) para o disco (/var/lib/waagent/custom-script/#/script.sh)
 1. execute o script utilizando _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.

####  <a name="property-managedidentity"></a>Propriedade: managedIdentity
> [!NOTE]
> Esta propriedade **deve** ser especificada apenas em configurações protegidas.

CustomScript (versão 2.1 em diante) suporta [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para descarregar ficheiros(s) de URLs fornecidos na definição "fileUris". Permite que o CustomScript aceda a blobs ou contentores privados do Azure Storage sem que o utilizador tenha de passar segredos como fichas SAS ou chaves de conta de armazenamento.

Para utilizar esta funcionalidade, o utilizador deve adicionar uma identidade atribuída ao [sistema](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) ou [atribuída](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) ao VM ou VMSS onde se espera que o CustomScript seja executado, e conceder o acesso de identidade gerido ao recipiente ou bolha de [armazenamento Azure](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Para utilizar a identidade atribuída ao sistema no VM/VMSS alvo, dedije o campo "managedidentity" a um objeto json vazio. 

> Exemplo:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Para utilizar a identidade atribuída ao utilizador no VM/VMSS alvo, configuure o campo "managedidentity" com o ID do cliente ou o ID do objeto da identidade gerida.

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
> propriedade managedIdentity **não deve** ser usado em conjunto com armazenamentoScome ou armazenamentoScolhostas

## <a name="template-deployment"></a>Implementação de modelos
As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão de script personalizada durante uma implementação do modelo do Azure Resource Manager. Um modelo de amostra que inclui a extensão de script personalizado pode ser encontrado aqui, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
>Estes nomes de propriedade são sensíveis a casos. Para evitar problemas de implantação, utilize os nomes como mostrado aqui.

## <a name="azure-cli"></a>CLI do Azure
Quando estiver a utilizar o Azure CLI para executar a Extensão de Script Personalizado, crie um ficheiro de configuração ou ficheiros. No mínimo, deve ter 'comandoToExecute'.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcionalmente, pode especificar as definições no comando como uma cadeia formatada JSON. Isto permite que a configuração seja especificada durante a execução e sem um ficheiro de configuração separado.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos da CLI do Azure

#### <a name="public-configuration-with-script-file"></a>Configuração pública com arquivo de script

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

#### <a name="public-configuration-with-no-script-file"></a>Configuração pública sem ficheiro de script

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

Utiliza um ficheiro de configuração pública para especificar o ficheiro de script URI. Utilize um ficheiro de configuração protegido para especificar o comando a ser executado.

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
Quando a Extensão de Script Personalizado é executado, o script é criado ou descarregado num diretório semelhante ao exemplo seguinte. A saída do comando também é guardada neste diretório `stdout` e `stderr` ficheiros.

```bash
/var/lib/waagent/custom-script/download/0/
```

Para resolver problemas, verifique primeiro o Registo do Agente Linux, certifique-se de que a extensão foi escorrê-lo, verifique:

```bash
/var/log/waagent.log 
```

Devias procurar a execução da extensão, vai parecer algo como:

```output
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
2. O download diz respeito ao download do pacote de extensão CustomScript do Azure, e não aos ficheiros de script especificados nos ficheirosUris.


A Extensão do Guião Azure produz um registo, que pode encontrar aqui:

```bash
/var/log/azure/custom-script/handler.log
```

Devias procurar a execução individual, vai parecer algo como:

```output
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
* O comando Enable a partir é este log
* As definições passaram para a extensão
* O ficheiro de descarregamento de extensão e o resultado disso.
* O comando está a ser executado e o resultado.

Também pode recuperar o estado de execução da Extensão de Script Personalizado, incluindo os argumentos reais aprovados como o através do `commandToExecute` Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída parece o seguinte texto:

```output
[
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/customscript",
    "resourceGroup": "rgname",
    "settings": {
      "commandToExecute": "sh script.sh > ",
      "fileUris": [
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh",
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh"
      ]
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "2.0",
    "virtualMachineExtensionType": "CustomScript"
  },
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/OmsAgentForLinux",
    "instanceView": null,
    "location": "eastus",
    "name": "OmsAgentForLinux",
    "protectedSettings": null,
    "provisioningState": "Succeeded",
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "resourceGroup": "rgname",
    "settings": {
      "workspaceId": "workspaceid"
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "1.0",
    "virtualMachineExtensionType": "OmsAgentForLinux"
  }
]
```

## <a name="next-steps"></a>Passos seguintes
Para ver o código, problemas e versões atuais, consulte o repo de [extensão-linux de script personalizado.](https://github.com/Azure/custom-script-extension-linux)