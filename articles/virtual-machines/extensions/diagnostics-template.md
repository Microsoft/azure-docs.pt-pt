---
title: Adicionar monitoramento & diagnóstico a uma máquina virtual do Azure
description: Use um modelo de Azure Resource Manager para criar uma nova máquina virtual do Windows com a extensão de diagnóstico do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2490c3de60e0deac6a1a4ddc5abc95cb46e240b2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073849"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Usar monitoramento e diagnóstico com uma VM do Windows e modelos de Azure Resource Manager
A extensão Diagnóstico do Azure fornece os recursos de monitoramento e diagnóstico em uma máquina virtual do Azure baseada no Windows. Você pode habilitar esses recursos na máquina virtual incluindo a extensão como parte do modelo de Azure Resource Manager. Consulte [criando modelos de Azure Resource Manager com extensões de VM](../windows/template-description.md#extensions) para obter mais informações sobre como incluir qualquer extensão como parte de um modelo de máquina virtual. Este artigo descreve como você pode adicionar a extensão de Diagnóstico do Azure a um modelo de máquina virtual do Windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adicionar a extensão de Diagnóstico do Azure à definição de recurso de VM
Para habilitar a extensão de diagnóstico em uma máquina virtual do Windows, você precisa adicionar a extensão como um recurso de VM no modelo do Resource Manager.

Para uma máquina virtual baseada no Gerenciador de recursos simples, adicione a configuração de extensão à matriz de *recursos* para a máquina virtual: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Outra convenção comum é adicionar a configuração de extensão no nó de recursos raiz do modelo, em vez de defini-la no nó recursos da máquina virtual. Com essa abordagem, você precisa especificar explicitamente uma relação hierárquica entre a extensão e a máquina virtual com os valores de *nome* e *tipo* . Por exemplo: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

A extensão é sempre associada à máquina virtual, você pode defini-la diretamente no nó de recurso da máquina virtual diretamente ou defini-la no nível de base e usar a Convenção de nomenclatura hierárquica para associá-la à máquina virtual.

Para conjuntos de dimensionamento de máquinas virtuais, a configuração de extensões é especificada na propriedade *extensionProfile* do *VirtualMachineProfile*.

A propriedade *Publisher* com o valor de **Microsoft. Azure. Diagnostics** e a propriedade *Type* com o valor de **IaaSDiagnostics** identifica exclusivamente a extensão diagnóstico do Azure.

O valor da propriedade *Name* pode ser usado para fazer referência à extensão no grupo de recursos. Defini-lo especificamente como **Microsoft. insights. VMDiagnosticsSettings** permite que ele seja facilmente identificado pelo portal do Azure, garantindo que os gráficos de monitoramento sejam exibidos corretamente no portal do Azure.

O *typeHandlerVersion* especifica a versão da extensão que você deseja usar. Definir a versão secundária *autoUpgradeMinorVersion* como **true** garante que você obtenha a versão secundária mais recente da extensão que está disponível. É altamente recomendável que você sempre defina *autoUpgradeMinorVersion* para sempre ser **verdadeiro** para que você sempre tenha que usar a extensão de diagnóstico mais recente disponível com todos os novos recursos e correções de bugs. 

O elemento *Settings* contém as propriedades de configurações da extensão que podem ser definidas e lidas de volta da extensão (às vezes conhecida como configuração pública). A propriedade *xmlcfg* contém a configuração baseada em XML para os logs de diagnóstico, os contadores de desempenho, etc., que são coletados pelo agente de diagnóstico. Consulte [esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter mais informações sobre o próprio esquema XML. Uma prática comum é armazenar a configuração XML real como uma variável no modelo Azure Resource Manager e, em seguida, concatenar e codificar Base64 e codificá-las para definir o valor de *xmlcfg*. Consulte a seção sobre as [variáveis de configuração de diagnóstico](#diagnostics-configuration-variables) para entender mais sobre como armazenar o XML em variáveis. A propriedade *storageAccount* especifica o nome da conta de armazenamento para a qual os dados de diagnóstico são transferidos. 

As propriedades em *protectedSettings* (às vezes chamadas de configuração privada) podem ser definidas, mas não podem ser lidas depois de serem definidas. A natureza somente gravação de *protectedSettings* torna útil armazenar segredos como a chave da conta de armazenamento onde os dados de diagnóstico são gravados.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Especificando a conta de armazenamento de diagnóstico como parâmetros
O trecho de JSON da extensão de diagnóstico acima pressupõe dois parâmetros *existingdiagnosticsStorageAccountName* e *existingdiagnosticsStorageResourceGroup* para especificar a conta de armazenamento de diagnóstico em que os dados de diagnóstico são armazenados. A especificação da conta de armazenamento de diagnóstico como um parâmetro facilita a alteração da conta de armazenamento de diagnóstico em diferentes ambientes, por exemplo, talvez você queira usar uma conta de armazenamento de diagnóstico diferente para teste e outra para o seu implantação de produção.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

É recomendável especificar uma conta de armazenamento de diagnóstico em um grupo de recursos diferente do grupo de recursos para a máquina virtual. Um grupo de recursos pode ser considerado uma unidade de implantação com seu próprio tempo de vida, uma máquina virtual pode ser implantada e reimplantada à medida que novas atualizações de configurações são feitas nela, mas talvez você queira continuar armazenando os dados de diagnóstico na mesma conta de armazenamento em essas implantações de máquina virtual. Ter a conta de armazenamento em um recurso diferente permite que a conta de armazenamento aceite dados de várias implantações de máquina virtual, facilitando a solução de problemas em várias versões.

> [!NOTE]
> Se você criar um modelo de máquina virtual do Windows a partir do Visual Studio, a conta de armazenamento padrão poderá ser definida para usar a mesma conta de armazenamento em que o VHD da máquina virtual é carregado. Isso é para simplificar a configuração inicial da VM. Refatore o modelo para usar uma conta de armazenamento diferente que pode ser passada como um parâmetro. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Variáveis de configuração de diagnóstico
O trecho de JSON da extensão de diagnóstico anterior define uma variável *AccountId* para simplificar a obtenção da chave de conta de armazenamento para o armazenamento de diagnóstico:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

A propriedade *xmlcfg* para a extensão de diagnóstico é definida usando várias variáveis que são concatenadas juntas. Os valores dessas variáveis estão em XML para que eles precisem ser ignorados corretamente ao definir as variáveis JSON.

O exemplo a seguir descreve o XML de configuração de diagnóstico que coleta contadores de desempenho de nível de sistema padrão juntamente com alguns logs de eventos do Windows e logs de infraestrutura de diagnóstico. Ele foi escapado e formatado corretamente para que a configuração possa ser colado diretamente na seção de variáveis do seu modelo. Consulte o [esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter um exemplo legível mais humano do XML de configuração.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

O nó XML de definição de métricas na configuração acima é um elemento de configuração importante, pois define como os contadores de desempenho definidos anteriormente no XML no nó *PerformanceCounter* são agregados e armazenados. 

> [!IMPORTANT]
> Essas métricas orientam os gráficos de monitoramento e alertas no portal do Azure.  O nó de **métricas** com *ResourceId* e **MetricAggregation** deve ser incluído na configuração de diagnóstico para sua VM se você quiser ver os dados de monitoramento de VM no portal do Azure. 
> 
> 

O exemplo a seguir mostra o XML para definições de métricas: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

O atributo *ResourceId* identifica exclusivamente a máquina virtual em sua assinatura. Certifique-se de usar as funções Subscription () e resourcegroup () para que o modelo atualize automaticamente esses valores com base na assinatura e no grupo de recursos que você está implantando.

Se você estiver criando várias máquinas virtuais em um loop, será necessário preencher o valor *ResourceId* com uma função copyIndex () para diferenciar corretamente cada VM individual. O valor de *xmlCfg* pode ser atualizado para dar suporte a isso da seguinte maneira:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

O valor MetricAggregation de *PT1M* e *PT1H* significam uma agregação em um minuto e uma agregação em uma hora, respectivamente.

## <a name="wadmetrics-tables-in-storage"></a>Tabelas WADMetrics no armazenamento
A configuração de métricas acima gera tabelas em sua conta de armazenamento de diagnóstico com as seguintes convenções de nomenclatura:

* **WADMetrics**: prefixo padrão para todas as tabelas WADMetrics
* **PT1H** ou **PT1M**: significa que a tabela contém dados agregados em 1 hora ou 1 minuto
* **P10D**: significa que a tabela conterá dados por 10 dias a partir do momento em que a tabela começou a coletar dados
* **V2s**: constante de cadeia de caracteres
* **aaaammdd**: a data na qual a tabela começou a coletar dados

Exemplo: *WADMetricsPT1HP10DV2S20151108* contém dados de métrica agregados em uma hora por 10 dias a partir de 11 de novembro de 2015    

Cada tabela WADMetrics contém as seguintes colunas:

* **PartitionKey**: a chave de partição é construída com base no valor *ResourceId* para identificar exclusivamente o recurso da VM. Por exemplo: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: segue o formato `<Descending time tick>:<Performance Counter Name>`. O cálculo da marcação de tempo decrescente é de tiques de tempo máximos menos a hora do início do período de agregação. Por exemplo, se o período de exemplo começou em 10-Nov-2015 e 00:00Hrs UTC, o cálculo seria: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Para o contador de desempenho bytes disponíveis de memória, a chave de linha será semelhante a: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: é o nome do contador de desempenho. Isso corresponde ao *especificador* de coincidência definido na configuração XML.
* **Máximo**: o valor máximo do contador de desempenho durante o período de agregação.
* **Mínimo**: o valor mínimo do contador de desempenho durante o período de agregação.
* **Total**: a soma de todos os valores do contador de desempenho relatados no período de agregação.
* **Contagem**: o número total de valores relatados para o contador de desempenho.
* **Média**: o valor médio (total/contagem) do contador de desempenho durante o período de agregação.

## <a name="next-steps"></a>Passos Seguintes
* Para obter um modelo de exemplo completo de uma máquina virtual do Windows com extensão de diagnóstico, consulte [201-VM-Monitoring-Diagnostics-Extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Implantar o modelo de Azure Resource Manager usando a [Azure PowerShell](../windows/ps-template.md) ou a [linha de comando do Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Saiba mais sobre a [criação de modelos de Azure Resource Manager](../../resource-group-authoring-templates.md)
