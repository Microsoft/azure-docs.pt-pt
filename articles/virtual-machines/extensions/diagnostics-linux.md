---
title: Computação do Azure-extensão de diagnóstico do Linux
description: Como configurar a extensão de diagnóstico Linux do Azure (LAD) para coletar métricas e eventos de log de VMs Linux em execução no Azure.
services: virtual-machines-linux
author: abhijeetgaiha
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: gwallace
ms.openlocfilehash: 046e61d82893bf1fcdb2d6697cfaaa9f5bde8c2c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359367"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Usar a extensão de diagnóstico do Linux para monitorar as métricas e os logs

Este documento descreve a versão 3,0 e mais recente da extensão de diagnóstico do Linux.

> [!IMPORTANT]
> Para obter informações sobre a versão 2,3 e mais antigas, consulte [este documento](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Introdução

A extensão de diagnóstico do Linux ajuda um usuário a monitorar a integridade de uma VM Linux em execução no Microsoft Azure. Ele tem os seguintes recursos:

* Coleta métricas de desempenho do sistema da VM e as armazena em uma tabela específica em uma conta de armazenamento designada.
* Recupera eventos de log do syslog e os armazena em uma tabela específica na conta de armazenamento designada.
* Permite que os usuários personalizem as métricas de dados que são coletadas e carregadas.
* Permite que os usuários personalizem os recursos de syslog e os níveis de severidade de eventos que são coletados e carregados.
* Permite que os usuários carreguem os arquivos de log especificados em uma tabela de armazenamento designada.
* Dá suporte ao envio de métricas e eventos de log para pontos de extremidade de EventHub arbitrários e blobs formatados em JSON na conta de armazenamento designada.

Essa extensão funciona com os dois modelos de implantação do Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalando a extensão em sua VM

Você pode habilitar essa extensão usando os cmdlets Azure PowerShell, scripts de CLI do Azure, modelos de ARM ou o portal do Azure. Para obter mais informações, consulte [recursos de extensões](features-linux.md).

Essas instruções de instalação e uma [configuração de exemplo baixável](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) configuram o Lad 3,0 para:

* Capture e armazene as mesmas métricas fornecidas pelo LAD 2,3;
* Capture um conjunto útil de métricas do sistema de arquivos, novo no LAD 3,0;
* capturar a coleção de syslog padrão habilitada pelo LAD 2,3;
* Habilite a experiência de portal do Azure para gráficos e alertas em métricas de VM.

A configuração que pode ser baixada é apenas um exemplo; Modifique-o de acordo com suas necessidades.

### <a name="prerequisites"></a>Pré-requisitos

* **Agente Linux do Azure versão 2.2.0 ou posterior**. A maioria das imagens da galeria do Linux de VM do Azure inclui a versão 2.2.7 ou posterior. Execute `/usr/sbin/waagent -version` para confirmar a versão instalada na VM. Se a VM estiver executando uma versão mais antiga do agente convidado, siga [estas instruções](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) para atualizá-la.
* **CLI do Azure**. [Configure o ambiente de CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu computador.
* O comando wget, se você ainda não o tiver: execute `sudo apt-get install wget`.
* Uma assinatura do Azure existente e uma conta de armazenamento existente dentro dela para armazenar os dados.
* A lista de distribuições do Linux com suporte está em https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Instalação de exemplo

Preencha os valores corretos para as variáveis na primeira seção antes de executar:

```bash
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

A URL para a configuração de exemplo e seu conteúdo estão sujeitas a alterações. Baixe uma cópia do arquivo JSON de configurações do portal e personalize-a para suas necessidades. Qualquer modelo ou automação que você construa deve usar sua própria cópia, em vez de baixar essa URL a cada vez.

### <a name="updating-the-extension-settings"></a>Atualizando as configurações de extensão

Depois de alterar suas configurações protegidas ou públicas, implante-as na VM executando o mesmo comando. Se algo for alterado nas configurações, as configurações atualizadas serão enviadas para a extensão. O LAD recarrega a configuração e se reinicia.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migração de versões anteriores da extensão

A versão mais recente da extensão é **3,0**. **Todas as versões antigas (2. x) são preteridas e podem não ser publicadas em ou após 31 de julho de 2018**.

> [!IMPORTANT]
> Essa extensão introduz alterações significativas na configuração da extensão. Uma dessas alterações foi feita para melhorar a segurança da extensão; Como resultado, a compatibilidade com versões anteriores com 2. x não pode ser mantida. Além disso, o editor de extensão para essa extensão é diferente do Publicador para as versões 2. x.
>
> Para migrar do 2. x para essa nova versão da extensão, você deve desinstalar a extensão antiga (com o nome do Publicador antigo) e, em seguida, instalar a versão 3 da extensão.

Recommendations

* Instale a extensão com a atualização de versão secundária automática habilitada.
  * Em VMs do modelo de implantação clássica, especifique ' 3. * ' como a versão se você estiver instalando a extensão por meio da CLI do Azure XPLAT ou do PowerShell.
  * Em Azure Resource Manager VMs do modelo de implantação, inclua ' "autoUpgradeMinorVersion": true ' no modelo de implantação de VM.
* Use uma conta de armazenamento nova/diferente para o LAD 3,0. Há várias incompatibilidades pequenas entre LAD 2,3 e LAD 3,0 que fazem o compartilhamento de uma conta problemática:
  * O LAD 3,0 armazena eventos de syslog em uma tabela com um nome diferente.
  * As cadeias de caracteres de metaespecificadores para métricas de `builtin` diferem no LAD 3,0.

## <a name="protected-settings"></a>Configurações protegidas

Esse conjunto de informações de configuração contém informações confidenciais que devem ser protegidas da exibição pública, por exemplo, credenciais de armazenamento. Essas configurações são transmitidas e armazenadas pela extensão em formato criptografado.

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
storageAccountName | O nome da conta de armazenamento na qual os dados são gravados pela extensão.
storageAccountEndPoint | adicional O ponto de extremidade que identifica a nuvem na qual a conta de armazenamento existe. Se essa configuração estiver ausente, LAD usa como padrão a nuvem pública do Azure, `https://core.windows.net`. Para usar uma conta de armazenamento no Azure Alemanha, no Azure governamental ou no Azure China, defina esse valor adequadamente.
storageAccountSasToken | Um [token SAS de conta](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) para serviços de BLOB e tabela (`ss='bt'`), aplicável a contêineres e objetos (`srt='co'`), que concede permissões de adicionar, criar, listar, atualizar e gravar (`sp='acluw'`). *Não* inclua o ponto de interrogação (?) à esquerda.
mdsdHttpProxy | adicional Informações de proxy HTTP necessárias para habilitar a extensão para se conectar à conta de armazenamento e ao ponto de extremidade especificados.
sinksConfig | adicional Detalhes de destinos alternativos para os quais as métricas e os eventos podem ser entregues. Os detalhes específicos de cada coletor de dados com suporte na extensão são abordados nas seções a seguir.

Para obter um token SAS em um modelo do Resource Manager, use a função **listAccountSas** . Para obter um modelo de exemplo, consulte [exemplo de função de lista](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Você pode construir facilmente o token SAS necessário por meio do portal do Azure.

1. Selecione a conta de armazenamento de uso geral para a qual você deseja que a extensão seja gravada
1. Selecione "assinatura de acesso compartilhado" na parte configurações do menu à esquerda
1. Faça as seções apropriadas conforme descrito anteriormente
1. Clique no botão "gerar SAS".

![imagem](./media/diagnostics-linux/make_sas.png)

Copie a SAS gerada para o campo storageAccountSasToken; remover o ponto de interrogação ("?") à esquerda.

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

Essa seção opcional define destinos adicionais para os quais a extensão envia as informações coletadas. A matriz "Sink" contém um objeto para cada coletor de dados adicional. O atributo "Type" determina os outros atributos no objeto.

Elemento | Valor
------- | -----
nome | Uma cadeia de caracteres usada para fazer referência a esse coletor em outro lugar na configuração da extensão.
tipo | O tipo de coletor que está sendo definido. Determina os outros valores (se houver) em instâncias deste tipo.

A versão 3,0 da extensão de diagnóstico do Linux dá suporte a dois tipos de coletor: EventHub e JsonBlob.

#### <a name="the-eventhub-sink"></a>O coletor do EventHub

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

A entrada "sasURL" contém a URL completa, incluindo o token SAS, para o Hub de eventos no qual os dados devem ser publicados. LAD exige um nome SAS de uma política que habilita a declaração de envio. Um exemplo:

* Crie um namespace de hubs de eventos chamado `contosohub`
* Crie um hub de eventos no namespace chamado `syslogmsgs`
* Criar uma política de acesso compartilhado no Hub de eventos chamada `writer` que habilita a declaração de envio

Se você tiver criado uma SAS boa até meia-noite UTC em 1º de janeiro de 2018, o valor de sasURL poderá ser:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Para obter mais informações sobre como gerar tokens SAS para hubs de eventos, consulte [esta página da Web](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>O coletor JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Os dados direcionados para um coletor JsonBlob são armazenados em BLOBs no armazenamento do Azure. Cada instância de LAD cria um blob a cada hora para cada nome de coletor. Cada blob sempre contém uma matriz JSON sintaticamente válida de objeto. Novas entradas são adicionadas atomicamente à matriz. Os BLOBs são armazenados em um contêiner com o mesmo nome que o coletor. As regras de armazenamento do Azure para nomes de contêiner de BLOBs se aplicam aos nomes de coletores JsonBlob: entre 3 e 63 caracteres ASCII alfanuméricos ou traços minúsculos.

## <a name="public-settings"></a>Configurações públicas

Essa estrutura contém vários blocos de configurações que controlam as informações coletadas pela extensão. Cada configuração é opcional. Se você especificar `ladCfg`, também deverá especificar `StorageAccount`.

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
StorageAccount | O nome da conta de armazenamento na qual os dados são gravados pela extensão. Deve ter o mesmo nome que o especificado nas [Configurações protegidas](#protected-settings).
mdsdHttpProxy | adicional O mesmo que nas [Configurações protegidas](#protected-settings). O valor público é substituído pelo valor particular, se definido. Coloque as configurações de proxy que contêm um segredo, como uma senha, nas [Configurações protegidas](#protected-settings).

Os elementos restantes são descritos detalhadamente nas seções a seguir.

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

Essa estrutura opcional controla a coleta de métricas e logs para entrega para o serviço de métricas do Azure e para outros coletores de dados. Você deve especificar `performanceCounters` ou `syslogEvents` ou ambos. Você deve especificar a estrutura de `metrics`.

Elemento | Valor
------- | -----
eventVolume | adicional Controla o número de partições criadas na tabela de armazenamento. Deve ser uma das `"Large"`, `"Medium"`ou `"Small"`. Se não for especificado, o valor padrão será `"Medium"`.
sampleRateInSeconds | adicional O intervalo padrão entre a coleção de métricas brutas (não agregadas). A menor taxa de amostra com suporte é de 15 segundos. Se não for especificado, o valor padrão será `15`.

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
resourceId | A ID de recurso Azure Resource Manager da VM ou do conjunto de dimensionamento de máquinas virtuais ao qual a VM pertence. Essa configuração também deverá ser especificada se qualquer coletor JsonBlob for usado na configuração.
scheduledTransferPeriod | A frequência na qual as métricas de agregação devem ser computadas e transferidas para as métricas do Azure, expressas como um intervalo de tempo de 8601. O menor período de transferência é de 60 segundos, ou seja, PT1M. Você deve especificar pelo menos um scheduledTransferPeriod.

Exemplos das métricas especificadas na seção performanceCounters são coletados a cada 15 segundos ou na taxa de amostragem explicitamente definida para o contador. Se várias frequências de scheduledTransferPeriod forem exibidas (como no exemplo), cada agregação será calculada de forma independente.

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

Esta seção opcional controla a coleção de métricas. As amostras brutas são agregadas para cada [scheduledTransferPeriod](#metrics) para produzir esses valores:

* média
* mínimo
* máximo
* último valor coletado
* contagem de amostras brutas usadas para calcular a agregação

Elemento | Valor
------- | -----
coletores | adicional Uma lista separada por vírgulas de nomes de coletores para os quais o LAD envia resultados de métrica agregados. Todas as métricas agregadas são publicadas em cada coletor listado. Consulte [sinksConfig](#sinksconfig). Exemplo: `"EHsink1, myjsonsink"`.
tipo | Identifica o provedor real da métrica.
Classe | Junto com "Counter", identifica a métrica específica dentro do namespace do provedor.
counter | Junto com "Class", identifica a métrica específica dentro do namespace do provedor.
counterSpecifier | Identifica a métrica específica no namespace de métricas do Azure.
condition | adicional Seleciona uma instância específica do objeto ao qual a métrica se aplica ou seleciona a agregação em todas as instâncias desse objeto. Para obter mais informações, consulte as definições de métrica de `builtin`.
sampleRate | É 8601 intervalo que define a taxa na qual as amostras brutas para essa métrica são coletadas. Se não estiver definido, o intervalo de coleta será definido pelo valor de [sampleRateInSeconds](#ladcfg). A taxa de amostra mais curta suportada é 15 segundos (PT15S).
unidade | Deve ser uma destas cadeias de caracteres: "Count", "bytes", "Seconds", "percent", "CountPerSecond", "BytesPerSecond", "milissegundo". Define a unidade para a métrica. Os consumidores dos dados coletados esperam que os valores de dados coletados correspondam a essa unidade. LAD ignora esse campo.
displayName | O rótulo (no idioma especificado pela configuração de localidade associada) a ser anexado a esses dados nas métricas do Azure. LAD ignora esse campo.

O possível especificador é um identificador arbitrário. Os consumidores de métricas, como o portal do Azure recurso de criação de gráficos e alertas, usam o coespecificador como a "chave" que identifica uma métrica ou uma instância de uma métrica. Para `builtin` métricas, recomendamos que você use valores de especificador de mesmo valor que comecem com `/builtin/`. Se você estiver coletando uma instância específica de uma métrica, recomendamos anexar o identificador da instância ao valor de metaespecificador. Alguns exemplos:

* `/builtin/Processor/PercentIdleTime`-média de tempo ocioso em todas as vCPUs
* `/builtin/Disk/FreeSpace(/mnt)`-espaço livre para o sistema de arquivos/mnt
* `/builtin/Disk/FreeSpace`-espaço livre médio em todos os sistemas de filemontados

Nem LAD nem a portal do Azure espera que o valor de metaespecificador corresponda a qualquer padrão. Seja consistente na forma como você constrói valores de especificadores.

Quando você especifica `performanceCounters`, o LAD sempre grava dados em uma tabela no armazenamento do Azure. Você pode ter os mesmos dados gravados em BLOBs JSON e/ou hubs de eventos, mas não pode desabilitar o armazenamento de dados em uma tabela. Todas as instâncias da extensão de diagnóstico configuradas para usar o mesmo nome de conta de armazenamento e ponto de extremidade adicionam suas métricas e logs à mesma tabela. Se muitas VMs estiverem gravando na mesma partição de tabela, o Azure poderá limitar as gravações nessa partição. A configuração eventVolume faz com que as entradas sejam distribuídas entre 1 (pequena), 10 (média) ou 100 (grande) partições diferentes. Normalmente, "médio" é suficiente para garantir que o tráfego não seja limitado. O recurso de métricas do Azure do portal do Azure usa os dados nesta tabela para produzir grafos ou disparar alertas. O nome da tabela é a concatenação dessas cadeias de caracteres:

* `WADMetrics`
* O "scheduledTransferPeriod" para os valores agregados armazenados na tabela
* `P10DV2S`
* Uma data, no formato "aaaammdd", que muda a cada 10 dias

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

Essa seção opcional controla a coleção de eventos de log do syslog. Se a seção for omitida, os eventos de syslog não serão capturados.

A coleção syslogEventConfiguration tem uma entrada para cada recurso de syslog de seu interesse. Se minSeverity for "NONE" para um recurso específico, ou se esse recurso não aparecer no elemento, nenhum evento desse recurso será capturado.

Elemento | Valor
------- | -----
coletores | Uma lista separada por vírgulas de nomes de coletores nos quais os eventos de log individuais são publicados. Todos os eventos de log que correspondem às restrições em syslogEventConfiguration são publicados em cada coletor listado. Exemplo: "EHforsyslog"
facilityName | Um nome de instalação de syslog (por exemplo, "LOG\_usuário" ou "LOG\_LOCAL0"). Consulte a seção "instalação" da [página do manual do syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para obter a lista completa.
minSeverity | Um nível de severidade de syslog (como "LOG\_ERR" ou "LOG\_INFO"). Consulte a seção "nível" da [página do manual do syslog](http://man7.org/linux/man-pages/man3/syslog.3.html) para obter a lista completa. A extensão captura eventos enviados para a instalação no nível especificado ou acima dele.

Quando você especifica `syslogEvents`, o LAD sempre grava dados em uma tabela no armazenamento do Azure. Você pode ter os mesmos dados gravados em BLOBs JSON e/ou hubs de eventos, mas não pode desabilitar o armazenamento de dados em uma tabela. O comportamento de particionamento para essa tabela é o mesmo descrito para `performanceCounters`. O nome da tabela é a concatenação dessas cadeias de caracteres:

* `LinuxSyslog`
* Uma data, no formato "aaaammdd", que muda a cada 10 dias

Os exemplos incluem `LinuxSyslog20170410` e `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Esta seção opcional controla a execução de consultas [OMI](https://github.com/Microsoft/omi) arbitrárias.

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
espaço de nomes | adicional O namespace OMI no qual a consulta deve ser executada. Se não for especificado, o valor padrão será "root/SCX", implementado pelos [provedores de plataforma cruzada do System Center](https://github.com/Microsoft/SCXcore).
consulta | A consulta OMI a ser executada.
table | adicional A tabela de armazenamento do Azure, na conta de armazenamento designada (consulte [Configurações protegidas](#protected-settings)).
frequência | adicional O número de segundos entre a execução da consulta. O valor padrão é 300 (5 minutos); o valor mínimo é 15 segundos.
coletores | adicional Uma lista separada por vírgulas de nomes de coletores adicionais para os quais os resultados brutos da métrica de exemplo devem ser publicados. Nenhuma agregação dessas amostras brutas é computada pela extensão ou pelas métricas do Azure.

A "tabela" ou "coletores" ou ambos devem ser especificados.

### <a name="filelogs"></a>fileLogs

Controla a captura de arquivos de log. O LAD captura novas linhas de texto conforme elas são gravadas no arquivo e as grava em linhas de tabela e/ou quaisquer coletores especificados (JsonBlob ou EventHub).

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
file | O nome do caminho completo do arquivo de log a ser monitorado e capturado. O PathName deve nomear um único arquivo; Ele não pode nomear um diretório nem conter curingas.
table | adicional A tabela de armazenamento do Azure, na conta de armazenamento designada (conforme especificado na configuração protegida), na qual novas linhas da "parte final" do arquivo são gravadas.
coletores | adicional Uma lista separada por vírgulas de nomes de coletores adicionais para os quais as linhas de log são enviadas.

A "tabela" ou "coletores" ou ambos devem ser especificados.

## <a name="metrics-supported-by-the-builtin-provider"></a>Métricas com suporte do provedor interno

O provedor de métrica interna é uma fonte de métricas mais interessante para um amplo conjunto de usuários. Essas métricas se enquadram em cinco classes amplas:

* Processador
* Memória
* Rede
* Sistema de ficheiros
* Discos

### <a name="builtin-metrics-for-the-processor-class"></a>métricas internas para a classe de processador

A classe de métricas processador fornece informações sobre o uso do processador na VM. Ao agregar porcentagens, o resultado é a média em todas as CPUs. Em uma VM de duas vCPU, se uma vCPU fosse 100% ocupada e a outra fosse 100% ociosa, o PercentIdleTime relatado seria 50. Se cada vCPU fosse 50% Busy durante o mesmo período, o resultado relatado também será 50. Em uma VM de quatro vCPU, com uma vCPU 100% ocupada e as outras ociosas, o PercentIdleTime relatado seria 75.

counter | Significado
------- | -------
PercentIdleTime | Porcentagem de tempo durante a janela de agregação que os processadores estavam executando o loop ocioso do kernel
PercentProcessorTime | Porcentagem de tempo que executa um thread não ocioso
PercentIOWaitTime | Porcentagem de tempo aguardando a conclusão das operações de e/s
PercentInterruptTime | Porcentagem de tempo que executa interrupções de hardware/software e DPCs (chamadas de procedimento deferidas)
PercentUserTime | De tempo não ocioso durante a janela de agregação, a porcentagem de tempo gasto no usuário mais em prioridade normal
PercentNiceTime | De tempo não ocioso, o percentual gastou em uma prioridade reduzida (boa)
PercentPrivilegedTime | De tempo não ocioso, o percentual gasto no modo privilegiado (kernel)

Os primeiros quatro contadores devem somar 100%. Os últimos três contadores também somam 100%; Eles subdividem a soma de PercentProcessorTime, PercentIOWaitTime e PercentInterruptTime.

Para obter uma única métrica agregada em todos os processadores, defina `"condition": "IsAggregate=TRUE"`. Para obter uma métrica para um processador específico, como o segundo processador lógico de uma VM de quatro vCPU, defina `"condition": "Name=\\"1\\""`. Os números de processador lógicos estão no intervalo `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>métricas internas para a classe de memória

A classe de métricas de memória fornece informações sobre utilização de memória, paginação e troca.

counter | Significado
------- | -------
AvailableMemory | Memória física disponível na MiB
PercentAvailableMemory | Memória física disponível como uma porcentagem da memória total
UsedMemory | Memória física em uso (MiB)
PercentUsedMemory | Memória física em uso como uma porcentagem da memória total
PagesPerSec | Paginação total (leitura/gravação)
PagesReadPerSec | Páginas lidas do repositório de backup (arquivo de permuta, arquivo de programa, arquivo mapeado, etc.)
PagesWrittenPerSec | Páginas gravadas no repositório de backup (arquivo de permuta, arquivo mapeado, etc.)
AvailableSwap | Espaço de permuta não utilizado (MiB)
PercentAvailableSwap | Espaço de permuta não utilizado como uma porcentagem do total de permuta
UsedSwap | MiB (espaço de permuta em uso)
PercentUsedSwap | Espaço de permuta em uso como um percentual do total de permuta

Essa classe de métricas tem apenas uma única instância. O atributo "Condition" não tem configurações úteis e deve ser omitido.

### <a name="builtin-metrics-for-the-network-class"></a>métricas internas para a classe de rede

A classe de métricas de rede fornece informações sobre a atividade de rede em uma interface de rede individual desde a inicialização. O LAD não expõe métricas de largura de banda, que podem ser recuperadas de métricas de host.

counter | Significado
------- | -------
BytesTransmitted | Total de bytes enviados desde a inicialização
BytesReceived | Total de bytes recebidos desde a inicialização
BytesTotal | Total de bytes enviados ou recebidos desde a inicialização
PacketsTransmitted | Total de pacotes enviados desde a inicialização
PacketsReceived | Total de pacotes recebidos desde a inicialização
TotalRxErrors | Número de erros de recebimento desde a inicialização
TotalTxErrors | Número de erros de transmissão desde a inicialização
TotalCollisions | Número de colisões relatadas pelas portas de rede desde a inicialização

 Embora essa classe seja instância, o LAD não dá suporte à captura de métricas de rede agregadas em todos os dispositivos de rede. Para obter as métricas para uma interface específica, como eth0, defina `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>métricas internas para a classe FileSystem

A classe de métricas FileSystem fornece informações sobre o uso do sistema de arquivos. Os valores absoluto e percentual são relatados como seriam exibidos para um usuário comum (não raiz).

counter | Significado
------- | -------
FreeSpace | Espaço em disco disponível em bytes
UsedSpace | Espaço em disco usado em bytes
PercentFreeSpace | Percentual de espaço livre
PercentUsedSpace | Percentual de espaço usado
PercentFreeInodes | Porcentagem de inodes não utilizados
PercentUsedInodes | Percentual de inodes alocados (em uso) somados em todos os sistemas de sistema
BytesReadPerSecond | Bytes lidos por segundo
BytesWrittenPerSecond | Bytes gravados por segundo
BytesPerSecond | Bytes lidos ou gravados por segundo
ReadsPerSecond | Operações de leitura por segundo
WritesPerSecond | Operações de gravação por segundo
TransfersPerSecond | Operações de leitura ou gravação por segundo

Valores agregados em todos os sistemas de arquivos podem ser obtidos pela definição de `"condition": "IsAggregate=True"`. Os valores para um sistema de arquivos montado específico, como "/mnt", podem ser obtidos com a configuração de `"condition": 'Name="/mnt"'`. 

**Observação**: se estiver usando o portal do Azure em vez de JSON, o formulário de campo de condição correto será Name = '/mnt '

### <a name="builtin-metrics-for-the-disk-class"></a>métricas internas para a classe de disco

A classe de métricas de disco fornece informações sobre o uso do dispositivo de disco. Essas estatísticas se aplicam a toda a unidade. Se houver vários sistemas de arquivos em um dispositivo, os contadores para esse dispositivo serão, com eficiência, agregados em todos eles.

counter | Significado
------- | -------
ReadsPerSecond | Operações de leitura por segundo
WritesPerSecond | Operações de gravação por segundo
TransfersPerSecond | Total de operações por segundo
AverageReadTime | Média de segundos por operação de leitura
AverageWriteTime | Média de segundos por operação de gravação
AverageTransferTime | Média de segundos por operação
AverageDiskQueueLength | Número médio de operações de disco em fila
ReadBytesPerSecond | Número de bytes lidos por segundo
WriteBytesPerSecond | Número de bytes gravados por segundo
BytesPerSecond | Número de bytes lidos ou gravados por segundo

Valores agregados em todos os discos podem ser obtidos pela definição de `"condition": "IsAggregate=True"`. Para obter informações para um dispositivo específico (por exemplo,/dev/sdf1), defina `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalando e Configurando o LAD 3,0 via CLI

Supondo que suas configurações protegidas estejam no arquivo PrivateConfig. JSON e suas informações de configuração pública estejam em PublicConfig. JSON, execute este comando:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

O comando pressupõe que você esteja usando o modo de gerenciamento de recursos do Azure (ARM) do CLI do Azure. Para configurar o LAD para VMs do modelo de implantação clássica (ASM), alterne para o modo "ASM" (`azure config mode asm`) e omita o nome do grupo de recursos no comando. Para obter mais informações, consulte a [documentação da CLI de plataforma cruzada](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Um exemplo de configuração do LAD 3,0

Com base nas definições anteriores, aqui está um exemplo de configuração de extensão do LAD 3,0 com alguma explicação. Para aplicar esse exemplo ao seu caso, você deve usar seu próprio nome de conta de armazenamento, token SAS de conta e tokens SAS EventHubs.

### <a name="privateconfigjson"></a>PrivateConfig.json

Essas configurações particulares são configuradas:

* Uma conta de armazenamento
* um token SAS da conta correspondente
* vários coletores (JsonBlob ou EventHubs com tokens SAS)

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

Essas configurações públicas fazem com que o LAD:

* Carregar a porcentagem de tempo do processador e as métricas de espaço em disco usadas para a tabela de `WADMetrics*`
* Carregar mensagens do recurso de syslog "usuário" e "informações" de gravidade para a tabela de `LinuxSyslog*`
* Carregar resultados brutos da consulta OMI (PercentProcessorTime e PercentIdleTime) na tabela `LinuxCPU` nomeada
* Carregar linhas acrescentadas no arquivo `/var/log/myladtestlog` à tabela `MyLadTestLog`

Em cada caso, os dados também são carregados em:

* Armazenamento de BLOBs do Azure (o nome do contêiner é conforme definido no coletor JsonBlob)
* Ponto de extremidade EventHubs (conforme especificado no coletor EventHubs)

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

O `resourceId` na configuração deve corresponder ao da VM ou ao conjunto de dimensionamento de máquinas virtuais.

* O gráfico e os alertas das métricas da plataforma Azure conhecem o ResourceId da VM em que você está trabalhando. Ele espera localizar os dados para sua VM usando o ResourceId a chave de pesquisa.
* Se você usar o dimensionamento automático do Azure, o ResourceId na configuração de dimensionamento automático deve corresponder ao ResourceId usado pelo LAD.
* O ResourceId é incorporado aos nomes de JsonBlobs gravados pelo LAD.

## <a name="view-your-data"></a>Ver os dados

Use o portal do Azure para exibir dados de desempenho ou definir alertas:

![imagem](./media/diagnostics-linux/graph_metrics.png)

Os dados de `performanceCounters` sempre são armazenados em uma tabela de armazenamento do Azure. As APIs de armazenamento do Azure estão disponíveis para várias linguagens e plataformas.

Os dados enviados aos coletores do JsonBlob são armazenados em BLOBs na conta de armazenamento denominada nas [Configurações protegidas](#protected-settings). Você pode consumir os dados de BLOB usando qualquer API de armazenamento de BLOBs do Azure.

Além disso, você pode usar essas ferramentas de interface do usuário para acessar os dados no armazenamento do Azure:

* Gerenciador de Servidores do Visual Studio.
* [Explorador de Armazenamento do Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Explorador do Armazenamento do Azure").

Esse instantâneo de uma sessão de Gerenciador de Armazenamento do Microsoft Azure mostra as tabelas de armazenamento do Azure e os contêineres gerados de uma extensão LAD 3,0 configurada corretamente em uma VM de teste. A imagem não corresponde exatamente à [configuração de exemplo do LAD 3,0](#an-example-lad-30-configuration).

![imagem](./media/diagnostics-linux/stg_explorer.png)

Consulte a documentação relevante do [EventHubs](../../event-hubs/event-hubs-what-is-event-hubs.md) para saber como consumir mensagens publicadas em um ponto de extremidade do EventHubs.

## <a name="next-steps"></a>Passos seguintes

* Crie alertas de métrica em [Azure monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) para as métricas coletadas.
* Crie [gráficos de monitoramento](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para suas métricas.
* Saiba como [criar um conjunto de dimensionamento de máquinas virtuais](../linux/tutorial-create-vmss.md) usando suas métricas para controlar o dimensionamento automático.
