---
title: Configurar o Reliable Services de Service Fabric do Azure | Microsoft Docs
description: Saiba mais sobre a configuração de Reliable Services com estado no Service Fabric do Azure.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 60a4669e20aa8aaf80ae174c88631f3dc572656d
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242897"
---
# <a name="configure-stateful-reliable-services"></a>Configurar Reliable Services com estado
Há dois conjuntos de parâmetros de configuração para os Reliable Services. Um conjunto é global para todos os Reliable Services no cluster, enquanto o outro conjunto é específico para um determinado serviço confiável.

## <a name="global-configuration"></a>Configuração global
A configuração global de serviço confiável é especificada no manifesto do cluster para o cluster na seção KtlLogger. Ele permite a configuração do local e do tamanho do log compartilhado, além dos limites de memória global usados pelo agente. O manifesto do cluster é um único arquivo XML que mantém configurações e configurações que se aplicam a todos os nós e serviços no cluster. O arquivo é normalmente chamado de ClusterManifest. xml. Você pode ver o manifesto do cluster para o cluster usando o comando Get-ServiceFabricClusterManifest do PowerShell.

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Quilobytes |8388608 |Número mínimo de KB para alocar no modo kernel para o pool de memória do buffer de gravação do agente. Esse pool de memória é usado para armazenar em cache informações de estado antes da gravação no disco. |
| WriteBufferMemoryPoolMaximumInKB |Quilobytes |Sem Limite |O tamanho máximo para o qual o pool de memória do buffer de gravação do agente pode crescer. |
| SharedLogId |VOLUME |"" |Especifica um GUID exclusivo a ser usado para identificar o arquivo de log compartilhado padrão usado por todos os Reliable Services em todos os nós no cluster que não especificam o SharedLogId em sua configuração específica de serviço. Se SharedLogId for especificado, SharedLogPath também deverá ser especificado. |
| SharedLogPath |Nome do caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado em que o arquivo de log compartilhado usado por todos os serviços confiáveis em todos os nós no cluster que não especificam o SharedLogPath em sua configuração específica de serviço. No entanto, se SharedLogPath for especificado, SharedLogId também deverá ser especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB de espaço em disco para alocar estaticamente para o log compartilhado. O valor deve ser 2048 ou maior. |

No modelo do Azure ARM ou local JSON, o exemplo a seguir mostra como alterar o log de transações compartilhado que é criado para fazer backup de quaisquer coleções confiáveis para serviços com estado.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Exemplo de seção de manifesto do cluster de desenvolvedor local
Se desejar alterar isso em seu ambiente de desenvolvimento local, você precisará editar o arquivo clustermanifest. XML local.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Observações
O agente de log tem um pool global de memória alocado de memória de kernel não paginável que está disponível para todos os Reliable Services em um nó para armazenar em cache os dados de estado antes de serem gravados no log dedicado associado à réplica de serviço confiável. O tamanho do pool é controlado pelas configurações de WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB especifica o tamanho inicial desse pool de memória e o menor tamanho para o qual o pool de memória pode ser reduzido. WriteBufferMemoryPoolMaximumInKB é o tamanho mais alto para o qual o pool de memória pode crescer. Cada réplica de serviço confiável que é aberta pode aumentar o tamanho do pool de memória por um sistema determinada quantidade de até WriteBufferMemoryPoolMaximumInKB. Se houver mais demanda de memória do pool de memória do que a disponível, as solicitações de memória serão atrasadas até que a memória esteja disponível. Portanto, se o pool de memória do buffer de gravação for muito pequeno para uma configuração específica, o desempenho poderá ser afetado.

As configurações de SharedLogId e SharedLogPath são sempre usadas juntas para definir o GUID e o local do log compartilhado padrão para todos os nós no cluster. O log compartilhado padrão é usado para todos os Reliable Services que não especificam as configurações no Settings. xml para o serviço específico. Para obter um melhor desempenho, os arquivos de log compartilhados devem ser colocados em discos que são usados somente para o arquivo de log compartilhado para reduzir a contenção.

SharedLogSizeInMB especifica a quantidade de espaço em disco a ser prealocada para o log compartilhado padrão em todos os nós.  SharedLogId e SharedLogPath não precisam ser especificados para que o SharedLogSizeInMB seja especificado.

## <a name="service-specific-configuration"></a>Configuração específica do serviço
Você pode modificar configurações padrão de Reliable Services com estado usando o pacote de configuração (config) ou a implementação do serviço (código).

