---
title: Adicione a monitorização & diagnósticos a uma máquina virtual Azure
description: Utilize um modelo de Gestor de Recursos Azure para criar uma nova máquina virtual do Windows com extensão de diagnóstico Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073849"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Utilize monitorização e diagnósticos com modelos de VM windows e gestor de recursos azure
A extensão de diagnóstico azure fornece as capacidades de monitorização e diagnóstico numa máquina virtual Azure baseada no Windows. Pode ativar estas capacidades na máquina virtual, incluindo a extensão como parte do modelo do Gestor de Recursos Azure. Consulte os modelos de [gestor de recursos do Azure autor com extensões VM](../windows/template-description.md#extensions) para obter mais informações sobre a inclusão de qualquer extensão como parte de um modelo de máquina virtual. Este artigo descreve como pode adicionar a extensão de Diagnóstico Azure a um modelo de máquina virtual windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adicione a extensão de Diagnóstico Azure à definição de recursos VM
Para ativar a extensão de diagnóstico numa Máquina Virtual do Windows, é necessário adicionar a extensão como recurso VM no modelo De gestor de recursos.

Para uma simples Máquina Virtual baseada em Recursos, adicione a configuração de extensão à matriz de *recursos* para a Máquina Virtual: 

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

Outra convenção comum é adicionar a configuração de extensão no nó de recursos radiculares do modelo em vez de defini-lo sob o nó de recursos da máquina virtual. Com esta abordagem, tem de especificar explicitamente uma relação hierárquica entre a extensão e a máquina virtual com o *nome* e os valores *do tipo.* Por exemplo: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

A extensão está sempre associada à máquina virtual, pode defini-la diretamente sob o nó de recursos da máquina virtual ou defini-la ao nível da base e usar a convenção de nomeação hierárquica para associá-la à máquina virtual.

Para a escala de máquina virtual, a configuração das extensões é especificada na propriedade *de extensãoPerfil* do *Perfil virtual*.

A propriedade *da editora* com o valor da **Microsoft.Azure.Diagnostics** e a propriedade *tipo* com o valor do **IaaSDiagnostics** identificam exclusivamente a extensão de Diagnóstico Sinuoso.

O valor da propriedade do *nome* pode ser usado para se referir à extensão no grupo de recursos. Defini-lo especificamente para **Microsoft.Insights.VMDiagnosticsSettings** permite que seja facilmente identificado pelo portal Azure garantindo que os gráficos de monitorização aparecem corretamente no portal Azure.

O *typeHandlerVersion* especifica a versão da extensão que gostaria de utilizar. Configurar a versão menor de *atualização automática* Da versão menor **garante** que obtém a versão mais recente da extensão que está disponível. É altamente recomendável que sempre tenha definido *o autoUpgradeMinorVersion* para ser sempre **verdade** para que possa sempre utilizar a mais recente extensão de diagnóstico disponível com todas as novas funcionalidades e correções de bugs. 

O elemento *de configuração* contém propriedades de configurações para a extensão que pode ser definida e lida a partir da extensão (por vezes referida como configuração pública). A propriedade *xmlcfg* contém configuração baseada em xml para os registos de diagnóstico, contadores de desempenho etc que são recolhidos pelo agente de diagnóstico. Consulte a Configuração de [Diagnóstico Schema](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter mais informações sobre o próprio esquema xml. Uma prática comum é armazenar a configuração xml real como uma variável no modelo do Gestor de Recursos Azure e, em seguida, concatenar e base64 enfixá-los para definir o valor para *xmlcfg*. Consulte a secção de variáveis de configuração de [diagnósticopara](#diagnostics-configuration-variables) entender mais sobre como armazenar o xml em variáveis. A propriedade *storageAccount* especifica o nome da conta de armazenamento para a qual os dados de diagnóstico são transferidos. 

As propriedades em *Definições protegidas* (por vezes referidas como configuração privada) podem ser definidas, mas não podem ser lidas de volta após serem definidas. A natureza apenas escrita de *Definições protegidas* torna-a útil para armazenar segredos como a chave da conta de armazenamento onde os dados de diagnóstico são escritos.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Especificação da conta de armazenamento de diagnósticos como parâmetros
A extensão de diagnóstico json snippet acima assume dois *parâmetros existentesStorageAccountName* e *diagnósticos existentesStorageResourceGroup* para especificar a conta de armazenamento de diagnósticos onde os dados de diagnóstico são armazenados. Especificar a conta de armazenamento de diagnósticocomo parâmetro facilita a alteração da conta de armazenamento de diagnósticos em diferentes ambientes, por exemplo, pode querer usar uma conta de armazenamento de diagnóstico diferente para testes e outra para o seu implantação de produção.  

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

É uma boa prática especificar uma conta de armazenamento de diagnóstico num grupo de recursos diferente do grupo de recursos para a máquina virtual. Um grupo de recursos pode ser considerado uma unidade de implantação com o seu próprio tempo de vida, uma máquina virtual pode ser implantada e reimplantada à medida que novas atualizações de configurações são feitas para ele, mas você pode querer continuar a armazenar os dados de diagnóstico na mesma conta de armazenamento em toda a as implantações de máquinas virtuais. Ter a conta de armazenamento num recurso diferente permite que a conta de armazenamento aceite dados de várias implementações de máquinas virtuais, facilitando a resolução de problemas nas várias versões.

> [!NOTE]
> Se criar um modelo de máquina virtual windows do Visual Studio, a conta de armazenamento padrão pode ser definida para usar a mesma conta de armazenamento onde a máquina virtual VHD é carregada. Isto é para simplificar a configuração inicial do VM. Refactor o modelo para usar uma conta de armazenamento diferente que pode ser passado como um parâmetro. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Variáveis de configuração de diagnóstico
A extensão de diagnóstico anterior json snippet define uma variável *contabilista* para simplificar a obtenção da chave da conta de armazenamento para o armazenamento de diagnósticos:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

A propriedade *xmlcfg* para a extensão de diagnóstico é definida usando múltiplas variáveis que são concatenadas em conjunto. Os valores destas variáveis estão em xml, pelo que precisam de ser escapados corretamente ao definir as variáveis json.

O exemplo seguinte descreve a configuração de diagnóstico xml que recolhe contadores de desempenho padrão de nível do sistema juntamente com alguns registos de infraestrutura de eventos do Windows e registos de infraestrutura de diagnóstico. Escapou e formatado corretamente para que a configuração possa ser colada diretamente na secção de variáveis do seu modelo. Consulte o Esquema de [Configuração](https://msdn.microsoft.com/library/azure/dn782207.aspx) de Diagnóstico sintetizando um exemplo mais legível pelo homem da configuração xml.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

O nó de definição de Métricas xml na configuração acima é um elemento de configuração importante, pois define como os contadores de desempenho definidos anteriormente no xml no nó *PerformanceCounter* são agregados e armazenados. 

> [!IMPORTANT]
> Estas métricas impulsionam as tabelas de monitorização e alertas no portal Azure.  O **Metrics** nó métrico com o *id de recursos* e a **agregação métrica** deve ser incluído na configuração de diagnóstico para o seu VM se quiser ver os dados de monitorização vm no portal Azure. 
> 
> 

O exemplo que se segue mostra o xml para definições de métricas: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

O atributo *de recurso ID* identifica exclusivamente a máquina virtual na sua subscrição. Certifique-se de utilizar as funções de subscrição() e recursos Group() para que o modelo atualize automaticamente os valores com base no grupo de subscrição e recursos para o qual está a implementar.

Se estiver a criar várias Máquinas Virtuais num loop, tem de preencher o valor *de ID* de recursos com uma função copyIndex() para diferenciar corretamente cada VM individual. O valor *xmlCfg* pode ser atualizado para suportar isto da seguinte forma:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

O valor de agregação de *PT1M* e *PT1H* significa uma agregação ao longo de um minuto e uma agregação ao longo de uma hora, respectivamente.

## <a name="wadmetrics-tables-in-storage"></a>Mesas WADMetrics no armazenamento
A configuração métrica acima gera tabelas na sua conta de armazenamento de diagnósticocom as seguintes convenções de nomeação:

* **WADMetrics**: Prefixo padrão para todas as tabelas WADMetrics
* **PT1H** ou **PT1M**: Significa que a tabela contém dados agregados ao longo de 1 hora ou 1 minuto
* **P10D**: Significa que a tabela conterá dados durante 10 dias a partir do momento em que a tabela começou a recolher dados
* **V2S**: Constante de corda
* **yyymmdd**: A data em que a tabela começou a recolher dados

Exemplo: *WADMetricsPT1HP10DV2S2011108* contém dados métricos agregados ao longo de uma hora durante 10 dias a partir de 11-Nov-2015    

Cada tabela WADMetrics contém as seguintes colunas:

* **PartitionKey**: A chave de partição é construída com base no valor de ID de *recursos* para identificar exclusivamente o recurso VM. Por exemplo: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **Tecla de linha** `<Descending time tick>:<Performance Counter Name>`: Segue o formato . O cálculo do tique-taque de tempo descendente é o tempo máximo que marca menos o tempo do início do período de agregação. Por exemplo, se o período de amostragem tivesse começado em 10-Nov-2015 e `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`00:00Hrs UTC então o cálculo seria: . Para a memória disponível bytes contador de desempenho, a chave da linha será como:`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **Contranome**: É o nome do contador de desempenho. Isto corresponde ao *contraespecificador* definido no config xml.
* **Máximo**: O valor máximo do contador de desempenho durante o período de agregação.
* **Mínimo**: O valor mínimo do contador de desempenho durante o período de agregação.
* **Total**: A soma de todos os valores do contador de desempenho reportado durante o período de agregação.
* **Contagem**: O número total de valores reportados para o contador de desempenho.
* **Média**: O valor médio (total/contagem) do contador de desempenho durante o período de agregação.

## <a name="next-steps"></a>Passos Seguintes
* Para obter um modelo completo de amostra de uma máquina virtual do Windows com extensão de diagnóstico, consulte [201-vm-monitor-diagnostics-extensão](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Implemente o modelo de Gestor de Recursos Azure utilizando a Linha de Comando [Azure PowerShell](../windows/ps-template.md) ou [Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Saiba mais sobre a autoria de [modelos de Gestor de Recursos Azure](../../resource-group-authoring-templates.md)
