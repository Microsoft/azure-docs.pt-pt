---
title: Histórico de versão de configuração de configuração de configuração do Windows Azure Diagnostics (WAD)
description: Relevante para a recolha de contadores perf em Máquinas Virtuais Azure, Conjuntos de Escala VM, Tecido de Serviço e Serviços em Nuvem.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 4dd91363cdebf18e6303238816e8269065a6a317
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77672247"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Versão e histórico de configuração de configuração de configuração de configuração de diagnóstico do Windows Azure (WAD)
Este artigo fornece o histórico de versão da extensão de diagnóstico do Azure para versões de [esquemas Windows (WAD)](diagnostics-extension-overview.md) enviadas como parte do Microsoft Azure SDK.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Gráfico de envio de versões Azure SDK e diagnósticos  

|Versão Azure SDK | Versão de extensão de diagnóstico | Modelo|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |plug-in|  
|2.0 - 2.4         |1.0                            |plug-in|  
|2.5               |1.2                            |extensão|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2,8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 A versão 1.0 do Azure Diagnostics foi enviada pela primeira vez num modelo plug-in -- o que significa que quando instalou o Azure SDK, recebeu a versão dos diagnósticos do Azure enviada seletiva com ele.  

 Começando pelo SDK 2.5 (versão de diagnóstico 1.2), os diagnósticos do Azure foram para um modelo de extensão. As ferramentas para utilizar novas funcionalidades só estavam disponíveis nos SDKs Azure mais recentes, mas qualquer serviço que utilize diagnósticos Azure iria captar a versão de envio mais recente diretamente do Azure. Por exemplo, qualquer pessoa que ainda utilize o SDK 2.5 estaria a carregar a versão mais recente mostrada na tabela anterior, independentemente de estarem a utilizar as funcionalidades mais recentes.  

