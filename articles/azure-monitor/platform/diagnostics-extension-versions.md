---
title: Histórico de versão de esquema de configuração do Windows Azure Diagnostics (WAD)
description: Relevante para a recolha de contadores perf em Máquinas Virtuais Azure, Conjuntos de Escala VM, Tecido de Serviço e Serviços cloud.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 9fa0fbea9a6c0e95eb9bec2795ae12e5fae36bfb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568665"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Versões e histórico de esquemas de configuração do Windows Azure Diagnostics (WAD)
Este artigo fornece o histórico da versão da [extensão Azure Diagnostics para versões](diagnostics-extension-overview.md) de esquema do Windows (WAD) enviadas como parte do Microsoft Azure SDK.  


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


 A versão 1.0 do Azure Diagnostics foi enviada pela primeira vez num modelo plug-in -- o que significa que quando instalou o Azure SDK, tem a versão dos diagnósticos Azure enviada com ele.  

 Começando com SDK 2.5 (versão de diagnóstico 1.2), os diagnósticos do Azure foram para um modelo de extensão. As ferramentas para utilizar novas funcionalidades só estavam disponíveis em Novos Azure SDKs, mas qualquer serviço que usasse diagnósticos Azure iria recolher a versão de envio mais recente diretamente do Azure. Por exemplo, quem ainda estiver a utilizar o SDK 2.5 estaria a carregar a versão mais recente mostrada na tabela anterior, independentemente de utilizarem as funcionalidades mais recentes.  