* **Config** -a configuração por meio do pacote de configuração é realizada alterando o arquivo Settings. XML que é gerado na raiz do pacote de Microsoft Visual Studio na pasta de configuração para cada serviço no aplicativo.
* O **código** -configuração por meio do código é feito criando um ReliableStateManager usando um objeto ReliableStateManagerConfiguration com as opções apropriadas definidas.

Por padrão, o tempo de execução do Azure Service Fabric procura nomes de seção predefinidos no arquivo Settings. xml e consome os valores de configuração ao criar os componentes de tempo de execução subjacentes.

> [!NOTE]
> Não **exclua** os nomes de seção das configurações a seguir no arquivo Settings. XML que é gerado na solução do Visual Studio, a menos que você planeje configurar seu serviço por meio de código.
> Renomear o pacote de configuração ou os nomes de seção exigirá uma alteração de código ao configurar o ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador são usadas para proteger o canal de comunicação usado durante a replicação. Isso significa que os serviços não poderão ver o tráfego de replicação uns dos outros, garantindo que os dados que se tornam altamente disponíveis também sejam seguros. Por padrão, uma seção de configuração de segurança vazia impede a segurança da replicação.

> [!IMPORTANT]
> Em nós do Linux, os certificados devem ser formatados por PEM. Para saber mais sobre como localizar e configurar certificados para o Linux, consulte [configurar certificados no Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Nome da seção padrão
ReplicatorSecurityConfig

> [!NOTE]
> Para alterar esse nome de seção, substitua o parâmetro replicatorSecuritySectionName para o Construtor ReliableStateManagerConfiguration ao criar o ReliableStateManager para esse serviço.
> 
> 

### <a name="replicator-configuration"></a>Configuração do replicador
As configurações do replicador configuram o replicador que é responsável por tornar o estado do serviço confiável sem estado altamente confiável ao replicar e persistir o estado localmente.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o replicador.

### <a name="default-section-name"></a>Nome da seção padrão
ReplicatorConfig

> [!NOTE]
> Para alterar esse nome de seção, substitua o parâmetro replicatorSettingsSectionName para o Construtor ReliableStateManagerConfiguration ao criar o ReliableStateManager para esse serviço.
> 
> 

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0, 15 |Período de tempo para o qual o replicador no secundário aguarda depois de receber uma operação antes de enviar de volta uma confirmação para a primária. Quaisquer outras confirmações a serem enviadas para operações processadas nesse intervalo são enviadas como uma resposta. |
| ReplicatorEndpoint |N/A |Nenhum padrão--parâmetro obrigatório |Endereço IP e porta que o replicador primário/secundário usará para se comunicar com outros replicadores no conjunto de réplicas. Isso deve fazer referência a um ponto de extremidade de recurso TCP no manifesto do serviço. Consulte [recursos de manifesto do serviço](service-fabric-service-manifest-resources.md) para ler mais sobre como definir recursos de ponto de extremidade em um manifesto do serviço. |
| MaxPrimaryReplicationQueueSize |Número de operações |8192 |Número máximo de operações na fila primária. Uma operação é liberada depois que o replicador primário recebe uma confirmação de todos os replicadores secundários. Esse valor deve ser maior que 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é liberada depois de tornar seu estado altamente disponível por meio de persistência. Esse valor deve ser maior que 64 e uma potência de 2. |
| CheckpointThresholdInMB |MB |50 |Quantidade de espaço do arquivo de log após o qual o estado é marcado. |
| MaxRecordSizeInKB |KB |1024 |O maior tamanho de registro que o replicador pode gravar no log. Esse valor deve ser um múltiplo de 4 e maior que 16. |
| MinLogSizeInMB |MB |0 (sistema determinado) |Tamanho mínimo do log transacional. O log não terá permissão para truncar um tamanho abaixo dessa configuração. 0 indica que o replicador determinará o tamanho mínimo do log. Aumentar esse valor aumenta a possibilidade de fazer cópias parciais e backups incrementais, já que as chances de registros de log relevantes sendo truncados são reduzidas. |
| TruncationThresholdFactor |Multi-fator |2 |Determina em qual tamanho do log o truncamento será disparado. O limite de truncamento é determinado por MinLogSizeInMB multiplicado por TruncationThresholdFactor. TruncationThresholdFactor deve ser maior que 1. MinLogSizeInMB * TruncationThresholdFactor deve ser menor que MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Multi-fator |4 |Determina em qual tamanho do log, a réplica começará a ser limitada. O limite de limitação (em MB) é determinado por Max ((MinLogSizeInMB * ThrottlingThresholdFactor), (CheckpointThresholdInMB * ThrottlingThresholdFactor)). O limite de limitação (em MB) deve ser maior que o limite de truncamento (em MB). O limite de truncamento (em MB) deve ser menor que MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Tamanho máximo acumulado (em MB) de logs de backup em uma cadeia de logs de backup específica. Uma solicitação de backup incremental falhará se o backup incremental gerar um log de backup que causaria os logs de backup acumulados, pois o backup completo relevante seria maior do que esse tamanho. Nesses casos, o usuário precisa fazer um backup completo. |
| SharedLogId |VOLUME |"" |Especifica um GUID exclusivo a ser usado para identificar o arquivo de log compartilhado usado com esta réplica. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogId for especificado, SharedLogPath também deverá ser especificado. |
| SharedLogPath |Nome do caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado em que o arquivo de log compartilhado para esta réplica será criado. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogPath for especificado, SharedLogId também deverá ser especificado. |
| SlowApiMonitoringDuration |Segundos |300 |Define o intervalo de monitoramento para chamadas de API gerenciadas. Exemplo: função de retorno de chamada de backup fornecida pelo usuário. Depois que o intervalo tiver passado, um relatório de integridade de aviso será enviado ao Gerenciador de integridade. |
| LogTruncationIntervalSeconds |Segundos |0 |Intervalo configurável no qual o truncamento de log será iniciado em cada réplica. Ele é usado para garantir que o log também seja truncado com base no tempo, em vez de apenas no tamanho do log. Essa configuração também força a limpeza de entradas excluídas no dicionário confiável. Portanto, ele pode ser usado para garantir que os itens excluídos sejam limpos em tempo hábil. |
| EnableStableReads |Booleano |Falso |A habilitação de leituras estáveis restringe as réplicas secundárias para retornar valores que foram confirmado de quorum. |

### <a name="sample-configuration-via-code"></a>Exemplo de configuração por meio de código
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Arquivo de configuração de exemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Observações
BatchAcknowledgementInterval controla a latência de replicação. Um valor de ' 0 ' resulta na menor latência possível, no custo da taxa de transferência (já que mais mensagens de confirmação devem ser enviadas e processadas, cada uma contendo menos confirmações).
Quanto maior o valor de BatchAcknowledgementInterval, maior será a taxa de transferência de replicação geral, com o custo de maior latência de operação. Isso se traduz diretamente na latência de confirmações de transação.

O valor de CheckpointThresholdInMB controla a quantidade de espaço em disco que o replicador pode usar para armazenar informações de estado no arquivo de log dedicado da réplica. Aumentar isso para um valor mais alto do que o padrão pode resultar em tempos de reconfiguração mais rápidos quando uma nova réplica é adicionada ao conjunto. Isso ocorre devido à transferência de estado parcial que ocorre devido à disponibilidade de mais histórico de operações no log. Isso pode aumentar potencialmente o tempo de recuperação de uma réplica após uma falha.

A configuração MaxRecordSizeInKB define o tamanho máximo de um registro que pode ser gravado pelo replicador no arquivo de log. Na maioria dos casos, o tamanho de registro padrão de 1024-KB é ideal. No entanto, se o serviço estiver fazendo com que itens de dados maiores façam parte das informações de estado, esse valor poderá precisar ser aumentado. Há pouco benefício em tornar o MaxRecordSizeInKB menor que 1024, pois registros menores usam apenas o espaço necessário para o registro menor. Esperamos que esse valor precise ser alterado apenas em casos raros.

As configurações de SharedLogId e SharedLogPath são sempre usadas juntas para fazer com que um serviço use um log compartilhado separado do log compartilhado padrão para o nó. Para obter a melhor eficiência, o máximo de serviços possível deve especificar o mesmo log compartilhado. Os arquivos de log compartilhados devem ser colocados em discos que são usados somente para o arquivo de log compartilhado para reduzir a contenção de movimento de cabeçalho. Esperamos que esse valor precise ser alterado apenas em casos raros.

## <a name="next-steps"></a>Passos seguintes
* [Depurar seu aplicativo Service Fabric no Visual Studio](service-fabric-debugging-your-application.md)
* [Referência do desenvolvedor para Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

