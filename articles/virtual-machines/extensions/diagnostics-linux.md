---
title: Azure Compute - Extensão de diagnóstico Linux 4.0
description: Como configurar a extensão de diagnóstico Azure Linux (LAD) 4.0 para recolher métricas e eventos de registo de VMs Linux em execução em Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/05/2021
ms.openlocfilehash: 2e862915bcc524db50e7e66c969b713f729c64aa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479649"
---
# <a name="use-the-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>Utilize a extensão de diagnóstico Linux 4.0 para monitorizar métricas e registos

Este documento descreve as versões mais recentes da extensão de diagnóstico Linux (LAD).

> [!IMPORTANT]
> Para obter informações sobre a versão 3.x, consulte  [a extensão de diagnóstico Linux 3.0 para monitorizar métricas e registos](./diagnostics-linux-v3.md). Para obter informações sobre a versão 2.3 e anteriores, consulte [monitorar os dados de desempenho e diagnóstico de um Linux VM](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Introdução

a extensão de diagnóstico Linux ajuda um utilizador a monitorizar a saúde de um Linux VM em execução no Microsoft Azure. Tem as seguintes capacidades:

* Recolhe métricas de desempenho do sistema a partir do VM e armazena-as numa tabela específica numa conta de armazenamento designada.
* Recupera eventos de registo do syslog e armazena-os numa tabela específica na conta de armazenamento designada.
* Permite que os utilizadores personalizem as métricas de dados que são recolhidas e carregadas.
* Permite aos utilizadores personalizar as instalações do syslog e os níveis de gravidade dos eventos que são recolhidos e carregados.
* Permite que os utilizadores carreguem ficheiros de registo especificados para uma tabela de armazenamento designada.
* Suporta o envio de métricas e eventos de registo para pontos finais arbitrários do EventHub e bolhas com formação JSON na conta de armazenamento designada.

Esta extensão funciona com ambos os modelos de implantação Azure.

## <a name="install-the-extension-on-a-vm"></a>Instale a extensão num VM

Pode ativar esta extensão utilizando os cmdlets Azure PowerShell, scripts Azure CLI, modelos Azure Resource Manager (modelos ARM) ou o portal Azure. Para obter mais informações, consulte [Extensões e funcionalidades.](features-linux.md)

>[!NOTE]
>Alguns componentes da extensão De Diagnóstico VM do Linux também são enviados na [extensão VM log Analytics](./oms-linux.md). Devido a esta arquitetura, os conflitos podem surgir se ambas as extensões forem instantâneas no mesmo modelo ARM. 
>
>Para evitar conflitos de tempo de instalação, utilize a [ `dependsOn` diretiva](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) para instalar as extensões sequencialmente. As extensões podem ser instaladas em qualquer ordem.

Utilize as instruções de instalação e uma [configuração de amostra descarregada](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) para configurar o LAD 4.0 para:

* Capture e guarde as mesmas métricas que as versões LAD 2.3 e 3.x fornecidas.
* Envie métricas para a pia do Monitor Azure juntamente com a pia habitual para o Azure Storage. Esta funcionalidade é nova em LAD 4.0.
* Capture um conjunto útil de métricas do sistema de ficheiros, como em LAD 3.0.
* Capture a coleção de syslog padrão ativada por LAD 2.3.
* Ativar a experiência do portal Azure para o charting e alerta nas métricas VM.

A configuração transferível é apenas um exemplo. Modifique-o de acordo com as suas necessidades.

### <a name="supported-linux-distributions"></a>Distribuição apoiada do Linux

A extensão de diagnóstico Linux suporta muitas distribuições e versões. A lista seguinte de distribuições e versões aplica-se apenas às imagens do fornecedor Linux, apoiadas pelo Azure. A extensão geralmente não suporta imagens BYOL e BYOS de terceiros, como aparelhos.

Uma distribuição que lista apenas versões importantes, como Debian 7, também é suportada para todas as versões menores. Se for especificada uma versão menor específica, apenas essa versão é suportada. Se um sinal de mais (+) for anexado, versões menores iguais ou posteriores à versão especificada são suportadas.

Distribuições e versões suportadas:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Pré-requisitos

* **Azure Linux agente versão 2.2.0 ou posterior**. A maioria das imagens da galeria Azure VM Linux incluem a versão 2.2.7 ou mais tarde. Corra `/usr/sbin/waagent -version` para confirmar a versão instalada no VM. Se o VM estiver a executar uma versão mais antiga do agente convidado, [atualize o agente Linux](./update-linux-agent.md).
* **Azure CLI**. [Confende o ambiente Azure CLI](/cli/azure/install-azure-cli) na sua máquina.
* **O `wget` comando.** Se ainda não o tem, `sudo apt-get install wget` corra.
* **Uma conta de subscrição azure e de armazenamento de finalidade geral** para armazenar os dados.  As contas de armazenamento de propósito geral suportam o armazenamento da mesa, o que é necessário.  Uma conta de armazenamento de bolhas não funciona.
* **Python 2**.

### <a name="python-requirement"></a>Requisito de Python

A extensão de diagnóstico Linux requer Python 2. Se a sua máquina virtual utilizar uma distribuição que não inclua python 2 por defeito, instale-a. 

Os seguintes comandos de amostras instalam Python 2 em várias distribuições:    

- Chapéu Vermelho, CentOS, Oráculo: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

O `python2` ficheiro executável deve ser aliasado à *pitão.* Aqui está uma maneira de definir este pseudónimo:

1. Executar o seguinte comando para remover quaisquer pseudónimos existentes.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Executar o seguinte comando para criar o pseudónimo.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Instalação de amostras

> [!NOTE]
> Para as seguintes amostras, preencha os valores corretos para as variáveis na primeira secção antes de executar o código. 

Nestes exemplos, a configuração da amostra recolhe um conjunto de dados padrão e envia-os para o armazenamento de mesas. O URL para a configuração da amostra e o seu conteúdo podem mudar. 

Na maioria dos casos, deve descarregar uma cópia do ficheiro JSON de definições do portal e personalizá-la para as suas necessidades. Em seguida, use modelos ou a sua própria automatização para usar uma versão personalizada do ficheiro de configuração em vez de descarregar a partir do URL cada vez.

> [!NOTE]
> Quando ativa o novo acerca do Azure Monitor, os VMs precisam de ter uma identidade atribuída ao sistema para gerar fichas de autenticação de identidade de serviço gerida (MSI). Pode adicionar estas definições durante ou após a criação de VM. 
>
> Para obter instruções para o portal Azure, o Azure CLI, PowerShell e Azure Resource Manager, consulte [as identidades geridas pela Configure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). 



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

# Enable system-assigned identity on the existing VM.
az vm identity assign -g $my_resource_group -n $my_linux_vm

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
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-40-on-a-virtual-machine-scale-set-instance"></a>Amostra de Azure CLI para instalar LAD 4.0 em uma caixa de escala de máquina virtual

```azurecli
# Set your Azure virtual machine scale set diagnostic variables. 
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing virtual machine scale set.
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

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
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Exemplo do PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable system-assigned identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for the storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0 
```

### <a name="update-the-extension-settings"></a>Atualizar as definições de extensão

Depois de alterar as definições protegidas ou públicas, coloque-as no VM executando o mesmo comando. Se alguma definição for alterada, as atualizações são enviadas para a extensão. O LAD recarrega a configuração e reinicia-se sozinho.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrar de versões anteriores da extensão

A versão mais recente da extensão é *4.0, que está atualmente em visualização pública.* Versões mais antigas de 3.x ainda são suportadas. Mas as versões 2.x foram depreciadas desde 31 de julho de 2018.

> [!IMPORTANT]
> Para migrar de 3.x para a versão mais recente da extensão, desinstale a antiga extensão. Em seguida, instale a versão 4, que inclui a configuração atualizada para a identidade atribuída ao sistema e pias para o envio de métricas para o lavatório do Azure Monitor.

Quando instalar a nova extensão, ative atualizações automáticas de versão menor:
* No modelo de implementação clássico VMs, especifique a versão `4.*` se estiver a instalar a extensão através do Azure Xplat CLI ou PowerShell.
* No modelo de implementação do Gestor de Recursos Azure, inclua `"autoUpgradeMinorVersion": true` no modelo de implementação VM.

Pode utilizar a mesma conta de armazenamento que usou para o LAD 3.x. 

## <a name="protected-settings"></a>Configurações protegidas

Este conjunto de informações de configuração contém informações sensíveis que devem ser protegidas da opinião pública. Contém, por exemplo, credenciais de armazenamento. As definições são transmitidas e armazenadas pela extensão sob forma encriptada.

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
storageAccountName | O nome da conta de armazenamento na qual a extensão escreve dados.
armazenamentoAccountEndPoint | (Opcional) O ponto final que identifica a nuvem em que a conta de armazenamento existe. Se esta definição estiver ausente, por defeito, a LAD utiliza a nuvem pública Azure, `https://core.windows.net` . Para utilizar uma conta de armazenamento na Alemanha Azure, O Governo Azure ou Azure China 21Vianet, de definido este valor conforme necessário.
armazenamentoSasToken | Um [token SAS conta para](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) serviços de bolhas e mesas `ss='bt'` (). Este símbolo aplica-se a recipientes e objetos `srt='co'` (). Concede permissões adicionais, criar, listar, atualizar e escrever permissões `sp='acluw'` (). *Não* inclua o ponto de interrogação principal (?).
mdsdHttpProxy | (Opcional) HTTP proxy informações que a extensão precisa para se ligar à conta de armazenamento especificada e ao ponto final.
afundaConfig | (Opcional) Detalhes de destinos alternativos aos quais métricas e eventos podem ser entregues. As secções seguintes fornecem detalhes sobre cada dado afundar os suportes de extensão.

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

A `sinksConfig` secção opcional define mais destinos para os quais a extensão envia informações recolhidas. A `"sink"` matriz contém um objeto para cada pia de dados adicional. O `"type"` atributo determina os outros atributos do objeto.

Elemento | Valor
------- | -----
name | Uma corda usada para se referir a esta pia em outro lugar na configuração da extensão.
tipo | O tipo de pia a ser definida. Determina os outros valores (se houver) em casos deste tipo.

A extensão de diagnóstico Linux 4.0 suporta dois tipos de pia: `EventHub` e `JsonBlob` .

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

A `"sasURL"` entrada contém o URL completo, incluindo o token SAS, para o centro de eventos a que os dados devem ser publicados. A LAD exige que um SAS nomeie uma política que permita o pedido de envio. Eis um exemplo:

* Crie um espaço de nomes de Centros de Eventos chamado `contosohub` .
* Crie um centro de eventos no espaço de nomes chamado `syslogmsgs` .
* Crie uma política de acesso partilhado no centro de eventos nomeado `writer` que permita a reclamação de envio.

Se criou um SAS que é bom até à meia-noite utc em 1 de janeiro de 2018, o `sasURL` valor pode ser como o exemplo seguinte.

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

As bolhas são armazenadas num recipiente com o mesmo nome que a pia. As regras de armazenamento Azure para nomes de recipientes blob aplicam-se aos nomes dos `JsonBlob` lavatórios. Ou seja, os nomes devem ter entre 3 e 63 caracteres ascii alfanuméricos minúsculos ou traços.

## <a name="public-settings"></a>Cenários públicos

A estrutura de configurações públicas contém vários blocos de configurações que controlam a informação que a extensão recolhe. Cada definição, `ladCfg` exceto, é opcional. Se especificar a recolha métrica ou syslog em `ladCfg` , também deve especificar `StorageAccount` . Deve especificar o `sinksConfig` elemento para ativar a pia do Monitor Azure para métricas a partir de LAD 4.0.

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
    "mdsdHttpProxy" : ""
}
```

Elemento | Valor
------- | -----
StorageAccount | O nome da conta de armazenamento na qual a extensão escreve dados. Deve ser o nome especificado nas [definições protegidas](#protected-settings).
mdsdHttpProxy | (Opcional) O representante especificado nas [definições protegidas](#protected-settings). Se o valor privado for definido, sobrepõe-se ao valor público. Coloque as definições de procuração que contenham um segredo, como uma palavra-passe, nas [definições protegidas](#protected-settings).

As seguintes secções fornecem detalhes sobre os restantes elementos.

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

A `ladCfg` estrutura controla a recolha de métricas e registos para entrega no serviço Azure Monitor Metrics e para outros sumidouros de dados. Especifique ou `performanceCounters` `syslogEvents` ambos. Especificar também a `metrics` estrutura.

Se não quiser ativar a recolha de syslog ou métricas, especifique uma estrutura vazia para o `ladCfg` elemento, como neste exemplo: 

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
    }
```