## <a name="schemas-index"></a>Índice de esquemas  
Diferentes versões de diagnósticos Azure utilizam diferentes esquemas de configuração. Schema 1.0 e 1.2 foram depreciadas. Para obter mais informações sobre a versão 1.3 e posterior, consulte [o Diagnóstico 1.3 e posterior Esquema de Configuração](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Histórico de versões

### <a name="diagnostics-extension-111"></a>Extensão de diagnóstico 1.11
Apoio adicional à pia do Monitor Azure. Esta pia só é aplicável aos contadores de desempenho. Permite o envio de contadores de desempenho recolhidos no seu VM, VMSS ou serviço de nuvem para O Azure Monitor como métricas personalizadas. O azure monitor suporta:
* Recuperação de todos os contadores de desempenho enviados para o Azure Monitor através das [métricas Azure Monitor APIs.](/rest/api/monitor/metrics/list)
* Alertando em todos os contadores de desempenho enviados ao Azure Monitor através da nova [experiência de alertas unificados](./alerts-overview.md) no Azure Monitor
* Tratar o operador wildcard nos contadores de desempenho como a dimensão "Exemplo" na sua métrica. Por exemplo, se recolhesse o \* contador "LogicalDisk,)/DiskWrites/sec" seria capaz de filtrar e dividir na dimensão "Exemplo" para traçar ou alertar no Disco Writes/seg para cada Disco Lógico (C:, D:, etc.)

Defina o Azure Monitor como uma nova pia na configuração da extensão de diagnóstico
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
> Configurar a pia do Monitor Azure para VMs clássicos e serviço CLoud clássico requer mais parâmetros para ser definido na configuração privada da extensão de Diagnóstico.
>
> Para mais detalhes, consulte a documentação detalhada do [esquema de extensão de diagnósticos.](diagnostics-extension-schema-windows.md)

Em seguida, pode configurar os seus contadores de desempenho para serem encaminhados para a Pia do Monitor Azure.
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
Pode especificar um token SAS em vez de uma chave de conta de armazenamento na configuração privada. Se for fornecida uma ficha SAS, a chave da conta de armazenamento é ignorada.


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
Tipo de armazenamento adicionado ao PublicConfig. O StorageType pode ser *tabela,* *Blob,* *TableAndBlob*. *Tabela* é o padrão.


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
Adicionou a capacidade de encaminhar para o EventHub.

### <a name="diagnostics-extension-15"></a>Extensão de diagnóstico 1.5
Adicionou o elemento de sumidouros e a capacidade de enviar dados de diagnóstico para [o Application Insights,](../app/cloudservices.md) facilitando o diagnóstico de problemas em toda a sua aplicação, bem como o nível de sistema e infraestrutura.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 e extensão de diagnóstico 1.3
Para os projetos do Cloud Service no Visual Studio, foram feitas as seguintes alterações. (Estas alterações aplicam-se também às versões posteriores do Azure SDK.)

* O emulador local agora suporta diagnósticos. Esta alteração significa que pode recolher dados de diagnóstico e garantir que a sua aplicação está a criar os traços certos enquanto está a desenvolver e a testar no Visual Studio. A cadeia de ligação `UseDevelopmentStorage=true` permite a recolha de dados de diagnóstico enquanto executa o seu projeto de serviço em nuvem no Estúdio Visual, utilizando o Emulador de Armazenamento Azure. Todos os dados de diagnóstico são recolhidos na conta de armazenamento (Armazenamento de Desenvolvimento).
* A cadeia de ligação à conta de armazenamento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é armazenada novamente no ficheiro de configuração de serviço (.cscfg). No Azure SDK 2.5 a conta de armazenamento de diagnósticos foi especificada no ficheiro diagnostics.wadcfgx.

Existem algumas diferenças notáveis entre como a cadeia de ligação funcionou em Azure SDK 2.4 e mais cedo e como funciona em Azure SDK 2.6 e mais tarde.

* No Azure SDK 2.4 e anteriormente, a cadeia de ligação foi utilizada em tempo de execução pelo plugin de diagnóstico para obter a informação da conta de armazenamento para transferir registos de diagnóstico.
* Em Azure SDK 2.6 e mais tarde, o Visual Studio utiliza a cadeia de ligação de diagnósticos para configurar a extensão de diagnóstico com as informações apropriadas da conta de armazenamento durante a publicação. A cadeia de ligação permite definir diferentes contas de armazenamento para diferentes configurações de serviço que o Visual Studio utilizará ao publicar. No entanto, como o plugin de diagnóstico já não está disponível (depois de Azure SDK 2.5), o ficheiro .cscfg por si só não pode ativar a Extensão de Diagnóstico. Tem de ativar a extensão separadamente através de ferramentas como o Visual Studio ou o PowerShell.
* Para simplificar o processo de configuração da extensão de diagnóstico com o PowerShell, a saída do pacote do Visual Studio também contém a configuração pública XML para a extensão de diagnóstico para cada função. O Visual Studio utiliza a cadeia de ligação de diagnósticos para preencher as informações da conta de armazenamento presentes na configuração pública. Os ficheiros config públicos são criados na pasta Extensões e seguem o padrão `PaaSDiagnostics.<RoleName>.PubConfig.xml` . Quaisquer implementações baseadas em PowerShell podem usar este padrão para mapear cada configuração para um Role.
* A cadeia de ligação no ficheiro .cscfg também é utilizada pelo portal Azure para aceder aos dados de diagnóstico para que possa aparecer no **separador Monitor.** O fio de ligação é necessário para configurar o serviço para mostrar dados de monitorização verbose no portal.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Projetos migratórios para Azure SDK 2.6 e mais tarde
Ao migrar do Azure SDK 2.5 para Azure SDK 2.6 ou mais tarde, se tiver uma conta de armazenamento de diagnóstico especificada no ficheiro .wadcfgx, então ficará lá. Para aproveitar a flexibilidade da utilização de diferentes contas de armazenamento para diferentes configurações de armazenamento, terá de adicionar manualmente a cadeia de ligação ao seu projeto. Se estiver a migrar um projeto de Azure SDK 2.4 ou mais cedo para Azure SDK 2.6, então as cordas de ligação de diagnóstico são preservadas. No entanto, note as alterações na forma como as cordas de ligação são tratadas em Azure SDK 2.6, conforme especificado na secção anterior.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnósticos
* Se uma cadeia de ligação de diagnóstico for especificada no ficheiro .cscfg, o Visual Studio utiliza-o para configurar a extensão de diagnósticos durante a publicação e ao gerar os ficheiros xml de configuração pública durante a embalagem.
* Se nenhuma cadeia de ligação de diagnóstico for especificada no ficheiro .cscfg, então o Visual Studio volta a utilizar a conta de armazenamento especificada no ficheiro .wadcfgx para configurar a extensão de diagnósticos ao publicar e gerar os ficheiros xml de configuração pública durante a embalagem.
* A cadeia de ligação de diagnósticos no ficheiro .cscfg tem precedência sobre a conta de armazenamento no ficheiro .wadcfgx. Se uma cadeia de ligação de diagnóstico for especificada no ficheiro .cscfg, então o Visual Studio utiliza isso e ignora a conta de armazenamento em .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz as cordas de conexão de armazenamento de desenvolvimento "Atualização..." caixa de verificação fazer?
A caixa de verificação para **cadeias de ligação de armazenamento de desenvolvimento de atualização para diagnósticos e Caching com credenciais de conta de armazenamento microsoft Azure ao publicar no Microsoft Azure** dá-lhe uma maneira conveniente de atualizar quaisquer cadeias de ligação de conta de armazenamento de desenvolvimento com a conta de armazenamento Azure especificadas durante a publicação.

Por exemplo, suponha que selecione esta caixa de verificação e a cadeia de ligação de diagnósticos especifica `UseDevelopmentStorage=true` . Quando publicar o projeto no Azure, o Visual Studio atualizará automaticamente a cadeia de ligação de diagnósticos com a conta de armazenamento especificada no assistente publicar. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de ligação de diagnóstico, então essa conta é usada.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico entre Azure SDK 2.4 e anterior e Azure SDK 2.5 e mais tarde
Se estiver a atualizar o seu projeto de Azure SDK 2.4 para Azure SDK 2.5 ou mais tarde, deve ter em conta as seguintes diferenças de funcionalidade de diagnóstico.

* **As APIs de configuração são depreciadas** – A configuração programática de diagnósticos está disponível em versões Azure SDK 2.4 ou anteriores, mas é depreciada em Azure SDK 2.5 e posterior. Se a configuração do seu diagnóstico estiver atualmente definida em código, terá de reconfigurar essas definições do zero no projeto migrado para que os diagnósticos continuem a funcionar. O ficheiro de configuração de diagnóstico para Azure SDK 2.4 é diagnóstico.wadcfg, e diagnósticos.wadcfgx para Azure SDK 2.5 e mais tarde.
* **Os diagnósticos para aplicações de serviço na nuvem só podem ser configurados ao nível da função, e não ao nível da instância.**
* **Sempre que implementa a sua aplicação, a configuração de diagnóstico é atualizada** – Isto pode causar problemas de paridade se alterar a configuração de diagnóstico do Server Explorer e, em seguida, recolocar a sua aplicação.
* **No Azure SDK 2.5 e posteriormente, os depósitos de colisão são configurados no ficheiro de configuração de diagnósticos, não em código** – Se tiver falhas configuradas em código, terá de transferir manualmente a configuração do código para o ficheiro de configuração, porque os depósitos de colisão não são transferidos durante a migração para Azure SDK 2.6.

