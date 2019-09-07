---
title: Histórico de versão do esquema de configuração de Diagnóstico do Azure extensão
description: Relevante para coletar contadores de desempenho em máquinas virtuais do Azure, conjuntos de dimensionamento de VM, Service Fabric e serviços de nuvem.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/04/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: e8ea8ea749243821e5382fc285e3c38f05d4c6b5
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735095"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Histórico e versões do esquema de configuração da extensão de Diagnóstico do Azure
Esta página indexa Diagnóstico do Azure versões de esquema de extensão fornecidas como parte do SDK do Microsoft Azure.  

> [!NOTE]
> A extensão de Diagnóstico do Azure é o componente usado para coletar contadores de desempenho e outras estatísticas de:
> - Máquinas Virtuais do Azure
> - Conjuntos de Dimensionamento de Máquinas Virtuais
> - Service Fabric
> - Serviços Cloud
> - Grupos de Segurança de Rede
>
> Essa página só será relevante se você estiver usando um desses serviços.

A extensão de Diagnóstico do Azure é usada com outros produtos de diagnóstico da Microsoft, como Azure Monitor, que inclui Application Insights e Log Analytics. Para obter mais informações, consulte [visão geral das ferramentas de monitoramento da Microsoft](../../azure-monitor/overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Gráfico de remessa de versões do SDK e diagnóstico do Azure  

|Versão do SDK do Azure | Versão da extensão de diagnóstico | Modelo|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |plug-in|  
|2.0 - 2.4         |1.0                            |plug-in|  
|2.5               |1.2                            |extensão|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1,11                           |"|


 Diagnóstico do Azure versão 1,0 fornecida primeiro em um modelo de plug-in – ou seja, quando você instalou o SDK do Azure, você obteve a versão do diagnóstico do Azure fornecida com ele.  

 A partir do SDK 2,5 (versão de diagnóstico 1,2), o diagnóstico do Azure passou para um modelo de extensão. As ferramentas para utilizar novos recursos só estavam disponíveis em SDKs mais recentes do Azure, mas qualquer serviço usando o diagnóstico do Azure pegaria a versão de envio mais recente diretamente do Azure. Por exemplo, qualquer pessoa que ainda estiver usando o SDK 2,5 estaria carregando a versão mais recente mostrada na tabela anterior, independentemente de estar usando os recursos mais recentes.  

## <a name="schemas-index"></a>Índice de esquemas  
Versões diferentes do diagnóstico do Azure usam esquemas de configuração diferentes. O esquema 1,0 e 1,2 foram preteridos. Para obter mais informações sobre a versão 1,3 e posterior, consulte [diagnóstico 1,3 e esquema de configuração posterior](diagnostics-extension-schema-1dot3.md)  

## <a name="version-history"></a>Histórico de versões

### <a name="diagnostics-extension-111"></a>Extensão de diagnóstico 1,11
Adicionado suporte para o coletor de Azure Monitor. Esse coletor só é aplicável a contadores de desempenho. Permite o envio de contadores de desempenho coletados em sua VM, VMSS ou serviço de nuvem para Azure Monitor como métricas personalizadas. O coletor de Azure Monitor dá suporte a:
* Recuperar todos os contadores de desempenho enviados para Azure Monitor por meio das [APIs de métricas de Azure monitor.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Alertas em todos os contadores de desempenho enviados para Azure Monitor por meio da nova [experiência de alertas unificados](../../azure-monitor/platform/alerts-overview.md) no Azure monitor
* Tratando o operador curinga em contadores de desempenho como a dimensão "instância" em sua métrica. Por exemplo, se você coletou o contador\*"LogicalDisk ()/DiskWrites/SEC", poderá filtrar e dividir na dimensão "instância" para plotar ou alertar as gravações de disco/s para cada disco lógico (C:, D:, etc.)

Definir Azure Monitor como um novo coletor em sua configuração de extensão de diagnóstico
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
> Configurar o coletor de Azure Monitor para VMs clássicas e o serviço de nuvem clássico requer que mais parâmetros sejam definidos na configuração privada da extensão de diagnóstico.
>
> Para obter mais detalhes, consulte a [documentação detalhada do esquema de extensão de diagnóstico.](diagnostics-extension-schema-1dot3.md)

Em seguida, você pode configurar seus contadores de desempenho para serem roteados para o coletor de Azure Monitor.
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

### <a name="diagnostics-extension-19"></a>Extensão de diagnóstico 1,9
Suporte adicionado ao Docker.


### <a name="diagnostics-extension-181"></a>1\.8.1 de extensão de diagnóstico
Pode especificar um token SAS em vez de uma chave de conta de armazenamento na configuração particular. Se um token SAS for fornecido, a chave da conta de armazenamento será ignorada.


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


### <a name="diagnostics-extension-18"></a>Extensão de diagnóstico 1,8
Tipo de armazenamento adicionado a PublicConfig. Storagetype pode ser *Table*, *blob*, *TableAndBlob*. A *tabela* é o padrão.


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


### <a name="diagnostics-extension-17"></a>Extensão de diagnóstico 1,7
Adicionada a capacidade de rotear para o EventHub.

### <a name="diagnostics-extension-15"></a>Extensão de diagnóstico 1,5
Adicionou o elemento Sinks e a capacidade de enviar dados de diagnóstico para [Application insights](../../azure-monitor/app/cloudservices.md) facilitando o diagnóstico de problemas em seu aplicativo, bem como no nível do sistema e da infraestrutura.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>SDK do Azure 2,6 e extensão de diagnóstico 1,3
Para projetos de serviço de nuvem no Visual Studio, as seguintes alterações foram feitas. (Essas alterações também se aplicam às versões posteriores do SDK do Azure.)

* O emulador local agora dá suporte a diagnósticos. Essa alteração significa que você pode coletar dados de diagnóstico e garantir que seu aplicativo esteja criando os rastreamentos corretos enquanto estiver desenvolvendo e testando no Visual Studio. A cadeia `UseDevelopmentStorage=true` de conexão habilita a coleta de dados de diagnóstico enquanto você estiver executando seu projeto de serviço de nuvem no Visual Studio usando o emulador de armazenamento do Azure. Todos os dados de diagnóstico são coletados na conta de armazenamento (armazenamento de desenvolvimento).
* A cadeia de conexão da conta de armazenamento de diagnóstico (Microsoft. WindowsAzure. plugins. Diagnostics. ConnectionString) é armazenada novamente no arquivo de configuração de serviço (. cscfg). No SDK do Azure 2,5, a conta de armazenamento de diagnóstico foi especificada no arquivo Diagnostics. wadcfgx.

Há algumas diferenças notáveis entre como a cadeia de conexão funcionou no SDK do Azure 2,4 e anteriores e como funciona no SDK do Azure 2,6 e posterior.

* No SDK do Azure 2,4 e anteriores, a cadeia de conexão foi usada em tempo de execução pelo plug-in de diagnóstico para obter as informações da conta de armazenamento para transferir logs de diagnóstico.
* No SDK do Azure 2,6 e posteriores, o Visual Studio usa a cadeia de conexão de diagnóstico para configurar a extensão de diagnóstico com as informações de conta de armazenamento apropriadas durante a publicação. A cadeia de conexão permite que você defina diferentes contas de armazenamento para diferentes configurações de serviço que o Visual Studio usará ao publicar. No entanto, como o plug-in de diagnóstico não está mais disponível (após o SDK do Azure 2,5), o arquivo. cscfg não pode habilitar a extensão de diagnóstico. Você precisa habilitar a extensão separadamente por meio de ferramentas como o Visual Studio ou o PowerShell.
* Para simplificar o processo de configuração da extensão de diagnóstico com o PowerShell, a saída do pacote do Visual Studio também contém o XML de configuração pública para a extensão de diagnóstico para cada função. O Visual Studio usa a cadeia de conexão de diagnóstico para preencher as informações da conta de armazenamento presentes na configuração pública. Os arquivos de configuração públicos são criados na pasta extensões e seguem o padrão `PaaSDiagnostics.<RoleName>.PubConfig.xml`. As implantações baseadas no PowerShell podem usar esse padrão para mapear cada configuração para uma função.
* A cadeia de conexão no arquivo. cscfg também é usada pelo portal do Azure para acessar os dados de diagnóstico para que ele possa aparecer na guia **monitoramento** . A cadeia de conexão é necessária para configurar o serviço para mostrar dados de monitoramento detalhados no Portal.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrando projetos para o SDK do Azure 2,6 e posterior
Ao migrar do SDK do Azure 2,5 para o SDK do Azure 2,6 ou posterior, se você tiver uma conta de armazenamento de diagnóstico especificada no arquivo. wadcfgx, ela permanecerá lá. Para aproveitar a flexibilidade de usar diferentes contas de armazenamento para diferentes configurações de armazenamento, você precisará adicionar manualmente a cadeia de conexão ao seu projeto. Se você estiver migrando um projeto do SDK 2,4 do Azure ou anterior para o SDK do Azure 2,6, as cadeias de conexão de diagnóstico serão preservadas. No entanto, observe as alterações em como as cadeias de conexão são tratadas no SDK do Azure 2,6, conforme especificado na seção anterior.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico
* Se uma cadeia de conexão de diagnóstico for especificada no arquivo. cscfg, o Visual Studio a usará para configurar a extensão de diagnóstico durante a publicação e ao gerar os arquivos XML de configuração pública durante o empacotamento.
* Se nenhuma cadeia de conexão de diagnóstico for especificada no arquivo. cscfg, o Visual Studio retornará ao uso da conta de armazenamento especificada no arquivo. wadcfgx para configurar a extensão de diagnóstico ao publicar e gerar os arquivos XML de configuração pública ao empacotar.
* A cadeia de conexão de diagnóstico no arquivo. cscfg tem precedência sobre a conta de armazenamento no arquivo. wadcfgx. Se uma cadeia de conexão de diagnóstico for especificada no arquivo. cscfg, o Visual Studio o usará e ignorará a conta de armazenamento em. wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz o "atualizar cadeias de conexão de armazenamento de desenvolvimento..." a caixa de seleção faz?
A caixa de seleção para **Atualizar cadeias de conexão de armazenamento de desenvolvimento para diagnóstico e cache com Microsoft Azure credenciais de conta de armazenamento ao publicar no Microsoft Azure** oferece uma maneira conveniente de atualizar qualquer conta de armazenamento de desenvolvimento cadeias de conexão com a conta de armazenamento do Azure especificada durante a publicação.

Por exemplo, suponha que você marque essa caixa de seleção e a cadeia `UseDevelopmentStorage=true`de conexão de diagnóstico especifique. Quando você publicar o projeto no Azure, o Visual Studio atualizará automaticamente a cadeia de conexão de diagnóstico com a conta de armazenamento especificada no assistente de publicação. No entanto, se uma conta de armazenamento real tiver sido especificada como a cadeia de conexão de diagnóstico, essa conta será usada em seu lugar.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico entre o SDK 2,4 do Azure e versões anteriores e o SDK do Azure 2,5 e posteriores
Se você estiver atualizando seu projeto do SDK do Azure 2,4 para o SDK do Azure 2,5 ou posterior, lembre-se das diferenças de funcionalidade de diagnóstico a seguir.

* As **APIs de configuração foram preteridas** – a configuração programática do diagnóstico está disponível no SDK do Azure 2,4 ou versões anteriores, mas foi preterida no SDK do Azure 2,5 e posterior. Se sua configuração de diagnóstico estiver atualmente definida no código, você precisará reconfigurar essas configurações do zero no projeto migrado para que o diagnóstico continue funcionando. O arquivo de configuração de diagnóstico para o SDK 2,4 do Azure é Diagnostics. wadcfg e Diagnostics. wadcfgx para o SDK do Azure 2,5 e posterior.
* **O diagnóstico para aplicativos de serviço de nuvem só pode ser configurado no nível de função, não no nível de instância.**
* **Sempre que você implantar seu aplicativo, a configuração de diagnóstico será atualizada** – isso poderá causar problemas de paridade se você alterar a configuração de diagnóstico de Gerenciador de servidores e, em seguida, reimplantar o aplicativo.
* **No SDK do Azure 2,5 e posteriores, os despejos de memória são configurados no arquivo de configuração de diagnóstico, não no código** – se você tiver despejos de memória configurados no código, será necessário transferir manualmente a configuração do código para o arquivo de configuração, pois a falha os despejos não são transferidos durante a migração para o SDK do Azure 2,6.

