---
title: Configurar o Azure Service Fabric Reliable Services | Documentos da Microsoft
description: Saiba mais sobre a configuração de Reliable Services com estado no Azure Service Fabric.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: c131cf96f11dcddfb0de87ccf47f2d2c8a99617d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850807"
---
# <a name="configure-stateful-reliable-services"></a>Configurar a reliable services com estado
Existem dois conjuntos de definições de configuração de serviços fiáveis. Um conjunto é global para todos os serviços fiáveis do cluster enquanto o outro conjunto é específico para um determinado serviço fiável.

## <a name="global-configuration"></a>Configuração global
A configuração do serviço fiável global é especificada no manifesto do cluster para o cluster na secção KtlLogger. Ele permite a configuração da localização do registo partilhado e tamanho além dos limites de memória global utilizados pelo agente. O manifesto do cluster é um único arquivo XML que contém definições e configurações que se aplicam a todos os nós e serviços no cluster. O ficheiro é normalmente chamado ClusterManifest.xml. Pode ver o cluster de manifesto para o seu cluster com o comando do powershell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Nomes de configuração
| Name | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobytes |8388608 |Número mínimo de KB para alocar no modo de kernel para o conjunto de memória de memória intermédia de escrita de agente de log. Este conjunto de memória é usado para colocar em cache de informações de estado antes de escrever no disco. |
| WriteBufferMemoryPoolMaximumInKB |Kilobytes |Sem Limite |Pode aumentar o tamanho máximo para o qual o agente de log escrever o conjunto de memória de memória intermédia. |
| SharedLogId |GUID |"" |Especifica um GUID exclusivo a utilizar para identificar o ficheiro de registo partilhado predefinido utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogId em suas configurações específicas do serviço. Se SharedLogId for especificada, em seguida, SharedLogPath deve também ser especificado. |
| SharedLogPath |Nome do caminho completamente qualificado |"" |Especifica o caminho totalmente qualificado, onde o ficheiro de registo partilhado utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogPath em suas configurações específicas do serviço. No entanto, se SharedLogPath for especificada, em seguida, SharedLogId deve também ser especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB de espaço em disco para alocar estaticamente para o início de sessão partilhado. O valor tem de ser maior ou 2048. |

No ARM do Azure ou no modelo JSON no local, o exemplo abaixo mostra como alterar o registo de transações partilhado que é criado para fazer uma cópia de todas as coleções fiáveis para serviços com estado.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Secção de manifesto do cluster de desenvolvedor local do exemplo
Se quiser alterá-lo no seu ambiente de desenvolvimento local, terá de editar o ficheiro de local clustermanifest.xml.

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
Agente coletor tem um conjunto global de memória alocada da memória não paginada do kernel, que está disponível para todos os serviços fiáveis num nó para colocar em cache os dados de estado antes de que estão sendo gravados no log de dedicado associadas com a réplica de reliable Services. O tamanho do conjunto é controlado pelas definições de WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Especifica o tamanho inicial deste agrupamento de memória e o tamanho mais baixo a que pode reduzir o conjunto de memória. WriteBufferMemoryPoolMaximumInKB é o tamanho maior para o qual pode aumentar o conjunto de memória. Cada réplica de reliable Services que é aberta pode aumentar o tamanho do conjunto de memória por uma quantidade de sistema determinado até WriteBufferMemoryPoolMaximumInKB. Se houver mais a pedido para a memória do conjunto de memória que está disponível, solicitações de memória serão atrasadas até que a memória está disponível. Portanto, se o conjunto de memória de memória intermédia de escrita é demasiado pequeno para uma configuração específica, em seguida, o desempenho poderá diminuir.

As definições de SharedLogId e SharedLogPath são sempre utilizadas em conjunto para definir o GUID e o local para o registo de partilhado do padrão para todos os nós do cluster. O log de partilhado do padrão é usado para todos os serviços fiáveis que não especificam as definições no Settings para o serviço específico. Para um melhor desempenho, os ficheiros de registo partilhado devem ser colocados em discos que são utilizados apenas para o ficheiro de registo partilhado para reduzir a contenção.

SharedLogSizeInMB Especifica a quantidade de espaço em disco para pré-alocar para o log de partilhado padrão em todos os nós.  SharedLogId e SharedLogPath não têm de ser especificado para que SharedLogSizeInMB seja especificado.

## <a name="service-specific-configuration"></a>Configuração específica do serviço
Pode modificar as configurações predefinidas dos Reliable Services com estado utilizando o pacote de configuração (configuração) ou a implementação de serviço (código).

