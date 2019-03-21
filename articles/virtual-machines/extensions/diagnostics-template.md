---
title: Adicionar a monitorização e diagnóstico para uma máquina virtual do Azure | Documentos da Microsoft
description: Utilize um modelo Azure Resource Manager para criar uma nova máquina virtual do Windows com a extensão de diagnóstico do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 00b4a145da9104cab410c5a07f6d7ec5ded5c45d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893548"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Utilizar a monitorização e diagnóstico com modelos de VM do Windows e do Azure Resource Manager
A extensão de diagnóstico do Azure fornece as capacidades de monitorização e diagnóstico numa máquina virtual do Azure baseado no Windows. Pode ativar estas capacidades na máquina virtual, incluindo a extensão como parte do modelo do Azure Resource Manager. Ver [criação de modelos do Azure Resource Manager com extensões de VM](../windows/template-description.md#extensions) para obter mais informações sobre a inclusão de qualquer extensão como parte de um modelo de máquina virtual. Este artigo descreve como adicionar a extensão de diagnóstico do Azure para um modelo de máquina virtual do windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adicione a extensão de diagnóstico do Azure para a definição do recurso VM
Para ativar a extensão de diagnóstico numa máquina de Virtual do Windows, terá de adicionar a extensão como um recurso VM no modelo do Resource Manager.

Para um Gerenciador de recursos simples baseada em máquina de Virtual adicionar a configuração da extensão para o *recursos* matriz para a Máquina Virtual: 

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

Outra convenção comum é adicionar a configuração de extensão no nó de recursos de raiz do modelo em vez de defini-lo no nó de recursos da máquina virtual. Com esta abordagem, precisa especificar explicitamente uma relação hierárquica entre a extensão e a máquina virtual com o *name* e *tipo* valores. Por exemplo: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

A extensão é sempre associada com a máquina virtual, pode diretamente defini-lo diretamente no nó de recursos da máquina virtual ou defini-lo no nível de base e utilizar a Convenção de nomenclatura hierárquica para associá-lo com a máquina virtual.

Para conjuntos de dimensionamento de máquinas virtuais, a configuração de extensões é especificada na *extensionProfile* propriedade da *VirtualMachineProfile*.

O *publicador* propriedade com o valor de **Microsoft.Azure.Diagnostics** e o *tipo* propriedade com o valor de **IaaSDiagnostics**identificar exclusivamente a extensão de diagnóstico do Azure.

O valor do *nome* propriedade pode ser usada para fazer referência a extensão no grupo de recursos. Defini-la especificamente ao **Microsoft.Insights.VMDiagnosticsSettings** lhe permite ser facilmente identificadas pelo portal do Azure, garantindo que a monitorização de gráficos de mostrar se corretamente no portal do Azure.

O *typeHandlerVersion* Especifica a versão da extensão que pretende utilizar. A definição *autoUpgradeMinorVersion* versão secundária para **verdadeiro** garante que obtenha a versão secundária mais recente da extensão do que está disponível. É altamente recomendável que sempre definidas *autoUpgradeMinorVersion* seja sempre **verdadeiro** para que sempre possa utilizar a extensão de diagnóstico disponível mais recente com todas as novas funcionalidades e correções de erros. 

O *definições* elemento contém propriedades de configurações para a extensão que podem ser definidas e ler novamente a extensão (por vezes referida como configuração pública). O *xmlcfg* propriedade contém a configuração de xml com base para os registos de diagnóstico, contadores de desempenho etc que são recolhidos pelo agente de diagnóstico. Ver [esquema de configuração de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter mais informações sobre o esquema de xml em si. Uma prática comum é armazenar a configuração xml real como uma variável no modelo do Azure Resource Manager e, em seguida, concatenar e base64 codificá-los para definir o valor para *xmlcfg*. Consulte a secção sobre [variáveis de configuração de diagnósticos](#diagnostics-configuration-variables) para saber mais sobre como armazenar o xml em variáveis. O *storageAccount* propriedade especifica o nome da conta de armazenamento para diagnóstico do qual os dados são transferidos. 

As propriedades na *protectedSettings* (configuração, às vezes, referida como particulares) pode ser definida, mas não podem ser lidos novamente após a ser definido. A natureza somente gravação *protectedSettings* que é útil para armazenar segredos, como a chave de conta de armazenamento onde os dados de diagnóstico estão escritos.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Especificar a conta de armazenamento de diagnóstico como parâmetros
O fragmento de json da extensão de diagnóstico acima assume dois parâmetros *existingdiagnosticsStorageAccountName* e *existingdiagnosticsStorageResourceGroup* para especificar o armazenamento de diagnóstico conta em que os dados são armazenados. Especificar a conta de armazenamento de diagnóstico como um parâmetro torna mais fácil alterar a conta de armazenamento de diagnósticos em ambientes diferentes, por exemplo, talvez queira usar uma conta de armazenamento de diagnóstico diferente para testes e outra para o implementação de produção.  

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

É melhor prática para especificar uma conta de armazenamento de diagnósticos no grupo de recursos diferente do que o grupo de recursos para a máquina virtual. Um grupo de recursos pode ser considerado uma unidade de implementação com o seu próprio ciclo de vida, pode ser implementada e reimplantada conforme novas atualizações de configurações são feitas ao mesmo, mas talvez queira continuar a armazenar os dados de diagnóstico na mesma conta de armazenamento numa máquina virtual essas implementações de máquina virtual. Ter a conta de armazenamento num recurso diferente, permite que a conta de armazenamento aceitar dados a partir de várias implementações de máquinas virtuais que facilita a resolução de problemas para as várias versões.

> [!NOTE]
> Se criar um modelo de máquina virtual do windows a partir do Visual Studio, a conta de armazenamento predefinida que pode ser definida para utilizar a mesma conta de armazenamento onde o VHD da máquina virtual é carregado. Esta é a simplificar a configuração inicial da VM. Decompor novamente o modelo a utilizar uma conta de armazenamento diferentes que pode ser passada como um parâmetro. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Variáveis de configuração de diagnósticos
O fragmento de json de extensão de diagnóstico anterior define um *accountid* variável para simplificar a obter a chave de conta de armazenamento para o armazenamento de diagnóstico:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

O *xmlcfg* propriedade para a extensão de diagnóstico é definida usando várias variáveis que são concatenadas em conjunto. Os valores destas variáveis são em xml devem ser escritos corretamente ao definir as variáveis de json.

O exemplo a seguir descreve o xml de configuração de diagnóstico que recolhe os contadores de desempenho de nível de sistema padrão, juntamente com alguns registos de eventos do windows e registos de infraestrutura de diagnóstico. Foi caracteres de escape e formatado corretamente para que a configuração diretamente pode ser colada em secção de variáveis do seu modelo. Consulte a [esquema de configuração de diagnósticos](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter um exemplo mais humano legível do xml de configuração.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

O nó de xml de definição de métricas na configuração acima é um elemento de configuração importantes, porque definem como os contadores de desempenho definido anteriormente no xml *PerformanceCounter* nó são agregados e armazenados. 

> [!IMPORTANT]
> Estas métricas orientar os gráficos de monitorização e alertas no portal do Azure.  O **métricas** nó com o *resourceID* e **MetricAggregation** têm de ser incluídos na configuração de diagnósticos para a sua VM se quiser ver a VM a monitorização dos dados no o portal do Azure. 
> 
> 

O exemplo seguinte mostra o xml de definições das métricas: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

O *resourceID* atributo identifica exclusivamente a máquina virtual na sua subscrição. Certifique-se utilizar as funções subscription() e resourceGroup() para que o modelo atualiza automaticamente esses valores com base na subscrição e grupo de recursos que está a implementar.

Se estiver a criar várias máquinas virtuais num loop, precisará Popular as *resourceID* valor com uma função copyindex () para diferenciar corretamente cada VM individual. O *xmlCfg* valor pode ser atualizado para oferecer suporte a isso da seguinte forma:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

O valor MetricAggregation *PT1M* e *PT1H* significar uma agregação ao longo de um minuto e uma agregação mais de uma hora, respetivamente.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics tabelas no armazenamento
A configuração de métricas acima gera tabelas na sua conta de armazenamento de diagnóstico com as seguintes convenções de nomenclatura:

* **WADMetrics**: Prefixo de padrão para todas as tabelas de WADMetrics
* **PT1H** ou **PT1M**: Significa que a tabela contém dados agregados de mais de 1 hora ou de 1 minuto
* **P10D**: Significa que a tabela irá conter dados durante 10 dias a partir de quando a tabela à recolha de dados
* **V2S**: Constante de cadeia
* **yyyymmdd**: A data em que a tabela à recolha de dados

Exemplo: *WADMetricsPT1HP10DV2S20151108* contém dados de métricas agregados mais de uma hora durante 10 dias a partir de 11-Novembro de 2015    

Cada tabela WADMetrics contém as seguintes colunas:

* **PartitionKey**: A chave de partição é construída com base na *resourceID* valor para identificar exclusivamente o recurso da VM. Por exemplo: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Segue o formato `<Descending time tick>:<Performance Counter Name>`. O cálculo de escala de tempo descendente é ticks máx. de tempo, menos a hora de início do período de agregação. Por exemplo, se o período de exemplo iniciado 10-Novembro de 2015 e 00:00Hrs UTC, em seguida, o cálculo seria: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Para os bytes disponíveis de memória, o contador de desempenho a chave de linha será semelhante: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: É o nome do contador de desempenho. Isso coincide com o *counterSpecifier* definidos na configuração do xml.
* **Máximo**: O valor máximo do contador de desempenho ao longo do período de agregação.
* **Mínimo**: O valor mínimo do contador de desempenho ao longo do período de agregação.
* **Total**: A soma de todos os valores do contador de desempenho comunicados ao longo do período de agregação.
* **Contagem de**: O número total de valores comunicados para o contador de desempenho.
* **Média**: O valor de média (contagem/total) do contador de desempenho ao longo do período de agregação.

## <a name="next-steps"></a>Próximos Passos
* Para um modelo de exemplo completo de uma máquina virtual do Windows com a extensão de diagnóstico, consulte [201-vm-monitorização--extensão de diagnóstico](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Implementar o modelo do Azure Resource Manager através de [do Azure PowerShell](../windows/ps-template.md) ou [linha de comandos do Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Saiba mais sobre [criar modelos do Azure Resource Manager](../../resource-group-authoring-templates.md)