## <a name="schemas-index"></a>Índice de schemas  
Diferentes versões de diagnósticos Azure usam diferentes esquemas de configuração. Schema 1.0 e 1.2 foram depreciados. Para mais informações sobre a versão 1.3 e posterior, consulte [Diagnóstico1.3 e posterior Configuração Schema](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Histórico de versões

### <a name="diagnostics-extension-111"></a>Extensão de diagnóstico 1.11
Suporte adicional para a pia do Monitor Azure. Esta pia só é aplicável aos contadores de desempenho. Permite o envio de contadores de desempenho recolhidos no seu VM, VMSS ou serviço de nuvem para o Azure Monitor como métricas personalizadas. O lavatório Azure Monitor suporta:
* Recuperando todos os contadores de desempenho enviados para o Monitor Azure através das [APIs métricas do Monitor Azure.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Alerta em todos os balcões de desempenho enviados ao Azure Monitor através da nova [experiência de alertas unificados](../../azure-monitor/platform/alerts-overview.md) no Monitor Azure
* Tratar o operador wildcard em contadores de desempenho como a dimensão "Instância" na sua métrica. Por exemplo, se recolheu\*o contador "LogicalDisk( )/DiskWrites/sec" seria capaz de filtrar e dividir na dimensão "Instância" para traçar ou alertar no Disco Escritas/seg para cada Disco Lógico (C:, D:, etc.)

Defina o Monitor Azure como um novo lavatório na configuração da extensão de diagnóstico
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
> Configurar o lavatório Do Monitor Azure para VMs clássicos e serviço Clássico CLoud requer mais parâmetros a definir na config privada da extensão de Diagnóstico.
>
> Para mais detalhes, consulte a [documentação detalhada](diagnostics-extension-schema-windows.md) de extensão de diagnóstico.

Em seguida, pode configurar os seus contadores de desempenho para serem encaminhados para o Azure Monitor Sink.
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
Acrescentou apoio ao Docker.


### <a name="diagnostics-extension-181"></a>Extensão de diagnóstico 1.8.1
Pode especificar um token SAS em vez de uma chave de conta de armazenamento no config privado. Se for fornecida uma ficha SAS, a chave da conta de armazenamento é ignorada.


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
Tipo de armazenamento adicionado ao PublicConfig. ArmazenamentoTipo pode ser *Tabela,* *Blob,* *TableAndBlob*. *A mesa* é o padrão.


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
Acrescentou a capacidade de encaminhar para eventHub.

### <a name="diagnostics-extension-15"></a>Extensão de diagnóstico 1.5
Acrescentou o elemento sinks e a capacidade de enviar dados de diagnóstico para [Application Insights,](../../azure-monitor/app/cloudservices.md) facilitando o diagnóstico de problemas em toda a sua aplicação, bem como o nível de sistema e infraestrutura.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 e extensão de diagnóstico 1.3
Para os projetos do Cloud Service no Estúdio Visual, foram feitas as seguintes alterações. (Estas alterações aplicam-se igualmente às versões posteriores do Azure SDK.)

* O emulador local agora apoia diagnósticos. Esta alteração significa que pode recolher dados de diagnóstico e garantir que a sua aplicação está a criar os traços certos enquanto está a desenvolver e testar no Estúdio Visual. A cadeia `UseDevelopmentStorage=true` de ligação permite a recolha de dados de diagnóstico enquanto executa o seu projeto de serviço na nuvem no Estúdio Visual utilizando o emulador de armazenamento Azure. Todos os dados de diagnóstico são recolhidos na conta de armazenamento (Development Storage).
* A cadeia de ligação à conta de armazenamento de diagnósticos (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é novamente armazenada no ficheiro de configuração do serviço (.cscfg). No Azure SDK 2.5 a conta de armazenamento de diagnósticofoi especificada no ficheiro diagnostics.wadcfgx.

Existem algumas diferenças notáveis entre como a cadeia de ligação funcionou em Azure SDK 2.4 e mais cedo e como funciona em Azure SDK 2.6 e mais tarde.

* Em Azure SDK 2.4 e anteriormente, a cadeia de ligação foi usada no tempo de execução pelo plugin de diagnóstico para obter a informação da conta de armazenamento para a transferência de registos de diagnóstico.
* No Azure SDK 2.6 e mais tarde, o Visual Studio utiliza a cadeia de ligação de diagnóstico para configurar a extensão de diagnóstico com as informações apropriadas da conta de armazenamento durante a publicação. A cadeia de ligação permite definir diferentes contas de armazenamento para diferentes configurações de serviço que o Visual Studio utilizará na publicação. No entanto, uma vez que o plugin de diagnóstico já não está disponível (depois do Azure SDK 2.5), o ficheiro .cscfg por si só não consegue ativar a Extensão de Diagnóstico. Tem de ativar a extensão separadamente através de ferramentas como o Visual Studio ou o PowerShell.
* Para simplificar o processo de configuração da extensão de diagnóstico com a PowerShell, a saída do pacote do Estúdio Visual também contém a configuração pública XML para a extensão de diagnóstico para cada função. O Visual Studio utiliza a cadeia de ligação de diagnósticos para preencher as informações da conta de armazenamento presentes na configuração pública. Os ficheiros de config público são criados `PaaSDiagnostics.<RoleName>.PubConfig.xml`na pasta Extensões e seguem o padrão . Quaisquer implementações baseadas no PowerShell podem usar este padrão para mapear cada configuração para um Papel.
* A cadeia de ligação no ficheiro .cscfg também é utilizada pelo portal Azure para aceder aos dados de diagnóstico para que possa aparecer no separador **Monitor.** A cadeia de ligação é necessária para configurar o serviço para mostrar dados de monitorização verbosa no portal.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Projetos migratórios para Azure SDK 2.6 e mais tarde
Ao migrar de Azure SDK 2.5 para Azure SDK 2.6 ou posterior, se tiver uma conta de armazenamento de diagnóstico especificada no ficheiro .wadcfgx, então permanecerá lá. Para tirar partido da flexibilidade de utilizar diferentes contas de armazenamento para diferentes configurações de armazenamento, terá de adicionar manualmente a cadeia de ligação ao seu projeto. Se estiver a migrar um projeto de Azure SDK 2.4 ou mais cedo para o Azure SDK 2.6, então as cordas de ligação de diagnóstico são preservadas. No entanto, note as alterações na forma como as cordas de ligação são tratadas em Azure SDK 2.6, conforme especificado na secção anterior.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Estúdio Visual determina a conta de armazenamento de diagnósticos
* Se for especificada uma cadeia de ligação de diagnóstico no ficheiro .cscfg, o Visual Studio utiliza-o para configurar a extensão de diagnóstico ao publicar e ao gerar ficheiros xml de configuração pública durante a embalagem.
* Se não for especificada a cadeia de ligação de diagnóstico no ficheiro .cscfg, o Visual Studio volta a utilizar a conta de armazenamento especificada no ficheiro .wadcfgx para configurar a extensão de diagnóstico ao publicar e gerar os ficheiros xml de configuração pública durante a embalagem.
* A cadeia de ligação de diagnósticono ficheiro .cscfg tem precedência sobre a conta de armazenamento no ficheiro .wadcfgx. Se uma cadeia de ligação de diagnóstico for especificada no ficheiro .cscfg, então o Visual Studio usa-o e ignora a conta de armazenamento em .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz as cordas de ligação de armazenamento de desenvolvimento "Atualizar..." caixa de verificação fazer?
A caixa de verificação para as cadeias de ligação de armazenamento de **desenvolvimento de Atualização para Diagnósticos e Caching com credenciais** de conta de armazenamento Microsoft Azure ao publicar no Microsoft Azure dá-lhe uma maneira conveniente de atualizar quaisquer cadeias de conexão de conta de armazenamento de desenvolvimento com a conta de armazenamento Azure especificada durante a publicação.

Por exemplo, suponha que selecione esta `UseDevelopmentStorage=true`caixa de verificação e especifica a cadeia de ligação de diagnósticos . Quando publicar o projeto no Azure, o Visual Studio atualizará automaticamente a cadeia de ligação de diagnóstico seleções com a conta de armazenamento especificada no assistente da Publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de ligação de diagnósticos, então essa conta é usada em vez disso.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico entre Azure SDK 2.4 e anterior e Azure SDK 2.5 e mais tarde
Se estiver a atualizar o seu projeto de Azure SDK 2.4 para Azure SDK 2.5 ou mais tarde, deve ter em conta as seguintes diferenças de funcionalidade de diagnóstico.

* **Configuração APIs são depreciadas** – A configuração programática de diagnósticos está disponível em versões Azure SDK 2.4 ou anteriores, mas é depreciada em Azure SDK 2.5 e mais tarde. Se a configuração de diagnóstico estiver atualmente definida em código, terá de reconfigurar essas definições do zero no projeto migrado para que os diagnósticos continuem a funcionar. O ficheiro de configuração de diagnóstico para Azure SDK 2.4 é diagnóstico.wadcfg, e diagnósticos.wadcfgx para Azure SDK 2.5 e mais tarde.
* **Os diagnósticos para aplicações de serviço na nuvem só podem ser configurados ao nível da função, e não ao nível da ocorrência.**
* **Sempre que implementa a sua aplicação, a configuração de diagnóstico é atualizada** – Isto pode causar problemas de paridade se alterar a configuração de diagnóstico do Server Explorer e depois recolocar a sua aplicação.
* **No Azure SDK 2.5 e posteriormente,** os depósitos de colisão são configurados no ficheiro de configuração de diagnóstico, não em código – Se tiver despejos de falha configurados em código, terá de transferir manualmente a configuração do código para o ficheiro de configuração, porque os depósitos de colisão não são transferidos durante a migração para o Azure SDK 2.6.