* **Config** -configuração através do pacote de configuração é realizada ao alterar o arquivo Settings XML que é gerado na raiz do pacote do Microsoft Visual Studio sob a pasta de configuração para cada serviço no aplicativo.
* **Código** -configuração por meio do código é realizada através da criação de um ReliableStateManager usando um objeto de ReliableStateManagerConfiguration com o conjunto de opções apropriadas.

Por predefinição, o tempo de execução do Azure Service Fabric procura os nomes predefinidos em Settings XML e consome os valores de configuração ao criar os componentes de tempo de execução subjacente.

> [!NOTE]
> Fazer **não** eliminar os nomes de seção das seguintes configurações em Settings XML que é gerado na solução do Visual Studio, a menos que planeia configurar o seu serviço por meio do código.
> Mudar o nome dos nomes de pacote ou a seção de configuração irá exigir uma alteração de código, quando configurar o ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
Configurações de segurança do replicador são utilizadas para proteger o canal de comunicação que é utilizado durante a replicação. Isso significa que os serviços não será capazes de ver uns dos outros tráfego de replicação, garantindo que os dados que esteja altamente disponíveis também são seguros. Por predefinição, uma seção de configuração de segurança vazio impede que a segurança de replicação.

> [!IMPORTANT]
> Em nós do Linux, certificados tem de ser formatada PEM. Para saber mais sobre localizar e configurar os certificados para Linux, veja [configurar certificados no Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Nome da secção predefinida
ReplicatorSecurityConfig

> [!NOTE]
> Para alterar este nome de secção, substitua o parâmetro de replicatorSecuritySectionName para o construtor de ReliableStateManagerConfiguration ao criar o ReliableStateManager para este serviço.
> 
> 

### <a name="replicator-configuration"></a>Configuração do replicador
Configurações do replicador configurar o replicator, que é responsável por verificar o estado do serviço fiável com estado altamente confiável, replicar e manter o estado localmente.
A configuração predefinida é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre as configurações adicionais que estão disponíveis para otimizar o replicator.

### <a name="default-section-name"></a>Nome da secção predefinida
ReplicatorConfig

> [!NOTE]
> Para alterar este nome de secção, substitua o parâmetro de replicatorSettingsSectionName para o construtor de ReliableStateManagerConfiguration ao criar o ReliableStateManager para este serviço.
> 
> 

### <a name="configuration-names"></a>Nomes de configuração
| Name | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicator nas esperas secundários depois de receber uma operação antes de enviar de volta uma confirmação para o principal. Qualquer as confirmações para serem enviados para operações processadas dentro deste intervalo são enviadas como uma resposta. |
| ReplicatorEndpoint |N/A |Sem predefinição - parâmetro necessário |Endereço IP e porta que o replicator primária/secundária irá utilizar para comunicar com outros replicators na réplica definidos. Isso deve fazer referência a um ponto de final de recurso TCP no manifesto do serviço. Consulte a [recursos do manifesto do serviço](service-fabric-service-manifest-resources.md) para ler mais sobre como definir os recursos de ponto final no manifesto do serviço. |
| MaxPrimaryReplicationQueueSize |Número de operações |8192 |Número máximo de operações na fila principal. Uma operação for liberada depois do replicador primário recebe uma confirmação de todos os replicators secundários. Este valor tem de ser superior a 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação for liberada depois de fazer o seu estado de elevada disponibilidade através de persistência. Este valor tem de ser superior a 64 e uma potência de 2. |
| CheckpointThresholdInMB |MB |50 |Quantidade de espaço de ficheiro de registo após o qual o estado é foi efetuada a verificação. |
| MaxRecordSizeInKB |KB |1024 |Maior tamanho de registo que o replicator pode escrever no registo. Este valor tem de ser um múltiplo de 4 e superior a 16. |
| MinLogSizeInMB |MB |0 (sistema de determinado) |Tamanho mínimo do registo transacional. O registo não terá permissão para truncar para um tamanho abaixo esta definição. 0 indica que o replicator determinará o tamanho de registo mínimo. Aumento deste valor aumenta a possibilidade de fazer cópias parciais e cópias de segurança incrementais desde as chances de registos de registo relevantes, originando o truncamento é reduzido. |
| TruncationThresholdFactor |Fator |2 |Determina o tamanho do registo, será acionado truncamento. Limiar de truncagem é determinado pelo MinLogSizeInMB multiplicado por TruncationThresholdFactor. TruncationThresholdFactor tem de ser superior a 1. MinLogSizeInMB * TruncationThresholdFactor tem de ser menor que MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Fator |4 |Determina o tamanho do registo, a réplica começarão a ser limitada. Limiar de limitação (em MB) é determinado pelo número máximo ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Limiar de limitação (em MB) tem de ser superior ao limiar de truncamento (em MB). Limiar de truncamento (em MB) tem de ser menor que MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Máx. acumulados tamanho (em MB) de logs de backup numa cadeia de registos de cópia de segurança especificado. Um pedidos de cópia de segurança incrementais irão falhar se a cópia de segurança incremental geraria um log de cópia de segurança que poderia fazer com que os registos de cópia de segurança acumulados desde o backup completo relevante para apresentar mais do que este tamanho. Nesses casos, é pedido ao utilizador para efetuar uma cópia de segurança completa. |
| SharedLogId |GUID |"" |Especifica um GUID exclusivo a utilizar para identificar o arquivo de log partilhados utilizado com esta réplica. Normalmente, os serviços não devem utilizar esta definição. No entanto, se SharedLogId for especificada, em seguida, SharedLogPath deve também ser especificado. |
| SharedLogPath |Nome do caminho completamente qualificado |"" |Especifica o caminho totalmente qualificado, onde o ficheiro de registo partilhado para esta réplica será criado. Normalmente, os serviços não devem utilizar esta definição. No entanto, se SharedLogPath for especificada, em seguida, SharedLogId deve também ser especificado. |
| SlowApiMonitoringDuration |Segundos |300 |Define o intervalo de monitorização para chamadas de API geridos. Exemplo: utilizador forneceu a função de retorno de chamada de cópia de segurança. Após ter passado o intervalo, será enviado um relatório de estado de funcionamento de aviso para o Gestor de estado de funcionamento. |
| LogTruncationIntervalSeconds |Segundos |0 |Intervalo configurável em qual log truncamento será iniciado em todas as réplicas. É utilizado para garantir que o registo ser truncado também com base no tempo, em vez de apenas tamanho do registo. Esta definição também força a remoção de entradas foi eliminadas no dicionário fiável. Por conseguinte, ele pode ser usado para se certificar de itens eliminados são removidos de forma atempada. |

### <a name="sample-configuration-via-code"></a>Exemplo de configuração por meio do código
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


### <a name="sample-configuration-file"></a>Ficheiro de configuração de exemplo
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
BatchAcknowledgementInterval controla a latência de replicação. Um valor de '0' resulta em menor latência possível, ao custo de débito (como mais mensagens de confirmação tem de ser enviadas e processadas, cada um deles contendo as confirmações menos).
Quanto maior for o valor para BatchAcknowledgementInterval, maior será a replicação produtividade geral, ao custo de latência superior de operação. Isto traduz-se diretamente para a latência de consolidações de transações.

O valor para CheckpointThresholdInMB controla a quantidade de espaço em disco que o replicator pode utilizar para armazenar informações de estado no arquivo de log dedicado a réplica. Aumentar isso para um valor mais alto que não o predefinido pode resultar em tempos mais rápidos de reconfiguração quando uma nova réplica é adicionada ao conjunto. Isso se deve a transferência de estado parcial que ocorre devido à disponibilidade de histórico mais operações no registo. Isso, potencialmente, pode aumentar o tempo de recuperação de uma réplica após uma pane.

A definição de MaxRecordSizeInKB define o tamanho máximo de um registo que pode ser gravado pelo replicador no ficheiro de registo. Na maioria dos casos, o tamanho de 1024 KB registo predefinido é o ideal. No entanto, se o serviço está a causar maiores itens de dados como parte das informações de estado, em seguida, este valor poderá ter de ser aumentados. Há poucas vantagens em se tornar MaxRecordSizeInKB menor do que 1024, como registos menores, utilize apenas o espaço necessário para o registo mais pequeno. Esperamos que este valor teriam de ser alteradas em casos raros apenas.

As definições de SharedLogId e SharedLogPath são sempre utilizadas em conjunto para tornar um serviço a utilizar o registo partilhado separado do log partilhada padrão para o nó. Para uma melhor eficiência, como vários serviços possível devem especificar o mesmo registo partilhado. Ficheiros de registo partilhado devem ser colocados em discos que são utilizados apenas para o ficheiro de registo partilhado para reduzir a contenção de movimentação do cabeçote. Esperamos que este valor teriam de ser alteradas em casos raros apenas.

## <a name="next-steps"></a>Passos Seguintes
* [Depurar a sua aplicação do Service Fabric no Visual Studio](service-fabric-debugging-your-application.md)
* [Referência do desenvolvedor do Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

