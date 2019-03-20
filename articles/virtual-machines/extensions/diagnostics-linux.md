---
title: Computação do Azure - extensão de diagnóstico do Linux | Documentos da Microsoft
description: Como configurar o Azure Linux diagnóstico extensão (LAD) para recolher métricas e eventos de registo de VMs do Linux em execução no Azure.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: agaiha
ms.openlocfilehash: af5d4e21bb5b41df4bcb88dc2f9eb7901fcaa597
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997959"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Utilize a extensão de diagnóstico do Linux para monitorizar métricas e registos

Este documento descreve a versão 3.0 e versões mais recente da extensão de diagnóstico do Linux.

> [!IMPORTANT]
> Para obter informações sobre a versão 2.3 e mais antigo, consulte [este documento](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introdução

A extensão de diagnóstico do Linux ajuda um monitor de utilizador o estado de funcionamento de uma VM do Linux em execução no Microsoft Azure. Ele tem as seguintes capacidades:

* Recolhe métricas de desempenho do sistema a partir da VM e armazena-os numa tabela específica numa conta de armazenamento designada.
* Obtém o registo de eventos do syslog e armazena-os numa tabela específica na conta de armazenamento designada.
* Permite aos utilizadores personalizar as métricas de dados que são recolhidas e a ser carregadas.
* Permite aos utilizadores personalizar as instalações do syslog e níveis de gravidade dos eventos que são recolhidos e a ser carregados.
* Permite aos utilizadores carregar ficheiros de registo especificado para uma tabela de armazenamento designada.
* Suporta o envio de métricas e registo de eventos para pontos de extremidade de EventHub arbitrários e formatada em JSON os blobs na conta de armazenamento designada.

Esta extensão funciona com ambos os modelos de implementação do Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalar a extensão na VM

Pode ativar esta extensão utilizando cmdlets do Azure PowerShell, scripts da CLI do Azure, modelos ARM ou o portal do Azure. Para obter mais informações, consulte [extensões funcionalidades](features-linux.md).

Estas instruções de instalação e um [configuração de exemplo para download](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configurar LAD 3.0 para:

* capturar e armazenar as mesmas métricas como foram fornecidos pela LAD 2.3;
* capturar um conjunto útil de métricas do sistema de arquivo, novos para LAD 3.0;
* capturar a coleção de syslog padrão, habilitada por LAD 2.3;
* Ative a experiência do portal do Azure para a criação de gráficos e os alertas em métricas VM.

A configuração que pode ser baixada é apenas um exemplo; modificá-lo para se adequar às suas necessidades.

### <a name="prerequisites"></a>Pré-requisitos

* **O agente Linux do Azure versão 2.2.0 ou posterior**. A maioria das imagens da galeria do Azure VM Linux incluem a versão 2.2.7 ou posterior. Executar `/usr/sbin/waagent -version` para confirmar a versão instalada na VM. Se a VM estiver a executar uma versão mais antiga do agente convidado, siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) atualizá-la.
* **CLI do Azure**. [Configurar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) ambiente no seu computador.
* O comando de wget, se ainda não tiver: Execute `sudo apt-get install wget`.
* Uma subscrição do Azure existente e uma conta de armazenamento existente na mesma para armazenar os dados.
* Lista de distribuições suportadas de Linux está ativada https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Instalação de exemplo

Preencha os parâmetros corretos nas primeiras três linhas, em seguida, execute este script como raiz:

```bash
# Set your Azure VM diagnostic parameters correctly below
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

O URL para a configuração de exemplo e seu conteúdo, estão sujeitos a alterações. Transferir uma cópia do ficheiro JSON de definições do portal e personalizá-lo para as suas necessidades. Quaisquer modelos ou automatização que construir, deve utilizar a sua própria cópia, em vez de baixar esse URL cada vez.

### <a name="updating-the-extension-settings"></a>A atualizar as definições de extensão

Depois de alterar seu protegido ou definições públicas, implementá-las para a VM ao executar o mesmo comando. Se nada alterado nas definições, as definições atualizadas são enviadas para a extensão. LAD recarrega a configuração e reinicia em si.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migração de versões anteriores da extensão

É a versão mais recente da extensão **3.0**. **As versões antigas (2.x) foram preteridas e pode ser anuladas ou após 31 de Julho de 2018**.

> [!IMPORTANT]
> Esta extensão introduzir alterações significativas na configuração da extensão. Uma dessas mudanças foi efetuada para melhorar a segurança da extensão; como resultado, com versões anteriores compatibilidade com 2.x pode não ser mantida. Além disso, o publicador da extensão para esta extensão é diferente de publicador para as versões 2.x.
>
> Para migrar do 2.x para esta nova versão da extensão, tem de desinstalar a extensão antiga (sob o antigo nome do publicador) e instalar a versão 3 da extensão.

Recomendações:

* Instale a extensão com a atualização de versão secundária automática ativada.
  * No modelo de implementação clássica VMs, especifica "3.*" como a versão, se estiver a instalar a extensão pela CLI XPLAT do Azure ou o Powershell.
  * No Azure Resource Manager deployment model VMs, incluir ' "autoUpgradeMinorVersion": true' no modelo de implementação de VM.
* Utilize uma conta de armazenamento novo/diferente para LAD 3.0. Existam vários pequenas incompatibilidades entre LAD 2.3 e LAD 3.0 que tornam a partilha de uma conta problemáticas:
  * LAD 3.0 armazena os eventos syslog numa tabela com um nome diferente.
  * O counterSpecifier cadeias de caracteres para `builtin` métricas diferem no LAD 3.0.

## <a name="protected-settings"></a>Definições protegidas

Este conjunto de informações de configuração contém informações confidenciais que devem ser protegidas contra a visualização pública, por exemplo, as credenciais de armazenamento. Estas definições são transmitidas para e armazenadas pela extensão no formato encriptado.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Value
---- | -----
storageAccountName | O nome da conta de armazenamento na qual os dados são escritos pela extensão.
storageAccountEndPoint | (opcional) O ponto final de identificar a cloud em que a conta de armazenamento existe. Se esta definição estiver ausente, LAD é predefinido para a cloud pública do Azure, `https://core.windows.net`. Para utilizar uma conta de armazenamento no Azure Alemanha, do Azure Government ou Azure China, defina este valor em conformidade.
storageAccountSasToken | Uma [token de SAS de conta](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) para serviços Blob e tabela (`ss='bt'`), aplicável a objetos e contentores (`srt='co'`), que concede adiciona, criar, listar, atualizar e permissões de escrita (`sp='acluw'`). Fazer *não* incluem o líder de interrogação (?).
mdsdHttpProxy | (opcional) Informações de proxy HTTP necessárias para ativar a extensão estabelecer a ligação para a conta de armazenamento especificada e o ponto final.
sinksConfig | (opcional) Detalhes de destinos alternativos para os quais podem ser entregues métricas e eventos. Os detalhes específicos de cada sink de dados suportado pela extensão são abordados nas seções a seguirem.


> [!NOTE]
> Ao implementar a extensão com um modelo de implementação do Azure, a conta de armazenamento e o SAS token tem criados previamente e, em seguida, passados para o modelo. Não é possível implementar uma VM, a conta de armazenamento e configurar a extensão num único modelo. Atualmente, a criar um token SAS dentro de um modelo não é suportada.

Possa facilmente construir o necessário token SAS através do portal do Azure.

1. Selecione a conta de armazenamento para fins gerais para o qual pretende que a extensão para escrever
1. Selecione "Assinatura de acesso partilhado" da parte as definições do menu à esquerda
1. Tornar as secções apropriadas, conforme descritas anteriormente
1. Clique no botão "Generate SAS".

![image](./media/diagnostics-linux/make_sas.png)

Copie a SAS gerada para o campo de storageAccountSasToken; remover o líder de interrogação ("?").

### <a name="sinksconfig"></a>sinksConfig

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

Esta secção opcional define destinos adicionais para que a extensão envia as informações que coleta. A matriz de "sink" contém um objeto para cada sink de dados adicionais. O atributo "type" determina os outros atributos no objeto.

Elemento | Value
------- | -----
nome | Uma cadeia de caracteres usada para fazer referência a este sink noutro local na configuração da extensão.
tipo | O tipo de sink, que está a ser definido. Determina os outros valores (se houver) em instâncias deste tipo.

A versão 3.0 da extensão de diagnóstico do Linux suporta dois tipos de coletor: EventHub e JsonBlob.

#### <a name="the-eventhub-sink"></a>O coletor de EventHub

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

A entrada de "sasURL" contém o URL completo, incluindo o token SAS para o Hub de eventos para o qual os dados devem ser publicados. LAD requer uma SAS uma diretiva que permita o envio de nomenclatura de afirmação. Um exemplo:

* Criar um espaço de nomes de Hubs de eventos chamado `contosohub`
* Criar um Hub de eventos no espaço de nomes chamado `syslogmsgs`
* Criar uma política de acesso partilhado no Hub de eventos com o nome `writer` que permite que a afirmação de envio

Se tiver criado uma SAS boa até meia-noite UTC no dia 1 de Janeiro de 2018, o valor de sasURL poderá ser:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Para obter mais informações sobre como gerar SAS tokens para os Hubs de eventos, consulte [essa página da web](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>O coletor de JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Dados direcionados para um sink de JsonBlob são armazenados em blobs no armazenamento do Azure. Cada instância de LAD cria um blob de hora a hora para cada nome de sink. Cada blob contém sempre uma matriz JSON sintaticamente válida do objeto. Atomicamente são adicionadas novas entradas para a matriz. Os BLOBs são armazenados num contentor com o mesmo nome como o sink. As regras de armazenamento do Azure para os nomes dos contentores de BLOBs são aplicadas aos nomes das JsonBlob sinks: entre 3 e 63 carateres ASCII alfanuméricos minúsculos ou travessões.

## <a name="public-settings"></a>Definições de públicas

Essa estrutura contém vários blocos de configurações que controlam as informações recolhidas pela extensão. Cada definição é opcional. Se especificar `ladCfg`, também tem de especificar `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elemento | Value
------- | -----
StorageAccount | O nome da conta de armazenamento na qual os dados são escritos pela extensão. Tem de ser o mesmo nome como está especificado no [definições protegido](#protected-settings).
mdsdHttpProxy | (opcional) Mesmo como mostra a [definições protegido](#protected-settings). O valor público é substituído pelo valor privado, se definir. Coloque as definições de proxy que contêm um segredo, por exemplo, uma palavra-passe no [definições protegido](#protected-settings).

Os elementos restantes são descritos em detalhe nas secções seguintes.

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

Coletores de coleta de métricas e registos para entrega para o serviço de métricas do Azure e outros dados de controles esta estrutura opcional. Deve especificar `performanceCounters` ou `syslogEvents` ou ambos. Tem de especificar o `metrics` estrutura.

Elemento | Value
------- | -----
eventVolume | (opcional) Controla o número de partições criadas dentro da tabela de armazenamento. Tem de ser `"Large"`, `"Medium"`, ou `"Small"`. Se não for especificado, o valor predefinido é `"Medium"`.
sampleRateInSeconds | (opcional) Intervalo padrão entre a coleção de métricas (unaggregated). A taxa de menor suportados exemplo é 15 segundos. Se não for especificado, o valor predefinido é `15`.

#### <a name="metrics"></a>métricas

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elemento | Value
------- | -----
resourceId | Definir o ID de recurso do Azure Resource Manager da VM ou de dimensionamento da máquina virtual para que a VM pertence. Esta definição tem de ser também especificado se qualquer sink JsonBlob é utilizada na configuração.
scheduledTransferPeriod | A frequência com que métricas de agregação estão a ser computado e transferidos para métricas do Azure, expresso como um intervalo de tempo é 8601. O mais pequeno período de transferência é 60 segundos, ou seja, PT1M. Tem de especificar pelo menos um scheduledTransferPeriod.

Exemplos de métricas especificados na seção performanceCounters são recolhidos a cada 15 segundos ou o exemplo classificar explicitamente definida para o contador. Se aparecerem várias scheduledTransferPeriod frequências (como no exemplo), cada agregação é calculada de forma independente.

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

Esta secção opcional controla a coleção de métricas. Os exemplos não processados são agregados para cada [scheduledTransferPeriod](#metrics) para produzir estes valores:

* média
* mínimo
* máximo
* recolhido o último valor
* Contagem de amostras brutas utilizadas para calcular o agregado

Elemento | Value
------- | -----
Coletores de | (opcional) Uma lista separada por vírgulas dos nomes dos sinks que LAD envia agregado resultados métrica. Todas as métricas agregadas são publicadas cada sink listada. Ver [sinksConfig](#sinksconfig). Exemplo: `"EHsink1, myjsonsink"`.
tipo | Identifica o fornecedor real da métrica.
classe | Em conjunto com "contador", identifica a métrica específica dentro do espaço de nomes do fornecedor.
counter | Em conjunto com "class", identifica a métrica específica dentro do espaço de nomes do fornecedor.
counterSpecifier | Identifica a métrica específica dentro do espaço de nomes de métricas do Azure.
condition | (opcional) Seleciona uma instância específica do objeto ao qual a métrica aplica-se ou seleciona a agregação de todas as instâncias desse objeto. Para obter mais informações, consulte o `builtin` as definições de métricas.
sampleRate | É o intervalo de 8601 que define a taxa em que os exemplos não processados para esta métrica são recolhidos. Se não definido, o intervalo de coleta é definido pelo valor de [sampleRateInSeconds](#ladcfg). A taxa de exemplo suportadas mais curta é 15 segundos (PT15S).
unidade | Deve ser um dessas cadeias de caracteres: "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Millisecond". Define a unidade para a métrica. Os consumidores dos dados recolhidos esperam que os valores de dados recolhidos para corresponder esta unidade. LAD ignora este campo.
displayName | A etiqueta (no idioma especificado da definição de região associados) ser anexados a esses dados de métricas do Azure. LAD ignora este campo.

O counterSpecifier é um identificador arbitrário. Os consumidores de métricas, como o gráfico portal do Azure e alertas de funcionalidade, utilize counterSpecifier como a "chave" que identifica uma métrica ou uma instância de uma métrica. Para `builtin` métricas, recomendamos que utilize os valores de counterSpecifier que começam com `/builtin/`. Se está a recolher uma instância específica de uma métrica, recomendamos que anexar o identificador da instância para o valor de counterSpecifier. Alguns exemplos:

* `/builtin/Processor/PercentIdleTime` -Uma média de tempo inativo em todas as vCPUs
* `/builtin/Disk/FreeSpace(/mnt)` -Espaço para o sistema de ficheiros /mnt
* `/builtin/Disk/FreeSpace` -Uma média de espaço livre em todos os sistemas de ficheiros montados

Nem LAD nem o portal do Azure espera que o valor de counterSpecifier para corresponder a qualquer padrão. Seja consistente na forma como constrói a valores de counterSpecifier.

Quando especificar `performanceCounters`, LAD sempre escreve dados para uma tabela no armazenamento do Azure. Pode ter os mesmos dados escritos para blobs JSON de e/ou os Hubs de eventos, mas não é possível desativar o armazenamento de dados a uma tabela. Todas as instâncias de extensão de diagnóstico do configurado para utilizar o mesmo nome de conta de armazenamento e o ponto final adicionar as suas métricas e registos para a mesma tabela. Se estiver a escrever muitas VMs para a mesma partição de tabela, o Azure pode limitar escritas para essa partição. A definição de eventVolume faz com que as entradas ser spread em 10 de (pequena), 1 (média) ou 100 partições em diferentes (grande). Normalmente, "Medium" é suficiente para garantir que o tráfego não está limitado. A funcionalidade de métrica do Azure do portal do Azure utiliza os dados nesta tabela, para produzir os gráficos de ou para acionar alertas. O nome da tabela é a concatenação dessas cadeias de caracteres:

* `WADMetrics`
* O "scheduledTransferPeriod" para os valores agregados, armazenados na tabela
* `P10DV2S`
* Uma data, sob a forma "AAAAMMDD", que altera a cada 10 dias

Os exemplos incluem `WADMetricsPT1HP10DV2S20170410` e `WADMetricsPT1MP10DV2S20170609`.

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

Esta secção opcional controla a recolha de eventos de registo do syslog. Se a seção for omitida, eventos syslog não são capturados em todos os.

A coleção de syslogEventConfiguration tem uma entrada para cada função do syslog de interesse. Se minSeverity é "Nenhum" para um recurso específico, ou se esse recurso não aparecer no elemento de todo, não existem eventos a partir desse recurso são capturados.

Elemento | Value
------- | -----
Coletores de | Uma lista separada por vírgulas dos nomes dos sinks ao qual os eventos de registo individuais são publicados. Todos os eventos de registo que correspondam as restrições no syslogEventConfiguration são publicados para cada sink listada. Exemplo: "EHforsyslog"
facilityName | Um nome de recurso do syslog (por exemplo, "LOG\_utilizador" ou "LOG\_LOCAL0"). Consulte a secção "recurso" a [página do syslog man](http://man7.org/linux/man-pages/man3/syslog.3.html) para a lista completa.
minSeverity | Um nível de gravidade do syslog (por exemplo, "LOG\_ERR" ou "LOG\_informações"). Consulte a secção "nível" a [página do syslog man](http://man7.org/linux/man-pages/man3/syslog.3.html) para a lista completa. A extensão de captura eventos enviados para o recurso no ou acima do nível especificado.

Quando especificar `syslogEvents`, LAD sempre escreve dados para uma tabela no armazenamento do Azure. Pode ter os mesmos dados escritos para blobs JSON de e/ou os Hubs de eventos, mas não é possível desativar o armazenamento de dados a uma tabela. O comportamento de criação de partições para esta tabela é igual à descrita para `performanceCounters`. O nome da tabela é a concatenação dessas cadeias de caracteres:

* `LinuxSyslog`
* Uma data, sob a forma "AAAAMMDD", que altera a cada 10 dias

Os exemplos incluem `LinuxSyslog20170410` e `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Esta secção opcional controla a execução de arbitrário [OMI](https://github.com/Microsoft/omi) consultas.

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

Elemento | Value
------- | -----
espaço de nomes | (opcional) O espaço de nomes OMI, no qual a consulta deve ser executada. Se não for especificado, o valor predefinido é "raiz/scx", implementado pelos [fornecedores de plataformas do System Center](https://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
consulta | A consulta OMI a ser executado.
tabela | (opcional) A tabela de armazenamento do Azure, na conta de armazenamento designada (consulte [definições protegido](#protected-settings)).
frequência | (opcional) O número de segundos entre a execução da consulta. Valor predefinido é 300 (5 minutos) valor mínimo é de 15 segundos.
Coletores de | (opcional) Uma lista separada por vírgulas dos nomes dos sinks adicionais para que os resultados de métrica de exemplo em bruto devem ser publicados. Nenhuma agregação destes exemplos não processados é calculada pela extensão ou por métricas do Azure.

O "tabela" ou "Coletores de" ou ambos, tem de ser especificados.

### <a name="filelogs"></a>fileLogs

Controla a captura de ficheiros de registo. LAD captura novas linhas de texto, como eles são gravados no arquivo e escreve-as linhas da tabela de e/ou quaisquer sinks especificados (JsonBlob ou EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elemento | Value
------- | -----
ficheiro | O nome do caminho completo do ficheiro de registo a ser observada e capturado. O nome do caminho deve nomeá um único arquivo; ela não é possível nomear um diretório ou conter os carateres universais.
tabela | (opcional) A tabela de armazenamento do Azure, na conta de armazenamento designada (conforme especificado na configuração protegida), no qual são escritas novas linhas de "cauda" do ficheiro.
Coletores de | (opcional) Uma lista separada por vírgulas dos nomes dos sinks adicionais para quais linhas de log enviadas.

O "tabela" ou "Coletores de" ou ambos, tem de ser especificados.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas suportadas pelo fornecedor de builtin

O fornecedor de métrica de builtin é uma origem de métricas mais interessantes para um conjunto amplo de utilizadores. Estas métricas enquadram-se a cinco classes amplas:

* Processador
* Memória
* Rede
* Sistema de ficheiros
* Disco

### <a name="builtin-metrics-for-the-processor-class"></a>métricas de Builtin para a classe de processador

A classe de processador de métricas fornece informações sobre a utilização de processador na VM. Ao agregar percentagens, o resultado é a média em todas as CPUs. Numa VM de vCPU de dois, se um vCPU foi 100% ocupado e o outro era 100% ocioso, o PercentIdleTime comunicada seria 50. Se cada vCPU estava ocupado para o mesmo período de 50%, o resultado comunicado também seria 50. Numa VM de vCPU de quatro, com um vCPU 100% ocupado e a outras pessoas ocioso, o PercentIdleTime comunicada seria 75.

counter | Significado
------- | -------
PercentIdleTime | Percentagem de tempo durante a janela de agregação que processadores foram executando o loop de inatividade de kernel
PercentProcessorTime | Percentagem de tempo de execução de um thread não inativo
PercentIOWaitTime | Percentagem de tempo a aguardar a conclusão das operações de e/s
PercentInterruptTime | Percentagem de tempo de execução de hardware/software interrupções e DPCs (chamadas de procedimento diferidas)
PercentUserTime | Tempo ativo durante a janela de agregação, a percentagem de tempo gasto no utilizador mais em prioridade normal
PercentNiceTime | De tempo ativo, a percentagem gasta em baixa expectativa com prioridade (bom)
PercentPrivilegedTime | Tempo ativo, a percentagem gasto no modo de privilégios (kernel)

Os primeiros quatro contadores devem somar para 100%. Os três últimos contadores também soma para 100%; eles subdividir a soma de PercentProcessorTime PercentIOWaitTime e PercentInterruptTime.

Para obter uma única métrica agregada em todos os processadores, defina `"condition": "IsAggregate=TRUE"`. Para obter uma métrica para um processador específico, como o segundo processador lógico de uma VM de vCPU de quatro, defina `"condition": "Name=\\"1\\""`. Números de processador lógico estão no intervalo `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>métricas de Builtin para a classe de memória

A classe de memória de métricas fornece informações sobre a utilização da memória, paginação e a troca.

counter | Significado
------- | -------
AvailableMemory | Memória física disponível no MiB
PercentAvailableMemory | Memória física disponível como uma percentagem do total de memória
UsedMemory | Em utilização memória física (MiB)
PercentUsedMemory | Memória de física em utilização como percentagem do total de memória
PagesPerSec | Paginação total (leitura/escrita)
PagesReadPerSec | Páginas de leitura de segurança dos store (ficheiro de comutação, ficheiros de programa, arquivo mapeada, etc.)
PagesWrittenPerSec | Páginas escritas para armazenamento de backup (ficheiro de comutação, arquivo mapeada, etc.)
AvailableSwap | Espaço de comutação utilizado (MiB)
PercentAvailableSwap | Espaço de comutação utilizado como uma percentagem de comutação total
UsedSwap | Em utilização de espaço de comutação (MiB)
PercentUsedSwap | Em utilização de espaço de comutação como uma percentagem de comutação total

Essa classe de métricas tem apenas uma única instância. O atributo "condição" não tem útil definições e deve ser omitido.

### <a name="builtin-metrics-for-the-network-class"></a>métricas de Builtin para a classe de rede

A classe de rede de métricas fornece informações sobre a atividade de rede nas interfaces de rede individuais desde o arranque. LAD não expõe as métricas de largura de banda, que podem ser obtidas a partir de métricas de anfitrião.

counter | Significado
------- | -------
BytesTransmitted | Total de bytes enviados desde o arranque
BytesReceived | Total de bytes recebidos desde o arranque
BytesTotal | Total de bytes enviado ou recebido desde o arranque
PacketsTransmitted | Total de pacotes enviados desde o arranque
PacketsReceived | Total de pacotes recebidos desde o arranque
TotalRxErrors | Número de recebe erros desde o arranque
TotalTxErrors | Número de erros de transmitir desde o arranque
TotalCollisions | Número de colisões é comunicado pelas portas de rede desde o arranque

 Embora essa classe é instanciada, LAD não suporta a capturar métricas de rede agregadas em todos os dispositivos de rede. Para obter as métricas para uma interface específica, como eth0, defina `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>métricas de Builtin para a classe de sistema de ficheiros

A classe de sistema de ficheiros de métricas fornece informações sobre a utilização do sistema de ficheiros. Valores absolutos e percentagem são comunicados como seria exibidos a um usuário comum (não raiz).

counter | Significado
------- | -------
EspaçoLivre | Espaço em disco disponível em bytes
UsedSpace | Utilizada de espaço em disco, em bytes
PercentFreeSpace | Percentagem de espaço livre
PercentUsedSpace | Percentagem de espaço utilizado
PercentFreeInodes | Percentagem de inodes não utilizados
PercentUsedInodes | Percentagem de inodes alocado (em utilização) somados em todos os sistemas de ficheiros
BytesReadPerSecond | Bytes lidos por segundo
BytesWrittenPerSecond | Bytes escritos por segundo
BytesPerSecond | Bytes lidos ou gravados por segundo
ReadsPerSecond | Operações de leitura por segundo
WritesPerSecond | Operações de escrita por segundo
TransfersPerSecond | Operações de leitura ou escrita por segundo

Valores agregados em todos os sistemas de ficheiros podem ser obtidos através da definição `"condition": "IsAggregate=True"`. Os valores de um sistema de ficheiros instalado específico, por exemplo, "/ mnt", podem ser obtidos através da definição `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>métricas de Builtin para a classe de disco

A classe de disco de métricas fornece informações sobre a utilização de dispositivos de disco. Estas estatísticas aplicam-se para a unidade completa. Se existirem vários sistemas de ficheiros num dispositivo, os contadores para esse dispositivo são, efetivamente, agregados em todos eles.

counter | Significado
------- | -------
ReadsPerSecond | Operações de leitura por segundo
WritesPerSecond | Operações de escrita por segundo
TransfersPerSecond | Total de operações por segundo
AverageReadTime | Média segundos por operação de leitura
AverageWriteTime | Média segundos por operação de escrita
AverageTransferTime | Média segundos por operação
AverageDiskQueueLength | Número médio de operações de disco em fila
ReadBytesPerSecond | Número de bytes lidos por segundo
WriteBytesPerSecond | Número de bytes escritos por segundo
BytesPerSecond | Número de bytes lidos ou gravados por segundo

Valores agregados em todos os discos podem ser obtidos através da definição `"condition": "IsAggregate=True"`. Para obter informações de um dispositivo específico (por exemplo, / desenvolvimento/sdf1), defina `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalar e configurar LAD 3.0 através da CLI

Partindo do princípio de que as definições de protegidos estão no ficheiro PrivateConfig.json e suas informações de configuração pública está em PublicConfig.json, execute este comando:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

O comando assume que está a utilizar o modo de gestão de recursos do Azure (arm) da CLI do Azure. Para configurar LAD para implementação clássica modelar VMs (ASM), mude para o modo de "asm" (`azure config mode asm`) e omitir o nome do grupo de recursos no comando. Para obter mais informações, consulte a [documentação da CLI de várias plataformas](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Uma configuração de exemplo LAD 3.0

Com base nas definições de anteriores, eis um exemplo de configuração de extensão LAD 3.0 com algumas explicação. Para aplicar este exemplo ao seu caso, deve usar seu próprio nome de conta de armazenamento, o token de SAS de conta e tokens SAS de EventHubs.

### <a name="privateconfigjson"></a>PrivateConfig.json

Configuram estas definições privadas:

* Uma conta de armazenamento
* um token SAS de conta correspondente
* vários coletores (JsonBlob ou EventHubs com SAS tokens)

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

Estas definições públicas causam LAD para:

* Carregar as métricas de tempo do processador % e utilizado-espaço em disco para o `WADMetrics*` tabela
* Carregar mensagens do syslog instalações "utilizador" e a gravidade "informações" para o `LinuxSyslog*` tabela
* Carregar resultados de consulta, em bruto OMI (PercentProcessorTime e PercentIdleTime) para o nomeado `LinuxCPU` tabela
* Carregar anexadas linhas no ficheiro `/var/log/myladtestlog` para o `MyLadTestLog` tabela

Em cada caso, os dados também são carregados para:

* Armazenamento de Blobs do Azure (nome do contentor é conforme definido no coletor JsonBlob)
* Ponto final de EventHubs (conforme especificado no coletor EventHubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
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

O `resourceId` na configuração tem de corresponder ao que a VM ou o dimensionamento de máquinas virtuais definido.

* Métricas de plataforma do Azure, criação de gráficos e os alertas sabe o resourceId da VM que está a trabalhar. Ele espera encontrar os dados para a VM com o resourceId a chave de pesquisa.
* Se utilizar o dimensionamento automático do Azure, o resourceId na configuração do dimensionamento automático tem de corresponder ao resourceId utilizado pelo LAD.
* O resourceId baseia-se para os nomes dos JsonBlobs escrito por LAD.

## <a name="view-your-data"></a>Ver os seus dados

Utilize o portal do Azure para ver dados de desempenho ou definir alertas:

![image](./media/diagnostics-linux/graph_metrics.png)

O `performanceCounters` sempre os dados são armazenados numa tabela do armazenamento do Azure. APIs de armazenamento do Azure estão disponíveis para vários idiomas e plataformas.

Os dados enviados para os sinks de JsonBlob são armazenados em blobs na conta de armazenamento com o nome no [definições protegido](#protected-settings). Pode consumir os dados de Blobs com qualquer APIs de armazenamento de Blobs do Azure.

Além disso, pode utilizar estas ferramentas de interface do Usuário para acessar os dados no armazenamento do Azure:

* Explorador de servidores do Visual Studio.
* [Explorador de armazenamento do Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Explorador de armazenamento do Azure").

Este instantâneo de uma sessão do Microsoft Azure Storage Explorer mostra o gerado tabelas de armazenamento do Azure e contentores de uma extensão de LAD 3.0 corretamente configurada numa VM de teste. A imagem não corresponde exatamente com o [exemplo de configuração de LAD 3.0](#an-example-lad-30-configuration).

![image](./media/diagnostics-linux/stg_explorer.png)

Consulte o relevante [EventHubs documentação](../../event-hubs/event-hubs-what-is-event-hubs.md) para saber como consumir mensagens publicadas para um ponto de extremidade do EventHubs.

## <a name="next-steps"></a>Passos Seguintes

* Criar alertas de métricas no [do Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) para as métricas que recolher.
* Crie [gráficos de monitorização](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para as suas métricas.
* Saiba como [criar um conjunto de dimensionamento de máquinas virtuais](../linux/tutorial-create-vmss.md) com as suas métricas para controlar o dimensionamento automático.
