---
title: Adicione diagnósticos de monitorização & a uma máquina virtual Azure
description: Utilize um modelo de Gestor de Recursos Azure para criar uma nova máquina virtual do Windows com extensão de diagnóstico Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: mimckitt
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 31f690277675650323763a7bc6872ad736f5776c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87837011"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Utilize monitorização e diagnósticos com modelos windows VM e Azure Resource Manager
A Extensão de Diagnóstico Azure fornece as capacidades de monitorização e diagnóstico numa máquina virtual Azure baseada no Windows. Pode ativar estas capacidades na máquina virtual, incluindo a extensão como parte do modelo do Gestor de Recursos Azure. Consulte [modelos de gestor de recursos Azure com extensões VM](../windows/template-description.md#extensions) para obter mais informações sobre a inclusão de qualquer extensão como parte de um modelo de máquina virtual. Este artigo descreve como pode adicionar a extensão Azure Diagnostics a um modelo de máquina virtual windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adicione a extensão Azure Diagnostics à definição de recurso VM
Para ativar a extensão de diagnóstico numa Máquina Virtual do Windows, é necessário adicionar a extensão como recurso VM no modelo de Gestor de Recursos.

Para uma máquina virtual baseada em gestor de recursos simples, adicione a configuração de extensão à matriz de *recursos* para a Máquina Virtual: 

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

Outra convenção comum é adicionar a configuração de extensão no nó de recursos radií outros do modelo em vez de defini-lo sob o nó de recursos da máquina virtual. Com esta abordagem, tem de especificar explicitamente uma relação hierárquica entre a extensão e a máquina virtual com o *nome* e os valores *do tipo.* Por exemplo: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

A extensão está sempre associada à máquina virtual, pode defini-la diretamente sob o nó de recursos da máquina virtual diretamente ou defini-la ao nível base e usar a convenção hierárquica de nomeação para associá-la à máquina virtual.

Para conjuntos de escala de máquina virtual, a configuração das extensões é especificada na *extensãoProfile* propriedade do *VirtualMachineProfile*.

A propriedade *da editora* com o valor de **Microsoft.Azure.Diagnostics** e a propriedade *tipo* com o valor de **IaaSDiagnostics** identificam exclusivamente a extensão Azure Diagnostics.

O valor da propriedade *do nome* pode ser usado para se referir à extensão no grupo de recursos. Defini-lo especificamente para **Microsoft.Insights.VMDiagnosticsSettings** permite que seja facilmente identificado pelo portal Azure, garantindo que os gráficos de monitorização aparecem corretamente no portal Azure.

O *typeHandlerVersion* especifica a versão da extensão que gostaria de utilizar. Configurar a versão *menor autoUpgradeMinorVersion* para **ser verdadeira,** garante que obtém a versão Minor mais recente da extensão que está disponível. É altamente recomendável que sempre ajuste *autoUpgradeMinversion* para ser sempre **verdadeiro** para que você sempre possa usar a mais recente extensão de diagnóstico disponível com todas as novas funcionalidades e correções de bugs. 

O elemento *de definições* contém propriedades de configurações para a extensão que podem ser definidas e lidas a partir da extensão (por vezes referida como configuração pública). A propriedade *xmlcfg* contém configuração baseada em xml para os registos de diagnóstico, contadores de desempenho etc que são recolhidos pelo agente de diagnóstico. Consulte [o Esquema de Configuração de Diagnóstico](../../azure-monitor/platform/diagnostics-extension-schema-windows.md) para obter mais informações sobre o próprio esquema de xml. Uma prática comum é armazenar a configuração xml real como uma variável no modelo Azure Resource Manager e, em seguida, concatenato e base64 codifica-los para definir o valor para *xmlcfg*. Consulte a secção sobre [variáveis de configuração de diagnóstico](#diagnostics-configuration-variables) para entender mais sobre como armazenar o xml em variáveis. A propriedade *de armazenamentoAcolho* especifica o nome da conta de armazenamento para a qual os dados de diagnóstico são transferidos. 

As propriedades em configurações protegidas (por *vezes designadas* como configuração privada) podem ser definidas, mas não podem ser lidas depois de definidas. A natureza apenas de escrita de *dispositivos protegidos torna-o* útil para armazenar segredos como a chave da conta de armazenamento onde os dados de diagnóstico são escritos.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Especificar a conta de armazenamento de diagnósticos como parâmetros
A extensão de diagnóstico json snippet acima assume dois parâmetros *existentesdiagnosticsStorageAccountName* e *os já existentesdiagnosticsStorageResourceGroup* para especificar a conta de armazenamento de diagnósticos onde os dados de diagnóstico são armazenados. Especificar a conta de armazenamento de diagnóstico como um parâmetro facilita a alteração da conta de armazenamento de diagnósticos em diferentes ambientes, por exemplo, pode querer utilizar uma conta de armazenamento de diagnósticos diferente para testes e outra diferente para a sua implementação de produção.  

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

É melhor especificar uma conta de armazenamento de diagnósticos num grupo de recursos diferente do grupo de recursos para a máquina virtual. Um grupo de recursos pode ser considerado uma unidade de implantação com a sua própria vida útil, uma máquina virtual pode ser implantada e redistribuída à medida que novas configurações atualizações são feitas para ele, mas você pode querer continuar a armazenar os dados de diagnóstico na mesma conta de armazenamento através dessas implementações de máquinas virtuais. Ter a conta de armazenamento num recurso diferente permite que a conta de armazenamento aceite dados de várias implementações de máquinas virtuais, facilitando a resolução de problemas nas várias versões.

> [!NOTE]
> Se criar um modelo de máquina virtual windows a partir do Visual Studio, a conta de armazenamento predefinida poderá ser definida para utilizar a mesma conta de armazenamento onde a máquina virtual VHD é carregada. Isto é para simplificar a configuração inicial do VM. Re-factor o modelo para usar uma conta de armazenamento diferente que pode ser passada como um parâmetro. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Variáveis de configuração de diagnóstico
A extensão de diagnóstico anterior json snippet define uma variável *accountid* para simplificar a obtenção da chave de conta de armazenamento para o armazenamento de diagnósticos:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

A propriedade *xmlcfg* para a extensão de diagnóstico é definida usando múltiplas variáveis que são concatenadas em conjunto. Os valores destas variáveis estão em xml, pelo que precisam de ser corretamente escapados ao definir as variáveis json.

O exemplo a seguir descreve a configuração de diagnóstico xml que recolhe contadores de desempenho de nível padrão do sistema, juntamente com alguns registos de eventos de janelas e registos de infraestruturas de diagnóstico. Foi escapado e formatado corretamente para que a configuração possa ser colada diretamente na secção de variáveis do seu modelo. Consulte o Esquema de [Configuração de Diagnósticos](../../azure-monitor/platform/diagnostics-extension-schema-windows.md) para obter um exemplo mais legível para o xml de configuração.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

O nó xml de definição de Métricas na configuração acima é um elemento de configuração importante, pois define como os contadores de desempenho definidos anteriormente no xml no nó *PerformanceCounter* são agregados e armazenados. 

> [!IMPORTANT]
> Estas métricas conduzem os gráficos de monitorização e alertas no portal Azure.  O nó **métrica** com o *recursoID* e **a agregação MetricA** deve ser incluído na configuração de diagnóstico para o seu VM se quiser ver os dados de monitorização VM no portal Azure. 
> 
> 

O exemplo a seguir mostra o xml para definições de métricas: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

O atributo *resourceID* identifica exclusivamente a máquina virtual na sua subscrição. Certifique-se de que utiliza as funções de subscrição e grupo de recursos para que o modelo atualize automaticamente esses valores com base na subscrição e grupo de recursos para o qual está a implementar.

Se estiver a criar várias Máquinas Virtuais num loop, tem de preencher o valor *deID de recurso* com uma função copyIndex() para diferenciar corretamente cada VM individual. O valor *xmlCfg* pode ser atualizado para suportar o seguinte:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

O valor de agregação métrica de *PT1M* e *PT1H* significa uma agregação ao longo de um minuto e uma agregação ao longo de uma hora, respectivamente.

## <a name="wadmetrics-tables-in-storage"></a>Mesas WADMetrics em armazenamento
A configuração métrica acima gera tabelas na sua conta de armazenamento de diagnóstico com as seguintes convenções de nomeação:

* **WADMetrics**: Prefixo padrão para todas as tabelas WADMetrics
* **PT1H** ou **PT1M**: Significa que a tabela contém dados agregados ao longo de 1 hora ou 1 minuto
* **P10D**: Significa que a tabela conterá dados durante 10 dias a partir do momento em que a tabela começou a recolher dados
* **V2S**: Constante de cordas
* **yyymmdd**: A data em que a mesa começou a recolher dados

Exemplo: *WADMetricsPT1HP10DV2S20151108* contém dados métricos agregados ao longo de uma hora durante 10 dias a partir de 11-Nov-2015    

Cada tabela WADMetrics contém as seguintes colunas:

* **PartitionKey**: A chave de partição é construída com base no valor *de recursosID* para identificar exclusivamente o recurso VM. Por exemplo: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Segue o formato `<Descending time tick>:<Performance Counter Name>` . O cálculo do tique-taque do tempo descendente é o tempo máximo, menos o tempo do início do período de agregação. Por exemplo, se o período de amostragem começou em 10-Nov-2015 e 00:00Hrs UTC, então o cálculo seria: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)` . Para a memória disponível bytes contador de desempenho a tecla de linha será: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **Contra-Nome**: É o nome do contador de desempenho. Isto corresponde ao *contraSpecifier* definido no xml config.
* **Máximo**: O valor máximo do contador de desempenho durante o período de agregação.
* **Mínimo**: O valor mínimo do contador de desempenho durante o período de agregação.
* **Total**: A soma de todos os valores do contador de desempenho reportados durante o período de agregação.
* **Contagem**: O número total de valores reportados para o contador de desempenho.
* **Média**: O valor médio (total/contagem) do contador de desempenho durante o período de agregação.

## <a name="next-steps"></a>Passos Seguintes
* Para obter um modelo completo de amostra de uma máquina virtual do Windows com extensão de diagnóstico, consulte [a extensão de diagnóstico de diagnóstico de monitorização de 201 vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Implemente o modelo do Gestor de Recursos Azure utilizando a linha de comando [Azure PowerShell](../windows/ps-template.md) ou [Azure Command Line](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Saiba mais sobre [a autoria de modelos do Gestor de Recursos Azure](../../azure-resource-manager/templates/template-syntax.md)