Elemento | Valor
------- | -----
eventVolume | (Opcional) Controla o número de divisórias criadas dentro da mesa de armazenamento. O valor deve ser `"Large"` `"Medium"` , ou . `"Small"` . Se o valor não for especificado, o valor predefinido é `"Medium"` .
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
resourceId | O ID de recurso do Gestor de Recursos Azure do VM ou da balança de máquina virtual a que o VM pertence. Especificar também esta definição se a configuração utilizar qualquer `JsonBlob` pia.
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
condição | (Opcional) Seleciona uma instância do objeto a que a métrica se aplica. Ou seleciona a agregação em todos os casos desse objeto. 
sampleRate | O intervalo IS 8601 que define a taxa a que são recolhidas amostras cruas para esta métrica. Se o valor não for definido, o intervalo de recolha é definido pelo valor de [`sampleRateInSeconds`](#ladcfg) . A taxa de amostra suportada mais curta é de 15 segundos (PT15S).
unit | Define a unidade para a métrica. Deve ser uma destas cordas: `"Count"` , , , , , , , . `"Bytes"` `"Seconds"` `"Percent"` `"CountPerSecond"` `"BytesPerSecond"` `"Millisecond"` . Os consumidores dos dados recolhidos esperam que os valores dos dados recolhidos correspondam a esta unidade. Lad ignora este campo.
displayName | A etiqueta a ser anexada aos dados em Azure Monitor Metrics. Esta etiqueta encontra-se no idioma especificado pela definição local associada. Lad ignora este campo.

`counterSpecifier`É um identificador arbitrário. Os consumidores de métricas, como o recurso de gráfico e alerta do portal Azure, utilizam `counterSpecifier` como "chave" que identifica uma métrica ou um exemplo de uma métrica. 

Para `builtin` métricas, recomendamos `counterSpecifier` valores que começam com `/builtin/` . Se estiver a recolher uma instância específica de uma métrica, ligue o identificador da instância ao `counterSpecifier` valor. Eis alguns exemplos:

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
nome de facilidades | Um nome de syslog facility, como `"LOG\_USER"` ou `"LOG\_LOCAL0"` . Para mais informações, consulte a secção "facilidade" da [página do homem syslog](http://man7.org/linux/man-pages/man3/syslog.3.html).
minSeverity | Um nível de severidade syslog, como `"LOG\_ERR"` ou `"LOG\_INFO"` . Para mais informações, consulte a secção "nível" da [página do homem syslog](http://man7.org/linux/man-pages/man3/syslog.3.html). A extensão captura eventos enviados para a instalação a um nível ou superior ao especificado.

Quando `syslogEvents` especifica, a LAD escreve sempre dados para uma tabela no Azure Storage. Os mesmos dados podem ser escritos para bolhas JSON ou Centros de Eventos ou ambos. Mas não se pode desativar o armazenamento de dados numa mesa. 

O comportamento de partição desta tabela é o mesmo que descrito para `performanceCounters` . O nome da mesa é a concatenação destas cordas:

* `LinuxSyslog`
* Uma data, no formulário "YYYYMMDD", que muda a cada 10 dias

Exemplos incluem `LinuxSyslog20170410` `LinuxSyslog20170609` e.

### <a name="sinksconfig"></a>afundaConfig

A `sinksConfig` secção opcional permite o envio de métricas para a pia do Azure Monitor, para além da conta de Armazenamento e da lâmina padrão das Métricas do Hóspede.

> [!NOTE]
> A `sinksConfig` secção requer que a identidade atribuída ao sistema seja ativada nos VMs ou no conjunto de escala de máquina virtual. Pode ativar a identidade atribuída ao sistema através do portal Azure, CLI, PowerShell ou Azure Resource Manager. Siga as [instruções detalhadas](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) ou consulte as amostras de instalações anteriores neste artigo. 

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```


### <a name="filelogs"></a>arquivos

A `fileLogs` secção controla a captura de ficheiros de registo. Lad captura novas linhas de texto à medida que são escritas para o ficheiro. Escreve-os para filas de mesa e/ou pias especificadas, tais como `JsonBlob` `EventHub` e .

> [!NOTE]
> Os `fileLogs` capturados por um subcomponente da LAD chamado `omsagent` . Para `fileLogs` recolher, certifique-se de que o `omsagent` utilizador leu permissões nos ficheiros que especificou. Também deve ter permissões de execução em todos os diretórios no caminho para esse ficheiro. Depois da instalação do LAD, pode verificar permissões executando `sudo su omsagent -c 'cat /path/to/file'` .

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
file | O nome do caminho completo do ficheiro de registo a ser observado e capturado. O nome do caminho é para um único ficheiro. Não pode nomear um diretório ou conter caracteres wildcard. A `omsagent` conta do utilizador deve ter lido o acesso ao caminho do ficheiro.
table | (Opcional) A tabela de armazenamento Azure em que estão escritas novas linhas da "cauda" do ficheiro. A tabela deve estar na conta de armazenamento designada, conforme especificado na configuração protegida. 
pias | (Opcional) Uma lista separada por vírgulas de nomes de mais pias para as quais são enviadas linhas de registo.

Ou `"table"` `"sinks"` ambos devem ser especificados.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas suportadas pelo fornecedor de builtin

> [!NOTE]
> As métricas padrão que o LAD suporta são agregadas em todos os sistemas de ficheiros, discos ou nomes. Para métricas não agregadas, consulte o suporte mais recente das métricas da pia do Azure Monitor.

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

### <a name="builtin-metrics-for-the-memory-class"></a>métricas de construção para a classe Memória

A classe memory das métricas fornece informações sobre o uso da memória, a pescamia e troca.

counter | Significado
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

### <a name="builtin-metrics-for-the-file-system-class"></a>métricas de builtin para a classe de sistema de arquivo

A classe de métricas do sistema de ficheiros fornece informações sobre a utilização do sistema de ficheiros. Os valores absolutos e percentuais são reportados como seriam apresentados a um utilizador comum (não raiz).

Contador | Significado
------- | -------
EspaçoLivre | Espaço disponível em disco em bytes
Espaço Usado | Espaço de disco usado em bytes
PercentFreeSpace | Percentagem de espaço livre
Espaço Por cento | Percentagem de espaço usado
PercentFreeInodes | Percentagem de nós de índice não reutilizados (inodes)
PercentUsedInodes | Percentagem de inodes atribuídos (em uso) resumidos em todos os sistemas de ficheiros
BytesReadPerSecond | Bytes ler por segundo
BytesWrittenPerSecond | Bytes escritos por segundo
BytesPerSecond | Bytes lidos ou escritos por segundo
ReadsPerSecond | Ler operações por segundo
WritesPerSecond | Escrever operações por segundo
TransfersPerSecond | Ler ou escrever operações por segundo

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

## <a name="install-and-configure-lad-40"></a>Instalar e configurar LAD 4.0

Pode instalar e configurar o LAD 4.0 no Azure CLI ou no PowerShell.

### <a name="azure-cli"></a>CLI do Azure

Se as suas definições protegidas estiverem no ficheiro *ProtectedSettings.js* e as informações de configuração pública *estiveremPublicSettings.js,* executar este comando:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

O comando pressupõe que está a utilizar o modo de Gestão de Recursos Azure do Azure CLI. Para configurar o LAD para os VMs modelos de implantação clássicos, mude para o modo "asm" ( `azure config mode asm` ) e omita o nome do grupo de recursos no comando. 

Para mais informações, consulte a [documentação do CLI de plataforma cruzada.](/cli/azure/authenticate-azure-cli)

### <a name="powershell"></a>PowerShell

Se as suas definições protegidas estiverem na variável e a `$protectedSettings` informação de configuração pública estiver na `$publicSettings` variável, execute este comando:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

## <a name="example-lad-40-configuration"></a>Configuração exemplo LAD 4.0

Com base nas definições anteriores, esta secção fornece uma configuração de extensão LAD 4.0 de amostra e alguma explicação. Para aplicar esta amostra no seu caso, use o nome da sua própria conta de armazenamento, ficha SAS da conta e fichas SAS do Event Hubs.

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

* Carregar métricas de tempo de processador por cento e métricas de espaço de disco usado para a `WADMetrics*` mesa,
* Faça upload de mensagens da instalação syslog `"user"` e gravidade `"info"` para a `LinuxSyslog*` mesa.
* Faça o upload de linhas anexadas em ficheiro `/var/log/myladtestlog` para a `MyLadTestLog` mesa.

Em cada caso, os dados também são enviados para:

* Azure Blob Storage. O nome do recipiente está definido na `JsonBlob` pia.
* Um ponto final do Event Hubs, conforme especificado na `EventHubs` pia.

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
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
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

Utilize o portal Azure para visualizar dados de desempenho ou definir alertas:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="A imagem mostra o portal Azure. É selecionado o espaço do disco usado na métrica. O gráfico resultante é mostrado.":::

Os `performanceCounters` dados são sempre armazenados numa tabela de Armazenamento Azure. As APIs de Armazenamento Azure estão disponíveis para muitos idiomas e plataformas.

Os `JsonBlob` dados enviados para as pias são armazenados em bolhas na conta de armazenamento indicada nas [definições protegidas](#protected-settings). Pode consumir os dados blob em quaisquer APIs de Armazenamento Azure Blob.

Também pode utilizar estas ferramentas de UI para aceder aos dados no Azure Storage:

* Explorador visual do servidor do estúdio
* [Explorador do Storage do Azure](https://azurestorageexplorer.codeplex.com/)

A imagem seguinte de uma sessão do Azure Storage Explorer mostra as mesas e recipientes de armazenamento Azure gerados a partir de uma extensão LAD 4.0 corretamente configurada num VM de teste. A imagem não corresponde exatamente à [configuração lad 4.0 da amostra](#example-lad-40-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="A screenshot mostra Azure Storage Explorer.":::

Para obter mais informações sobre como consumir mensagens publicadas num ponto final do Event Hubs, consulte a documentação relevante do [Event Hubs](../../event-hubs/event-hubs-about.md).

## <a name="next-steps"></a>Passos seguintes

* No [Azure Monitor,](../../azure-monitor/alerts/alerts-classic-portal.md)crie alertas para as métricas que recolhe.
* [Crie gráficos de monitorização](../../azure-monitor/data-platform.md) para as suas métricas.
* [Crie uma balança de máquina virtual definida](../linux/tutorial-create-vmss.md) utilizando as suas métricas para controlar a autoscalagem.