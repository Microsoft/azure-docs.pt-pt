---
title: Azure Compute - Extensão de diagnóstico Linux 3.0
description: Como configurar a extensão de diagnóstico Azure Linux (LAD) 3.0 para recolher métricas e eventos de registo de VMs Linux que estão em execução em Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.openlocfilehash: fe03bbfb33f3637eecc4e68f24846c929dad5fa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479258"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Utilize a extensão de diagnóstico Linux 3.0 para monitorizar métricas e registos

Este documento descreve a versão 3.0 e a mais recente da extensão de diagnóstico Linux (LAD).

> [!IMPORTANT]
> Para obter informações sobre a versão 2.3 e anteriores, consulte [monitorar os dados de desempenho e diagnóstico de um Linux VM](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Introdução

A extensão de diagnóstico Linux ajuda um utilizador a monitorizar a saúde de um Linux VM que funciona no Microsoft Azure. Tem as seguintes capacidades:

* Recolhe métricas de desempenho do sistema a partir do VM e armazena-as numa tabela específica numa conta de armazenamento designada.
* Recupera eventos de registo do syslog e armazena-os numa tabela específica na conta de armazenamento designada.
* Permite que os utilizadores personalizem as métricas de dados que são recolhidas e carregadas.
* Permite aos utilizadores personalizar as instalações do syslog e os níveis de gravidade dos eventos que são recolhidos e carregados.
* Permite que os utilizadores carreguem ficheiros de registo especificados para uma tabela de armazenamento designada.
* Suporta o envio de métricas e eventos de registo para os pontos finais arbitrários do Azure Event Hubs e blobs com formato JSON na conta de armazenamento designada.

Esta extensão funciona com ambos os modelos de implantação Azure.

## <a name="install-the-extension-on-a-vm"></a>Instale a extensão num VM

Pode ativar a extensão utilizando cmdlets Azure PowerShell, scripts Azure CLI, modelos de Monitor de Recursos Azure (modelos ARM) ou o portal Azure. Para obter mais informações, consulte [as funcionalidades extensões](features-linux.md).

>[!NOTE]
>Alguns componentes da extensão LAD VM também são enviados na [extensão Log Analytics VM](./oms-linux.md). Devido a esta arquitetura, os conflitos podem surgir se ambas as extensões forem instantâneas no mesmo modelo ARM. 
>
>Para evitar conflitos de tempo de instalação, utilize a [ `dependsOn` diretiva](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) para garantir que as extensões são instaladas sequencialmente. As extensões podem ser instaladas em qualquer ordem.

Estas instruções de instalação e uma [configuração de amostra transferível](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) para configurar o LAD 3.0 para:

* Capture e guarde as mesmas métricas que no LAD 2.3.
* Capture um conjunto útil de métricas do sistema de ficheiros. Esta funcionalidade é nova em LAD 3.0.
* Capture a coleção de syslog padrão que o LAD 2.3 ativou.
* Ativar a experiência do portal Azure para o charting e alerta nas métricas VM.

A configuração transferível é apenas um exemplo. Modifique-o de acordo com as suas necessidades.

### <a name="supported-linux-distributions"></a>Distribuição apoiada do Linux

A LAD suporta as seguintes distribuições e versões. A lista de distribuições e versões aplica-se apenas às imagens do fornecedor Linux, apoiadas pelo Azure. A extensão geralmente não suporta imagens BYOL e BYOS de terceiros, como aparelhos.

Uma distribuição que lista apenas versões importantes, como Debian 7, também é suportada para todas as versões menores. Se for especificada uma versão menor, apenas essa versão é suportada. Se um sinal de mais (+) for anexado, versões menores iguais ou posteriores à versão especificada são suportadas.

Distribuições e versões suportadas:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Pré-requisitos

* **Azure Linux Agent versão 2.2.0 ou posterior**. A maioria das imagens da galeria Azure VM Linux incluem a versão 2.2.7 ou mais tarde. Corra `/usr/sbin/waagent -version` para confirmar a versão instalada no VM. Se o VM estiver a executar uma versão mais antiga, [atualize o agente convidado](./update-linux-agent.md).
* O **Azure CLI.** Se necessário, instale o ambiente [Azure CLI](/cli/azure/install-azure-cli) na sua máquina.
* O **comando wget**. Se ainda não o tem, `sudo apt-get install wget` corra.
* Uma **assinatura Azure** existente. 
* Uma **conta de armazenamento de finalidade geral** existente para armazenar os dados. As contas de armazenamento para fins gerais devem suportar o armazenamento de mesas. Uma conta de armazenamento de bolhas não funciona.
* **Python 2**.

### <a name="python-requirement"></a>Requisito de Python

A extensão de diagnóstico Linux requer Python 2. Se a sua máquina virtual utilizar uma distribuição que não inclua python 2 por defeito, tem de a instalar. Os seguintes comandos de amostras instalam Python 2 em várias distribuições:   

- Chapéu Vermelho, CentOS, Oráculo: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

O `python2` ficheiro executável deve ser aliasado à *pitão.* Aqui está um método para definir este pseudónimo:

1. Executar o seguinte comando para remover quaisquer pseudónimos existentes.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Executar o seguinte comando para criar o pseudónimo.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Instalação de amostras

A configuração da amostra descarregada nos seguintes exemplos recolhe um conjunto de dados padrão e envia-os para o armazenamento de mesas. O URL para a configuração da amostra e o seu conteúdo podem mudar. 

Na maioria dos casos, deve descarregar uma cópia do ficheiro JSON de definições do portal e personalizá-la para as suas necessidades. Em seguida, use modelos ou a sua própria automatização para usar uma versão personalizada do ficheiro de configuração em vez de descarregar a partir do URL cada vez.

> [!NOTE]
> Para as seguintes amostras, preencha os valores corretos para as variáveis na primeira secção antes de executar o código. 
#### <a name="azure-cli-sample"></a>Amostra de Azure CLI

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>Amostra de Azure CLI para instalar LAD 3.0 na caixa de conjunto de escala de máquina virtual

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Exemplo do PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>Atualizar as definições de extensão

Depois de alterar as definições protegidas ou públicas, coloque-as no VM executando o mesmo comando. Se as definições forem alteradas, as atualizações são enviadas para a extensão. O LAD recarrega a configuração e reinicia-se sozinho.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrar de versões anteriores da extensão

A versão mais recente da extensão é *4.0*. 

> [!IMPORTANT]
> Esta extensão introduz alterações de rutura na configuração. Uma dessas mudanças melhorou a segurança da extensão, por isso a retrocompatibilidade com 2.x não pôde ser mantida. Além disso, a editora de extensão para esta extensão difere da editora para as versões 2.x.
>
> Para migrar de 2.x para a nova versão, desinstale primeiro a antiga extensão (sob o nome da antiga editora). Em seguida, instale a versão 3.

Recomendações:

* Instale a extensão com a atualização automática de versão menor ativada.
  * No modelo de implementação clássico VMs, especifique a versão `3.*` se estiver a instalar a extensão através do Azure XPLAT CLI ou PowerShell.
  * No modelo de implementação do Gestor de Recursos Azure, inclua `"autoUpgradeMinorVersion": true` no modelo de implementação VM.
* Utilize uma conta de armazenamento nova ou diferente para LAD 3.0. LAD 2.3 e LAD 3.0 têm várias pequenas incompatibilidades que tornam a partilha de uma conta problemática:
  * LAD 3.0 armazena eventos de syslog numa mesa com um nome diferente.
  * As `counterSpecifier` cordas para `builtin` métricas diferem em LAD 3.0.

## <a name="protected-settings"></a>Configurações protegidas

Este conjunto de informações de configuração contém informações sensíveis que devem ser protegidas da opinião pública. Contém, por exemplo, credenciais de armazenamento. Estas definições são transmitidas e armazenadas pela extensão sob forma encriptada.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Valor
---- | -----
storageAccountName | O nome da conta de armazenamento em que os dados são escritos pela extensão.
armazenamentoAccountEndPoint | (Opcional) O ponto final que identifica a nuvem em que a conta de armazenamento existe. Se esta definição estiver ausente, o padrão LAD é a nuvem pública Azure, `https://core.windows.net` . Para utilizar uma conta de armazenamento na Alemanha Azure, O Governo Azure ou Azure China 21Vianet, de definido este valor conforme necessário.
armazenamentoSasToken | Um [token SAS conta para](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) serviços de bolhas e mesas `ss='bt'` (). Aplica-se a recipientes e objetos `srt='co'` (). Concede permissões adicionais, criar, listar, atualizar e escrever permissões `sp='acluw'` (). *Não* inclua o ponto de interrogação principal (?).
mdsdHttpProxy | (Opcional) HTTP proxy informações de que a extensão precisa de se ligar à conta de armazenamento especificada e ao ponto final.
afundaConfig | (Opcional) Detalhes de destinos alternativos aos quais métricas e eventos podem ser entregues. As seguintes secções abordam detalhes sobre cada sumidouro de dados suportado pela extensão.

Para obter um token SAS dentro de um modelo ARM, utilize a `listAccountSas` função. Para um modelo de exemplo, consulte [exemplo de função lista](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Pode construir o token SAS necessário através do portal Azure:

1. Selecione a conta de armazenamento para fins gerais para a qual deseja que a extensão escreva.
1. No menu à esquerda, em **Definições,** selecione **assinatura de acesso partilhado**.
1. Faça as seleções como descrito anteriormente.
1. Selecione **gerar SAS**.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="A screenshot mostra a página de assinatura de acesso compartilhado com o botão Gerar S A S.":::

Copie o SAS gerado para o `storageAccountSasToken` campo. Remova o ponto de interrogação principal (?).

### <a name="sinksconfig"></a>afundaConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

A `sinksConfig` secção opcional define mais destinos para os quais a extensão envia a informação que recolhe. A `sink` matriz contém um objeto para cada pia de dados adicional. O `type` atributo determina os outros atributos do objeto.

Elemento | Valor
------- | -----
name | Uma corda que se refere a esta pia em outro lugar na configuração da extensão.
tipo | O tipo de pia a ser definida. Determina os outros valores (se houver) em casos deste tipo.

A versão LAD 3.0 suporta dois tipos de pia: `EventHub` e `JsonBlob` .

#### <a name="eventhub-sink"></a>Afundanço do EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

A `"sasURL"` entrada contém o URL completo, incluindo o token SAS, para o centro de eventos a que os dados devem ser publicados. A LAD exige que um SAS nomeie uma política que permita o pedido de envio. 

Por exemplo:

* Crie um espaço de nome Azure Event Hubs chamado `contosohub` .
* Crie um centro de eventos no espaço de nomes chamado `syslogmsgs` .
* Crie uma política de acesso partilhado no centro de eventos que permita a reclamação de envio. Diga a `writer` apólice.

Se o seu SAS estiver bom até à meia-noite utc em 1 de janeiro de 2018, o valor sasURL pode ser como este exemplo:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Para obter mais informações sobre a geração e recuperação de informações sobre fichas SAS para Centros de Eventos, consulte [Gerar um token SAS](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="jsonblob-sink"></a>Pia JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Os dados direcionados para uma `JsonBlob` pia são armazenados em bolhas no Azure Storage. Cada instância de LAD cria uma bolha a cada hora para cada nome da pia. Cada bolha contém sempre uma matriz de objetos JSON sintaticamente válido. Novas entradas são adicionadas atomicamente à matriz. 

As bolhas são armazenadas num recipiente com o mesmo nome que a pia. As regras de armazenamento Azure para nomes de recipientes blob aplicam-se aos nomes dos `JsonBlob` lavatórios. O nome deve ter entre 3 e 63 caracteres ascii alfanuméricos minúsculos ou traços.

## <a name="public-settings"></a>Cenários públicos

A estrutura de configurações públicas contém vários blocos de configurações que controlam a informação que a extensão recolhe. Cada definição é opcional. Se `ladCfg` especificar, também deve especificar `StorageAccount` .

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elemento | Valor
------- | -----
StorageAccount | O nome da conta de armazenamento em que os dados são escritos pela extensão. Deve ser o nome especificado nas [definições protegidas](#protected-settings).
mdsdHttpProxy | (Opcional) O mesmo que nas [definições protegidas](#protected-settings). O valor público é ultrapassado pelo valor privado, se for definido. Coloque as definições de procuração que contenham um segredo, como uma palavra-passe, nas [definições protegidas](#protected-settings).

As seguintes secções fornecem detalhes para os restantes elementos.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

A `ladCfg` estrutura é opcional. Controla a recolha de métricas e registos que são entregues ao serviço Azure Monitor Metrics e a outros sumidouros de dados. Deve especificar:

* Ou `performanceCounters` `syslogEvents` ou ambos. 
* A `metrics` estrutura.

Elemento | Valor
------- | -----
eventVolume | (Opcional) Controla o número de divisórias criadas dentro da mesa de armazenamento. Deve `"Large"` ser, `"Medium"` ou . `"Small"` . Se um valor não for especificado, o padrão é `"Medium"` .
sampleRateInSeconds | (Opcional) O intervalo padrão entre a recolha de métricas cruas (não agregadas). A menor taxa de amostra suportada é de 15 segundos. Se o valor não for especificado, o padrão é `15` .

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elemento | Valor
------- | -----
resourceId | O ID de recurso Azure Resource Manager do VM ou da escala a que o VM pertence. Esta definição também deve ser especificada se for utilizada qualquer `JsonBlob` pia na configuração.
programadoTransferPeriod | A frequência em que as métricas agregadas são calculadas e transferidas para a Azure Monitor Metrics. A frequência é expressa como um intervalo de tempo IS 8601. O menor período de transferência é de 60 segundos, ou seja, PT1M. Especificar pelo menos um `scheduledTransferPeriod` .

As amostras das métricas especificadas na secção são recolhidas a `performanceCounters` cada 15 segundos ou à taxa de amostra explicitamente definida para o contador. Se `scheduledTransferPeriod` várias frequências aparecerem, como no exemplo, cada agregação é calculada de forma independente.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

A `performanceCounters` secção opcional controla a recolha de métricas. São agregadas amostras brutas para cada um [`scheduledTransferPeriod`](#metrics) produzir estes valores:

* Média
* Mínimo
* Máximo
* Valor recolhido pela última vez
* Contagem de amostras cruas usadas para calcular o agregado

Elemento | Valor
------- | -----
pias | (Opcional) Uma lista separada por vírgulas de nomes de pias para as quais o LAD envia resultados métricos agregados. Todas as métricas agregadas são publicadas em cada pia listada. Exemplo: `"EHsink1, myjsonsink"`. Para obter mais informações, veja [`sinksConfig`](#sinksconfig).
tipo | Identifica o fornecedor real da métrica.
classe | Juntamente `"counter"` com, identifica a métrica específica dentro do espaço de nome do fornecedor.
counter | Juntamente `"class"` com, identifica a métrica específica dentro do espaço de nome do fornecedor.
contraSpecificador | Identifica a métrica específica dentro do espaço de nomeS Azure Monitor Metrics.
condição | (Opcional) Seleciona uma instância específica do objeto a que a métrica se aplica. Ou seleciona a agregação em todos os casos desse objeto. 
sampleRate | O intervalo IS 8601 que define a taxa a que são recolhidas amostras cruas para esta métrica. Se o valor não for definido, o intervalo de recolha é definido pelo valor de [`sampleRateInSeconds`](#ladcfg) . A taxa de amostra suportada mais curta é de 15 segundos (PT15S).
unit | Define a unidade para a métrica. Deve ser uma destas cordas: `"Count"` , , , , , , , . `"Bytes"` `"Seconds"` `"Percent"` `"CountPerSecond"` `"BytesPerSecond"` `"Millisecond"` . Os consumidores dos dados recolhidos esperam que os valores dos dados recolhidos correspondam a esta unidade. Lad ignora este campo.
displayName | A etiqueta a ser anexada aos dados em Azure Monitor Metrics. Esta etiqueta encontra-se no idioma especificado pela definição local associada. Lad ignora este campo.

`counterSpecifier`É um identificador arbitrário. Os consumidores de métricas, como o recurso de gráfico e alerta do portal Azure, utilizam `counterSpecifier` como "chave" que identifica uma métrica ou um exemplo de uma métrica. 

Para `builtin` métricas, recomendamos `counterSpecifier` valores que começam com `/builtin/` . Se estiver a recolher uma instância específica de uma métrica, recomendamos que anexe o identificador da instância ao `counterSpecifier` valor. 

Eis alguns exemplos:

* `/builtin/Processor/PercentIdleTime` - Tempo inativo mediado em todos os vCPUs
* `/builtin/Disk/FreeSpace(/mnt)` - Espaço gratuito para o `/mnt` sistema de ficheiros
* `/builtin/Disk/FreeSpace` - Espaço livre mediado em todos os sistemas de ficheiros montados

Lad e o portal Azure não esperam que o `counterSpecifier` valor corresponda a qualquer padrão. Seja consistente na forma como constrói `counterSpecifier` valores.

Quando `performanceCounters` especifica, a LAD escreve sempre dados para uma tabela no Azure Storage. Os mesmos dados podem ser escritos para bolhas JSON ou Centros de Eventos ou ambos. Mas não se pode desativar o armazenamento de dados numa mesa. 

Todas as instâncias de LAD que usam o mesmo nome de conta de armazenamento e ponto final adicionam as suas métricas e troncos à mesma tabela. Se muitos VMs escreverem para a mesma divisória de mesa, Azure pode acelerar escreve para essa partição. 

A `eventVolume` definição faz com que as entradas sejam espalhadas por 1 (pequenas), 10 (médias) ou 100 (grandes) divisórias. Normalmente, as divisórias médias são suficientes para evitar o estrangulamento do tráfego. 

A funcionalidade Azure Monitor Metrics do portal Azure utiliza os dados desta tabela para produzir gráficos ou para desencadear alertas. O nome da mesa é a concatenação destas cordas:

* `WADMetrics`
* O `"scheduledTransferPeriod"` para os valores agregados armazenados na tabela
* `P10DV2S`
* Uma data, no formulário "YYYYMMDD", que muda a cada 10 dias

Exemplos incluem `WADMetricsPT1HP10DV2S20170410` `WADMetricsPT1MP10DV2S20170609` e.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

A `syslogEvents` secção opcional controla a recolha de eventos de log a partir de syslog. Se a secção for omitida, os eventos de syslog não são capturados.

A `syslogEventConfiguration` coleção tem uma entrada para cada syslog facilidade de interesse. Se `minSeverity` for para uma `"NONE"` instalação em particular, ou se as instalações não aparecerem no elemento, nenhum evento dessa instalação é capturado.

Elemento | Valor
------- | -----
pias | Uma lista separada por vírgulas de nomes de pias para as quais são publicados eventos individuais de registo. Todos os eventos de registo que correspondam às restrições são publicados em `syslogEventConfiguration` cada pia listada. Exemplo: `"EHforsyslog"`
nome de facilidades | Um nome de syslog facility, como `"LOG_USER"` ou `"LOG\LOCAL0"` . Para mais informações, consulte a secção "Facility" da [página syslog man](http://man7.org/linux/man-pages/man3/syslog.3.html).
minSeverity | Um nível de severidade syslog, como `"LOG_ERR"` ou `"LOG_INFO"` . Para mais informações, consulte a secção "Nível" da [página do homem syslog](http://man7.org/linux/man-pages/man3/syslog.3.html). A extensão captura eventos enviados para a instalação a um nível ou superior ao especificado.

Quando `syslogEvents` especifica, a LAD escreve sempre dados para uma tabela no Azure Storage. Os mesmos dados podem ser escritos para bolhas JSON ou Centros de Eventos ou ambos. Mas não se pode desativar o armazenamento de dados numa mesa. 

O comportamento de partição para a tabela é o mesmo que descrito para `performanceCounters` . O nome da mesa é a concatenação destas cordas:

* `LinuxSyslog`
* Uma data, no formulário "YYYYMMDD", que muda a cada 10 dias

Exemplos incluem `LinuxSyslog20170410` `LinuxSyslog20170609` e.

### <a name="perfcfg"></a>perfCfg

A `perfCfg` secção é opcional. Controla o funcionamento de consultas arbitrárias da [Infraestrutura de Gestão Aberta (OMI).](https://github.com/Microsoft/omi)

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Elemento | Valor
------- | -----
espaço de nomes | (Opcional) O espaço de nome OMI dentro do qual a consulta deve ser executada. Se não for especificado, o valor predefinido é `"root/scx"` . É implementado pelos [fornecedores de plataformas cruzadas do System Center.](https://github.com/Microsoft/SCXcore)
consulta | A consulta da OMI para correr.
table | (Opcional) A tabela de armazenamento Azure, na conta de armazenamento designada. Para obter mais informações, consulte [as definições protegidas](#protected-settings).
frequência | (Opcional) O número de segundos entre a consulta é executado. O valor predefinido é de 300 (5 minutos). O valor mínimo é de 15 segundos.
pias | (Opcional) Deve ser publicada uma lista separada por vírgulas de nomes de mais sumidouros aos quais devem ser publicados resultados métricos brutos da amostra. Nenhuma agregação destas amostras cruas é calculada pela extensão ou pela Azure Monitor Metrics.

Ou `"table"` `"sinks"` ambos devem ser especificados.

### <a name="filelogs"></a>arquivos

A `fileLogs` secção controla a captura de ficheiros de registo. Lad captura novas linhas de texto à medida que são escritas para o ficheiro. Escreve-os para filas de mesa e/ou pias especificadas `JsonBlob` (ou `EventHub` .

> [!NOTE]
> Os `fileLogs` capturados por um subcomponente da LAD chamado `omsagent` . Para `fileLogs` recolher, certifique-se de que o `omsagent` utilizador leu permissões nos ficheiros que especificou. O utilizador também deve ter permissões de execução em todos os diretórios no caminho para esse ficheiro. Depois da instalação do LAD, pode verificar permissões executando `sudo su omsagent -c 'cat /path/to/file'` .

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elemento | Valor
------- | -----
file | O nome do caminho completo do ficheiro de registo a ser observado e capturado. O nome do caminho deve nomear um único ficheiro. Não pode nomear um diretório ou conter caracteres wildcard. A `omsagent` conta do utilizador deve ter lido o acesso ao caminho do ficheiro.
table | (Opcional) A tabela de armazenamento Azure em que estão escritas novas linhas da "cauda" do ficheiro. A tabela deve estar na conta de armazenamento designada, conforme especificado na configuração protegida. 
pias | (Opcional) Uma lista separada por vírgulas de nomes de mais pias para as quais são enviadas linhas de registo.

Ou `"table"` `"sinks"` ou ambos devem ser especificados.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas suportadas pelo fornecedor de builtin

O `builtin` provedor métrico é uma fonte de métricas que são as mais interessantes para um vasto conjunto de utilizadores. Estas métricas caem em cinco classes amplas:

* Processador
* Memória
* Rede
* Sistema de ficheiros
* Disco

### <a name="builtin-metrics-for-the-processor-class"></a>métricas de builtin para a classe processador

A classe de métricas do processador fornece informações sobre a utilização do processador no VM. Quando as percentagens são agregadas, o resultado é a média em todos os CPUs. 

Num VM de dois vCPU, se um vCPU estiver 100% ocupado e o outro estiver 100% inativo, o reportado `PercentIdleTime` é de 50. Se cada vCPU estiver 50% ocupado durante o mesmo período, o resultado reportado também é de 50. Num VM de quatro vCPU, quando um vCPU está 100% ocupado e os outros estão inativos, o relatório `PercentIdleTime` é de 75.

Contador | Significado
------- | -------
PercentIdleTime | Percentagem de tempo durante a janela de agregação que os processadores executaram o ciclo de idle kernel
PercentProcessorTime | Percentagem de tempo executando um fio não ocioso
PercentIOWaitTime | Percentagem de tempo à espera que as operações de IO terminem
PercentInterruptTime | Percentagem de tempo de execução de hardware ou software interrompe e DPCs (chamadas de procedimento diferido)
PercentUserTime | De tempo não inativo durante a janela de agregação, a percentagem de tempo gasto no modo de utilizador na prioridade normal
PercentNiceTime | De tempo não inativo, a percentagem gasta na prioridade reduzida (nice)
Tempo de Prioridades | De tempo não inativo, a percentagem gasta em modo privilegiado (kernel)

Os primeiros quatro balcões devem chegar a 100%. Os últimos três balcões também são 100%. Estes três balcões subdividem a soma `PercentProcessorTime` `PercentIOWaitTime` de, e `PercentInterruptTime` .

Para agregar uma única métrica em todos os processadores, definir `"condition": "IsAggregate=TRUE"` . Para obter uma métrica para um processador específico, como o segundo processador lógico de um VM de quatro vCPU, definido `"condition": "Name=\\"1\\""` . Os números lógicos do processador estão na `[0..n-1]` gama.

### <a name="builtin-metrics-for-the-memory-class"></a>métricas de construção para a classe Memória

A classe memory das métricas fornece informações sobre o uso da memória, a pescamia e troca.

Contador | Significado
------- | -------
DisponívelMemory | Memória física disponível em MiB
Por cento DisponívelMemory | Memória física disponível em percentagem da memória total
UsadoMemory | Memória física em uso (MiB)
PercentUsedMemory | Memória física em uso em percentagem da memória total
PagesPerSec | Paging total (ler/escrever)
PagesReadPerSec | Páginas lidas na loja de apoio, tais como ficheiro de troca, ficheiro de programa e ficheiro mapeado
PagesWrittenPerSec | Páginas escritas para a loja de apoio, tais como ficheiro de troca e ficheiro mapeado
DisponívelSwap | Espaço de troca não reutilizado (MiB)
Por cento DisponívelSwap | Espaço de troca não reutilizado em percentagem do swap total
Usswap | Espaço de troca em uso (MiB)
PercentUsedSwap | Espaço de troca em uso em percentagem do swap total

Esta classe de métricas tem apenas um caso. O `"condition"` atributo não tem configurações úteis e deve ser omitido.

### <a name="builtin-metrics-for-the-network-class"></a>métricas de builtin para a classe Rede

A classe de métricas da Rede fornece informações sobre a atividade da rede numa interface de rede individual desde o arranque. 

Lad não expõe métricas de largura de banda. Pode obter estas métricas a partir de métricas hospedeiras.

Contador | Significado
------- | -------
BytesTransmitted | Total de bytes enviados desde o arranque
BytesReceived | Total de bytes recebidos desde o arranque
BytesTotal | Total de bytes enviados ou recebidos desde o arranque
PacotesTransmed | Total de pacotes enviados desde o arranque
Pacotes Reeceu | Total de pacotes recebidos desde o arranque
TotalRxErrors | Número de erros de receção desde o arranque
TotalTxErrors | Número de erros de transmissão desde o arranque
TotalCollisions | Número de colisões reportadas pelos portos da rede desde o arranque

Embora a classe Network seja exemplo, a LAD não suporta a captura de métricas de Rede agregadas em todos os dispositivos de rede. Para obter as métricas para uma interface específica, como a eth0, definir `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-file-system-class"></a>métricas de builtin para a classe de sistema de arquivo

A classe de métricas do sistema de ficheiros fornece informações sobre a utilização do sistema de ficheiros. Os valores absolutos e percentuais são reportados como seriam apresentados a um utilizador comum (não raiz).

Contador | Significado
------- | -------
EspaçoLivre | Espaço disponível em disco em bytes
Espaço Usado | Espaço de disco usado em bytes
PercentFreeSpace | Espaço livre percentual
Espaço Por cento | Percentagem de espaço usado
PercentFreeInodes | Percentagem de nós de índice não reutilizados (inodes)
PercentUsedInodes | Percentagem de inodes atribuídos (em uso) resumidos em todos os sistemas de ficheiros
BytesReadPerSecond | Bytes ler por segundo
BytesWrittenPerSecond | Bytes escritos por segundo
BytesPerSecond | Bytes lidos ou escritos por segundo
ReadsPerSecond | Ler operações por segundo
WritesPerSecond | Escrever operações por segundo
TransfersPerSecond | Ler ou escrever operações por segundo

Pode obter valores agregados em todos os sistemas de ficheiros, definindo `"condition": "IsAggregate=True"` . Obtenha valores para um sistema de ficheiros montado específico, `"/mnt"` como, por exemplo, por definição `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> Se estiver a trabalhar no portal Azure em vez de JSON, o formulário de campo de condição é `Name='/mnt'` .

### <a name="builtin-metrics-for-the-disk-class"></a>métricas de builtin para a classe Disco

A classe disco de métricas fornece informações sobre o uso do dispositivo de disco. Estas estatísticas aplicam-se a toda a unidade. 

Quando um dispositivo tem vários sistemas de ficheiros, os contadores para este dispositivo são, efetivamente, agregados em todos os sistemas de ficheiros.

Contador | Significado
------- | -------
ReadsPerSecond | Ler operações por segundo
WritesPerSecond | Escrever operações por segundo
TransfersPerSecond | Total de operações por segundo
Média DeReadTime | Média de segundos por operação de leitura
Média De4014 | Segundos médios por operação de escrita
Média DeTrassa | Segundos médios por operação
Média DediskQueueLength | Número médio de operações de disco em fila
ReadBytesPerSecond | Número de bytes lidos por segundo
WriteBytesPerSecond | Número de bytes escritos por segundo
BytesPerSecond | Número de bytes lidos ou escritos por segundo

Pode obter valores agregados em todos os discos definindo `"condition": "IsAggregate=True"` . Para obter informações sobre um dispositivo específico (por exemplo, `/dev/sdf1` ), definir `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="install-and-configure-lad-30"></a>Instalar e configurar LAD 3.0

### <a name="azure-cli"></a>CLI do Azure

Se as suas definições protegidas estiverem no ficheiro *ProtectedSettings.js* e as informações de configuração pública *estiveremPublicSettings.js,* executar o seguinte comando.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

O comando pressupõe que está a utilizar o modo Azure Resource Manager do Azure CLI. Para configurar o LAD para os VMs modelos de implantação clássicos, mude para o modo "asm" ( `azure config mode asm` ) e omita o nome do grupo de recursos no comando. 

Para mais informações, consulte a [documentação do CLI de plataforma cruzada.](/cli/azure/authenticate-azure-cli)

### <a name="powershell"></a>PowerShell

Se as suas definições protegidas estiverem na variável e a `$protectedSettings` informação de configuração pública estiver na `$publicSettings` variável, execute este comando:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>Configuração exemplo LAD 3.0

Com base nas definições anteriores, esta secção fornece uma configuração de extensão LAD 3.0 de amostra e alguma explicação. Para aplicar esta amostra no seu caso, use o nome da sua própria conta de armazenamento, ficha SAS da conta e fichas SAS do Event Hubs.

> [!NOTE]
> Dependendo se utiliza o Azure CLI ou o PowerShell para instalar o LAD, o método para fornecer configurações públicas e protegidas difere: 
>
> * Se estiver a utilizar o CLI Azure, guarde as seguintes definições para *ProtectedSettings.js* e *PublicSettings.js* para utilizar o comando da amostra anterior. 
> * Se estiver a utilizar o PowerShell, guarde as seguintes definições para `$protectedSettings` e `$publicSettings` executando `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Configurações protegidas

As configurações de definições protegidas configuram:

* Uma conta de armazenamento.
* Uma conta correspondente sas token.
* Vários lavatórios `JsonBlob` (ou `EventHub` com fichas SAS).

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Cenários públicos

As configurações públicas fazem com que o LAD:

* Carreque métricas de tempo de processador por cento e métricas de espaço em disco usado para a `WADMetrics*` mesa.
* Faça upload de mensagens da instalação syslog `"user"` e gravidade `"info"` para a `LinuxSyslog*` mesa.
* Faça o upload dos resultados da consulta OMI `PercentProcessorTime` `PercentIdleTime` (e) para a tabela `LinuxCPU` nomeada.
* Faça o upload de linhas anexadas em ficheiro `/var/log/myladtestlog` para a `MyLadTestLog` mesa.

Em cada caso, os dados também são enviados para:

* Azure Blob Storage. O nome do recipiente está definido na `JsonBlob` pia.
* O ponto final do Event Hubs, conforme especificado na `EventHub` pia.

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

A `resourceId` configuração deve corresponder à do VM ou do conjunto de balanças de máquina virtual.

* As métricas da plataforma Azure, que estão a traçar e a alertar, sabem `resourceId` da VM em que estás a trabalhar. Espera encontrar os dados para o seu VM utilizando `resourceId` a chave de procura.
* Se utilizar a Azure Autoscale, `resourceId` a configuração de autoescala deve corresponder à `resourceId` utilização do LAD.
* O `resourceId` é incorporado aos nomes das bolhas JSON escritas pela LAD.

## <a name="view-your-data"></a>Ver os dados

Utilize o portal Azure para visualizar dados de desempenho ou para definir alertas:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="A imagem mostra o portal Azure. É selecionado o espaço do disco usado na métrica. O gráfico resultante é mostrado.":::

Os `performanceCounters` dados são sempre armazenados numa tabela de Armazenamento Azure. As APIs de Armazenamento Azure estão disponíveis para muitos idiomas e plataformas.

Os `JsonBlob` dados enviados para as pias são armazenados em bolhas na conta de armazenamento indicada nas [definições protegidas](#protected-settings). Pode consumir os dados blob utilizando quaisquer APIs de armazenamento de blob Azure.

Também pode utilizar estas ferramentas de UI para aceder aos dados no Azure Storage:

* Explorador visual do servidor do estúdio
* [Explorador do Storage do Azure](https://azurestorageexplorer.codeplex.com/)

A imagem seguinte de uma sessão do Azure Storage Explorer mostra as mesas e recipientes de armazenamento Azure gerados a partir de uma extensão LAD 3.0 corretamente configurada num VM de teste. A imagem não corresponde exatamente à [configuração lad 3.0 da amostra.](#example-lad-30-configuration)

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="A screenshot mostra Azure Storage Explorer.":::


Para obter mais informações sobre como consumir mensagens publicadas num ponto final do Event Hubs, consulte a documentação relevante do [Event Hubs](../../event-hubs/event-hubs-about.md).

## <a name="next-steps"></a>Passos seguintes

* No [Azure Monitor,](../../azure-monitor/alerts/alerts-classic-portal.md)crie alertas para as métricas que recolhe.
* Crie [gráficos de monitorização](../../azure-monitor/data-platform.md) para as suas métricas.
* [Crie uma balança de máquina virtual definida](../linux/tutorial-create-vmss.md) utilizando as suas métricas para controlar a autoscalagem.
