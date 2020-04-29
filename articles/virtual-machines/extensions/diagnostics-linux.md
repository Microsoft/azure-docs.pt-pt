---
title: Azure Compute - Extensão de Diagnóstico linux
description: Como configurar a Extensão de Diagnóstico Azure Linux (LAD) para recolher métricas e registar eventos de VMs Linux em Execução em Azure.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 7a7c1af1193ba391550438229a22c4a8c116e6be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289180"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Using Linux Diagnostic Extension to monitor metrics and logs (Utilizar a Extensão de Diagnóstico do Linux para monitorizar métricas e registos)

Este documento descreve a versão 3.0 e o mais recente da Extensão de Diagnóstico linux.

> [!IMPORTANT]
> Para obter informações sobre a versão 2.3 ou mais, consulte [este documento.](../linux/classic/diagnostic-extension-v2.md)

## <a name="introduction"></a>Introdução

A Extensão de Diagnóstico Linux ajuda um utilizador a monitorizar a saúde de um VM Linux em execução no Microsoft Azure. Tem as seguintes capacidades:

* Recolhe métricas de desempenho do sistema a partir do VM e armazena-as numa tabela específica numa conta de armazenamento designada.
* Recupera eventos de log a partir de syslog e armazena-os numa tabela específica na conta de armazenamento designada.
* Permite que os utilizadores personalizem as métricas de dados que são recolhidas e carregadas.
* Permite que os utilizadores personalizem as instalações de syslog e os níveis de gravidade dos eventos que são recolhidos e carregados.
* Permite que os utilizadores carreguem ficheiros de registo especificados para uma tabela de armazenamento designada.
* Suporta o envio de métricas e eventos de registo para pontos finais arbitrários do EventHub e bolhas formatadas jSON na conta de armazenamento designada.

Esta extensão funciona com ambos os modelos de implantação Azure.

## <a name="installing-the-extension-in-your-vm"></a>Installing the extension in your VM (Instalar a extensão na sua VM)

Pode ativar esta extensão utilizando os cmdlets Azure PowerShell, scripts Azure CLI, modelos ARM ou o portal Azure. Para mais informações, consulte funcionalidades de [extensões](features-linux.md).

Estas instruções de instalação e uma [configuração de amostra supressorconfigurar](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) LAD 3.0 para:

* capturar e armazenar as mesmas métricas que foram fornecidas pelo LAD 2.3;
* capturar um conjunto útil de métricas do sistema de ficheiros, novos para LAD 3.0;
* capturar a coleção de sislog predefinido ativada por LAD 2.3;
* permitir a experiência do portal Azure para cartografar e alertar sobre métricas VM.

A configuração transferível é apenas um exemplo; modificá-lo de acordo com as suas próprias necessidades.

### <a name="prerequisites"></a>Pré-requisitos

