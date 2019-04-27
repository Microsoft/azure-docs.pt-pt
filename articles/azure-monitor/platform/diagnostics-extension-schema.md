---
title: O Azure Diagnostics extensão configuração versão histórico de esquema
description: Relevantes para a recolha de contadores de desempenho em máquinas virtuais do Azure, conjuntos de dimensionamento de VM, Service Fabric e serviços em nuvem.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 1230a9bcea01ef394a6299c50b8d5537850cfee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60527303"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Versões do esquema de configuração de extensão do Azure Diagnostics e o histórico
Esta página de índices versões de esquema de extensão de diagnóstico do Azure fornecidos como parte do SDK do Microsoft Azure.  

> [!NOTE]
> A extensão de diagnóstico do Azure é o componente utilizado para recolher contadores de desempenho e outras estatísticas de:
> - Máquinas Virtuais do Azure
> - Conjuntos de Dimensionamento de Máquinas Virtuais
> - Service Fabric
> - Serviços Cloud
> - Grupos de Segurança de Rede
>
> Esta página só é relevante se estiver a utilizar um destes serviços.

A extensão de diagnóstico do Azure é utilizada com outros produtos de diagnóstico da Microsoft, como o Azure Monitor, que inclui o Application Insights e o Log Analytics. Para obter mais informações, consulte [descrição de geral de ferramentas de monitorização Microsoft](../../azure-monitor/overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Versões SDK e o diagnóstico do Azure, gráfico de envio  

|Versão do SDK do Azure | Versão de extensão de diagnóstico | Modelo|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |Plug-in|  
|2.0 - 2.4         |1.0                            |Plug-in|  
|2.5               |1.2                            |Extensão|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Versão do diagnóstico do Azure 1.0 foi fornecido inicialmente num modelo de plug-in – que significa que, quando instalou o SDK do Azure, tem a versão do diagnóstico do Azure foi fornecido com o mesmo.  

 A partir do SDK 2.5 (diagnóstico versão 1.2), o diagnóstico do Azure passou a um modelo de extensão. As ferramentas para utilizar novas funcionalidades só estavam disponíveis em mais recente do Azure SDKs, mas seria escolher a versão mais recente do envio de qualquer serviço com o diagnóstico do Azure diretamente a partir do Azure. Por exemplo, alguém ainda usa o SDK 2.5 deve ser a carregar a versão mais recente, mostrada na tabela anterior, independentemente se estão a utilizar as funcionalidades mais recentes.  

## <a name="schemas-index"></a>Índice de esquemas  
Versões diferentes do diagnóstico do Azure utilizam esquemas de configuração diferentes.

[Esquema de configuração de diagnóstico 1.0](diagnostics-extension-schema-1dot0.md)  

[Esquema de configuração de diagnóstico 1.2](diagnostics-extension-schema-1dot2.md)  

[Diagnóstico 1.3 e posterior de configuração de esquema](diagnostics-extension-schema-1dot3.md)  

## <a name="version-history"></a>Histórico de versões

### <a name="diagnostics-extension-111"></a>Extensão de diagnóstico 1.11
Foi adicionado suporte para o sink do Azure Monitor. Este sink só é aplicável a contadores de desempenho. Ativa o envio de contadores de desempenho recolhidos na sua VM, VMSS ou serviço em nuvem para o Azure Monitor, como métricas personalizadas. Suporta o sink do Azure Monitor:
* Obter todos os contadores de desempenho enviados para o Azure Monitor através do [métricas do Azure Monitor APIs.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Alertas em todos os contadores de desempenho enviados para o Azure Monitor através do novo [experiência de alertas unificada](../../azure-monitor/platform/alerts-overview.md) no Azure Monitor
* Ao tratar o operador de caráter universal no contadores de desempenho como a dimensão de "Instância" em sua métrica. Por exemplo, se recolheu o "disco lógico (\*) / DiskWrites/seg" contador deve ser capaz de filtro e dividir a dimensão de "Instância" plotagem ou alertar relativamente a escritas de disco/seg para cada disco lógico (c:, d:, etc.)

Definir o Monitor do Azure como um sink novo na sua configuração de extensão de diagnóstico
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Configurar o sink do Azure Monitor para VMs clássicas e do serviço de CLoud clássico requer mais parâmetros definidos na configuração privada da extensão de diagnóstico.
>
> Para obter mais detalhes, consulte o [documentação de esquema de extensão de diagnóstico detalhada.](diagnostics-extension-schema-1dot3.md)

Em seguida, pode configurar os contadores de desempenho para serem encaminhados para o Sink do Azure Monitor.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Extensão de diagnóstico 1.9
Foi adicionado suporte de Docker.


### <a name="diagnostics-extension-181"></a>Extensão de diagnóstico 1.8.1
Pode especificar um token SAS em vez de uma chave de conta de armazenamento a configuração privada. Se não for fornecido um token SAS, a chave de conta de armazenamento é ignorada.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Extensão de diagnóstico 1.8
Tipo de armazenamento adicionado PublicConfig. Pode ser StorageType *tabela*, *Blob*, *TableAndBlob*. *Tabela* é a predefinição.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Extensão de diagnóstico 1.7
Foi adicionada a capacidade para encaminhar para EventHub.

### <a name="diagnostics-extension-15"></a>Extensão de diagnóstico 1.5
Adicionado o elemento de coletores e a capacidade de enviar dados de diagnóstico [Application Insights](../../azure-monitor/app/cloudservices.md) tornando mais fácil diagnosticar problemas em seu aplicativo como o nível de sistema e a infraestrutura.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Extensão do Azure SDK 2.6 e de diagnóstico 1.3
Para projetos de serviço em nuvem no Visual Studio, as seguintes alterações foram feitas. (Essas alterações também se aplicam a versões posteriores do SDK do Azure.)

* O emulador local agora suporta diagnósticos. Esta alteração significa que pode recolher dados de diagnóstico e certifique-se de que a aplicação está a criar os rastreios certos enquanto estiver a desenvolver e testar no Visual Studio. A cadeia de ligação `UseDevelopmentStorage=true` permite a recolha de dados de diagnóstico enquanto está a executar o seu projeto de serviço em nuvem no Visual Studio, utilizando o emulador de armazenamento do Azure. Todos os dados de diagnóstico são recolhidos na conta de armazenamento (armazenamento de desenvolvimento).
* A cadeia de ligação de conta de armazenamento de diagnósticos (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é armazenada mais uma vez no ficheiro de configuração (. cscfg) do serviço. No Azure SDK 2.5 a conta de armazenamento de diagnóstico foi especificada no ficheiro diagnostics.wadcfgx.

Existem algumas diferenças notáveis entre como a cadeia de ligação trabalhou no Azure SDK 2.4 e versões anteriores e como ele funciona no Azure SDK 2.6 e versões posteriores.

* No Azure SDK 2.4 e anteriores, a cadeia de ligação foi utilizada no tempo de execução pelo plug-in de diagnóstico para obter as informações de conta de armazenamento para transferir os registos de diagnóstico.
* No Azure SDK 2.6 ou posterior, o Visual Studio utiliza a cadeia de ligação de diagnóstico para configurar a extensão de diagnóstico com as informações de conta de armazenamento adequado durante a publicação. A cadeia de ligação permite que defina as contas de armazenamento diferentes para as configurações de serviço diferentes que o Visual Studio irá utilizar quando publicar. No entanto, uma vez que o plug-in de diagnóstico já não está disponível (após o Azure SDK 2.5), o ficheiro. cscfg por si só, não é possível ativar a extensão de diagnóstico. Tem de ativar a extensão em separado através de ferramentas, como o Visual Studio ou o PowerShell.
* Para simplificar o processo de configuração a extensão de diagnóstico com o PowerShell, a saída de pacote do Visual Studio também contém o XML de configuração pública para a extensão de diagnóstico para cada função. Visual Studio utiliza a cadeia de ligação de diagnóstico para preencher as informações de conta de armazenamento presentes na configuração do pública. Os ficheiros de configuração públicos são criados na pasta de extensões e seguem o padrão `PaaSDiagnostics.<RoleName>.PubConfig.xml`. Todas as implementações do PowerShell com base em podem utilizar este padrão para mapear cada configuração a uma função.
* A cadeia de ligação no ficheiro. cscfg também é utilizada pelo portal do Azure para acessar os dados de diagnóstico para que ele pode ser exibido no **monitorização** separador. A cadeia de ligação é necessário configurar o serviço para mostrar dados de monitorização verbosos no portal.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrando projetos ao Azure SDK 2.6 ou posterior
Ao migrar a partir do Azure SDK 2.5 para o Azure SDK 2.6 ou posterior, se tiver uma conta de armazenamento de diagnóstico especificada no ficheiro .wadcfgx, em seguida, ele permanecerá lá. Para tirar partido da flexibilidade de usar contas de armazenamento diferentes para configurações de armazenamento diferente, terá de adicionar manualmente a cadeia de ligação ao seu projeto. Se estiver a migrar um projeto do Azure SDK 2.4 ou anteriormente para o Azure SDK 2.6, as cadeias de ligação de diagnóstico são preservadas. No entanto, tenha em atenção as alterações no como cadeias de ligação são processadas no Azure SDK 2.6 conforme especificado na secção anterior.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico
* Se uma cadeia de ligação de diagnóstico é especificada no ficheiro. cscfg, o Visual Studio utiliza-o para configurar a extensão de diagnóstico durante a publicação e ao gerar os arquivos xml de configuração pública durante empacotamento.
* Se nenhuma cadeia de ligação de diagnóstico é especificada no ficheiro. cscfg, em seguida, Visual Studio retrocede para com a conta de armazenamento especificada no ficheiro .wadcfgx para configurar a extensão de diagnóstico quando a publicação e gerar os arquivos xml de configuração pública Quando o empacotamento.
* A cadeia de ligação de diagnóstico no ficheiro. cscfg tem precedência sobre a conta de armazenamento no ficheiro .wadcfgx. Se uma cadeia de ligação de diagnóstico é especificada no ficheiro. cscfg, o Visual Studio usa e ignora a conta de armazenamento no .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz a caixa de verificação "Atualizar cadeias de ligação de armazenamento de desenvolvimento..."?
A caixa de verificação **atualizar cadeias de ligação de armazenamento de desenvolvimento para diagnóstico e de colocação em cache com as credenciais de conta de armazenamento do Microsoft Azure durante a publicação para o Microsoft Azure** dá-lhe uma forma conveniente para atualizar qualquer desenvolvimento cadeias de ligação de conta de armazenamento com a conta de armazenamento do Azure especificada durante a publicação.

Por exemplo, suponha que selecione esta caixa de verificação e especifica a cadeia de ligação de diagnóstico `UseDevelopmentStorage=true`. Quando publica o projeto para o Azure, Visual Studio atualizará automaticamente a cadeia de ligação de diagnóstico com a conta de armazenamento que especificou no Assistente de publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de ligação de diagnóstico, em seguida, essa conta é utilizada em vez disso.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico entre o Azure SDK 2.4 e anterior e o Azure SDK 2.5 e posterior
Se estiver a atualizar o seu projeto do Azure SDK 2.4 para o Azure SDK 2.5 ou posterior, deve ter em mente as seguintes diferenças de funcionalidade de diagnóstico.

* **APIs de configuração são preteridas** – a configuração programática de diagnóstico está disponível no Azure SDK 2.4 ou versões anteriores, mas foi preterida no Azure SDK 2.5 e versões posteriores. Se a configuração de diagnósticos atualmente definida no código, terá de reconfigurar as configurações do zero no projeto migrado pela ordem para obter um diagnóstico para continuar a trabalhar. O ficheiro de configuração de diagnósticos para o Azure SDK 2.4 é diagnostics.wadcfg e diagnostics.wadcfgx para o Azure SDK 2.5 e posterior.
* **Diagnósticos para aplicações de serviço em nuvem só podem ser configurados ao nível da função, não ao nível da instância.**
* **Sempre que implemente a sua aplicação, a configuração de diagnósticos é atualizada** – isso pode causar problemas de paridade, se alterar a configuração de diagnóstico no Explorador de servidores e, em seguida, volte a implementar a sua aplicação.
* **No Azure SDK 2.5 e posterior, despejos de memória estão configurados no arquivo de configuração de diagnósticos, não no código** – se tiver despejos de memória configurados no código, terá que transferir manualmente a configuração a partir do código ao arquivo de configuração, porque o despejos de memória não são transferidos durante a migração para o Azure SDK 2.6.

