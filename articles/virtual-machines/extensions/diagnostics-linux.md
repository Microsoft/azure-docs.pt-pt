---
title: Azure Compute - Extensão de Diagnóstico Linux
description: Como configurar a Extensão de Diagnóstico Azure Linux (LAD) para recolher métricas e eventos de registo de VMs Linux em execução em Azure.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 0ae6366acf270d762b1c15563bfec1b2eb2a1b8d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421078"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Using Linux Diagnostic Extension to monitor metrics and logs (Utilizar a Extensão de Diagnóstico do Linux para monitorizar métricas e registos)

Este documento descreve a versão 3.0 e a mais recente da extensão de diagnóstico linux.

> [!IMPORTANT]
> Para obter informações sobre a versão 2.3 ou mais antiga, consulte [este documento](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Introdução

A extensão de diagnóstico Linux ajuda um utilizador a monitorizar a saúde de um Linux VM em execução no Microsoft Azure. Tem as seguintes capacidades:

* Recolhe métricas de desempenho do sistema a partir do VM e armazena-as numa tabela específica numa conta de armazenamento designada.
* Recupera eventos de registo do syslog e armazena-os numa tabela específica na conta de armazenamento designada.
* Permite que os utilizadores personalizem as métricas de dados que são recolhidas e carregadas.
* Permite aos utilizadores personalizar as instalações do syslog e os níveis de gravidade dos eventos que são recolhidos e carregados.
* Permite que os utilizadores carreguem ficheiros de registo especificados para uma tabela de armazenamento designada.
* Suporta o envio de métricas e eventos de registo para pontos finais arbitrários do EventHub e bolhas com formação JSON na conta de armazenamento designada.

Esta extensão funciona com ambos os modelos de implantação Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalar a extensão na sua VM

Pode ativar esta extensão utilizando os cmdlets Azure PowerShell, scripts Azure CLI, modelos ARM ou o portal Azure. Para obter mais informações, consulte [as funcionalidades de extensões.](features-linux.md)

>[!NOTE]
>Certos componentes da extensão VM de Diagnóstico também são enviados na [extensão VM Log Analytics](./oms-linux.md). Devido a esta arquitetura, podem surgir conflitos se ambas as extensões forem instantâneas no mesmo modelo ARM. Para evitar estes conflitos de tempo de instalação, utilize a [ `dependsOn` diretiva](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) para garantir que as extensões são instaladas sequencialmente. As extensões podem ser instaladas em qualquer ordem.

Estas instruções de instalação e uma [configuração de configuração de amostra transferível](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configuram LAD 3.0 para:

* capturar e armazenar as mesmas métricas que foram fornecidas pela LAD 2.3;
* capturar um conjunto útil de métricas do sistema de ficheiros, novas para LAD 3.0;
* capturar a coleção de syslog padrão ativada por LAD 2.3;
* permitir a experiência do portal Azure para o charting e alerta nas métricas VM.

A configuração transferível é apenas um exemplo; modificá-lo de acordo com as suas próprias necessidades.

### <a name="supported-linux-distributions"></a>Distribuição apoiada do Linux

A Extensão de Diagnóstico Linux suporta as seguintes distribuições e versões. A lista de distribuições e versões aplica-se apenas às imagens do fornecedor Linux, apoiadas pelo Azure. Imagens BYOL e BYOS de terceiros, como aparelhos, geralmente não são suportadas para a extensão de diagnóstico Linux.

Uma distribuição que lista apenas versões importantes, como Debian 7, também é suportada para todas as versões menores. Se for especificada uma versão menor específica, apenas essa versão específica é suportada; se "+" for anexado, são suportadas versões menores iguais ou superiores à versão especificada.

Distribuições e versões suportadas:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- RHEL 7, 6.7+

### <a name="prerequisites"></a>Pré-requisitos

* **Azure Linux Agent versão 2.2.0 ou posterior**. A maioria das imagens da galeria Azure VM Linux incluem a versão 2.2.7 ou mais tarde. Corra `/usr/sbin/waagent -version` para confirmar a versão instalada no VM. Se o VM estiver a executar uma versão mais antiga do agente convidado, siga [estas instruções](./update-linux-agent.md) para atualizá-lo.
* **Azure CLI**. [Confende o ambiente Azure CLI](/cli/azure/install-azure-cli) na sua máquina.
* O comando wget, se ainda não o tem: `sudo apt-get install wget` Corra.
* Uma subscrição Azure existente e uma conta de armazenamento de finalidade geral existente para armazenar os dados em.  Contas de armazenamento de finalidade geral suportam armazenamento de mesa que é necessário.  Uma conta de armazenamento Blob não funcionará.
* Python 2

### <a name="python-requirement"></a>Requisito de Python

A extensão de diagnóstico Linux requer Python 2. Se a sua máquina virtual estiver a utilizar um distro que não inclua python 2 por defeito, então deve instalá-lo. Os seguintes comandos de amostra instalarão Python 2 em diferentes distros.    

 - Chapéu Vermelho, CentOS, Oráculo: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

O python2 executável deve ser aliasado a *pitão.* Segue-se um método que pode utilizar para definir este pseudónimo:

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
> Para qualquer uma das amostras, preencha os valores corretos para as variáveis na primeira secção antes de correr. 

A configuração da amostra descarregada nestes exemplos recolhe um conjunto de dados padrão e envia-os para o armazenamento de mesas. O URL para a configuração da amostra e o seu conteúdo estão sujeitos a alterações. Na maioria dos casos, deve descarregar uma cópia do ficheiro JSON de definições de portal e personalizá-lo para as suas necessidades, em seguida, ter quaisquer modelos ou automatização que construa usar a sua própria versão do ficheiro de configuração em vez de descarregar esse URL cada vez.

#### <a name="azure-cli-sample"></a>Amostra de Azure CLI

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-30-extension-on-the-vmss-instance"></a>Amostra Azure CLI para instalação da extensão LAD 3.0 na instância VMSS

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
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

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Atualizar as definições de extensão

Depois de alterar as definições Protegidas ou Públicas, coloque-as no VM executando o mesmo comando. Se algo alterado nas definições, as definições atualizadas são enviadas para a extensão. O LAD recarrega a configuração e reinicia-se sozinho.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migração de versões anteriores da extensão

A versão mais recente da extensão é **3.0**. **Quaisquer versões antigas (2.x) são depreciadas e podem não ser publicadas em ou após 31 de julho de 2018**.

> [!IMPORTANT]
> Esta extensão introduz alterações de rutura na configuração da extensão. Uma dessas alterações foi feita para melhorar a segurança da extensão; como resultado, a retrocompatibilidade com 2.x não pôde ser mantida. Além disso, o Editor de Extensão para esta extensão é diferente do editor para as versões 2.x.
>
> Para migrar de 2.x para esta nova versão da extensão, deve desinstalar a antiga extensão (sob o nome da antiga editora), e depois instalar a versão 3 da extensão.

Recomendações:

* Instale a extensão com a atualização automática de versão menor ativada.
  * No modelo de implementação clássico VMs, especifique '3.*' como a versão se estiver a instalar a extensão através do Azure XPLAT CLI ou powershell.
  * No modelo de implementação do Azure Resource Manager, os VMs incluem "autoUpgradeMinorVersion": verdadeiros no modelo de implementação VM.
* Utilize uma conta de armazenamento nova/diferente para LAD 3.0. Existem várias pequenas incompatibilidades entre LAD 2.3 e LAD 3.0 que tornam a partilha de uma conta problemática:
  * LAD 3.0 armazena eventos de syslog numa mesa com um nome diferente.
  * As cordas do contraspecificador para `builtin` métricas diferem em LAD 3.0.

## <a name="protected-settings"></a>Configurações protegidas

Este conjunto de informações de configuração contém informações sensíveis que devem ser protegidas da opinião pública, por exemplo, credenciais de armazenamento. Estas definições são transmitidas e armazenadas pela extensão sob forma encriptada.

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
armazenamentoAccountEndPoint | (opcional) O ponto final identificando a nuvem em que a conta de armazenamento existe. Se esta definição estiver ausente, o LAD desrescume da nuvem pública Azure, `https://core.windows.net` . Para utilizar uma conta de armazenamento na Alemanha Azure, no Governo Azure ou na Azure China, decidiu esse valor em conformidade.
armazenamentoSasToken | Um [token SAS de conta](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) para serviços blob e table `ss='bt'` (), aplicável a recipientes e objetos ( `srt='co'` ), que concede adicionar, criar, listar, atualizar e escrever permissões ( `sp='acluw'` ). *Não* inclua o ponto de interrogação principal (?).
mdsdHttpProxy | (opcional) HTTP proxy informações necessárias para permitir a extensão de ligar à conta de armazenamento especificada e ao ponto final.
afundaConfig | (opcional) Detalhes de destinos alternativos aos quais métricas e eventos podem ser entregues. Os detalhes específicos de cada sumidouro de dados suportado pela extensão estão abrangidos pelas secções que se seguem.

Para obter um token SAS dentro de um modelo de Gestor de Recursos, utilize a função **listAccountSas.** Para um modelo de exemplo, consulte [exemplo de função lista](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Você pode facilmente construir o token SAS necessário através do portal Azure.

1. Selecione a conta de armazenamento para fins gerais para a qual deseja que a extensão escreva
1. Selecione "Assinatura de acesso partilhado" a partir da parte Definições do menu esquerdo
1. Faça as secções apropriadas como descrito anteriormente
1. Clique no botão "Gerar SAS".

![A screenshot mostra a página de assinatura de acesso compartilhado com Generate S A S.](./media/diagnostics-linux/make_sas.png)

Copie o SAS gerado no campo de armazenamentoSasToken; remover o ponto de interrogação principal ("?").

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

Esta secção opcional define destinos adicionais para os quais a extensão envia a informação que recolhe. A matriz "pia" contém um objeto para cada pia de dados adicional. O atributo "tipo" determina os outros atributos do objeto.

Elemento | Valor
------- | -----
name | Uma corda usada para se referir a esta pia em outro lugar na configuração da extensão.
tipo | O tipo de pia a ser definida. Determina os outros valores (se houver) em casos deste tipo.

A versão 3.0 da Extensão de Diagnóstico Linux suporta dois tipos de pia: EventHub e JsonBlob.

#### <a name="the-eventhub-sink"></a>A pia eventHub

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

A entrada "sasURL" contém o URL completo, incluindo o token SAS, para o Centro de Eventos para o qual os dados devem ser publicados. Lad requer um SAS nomeando uma política que permite a reclamação enviar. Um exemplo:

* Criar um espaço de nomes de Centros de Eventos chamado `contosohub`
* Criar um Centro de Eventos no espaço de nomes chamado `syslogmsgs`
* Criar uma política de acesso compartilhado no Centro de Eventos nomeado `writer` que permite a reclamação Enviar

Se criou um SAS bom até à meia-noite utc em 1 de janeiro de 2018, o valor sasURL pode ser:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Para obter mais informações sobre a geração e recuperação de informações sobre fichas SAS para Centros de Eventos, consulte [esta página web](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>A pia JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Os dados direcionados para uma pia JsonBlob são armazenados em bolhas no armazenamento Azure. Cada instância de LAD cria uma bolha a cada hora para cada nome da pia. Cada bolha contém sempre uma matriz de objeto JSON sintaticamente válida. Novas entradas são adicionadas atomicamente à matriz. As bolhas são armazenadas num recipiente com o mesmo nome da pia. As regras de armazenamento Azure para nomes de recipientes blob aplicam-se aos nomes dos lavatórios JsonBlob: entre 3 e 63 caracteres ASCII alfanuméricos inferiores ou traços.

## <a name="public-settings"></a>Cenários públicos

Esta estrutura contém vários blocos de configurações que controlam as informações recolhidas pela extensão. Cada definição é opcional. Se `ladCfg` especificar, também deve especificar `StorageAccount` .

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
StorageAccount | O nome da conta de armazenamento em que os dados são escritos pela extensão. Deve ter o mesmo nome especificado nas [definições protegidas](#protected-settings).
mdsdHttpProxy | (opcional) O mesmo que nas [definições protegidas](#protected-settings). O valor público é ultrapassado pelo valor privado, se definido. Coloque as definições de procuração que contenham um segredo, como uma palavra-passe, nas [definições protegidas](#protected-settings).

Os restantes elementos são descritos em pormenor nas seguintes secções.

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

Esta estrutura opcional controla a recolha de métricas e registos para entrega ao serviço Azure Metrics e a outros sumidouros de dados. Deve especificar um `performanceCounters` ou `syslogEvents` ambos. Tem de especificar a `metrics` estrutura.

Elemento | Valor
------- | -----
eventVolume | (opcional) Controla o número de divisórias criadas dentro da mesa de armazenamento. Deve ser um `"Large"` `"Medium"` dos, ou `"Small"` . Se não for especificado, o valor predefinido é `"Medium"` .
sampleRateInSeconds | (opcional) O intervalo padrão entre a recolha de métricas cruas (não agregadas). A menor taxa de amostra suportada é de 15 segundos. Se não for especificado, o valor predefinido é `15` .

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
resourceId | O ID de recurso do Gestor de Recursos Azure do VM ou da balança de máquina virtual a que o VM pertence. Esta definição também deve ser especificada se for utilizada qualquer pia JsonBlob na configuração.
programadoTransferPeriod | A frequência em que as métricas agregadas devem ser calculadas e transferidas para a Azure Metrics, expressa como intervalo de tempo IS 8601. O menor período de transferência é de 60 segundos, ou seja, PT1M. Deve especificar pelo menos umPeriod de Transporte programado.

As amostras das métricas especificadas na secção PerformanceCounters são recolhidas a cada 15 segundos ou à taxa de amostra explicitamente definida para o contador. Se aparecerem várias frequências dePeriodperiod programados (como no exemplo), cada agregação é calculada de forma independente.

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

Esta secção opcional controla a recolha de métricas. As amostras brutas são agregadas para cada [11100transferperiod](#metrics) para produzir estes valores:

* mean
* mínimo
* máximo
* valor recolhido pela última vez
* contagem de amostras cruas usadas para calcular o agregado

Elemento | Valor
------- | -----
pias | (opcional) Uma lista separada por vírgulas de nomes de pias para as quais o LAD envia resultados métricos agregados. Todas as métricas agregadas são publicadas em cada pia listada. Ver [piasConfig.](#sinksconfig) Exemplo: `"EHsink1, myjsonsink"`.
tipo | Identifica o fornecedor real da métrica.
classe | Juntamente com o "balcão", identifica a métrica específica dentro do espaço de nome do fornecedor.
counter | Juntamente com a "classe", identifica a métrica específica dentro do espaço de nome do fornecedor.
contraSpecificador | Identifica a métrica específica dentro do espaço de nomes Azure Metrics.
condição | (opcional) Seleciona uma instância específica do objeto a que a métrica se aplica ou seleciona a agregação em todas as instâncias desse objeto. Para mais informações, consulte as `builtin` definições métricas.
sampleRate | Intervalo IS 8601 que define a taxa a que são recolhidas amostras cruas para esta métrica. Se não for definido, o intervalo de recolha é definido pelo valor da [amostraRateInSeconds](#ladcfg). A taxa de amostra suportada mais curta é de 15 segundos (PT15S).
unit | Deve ser uma destas cordas: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Millisecond". Define a unidade para a métrica. Os consumidores dos dados recolhidos esperam que os valores dos dados recolhidos correspondam a esta unidade. Lad ignora este campo.
displayName | O rótulo (na língua especificada pela definição local associada) a ser anexado a estes dados em Azure Metrics. Lad ignora este campo.

O contraSpecifier é um identificador arbitrário. Os consumidores de métricas, como o recurso de gráfico e alerta do portal Azure, utilizam o contraSpecifier como a "chave" que identifica uma métrica ou um caso de uma métrica. Para `builtin` métricas, recomendamos que utilize valores contraSpecifier que comecem por `/builtin/` . Se estiver a recolher uma instância específica de uma métrica, recomendamos que anexe o identificador da instância ao valor do contadorSes. Alguns exemplos:

* `/builtin/Processor/PercentIdleTime` - Tempo inativo mediado em todos os vCPUs
* `/builtin/Disk/FreeSpace(/mnt)` - Espaço gratuito para o sistema de ficheiros /mnt
* `/builtin/Disk/FreeSpace` - Espaço livre mediado em todos os sistemas de ficheiros montados

Nem o LAD nem o portal Azure esperam que o valor do contraSpecifier corresponda a qualquer padrão. Seja consistente na forma como constrói valores de contraspecifier.

Quando `performanceCounters` especifica, a LAD escreve sempre dados para uma tabela no armazenamento Azure. Pode ter os mesmos dados escritos para as bolhas JSON e/ou Centros de Eventos, mas não é possível desativar os dados de armazenamento numa tabela. Todas as instâncias da extensão de diagnóstico configuradas para usar o mesmo nome de conta de armazenamento e ponto final adicionam as suas métricas e troncos à mesma tabela. Se muitos VMs estiverem escrevendo para a mesma divisória de mesa, Azure pode acelerar escreve para essa partição. A definição de eventoVolume faz com que as entradas sejam distribuídas por 1 (Pequena), 10 (Média) ou 100 (Grandes) diferentes divisórias. Normalmente, "Médio" é suficiente para garantir que o tráfego não seja acelerado. A funcionalidade Azure Metrics do portal Azure utiliza os dados desta tabela para produzir gráficos ou para desencadear alertas. O nome da mesa é a concatenação destas cordas:

* `WADMetrics`
* O "ProgramadoTransferPeriod" para os valores agregados armazenados na tabela
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

Esta secção opcional controla a recolha de eventos de log a partir de syslog. Se a secção for omitida, os eventos de syslog não são capturados.

A coleção syslogEventConfiguration tem uma entrada para cada facilidade de interesse syslog. Se a minSeverity for "NENHUMA" para uma determinada instalação, ou se essa instalação não aparecer no elemento, não são capturados quaisquer eventos dessa instalação.

Elemento | Valor
------- | -----
pias | Uma lista separada por vírgulas de nomes de pias para as quais são publicados eventos individuais de registo. Todos os eventos de registo correspondentes às restrições no syslogEventConfiguration são publicados em cada pia listada. Exemplo: "EHforsyslog"
nome de facilidades | Um nome de instalação de syslog (como "LOG \_ USER" ou "LOG \_ LOCAL0"). Consulte a secção "facility" da [página do homem syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para a lista completa.
minSeverity | Um nível de severidade de syslog (como "LOG \_ ERR" ou "LOG \_ INFO"). Consulte a secção "nível" da [página do homem syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para a lista completa. A extensão captura eventos enviados para a instalação a um nível ou superior ao especificado.

Quando `syslogEvents` especifica, a LAD escreve sempre dados para uma tabela no armazenamento Azure. Pode ter os mesmos dados escritos para as bolhas JSON e/ou Centros de Eventos, mas não é possível desativar os dados de armazenamento numa tabela. O comportamento de partição desta tabela é o mesmo que descrito para `performanceCounters` . O nome da mesa é a concatenação destas cordas:

* `LinuxSyslog`
* Uma data, no formulário "YYYYMMDD", que muda a cada 10 dias

Exemplos incluem `LinuxSyslog20170410` `LinuxSyslog20170609` e.

### <a name="perfcfg"></a>perfCfg

Esta secção opcional controla a execução de consultas arbitrárias [de OMI.](https://github.com/Microsoft/omi)

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
espaço de nomes | (opcional) O espaço de nome OMI dentro do qual a consulta deve ser executada. Se não for especificado, o valor padrão é "raiz/scx", implementado pelos [Fornecedores de plataformas cruzadas do System Center](https://github.com/Microsoft/SCXcore).
consulta | A consulta da OMI a ser executada.
mesa | (opcional) A tabela de armazenamento Azure, na conta de armazenamento designada (ver [definições protegidas).](#protected-settings)
frequência | (opcional) O número de segundos entre a execução da consulta. O valor predefinido é de 300 (5 minutos); o valor mínimo é de 15 segundos.
pias | (opcional) Deve ser publicada uma lista separada de vírgulas de sumidouros adicionais aos quais devem ser publicados resultados da amostra bruta. Nenhuma agregação destas amostras cruas é calculada pela extensão ou pela Azure Metrics.

Ou "mesa" ou "pias", ou ambos, devem ser especificados.

### <a name="filelogs"></a>arquivos

Controla a captura de ficheiros de registo. Lad captura novas linhas de texto à medida que são escritas para o ficheiro e as escreve para linhas de mesa e/ou quaisquer pias especificadas (JsonBlob ou EventHub).

> [!NOTE]
> os filelogs são capturados por um subcomponente da LAD chamado `omsagent` . Para recolher os ficheirosLogia, deve certificar-se de que o `omsagent` utilizador leu permissões nos ficheiros que especifica, bem como executar permissões em todos os diretórios no caminho para esse ficheiro. Pode verificar isto correndo `sudo su omsagent -c 'cat /path/to/file'` depois da instalação do LAD.

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
file | O nome de caminho completo do ficheiro de registo a ser observado e capturado. O nome de pathname deve nomear um único ficheiro; não pode nomear um diretório ou conter wildcards. A conta de utilizador 'omsagent' deve ter lido o acesso à via do ficheiro.
mesa | (opcional) A tabela de armazenamento Azure, na conta de armazenamento designada (conforme especificado na configuração protegida), na qual são escritas novas linhas a partir da "cauda" do ficheiro.
pias | (opcional) Uma lista separada por vírgulas de nomes de pias adicionais para as quais as linhas de registo enviadas.

Ou "mesa" ou "pias", ou ambos, devem ser especificados.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas suportadas pelo fornecedor de builtin

O fornecedor de métricas builtin é uma fonte de métricas mais interessantes para um conjunto amplo de utilizadores. Estas métricas caem em cinco classes amplas:

* Processador
* Memória
* Rede
* Filesystem
* Disco

### <a name="builtin-metrics-for-the-processor-class"></a>métricas de builtin para a classe processador

A classe de métricas do processador fornece informações sobre a utilização do processador no VM. Ao agregar percentagens, o resultado é a média em todas as CPUs. Num VM de dois vCPU, se um vCPU estivesse 100% ocupado e o outro estivesse 100% inativo, o PercentIdleTime reportado seria 50. Se cada vCPU estivesse 50% ocupado durante o mesmo período, o resultado reportado também seria de 50. Num VM de quatro vCPU, com um vCPU 100% ocupado e os outros inativos, o PercentIdleTime reportado seria 75.

counter | Significado
------- | -------
PercentIdleTime | Percentagem de tempo durante a janela de agregação que os processadores estavam executando o laço de idle kernel
PercentProcessorTime | Percentagem de tempo executando um fio não ocioso
PercentIOWaitTime | Percentagem de tempo à espera que as operações de IO completem
PercentInterruptTime | Percentagem de tempo de execução de interrupções de hardware/software e DPCs (chamadas de procedimento diferido)
PercentUserTime | De tempo não inativo durante a janela de agregação, a percentagem de tempo gasto no utilizador mais na prioridade normal
PercentNiceTime | De tempo não inativo, a percentagem gasta na prioridade reduzida (nice)
Tempo de Prioridades | De tempo não inativo, a percentagem gasta em modo privilegiado (kernel)

Os primeiros quatro balcões devem chegar a 100%. Os últimos três balcões também somam 100%; subdividem a soma de PercentProcessorTime, PercentIOWaitTime e PercentInterruptTime.

Para obter uma única métrica agregada em todos os processadores, definir `"condition": "IsAggregate=TRUE"` . Para obter uma métrica para um processador específico, como o segundo processador lógico de um VM de quatro vCPU, definido `"condition": "Name=\\"1\\""` . Os números lógicos do processador estão na `[0..n-1]` gama.

### <a name="builtin-metrics-for-the-memory-class"></a>métricas de construção para a classe Memória

A classe memory das métricas fornece informações sobre a utilização da memória, o paging e a troca.

counter | Significado
------- | -------
DisponívelMemory | Memória física disponível em MiB
Por cento DisponívelMemory | Memória física disponível como uma por cento da memória total
UsadoMemory | Memória física em uso (MiB)
PercentUsedMemory | Memória física em uso como uma por cento da memória total
PagesPerSec | Paging total (ler/escrever)
PagesReadPerSec | Páginas lidas a partir de loja de apoio (ficheiro de troca, ficheiro de programa, ficheiro mapeado, etc.)
PagesWrittenPerSec | Páginas escritas para loja de suporte (ficheiro de troca, ficheiro mapeado, etc.)
DisponívelSwap | Espaço de troca não reutilizado (MiB)
Por cento DisponívelSwap | Espaço de troca não reutilizado em percentagem do swap total
Usswap | Espaço de troca em uso (MiB)
PercentUsedSwap | Espaço de troca em uso em percentagem do swap total

Esta classe de métricas tem apenas um único exemplo. O atributo "condição" não tem configurações úteis e deve ser omitido.

### <a name="builtin-metrics-for-the-network-class"></a>métricas de builtin para a classe Rede

A classe de métricas da Rede fornece informações sobre a atividade da rede em interfaces de rede individuais desde o arranque. Lad não expõe métricas de largura de banda, que podem ser recuperadas a partir de métricas hospedeiras.

counter | Significado
------- | -------
BytesTransmitted | Total de bytes enviados desde a bota
BytesReceived | Total de bytes recebidos desde o arranque
BytesTotal | Total de bytes enviados ou recebidos desde o arranque
PacotesTransmed | Total de pacotes enviados desde a arranque
Pacotes Reeceu | Total de pacotes recebidos desde o arranque
TotalRxErrors | Número de erros de receção desde o arranque
TotalTxErrors | Número de erros de transmissão desde o arranque
TotalCollisions | Número de colisões reportadas pelas portas da rede desde o arranque

 Embora esta classe seja exemplo, a LAD não suporta a captura de métricas de rede agregadas em todos os dispositivos de rede. Para obter as métricas para uma interface específica, como a eth0, definir `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-filesystem-class"></a>métricas de builtin para a classe Filesystem

A classe de métricas do Sistema de Ficheiros fornece informações sobre a utilização do sistema de ficheiros. Os valores absolutos e percentuais são reportados como seriam apresentados a um utilizador comum (não raiz).

counter | Significado
------- | -------
EspaçoLivre | Espaço disponível em disco em bytes
Espaço Usado | Espaço de disco usado em bytes
PercentFreeSpace | Espaço livre percentual
Espaço Por cento | Percentagem de espaço usado
PercentFreeInodes | Percentagem de inodes não reutilizados
PercentUsedInodes | Percentagem de inodes atribuídos (em uso) resumidos em todos os sistemas de ficheiros
BytesReadPerSecond | Bytes ler por segundo
BytesWrittenPerSecond | Bytes escritos por segundo
BytesPerSecond | Bytes lidos ou escritos por segundo
ReadsPerSecond | Ler operações por segundo
WritesPerSecond | Escrever operações por segundo
TransfersPerSecond | Ler ou escrever operações por segundo

Os valores agregados em todos os sistemas de ficheiros podem ser obtidos por definição `"condition": "IsAggregate=True"` . Os valores de um sistema de ficheiros montado específico, como "/mnt", podem ser obtidos por definição `"condition": 'Name="/mnt"'` . 

**NOTA:** Se utilizar o Portal Azure em vez de JSON, o formulário de campo de condição correta é Name='/mnt'

### <a name="builtin-metrics-for-the-disk-class"></a>métricas de builtin para a classe Disco

A classe disco de métricas fornece informações sobre o uso do dispositivo de disco. Estas estatísticas aplicam-se a toda a unidade. Se existirem vários sistemas de ficheiros num dispositivo, os contadores para este dispositivo são, efetivamente, agregados em todos eles.

counter | Significado
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

Os valores agregados em todos os discos podem ser obtidos através da definição `"condition": "IsAggregate=True"` . Para obter informações sobre um dispositivo específico (por exemplo, /dev/sdf1), definir `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="installing-and-configuring-lad-30"></a>Instalação e configuração LAD 3.0

### <a name="azure-cli"></a>CLI do Azure

Assumindo que as suas definições protegidas estão no ficheiro ProtectedSettings.jse as informações de configuração pública estão em PublicSettings.js, execute este comando:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

O comando pressupõe que está a utilizar o modo de Gestão de Recursos Azure do Azure CLI. Para configurar o LAD para o modelo clássico de implementação (ASM) VMs, mude para o modo "asm" ( `azure config mode asm` ) e omita o nome do grupo de recursos no comando. Para mais informações, consulte a [documentação do CLI de plataforma cruzada.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

### <a name="powershell"></a>PowerShell

Assumindo que as suas definições protegidas estão na variável e que a `$protectedSettings` sua informação de configuração pública está na `$publicSettings` variável, execute este comando:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="an-example-lad-30-configuration"></a>Uma configuração de exemplo LAD 3.0

Com base nas definições anteriores, aqui está uma configuração de extensão LAD 3.0 com alguma explicação. Para aplicar esta amostra no seu caso, deverá utilizar o nome da sua própria conta de armazenamento, ficha SAS e fichas SAS do EventHubs.

> [!NOTE]
> Dependendo se utiliza o Azure CLI ou o PowerShell para instalar o LAD, o método para fornecer configurações públicas e protegidas diferirá. Se utilizar o CLI Azure, guarde as seguintes definições para ProtectedSettings.jse PublicSettings.jspara utilizar com o comando da amostra acima. Se utilizar o PowerShell, guarde as definições de e para `$protectedSettings` `$publicSettings` o funcionamento `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Configurações protegidas

Estas configurações protegidas configuram:

* uma conta de armazenamento
* uma conta correspondente SAS token
* vários lavatórios (JsonBlob ou EventHubs com fichas SAS)

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

### <a name="public-settings"></a>Configurações Públicas

Estas configurações públicas fazem com que o LAD:

* Carregar percent-processador-tempo e métricas de espaço de disco usado para a `WADMetrics*` mesa
* Enviar mensagens do syslog facility "user" e severidade "info" para a `LinuxSyslog*` tabela
* Faça o upload dos resultados da consulta OMI (PercentProcessorTime e PercentIdleTime) para a tabela nomeada `LinuxCPU`
* Carregar linhas anexadas em arquivo `/var/log/myladtestlog` para a `MyLadTestLog` mesa

Em cada caso, os dados também são enviados para:

* Armazenamento Azure Blob (o nome do recipiente é definido na pia JsonBlob)
* Ponto final do EventHubs (conforme especificado na pia eventHubs)

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

* As métricas da plataforma Azure, que estão a traçar e a alertar, conhecem os recursos do VM em que estás a trabalhar. Espera encontrar os dados para o seu VM utilizando os recursosId a chave de procura.
* Se utilizar a autoescala Azure, os recursosid na configuração de autoescala devem corresponder aos recursosidários utilizados pela LAD.
* O recursoId é incorporado nos nomes de JsonBlobs escritos pela LAD.

## <a name="view-your-data"></a>Ver os dados

Utilize o portal Azure para visualizar dados de desempenho ou definir alertas:

![A screenshot mostra o portal Azure com o espaço do disco usado na métrica selecionada e no gráfico resultante.](./media/diagnostics-linux/graph_metrics.png)

Os `performanceCounters` dados são sempre armazenados numa tabela de Armazenamento Azure. As APIs de Armazenamento Azure estão disponíveis para muitos idiomas e plataformas.

Os dados enviados para as pias JsonBlob são armazenados em bolhas na conta de armazenamento indicada nas [definições protegidas](#protected-settings). Pode consumir os dados blob usando quaisquer APIs de armazenamento de blob Azure.

Além disso, pode utilizar estas ferramentas de UI para aceder aos dados no Azure Storage:

* Explorador visual do servidor do estúdio.
* [A screenshot mostra contentores e mesas no Azure Storage Explorer.](https://azurestorageexplorer.codeplex.com/ "Explorador do Storage do Azure")

Esta imagem de uma sessão do Microsoft Azure Storage Explorer mostra as mesas e recipientes de armazenamento Azure gerados a partir de uma extensão LAD 3.0 corretamente configurada num VM de teste. A imagem não corresponde exatamente à [configuração lad 3.0](#an-example-lad-30-configuration)da amostra .

![image](./media/diagnostics-linux/stg_explorer.png)

Consulte a documentação relevante do [EventHubs](../../event-hubs/event-hubs-about.md) para aprender a consumir mensagens publicadas num ponto final do EventHubs.

## <a name="next-steps"></a>Passos seguintes

* Crie alertas métricos no [Azure Monitor](../../azure-monitor/platform/alerts-classic-portal.md) para as métricas que recolhe.
* Crie [gráficos de monitorização](../../azure-monitor/platform/data-platform.md) para as suas métricas.
* Aprenda a [criar um conjunto de balança de máquina virtual](../linux/tutorial-create-vmss.md) utilizando as suas métricas para controlar a autoscalagem.