* **Azure Linux Agent versão 2.2.0 ou mais tarde**. A maioria das imagens da galeria Azure VM Linux incluem a versão 2.2.7 ou posterior. Executar `/usr/sbin/waagent -version` para confirmar a versão instalada no VM. Se o VM estiver a executar uma versão mais antiga do agente convidado, siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) para atualizá-lo.
* **Azure CLI.** [Instale o ambiente Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na sua máquina.
* O comando wget, se ainda não o `sudo apt-get install wget`tiver: Corra .
* Uma subscrição Azure existente e uma conta de armazenamento existente no seu interior para armazenar os dados.
* A lista de distribuições linux suportadas está emhttps://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Instalação de amostras

Preencha os valores corretos para as variáveis na primeira secção antes de correr:

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

A configuração da amostra descarregada nestes exemplos recolhe um conjunto de dados padrão e envia-os para armazenamento de mesa. O URL para a configuração da amostra e o seu conteúdo estão sujeitos a alterações. Na maioria dos casos, deve descarregar uma cópia do ficheiro JSON de definições do portal e personalizá-lo para as suas necessidades, depois ter quaisquer modelos ou automação que construa use a sua própria versão do ficheiro de configuração em vez de descarregar esse URL de cada vez.

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
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Atualizar as definições de extensão

Depois de ter alterado as definições protegidas ou públicas, desemperte-as para o VM executando o mesmo comando. Se algo alterado nas definições, as definições atualizadas são enviadas para a extensão. Lad recarrega a configuração e reinicia-se.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migração de versões anteriores da extensão

A versão mais recente da extensão é **3.0**. **Quaisquer versões antigas (2.x) são depreciadas e podem não ser publicadas em ou depois**de 31 de julho de 2018 .

> [!IMPORTANT]
> Esta extensão introduz alterações de rutura na configuração da extensão. Foi feita uma dessas alterações para melhorar a segurança da extensão; como resultado, a compatibilidade para trás com 2.x não podia ser mantida. Além disso, o Editor de Extensão para esta extensão é diferente do editor para as versões 2.x.
>
> Para migrar de 2.x para esta nova versão da extensão, deve desinstalar a antiga extensão (sob o nome de editor antigo), em seguida, instalar a versão 3 da extensão.

Recomendações:

* Instale a extensão com atualização automática de versão menor ativada.
  * Nos VMs modelo de implementação clássico, especifique '3.*' como versão se estiver a instalar a extensão através do Azure XPLAT CLI ou powershell.
  * No modelo de implementação do Gestor de Recursos Azure, incluem "autoUpgradeMinorVersion": verdadeiro" no modelo de implementação vm.
* Utilize uma conta de armazenamento nova/diferente para LAD 3.0. Existem várias pequenas incompatibilidades entre lad 2.3 e LAD 3.0 que tornam a partilha de uma conta problemática:
  * LAD 3.0 armazena eventos syslog numa mesa com um nome diferente.
  * As cordas contraEspecificadoras para `builtin` métricas diferem em LAD 3.0.

## <a name="protected-settings"></a>Definições protegidas

Este conjunto de informações de configuração contém informações sensíveis que devem ser protegidas da opinião pública, por exemplo, credenciais de armazenamento. Estas definições são transmitidas e armazenadas pela extensão de forma encriptada.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Nome | Valor
---- | -----
storageAccountName | O nome da conta de armazenamento em que os dados são escritos pela extensão.
armazenamentoAccountEndPoint | (opcional) O ponto final identificando a nuvem em que a conta de armazenamento existe. Se esta definição estiver ausente, o LAD `https://core.windows.net`falha na nuvem pública de Azure, . Para utilizar uma conta de armazenamento na Azure Germany, Azure Government, ou Azure China, estabeleceu este valor em conformidade.
armazenamentoAccountSasToken | Um [token De Conta SAS](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) para`ss='bt'`serviços Blob e`srt='co'`Table (), aplicável a contentores e objetos`sp='acluw'`( ), que concede adicionar, criar, listar, atualizar e escrever permissões (). Não *not* inclua o principal ponto de interrogação (?).
mdsdHttpProxy | (opcional) HTTP informações de procuração necessárias para permitir que a extensão se conectem à conta de armazenamento especificada e ao ponto final.
piasConfig | (opcional) Detalhes de destinos alternativos aos quais as métricas e eventos podem ser entregues. Os detalhes específicos de cada sumidouro de dados suportados pela extensão estão cobertos nas secções que se seguem.

Para obter um token SAS dentro de um modelo de Gestor de Recursos, use a função **listAccountSas.** Para um modelo de exemplo, consulte o [exemplo da função lista](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Pode facilmente construir o token SAS necessário através do portal Azure.

1. Selecione a conta de armazenamento de uso geral à qual pretende que a extensão escreva
1. Selecione "Assinatura de acesso partilhado" a partir da parte definições do menu esquerdo
1. Faça as secções apropriadas como descrito anteriormente
1. Clique no botão "Gerar SAS".

![image](./media/diagnostics-linux/make_sas.png)

Copiar o SAS gerado no campo storageAccountSasToken; remover o principal ponto de interrogação ("?").

### <a name="sinksconfig"></a>piasConfig

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

Esta secção opcional define destinos adicionais para os quais a extensão envia a informação que recolhe. A matriz "pia" contém um objeto para cada pia adicional de dados. O atributo "tipo" determina os outros atributos no objeto.

Elemento | Valor
------- | -----
nome | Uma corda usada para se referir a esta pia em outro lugar na configuração da extensão.
tipo | O tipo de pia a ser definido. Determina os outros valores (se houver) em casos deste tipo.

A versão 3.0 da extensão de diagnóstico do Linux suporta dois tipos de pia: EventHub e JsonBlob.

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

A entrada "sasURL" contém o URL completo, incluindo o token SAS, para o Centro de Eventos a que os dados devem ser publicados. Lad requer um SAS nomeando uma política que permite a reclamação enviar. Um exemplo:

* Criar um espaço de nome de Hubs de Eventochamado`contosohub`
* Criar um Hub de Eventos no espaço de nome chamado`syslogmsgs`
* Criar uma política de acesso `writer` partilhado no Hub do Evento nomeado que permite a reclamação enviar

Se criou um SAS bom até à meia-noite UTC em 1 de janeiro de 2018, o valor sasURL pode ser:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Para obter mais informações sobre a geração e recuperação de informações sobre tokens SAS para Centros de Eventos, consulte [esta página web](https://docs.microsoft.com/rest/api/eventhub/generate-sas-token#powershell).

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

Os dados direcionados para um lavatório JsonBlob são armazenados em bolhas no armazenamento Azure. Cada instância de LAD cria uma bolha a cada hora para cada nome de pia. Cada bolha contém sempre uma matriz de objeto JSON sintáticamente válida. Novas entradas são atomicamente adicionadas à matriz. As bolhas são armazenadas num recipiente com o mesmo nome que a pia. As regras de armazenamento azure para os nomes de contentores blob aplicam-se aos nomes dos lavatórios JsonBlob: entre 3 e 63 caracteres alfanuméricos inferiores ou traços.

## <a name="public-settings"></a>Cenários públicos

Esta estrutura contém vários blocos de configurações que controlam as informações recolhidas pela extensão. Cada definição é opcional. Se especificar, `ladCfg`também deve `StorageAccount`especificar .

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
mdsdHttpProxy | (opcional) O mesmo que nas [definições protegidas.](#protected-settings) O valor público é ultrapassado pelo valor privado, se definido. Coloque as definições de procuração que contenham um segredo, como uma palavra-passe, nas [definições protegidas](#protected-settings).

Os elementos restantes são descritos em detalhe nas seguintes secções.

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

Esta estrutura opcional controla a recolha de métricas e registos para entrega ao serviço De Métricas Azure e a outros sumidouros de dados. Deve especificar `performanceCounters` ou `syslogEvents` ambos. Deve especificar `metrics` a estrutura.

Elemento | Valor
------- | -----
eventoVolume | (opcional) Controla o número de divisórias criadas dentro da tabela de armazenamento. Deve ser `"Large"`um `"Medium"`dos. `"Small"` Se não especificado, o `"Medium"`valor predefinido é .
sampleRateInSeconds | (opcional) O intervalo padrão entre a recolha de métricas cruas (não agregadas). A menor taxa de amostra suportada é de 15 segundos. Se não especificado, o `15`valor predefinido é .

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
resourceId | O Id de recurso do Gestor de Recursos Azure do VM ou da escala de máquina virtual a que pertence o VM. Esta definição também deve ser especificada se algum lavatório JsonBlob for utilizado na configuração.
período de transferência programado | A frequência com que as métricas agregadas devem ser calculadas e transferidas para as Métricas Azure, expressa como um intervalo de tempo IS 8601. O menor período de transferência é de 60 segundos, isto é, PT1M. Deve especificar pelo menos um Período de Transferência programado.

As amostras das métricas especificadas na secção performanceCounters são recolhidas a cada 15 segundos ou à taxa de amostra explicitamente definida para o contador. Se aparecerem várias frequências programadas do TransferPeriod (como no exemplo), cada agregação é calculada de forma independente.

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

Esta secção opcional controla a recolha de métricas. As amostras em bruto são agregadas para cada período [de transferência programado](#metrics) para produzir estes valores:

* mean
* mínimo
* máximo
* último valor recolhido
* contagem de amostras cruas usadas para calcular o agregado

Elemento | Valor
------- | -----
lava-se | (opcional) Uma lista separada de vírgulas de nomes de lavatórios para os quais o LAD envia resultados métricos agregados. Todas as métricas agregadas são publicadas em cada pia listada. Ver [piasConfig.](#sinksconfig) Exemplo: `"EHsink1, myjsonsink"`.
tipo | Identifica o provedor real da métrica.
classe | Juntamente com o "contador", identifica a métrica específica dentro do espaço de nome do fornecedor.
counter | Juntamente com a "classe", identifica a métrica específica dentro do espaço de nome do fornecedor.
contraespecificador | Identifica a métrica específica dentro do espaço de nome das Métricas Azure.
condição | (opcional) Seleciona uma instância específica do objeto a que a métrica se aplica ou seleciona a agregação em todas as instâncias desse objeto. Para mais informações, consulte as `builtin` definições métricas.
sampleRate | Intervalo IS 8601 que define a taxa a que são recolhidas amostras cruas para esta métrica. Se não for definido, o intervalo de recolha é definido pelo valor da [amostraRateInSeconds](#ladcfg). A taxa de amostra mais curta suportada é de 15 segundos (PT15S).
unit | Deve ser uma dessas cordas: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Millisecond". Define a unidade para a métrica. Os consumidores dos dados recolhidos esperam que os valores de dados recolhidos correspondam a esta unidade. Lad ignora este campo.
displayName | O rótulo (na língua especificada pela definição local associada) a ser anexado a estes dados em Métricas Azure. Lad ignora este campo.

O contra-Especificador é um identificador arbitrário. Os consumidores de métricas, como o portal Azure que mapeia e a funcionalidade de alerta, utilizam o contraespecificador como a "chave" que identifica uma métrica ou um exemplo de métrica. Para `builtin` métricas, recomendamos que utilize valores `/builtin/`contraespecificadores que começam com . Se estiver a recolher uma instância específica de uma métrica, recomendamos que prenda o identificador da instância ao valor do contraespecificador. Alguns exemplos:

* `/builtin/Processor/PercentIdleTime`- Tempo inativo em média em todos os vCPUs
* `/builtin/Disk/FreeSpace(/mnt)`- Espaço gratuito para o sistema de ficheiros /mnt
* `/builtin/Disk/FreeSpace`- Espaço livre em média em todos os sistemas de ficheiros montados

Nem o LAD nem o portal Azure esperam que o valor do contraespecificador corresponda a qualquer padrão. Seja consistente na forma como constrói valores contraespecificadores.

Quando especifica, `performanceCounters`o LAD escreve sempre dados para uma tabela no armazenamento Azure. Pode ter os mesmos dados escritos para blobs JSON e/ou Hubs de Eventos, mas não pode desativar o armazenamento de dados numa tabela. Todas as instâncias da extensão de diagnóstico configurada para usar o mesmo nome de conta de armazenamento e ponto final adicionam as suas métricas e registos à mesma tabela. Se muitos VMs estão escrevendo para a mesma divisória de mesa, Azure pode estrangular escrito para essa partição. A definição de eventosVolume faz com que as entradas sejam distribuídas por 1 (Pequena), 10 (Média) ou 100 (Grandes) divisórias diferentes. Normalmente, "Médio" é suficiente para garantir que o tráfego não é estrangulado. A funcionalidade De Métricas Azure do portal Azure utiliza os dados desta tabela para produzir gráficos ou para desencadear alertas. O nome da mesa é a concatenação destas cordas:

* `WADMetrics`
* O "Período de Transferência programado" para os valores agregados armazenados no quadro
* `P10DV2S`
* Uma data, na forma "YYYYMMDD", que muda a cada 10 dias

Exemplos `WADMetricsPT1HP10DV2S20170410` incluem e `WADMetricsPT1MP10DV2S20170609`.

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

Esta secção opcional controla a recolha de eventos de registo a partir de syslog. Se a secção for omitida, os eventos syslog não são capturados.

A coleção syslogEventConfiguration tem uma entrada para cada facilidade de interesse do syslog. Se a minSeveridade for "NENHUMA" para uma determinada instalação, ou se essa instalação não aparecer no elemento, não são capturados quaisquer eventos dessa instalação.

Elemento | Valor
------- | -----
lava-se | Uma lista separada de vírgulas de nomes de pias para os quais são publicados eventos individuais de registo. Todos os eventos de registo que correspondam às restrições em syslogEventConfiguration são publicados em cada pia listada. Exemplo: "EHforsyslog"
facilidadeNome | Um nome de instalação syslog (como\_\_"LOG USER" ou "LOG LOCAL0"). Consulte a secção "facilidade" da página do [homem syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para a lista completa.
minSeveridade | Um nível de severidade syslog (como "LOG\_ERR" ou "LOG\_INFO"). Consulte a secção "nível" da página do [homem syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para a lista completa. A extensão captura eventos enviados para a instalação a um nível ou superior ao nível especificado.

Quando especifica, `syslogEvents`o LAD escreve sempre dados para uma tabela no armazenamento Azure. Pode ter os mesmos dados escritos para blobs JSON e/ou Hubs de Eventos, mas não pode desativar o armazenamento de dados numa tabela. O comportamento de partição desta tabela `performanceCounters`é o mesmo que descrito para . O nome da mesa é a concatenação destas cordas:

* `LinuxSyslog`
* Uma data, na forma "YYYYMMDD", que muda a cada 10 dias

Exemplos `LinuxSyslog20170410` incluem e `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Esta secção opcional controla a execução de consultas arbitrárias de [OMI.](https://github.com/Microsoft/omi)

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
espaço de nomes | (opcional) O espaço de nome OMI no qual a consulta deve ser executada. Se não especificado, o valor predefinido é "raiz/scx", implementado pelos [Fornecedores de plataformas cross-plataforma do System Center](https://github.com/Microsoft/SCXcore).
consulta | A consulta da OMI a ser executada.
tabela | (opcional) O quadro de armazenamento Azure, na conta de armazenamento designada (ver [definições protegidas).](#protected-settings)
frequência | (opcional) O número de segundos entre a execução da consulta. O valor predefinido é de 300 (5 minutos); o valor mínimo é de 15 segundos.
lava-se | (opcional) Uma lista separada da vírgula de nomes de pias adicionais aos quais devem ser publicados os resultados métricos da amostra bruta. Nenhuma agregação destas amostras cruas é calculada pela extensão ou pela Azure Metrics.

Ou "mesa" ou "pias", ou ambas, devem ser especificadas.

### <a name="filelogs"></a>fileLogs

Controla a captura de ficheiros de registo. Lad captura novas linhas de texto à medida que são escritas no ficheiro e escreve-as para linhas de mesa e/ou quaisquer pias especificadas (JsonBlob ou EventHub).

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
file | O nome completo do ficheiro de registo para ser vigiado e capturado. O nome do caminho deve nomear um único ficheiro; não pode nomear um diretório ou conter wildcards.
tabela | (opcional) O quadro de armazenamento Azure, na conta de armazenamento designada (conforme especificado na configuração protegida), em que estão escritas novas linhas da "cauda" do ficheiro.
lava-se | (opcional) Uma lista separada de vírgulas de nomes de pias adicionais para as quais as linhas de registo enviadas.

Ou "mesa" ou "pias", ou ambas, devem ser especificadas.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas suportadas pelo fornecedor de construção

O fornecedor de métricas builtin é uma fonte de métricas mais interessante para um vasto conjunto de utilizadores. Estas métricas caem em cinco classes largas:

* Processador
* Memória
* Rede
* Filesystem
* Disco

### <a name="builtin-metrics-for-the-processor-class"></a>métricas de builtin para a classe Processador

A classe de métricas do processador fornece informações sobre a utilização do processador no VM. Ao agregar percentagens, o resultado é a média em todos os CPUs. Num VM de dois vCPU, se um vCPU estivesse 100% ocupado e o outro estivesse 100% inativo, o percentIdleTime reportado seria 50. Se cada vCPU estivesse 50% ocupado durante o mesmo período, o resultado reportado também seria de 50. Num VM de quatro vCPU, com um vCPU 100% ocupado e os outros inativos, o percentIdleTime reportado seria de 75.

counter | Significado
------- | -------
PercentidleTime | Percentagem de tempo durante a janela de agregação que os processadores estavam executando o laço ocioso kernel
Tempo de Processador Percentual | Percentagem de tempo executando um fio não ocioso
Percentiowaittime | Percentagem de tempo à espera que as operações da OI completem
Tempo de interrupção por cento | Percentagem de tempo de execução de interrupções de hardware/software e DPCs (chamadas de procedimento diferido)
PercentUserTime | Do tempo não inativo durante a janela de agregação, a percentagem de tempo gasto no utilizador mais na prioridade normal
PercentNiceTime | Do tempo não inativo, a percentagem gasta na prioridade reduzida (agradável)
Por percentPrivilegedTime | Do tempo não inativo, a percentagem gasta em modo privilegiado (kernel)

Os primeiros quatro balcões devem resumir-se a 100%. Os últimos três balcões também somam 100%; subdividem a soma de PercentProcessorTime, PercentIOWaitTime e PercentInterruptTime.

Para obter uma única métrica agregada `"condition": "IsAggregate=TRUE"`em todos os processadores, set . Para obter uma métrica para um processador específico, como o segundo processador lógico `"condition": "Name=\\"1\\""`de um VM de quatro vCPU, definido . Os números lógicos `[0..n-1]`do processador estão na gama.

### <a name="builtin-metrics-for-the-memory-class"></a>métricas construídas para a classe Memória

A classe memória de métricas fornece informações sobre utilização da memória, paging e troca.

counter | Significado
------- | -------
Memória Disponível | Memória física disponível no MiB
Memória Disponível por percenting | Memória física disponível como um por cento da memória total
Memória Usada | Memória física em uso (MiB)
Memória Percentusa | Memória física em uso como um por cento da memória total
PagesPerSec | Pagões totais (ler/escrever)
PagesReadPerSec | Páginas lidas a partir de backup store (ficheiro de troca, ficheiro de programa, ficheiro mapeado, etc.)
PáginasWrittenPerSec | Páginas escritas para backup store (ficheiro de troca, ficheiro mapeado, etc.)
Swap disponível | Espaço de troca não utilizado (MiB)
PerpercentAvailableSwap | Espaço de swap não utilizado em percentagem do swap total
Swap usado | Espaço de troca de utilização (MiB)
Perusqueswap | Espaço de swap em uso em percentagem do swap total

Esta classe de métricas tem apenas um único caso. O atributo "condição" não tem configurações úteis e deve ser omitido.

### <a name="builtin-metrics-for-the-network-class"></a>métricas construídas para a classe Rede

A classe de métricas da Rede fornece informações sobre a atividade da rede em interfaces de rede individuais desde o arranque. Lad não expõe métricas de largura de banda, que podem ser recuperadas a partir das métricas do hospedeiro.

counter | Significado
------- | -------
BytesTransmitidos | Total de bytes enviados desde a bota
BytesReceived | Total de bytes recebidos desde o arranque
BytesTotal | Total de bytes enviados ou recebidos desde o arranque
Pacotes Transmitidos | Total de pacotes enviados desde a bota
Pacotes Recebidos | Total de pacotes recebidos desde o arranque
TotalRxErrors | Número de erros de receção desde o arranque
Erros TotalTx | Número de erros de transmissão desde o arranque
TotalColisões | Número de colisões reportadas pelas portas da rede desde o arranque

 Embora esta classe seja acaso, o LAD não suporta a captura de métricas da Rede agregadas em todos os dispositivos de rede. Para obter as métricas para uma interface específica, `"condition": "InstanceID=\\"eth0\\""`como eth0, set .

### <a name="builtin-metrics-for-the-filesystem-class"></a>métricas construídas para a classe Filesystem

A classe de métricas do Sistema de Ficheiros fornece informações sobre o uso do sistema de ficheiros. Os valores absolutos e percentuais são reportados como seriam apresentados a um utilizador comum (não raiz).

counter | Significado
------- | -------
EspaçoLivre | Espaço de disco disponível em bytes
Espaço Usado | Espaço de disco usado em bytes
PercentFreeSpace | Espaço livre percentual
Espaço Percentused | Percentagem de espaço usado
PercentFreeInodes | Percentagem de inodos não utilizados
PercentUsedInodes | Percentagem de inodes atribuídos (em uso) resumidos em todos os sistemas de ficheiros
BytesReadPerSecond | Bytes lidos por segundo
BytesWrittenPerSecond | Bytes escritos por segundo
BytesPerSecond | Bytes lidos ou escritos por segundo
Lê-se Em segundo lugar | Ler operações por segundo
WritesPerSecond | Escrever operações por segundo
TransfersPerSecond | Ler ou escrever operações por segundo

Os valores agregados em todos os `"condition": "IsAggregate=True"`sistemas de ficheiros podem ser obtidos através da definição . Os valores para um sistema específico de ficheiros montados, `"condition": 'Name="/mnt"'`tais como "/mnt", podem ser obtidos por definição . 

**NOTA:** Se utilizar o Portal Azure em vez de JSON, o formulário de campo de condição correta é Name='mnt'

### <a name="builtin-metrics-for-the-disk-class"></a>métricas construídas para a classe Disk

A classe de métricas do Disco fornece informações sobre o uso do dispositivo de disco. Estas estatísticas aplicam-se a todo o impulso. Se existirem vários sistemas de ficheiros num dispositivo, os contadores para esse dispositivo são, efetivamente, agregados em todos eles.

counter | Significado
------- | -------
Lê-se Em segundo lugar | Ler operações por segundo
WritesPerSecond | Escrever operações por segundo
TransfersPerSecond | Total de operações por segundo
Tempo de leitura média | Média de segundos por operação de leitura
Tempo médio de escrita | Média de segundos por operação de escrita
Tempo médio de transferência | Segundos médios por operação
Comprimento de fila de discos médios | Número médio de operações em disco em fila
ReadBytesPerSecond | Número de bytes lidos por segundo
WriteBytesPerSecond | Número de bytes escritos por segundo
BytesPerSecond | Número de bytes lidos ou escritos por segundo

Os valores agregados em todos os `"condition": "IsAggregate=True"`discos podem ser obtidos por definição . Para obter informações para um dispositivo específico (por exemplo, `"condition": "Name=\\"/dev/sdf1\\""`/dev/sdf1), definido .

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalar e configurar o LAD 3.0 através da CLI

Assumindo que as suas definições protegidas estão no ficheiro PrivateConfig.json e que a sua informação de configuração pública está em PublicConfig.json, execute este comando:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

O comando pressupõe que está a utilizar o modo de gestão de recursos Azure (braço) do Azure CLI. Para configurar lad para vMs modelo de implementação clássico (ASM), mude para o modo "asm" ()`azure config mode asm`e omita o nome do grupo de recursos no comando. Para mais informações, consulte a [documentação CLI transversal.](https://docs.microsoft.com/azure/xplat-cli-connect)

## <a name="an-example-lad-30-configuration"></a>Uma configuração de exemplo LAD 3.0

Com base nas definições anteriores, aqui está uma configuração de extensão LAD 3.0 com alguma explicação. Para aplicar esta amostra no seu caso, deve usar o nome da sua própria conta de armazenamento, token de conta SAS e fichas SAS eventHubs.

### <a name="privateconfigjson"></a>PrivateConfig.json

Estas configurações privadas configuram:

* uma conta de armazenamento
* uma conta correspondente SAS token
* vários lavatórios (JsonBlob ou EventHubs com tokens SAS)

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

### <a name="publicconfigjson"></a>PublicConfig.json

Estas configurações públicas fazem com que o LAD:

* Carregue métricas de tempo de processador por cento `WADMetrics*` e espaço de disco usado para a mesa
* Enviar mensagens do syslog facility "user" e `LinuxSyslog*` severity "info" para a mesa
* Faça upload dos resultados brutos da consulta oMI `LinuxCPU` (PercentProcessorTime e PercentIdleTime) para a tabela com nome
* Carregar linhas anexas `/var/log/myladtestlog` no `MyLadTestLog` ficheiro para a mesa

Em cada caso, os dados também são enviados para:

* Armazenamento Azure Blob (nome do recipiente é definido na pia JsonBlob)
* Ponto final do EventHubs (conforme especificado no afundatório EventHubs)

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

A `resourceId` configuração deve corresponder à do VM ou do conjunto de escala de máquina virtual.

* As métricas da plataforma Azure que mapeam e alertam sabem o recurso Id do VM em que está a trabalhar. Espera encontrar os dados para o seu VM utilizando o recursoId a chave de procura.
* Se utilizar a escala automática Azure, o recursoId na configuração de escala automática deve corresponder ao recurso Id utilizado pelo LAD.
* O resourceId é incorporado nos nomes de JsonBlobs escritos por LAD.

## <a name="view-your-data"></a>Ver os dados

Utilize o portal Azure para visualizar dados de desempenho ou alertas definidos:

![image](./media/diagnostics-linux/graph_metrics.png)

Os `performanceCounters` dados são sempre armazenados numa tabela de armazenamento Azure. As APIs de Armazenamento Azure estão disponíveis para muitos idiomas e plataformas.

Os dados enviados aos lavatórios JsonBlob são armazenados em bolhas na conta de armazenamento indicada nas [definições protegidas](#protected-settings). Pode consumir os dados blob utilizando quaisquer APIs de Armazenamento De Blob Azure.

Além disso, pode utilizar estas ferramentas ui para aceder aos dados no Armazenamento Azure:

* Visual Studio Server Explorer.
* [Explorador de Armazenamento do Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Explorador do Storage do Azure").

Esta foto de uma sessão do Microsoft Azure Storage Explorer mostra as tabelas e contentores de armazenamento azure gerados a partir de uma extensão LAD 3.0 corretamente configurada num VM de teste. A imagem não corresponde exatamente à [configuração LAD 3.0](#an-example-lad-30-configuration)da amostra .

![image](./media/diagnostics-linux/stg_explorer.png)

Consulte a documentação relevante do [EventHubs](../../event-hubs/event-hubs-what-is-event-hubs.md) para aprender a consumir mensagens publicadas num ponto final do EventHubs.

## <a name="next-steps"></a>Passos seguintes

* Crie alertas métricos no [Monitor Azure](../../monitoring-and-diagnostics/insights-alerts-portal.md) para as métricas que recolhe.
* Crie gráficos de [monitorização](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para as suas métricas.
* Aprenda a [criar um conjunto](../linux/tutorial-create-vmss.md) de escala de máquina virtual utilizando as suas métricas para controlar a autoscalcificação.
