---
title: Configure ServiçoS Fiáveis de Tecido Azure
description: Saiba como configurar serviços fidedigdos numa aplicação Azure Service Fabric globalmente e para um único serviço.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.custom: devx-track-csharp
ms.openlocfilehash: cda0a9f988afae58a60bff051885a5eec8afe434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021974"
---
# <a name="configure-stateful-reliable-services"></a>Configure serviços fidedigcos estatais
Existem dois conjuntos de configurações para serviços fiáveis. Um conjunto é global para todos os serviços fiáveis no cluster, enquanto o outro conjunto é específico de um serviço de confiança particular.

## <a name="global-configuration"></a>Configuração Global
A configuração global de serviço fiável é especificada no manifesto de cluster para o cluster sob a secção KtlLogger. Permite a configuração da localização e tamanho do registo partilhado, além dos limites globais de memória utilizados pelo madeirão. O manifesto de cluster é um único ficheiro XML que contém configurações e configurações que se aplicam a todos os nós e serviços do cluster. O ficheiro é normalmente chamado ClusterManifest.xml. Pode ver o manifesto de cluster para o seu cluster usando o comando Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobytes |8388608 |Número mínimo de KB para alocar no modo kernel para o logger escrever piscina de memória tampão. Este conjunto de memórias é utilizado para cache informações do estado antes de escrever no disco. |
| WriteBufferMemoryPoolMaximumInKB |Kilobytes |Sem Limite |Tamanho máximo ao qual o madeireiro escreve piscina de memória tampão pode crescer. |
| SharedLogId |GUID |"" |Especifica um GUID único para utilizar para identificar o ficheiro de registo partilhado padrão utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogId na sua configuração específica de serviço. Se o SharedLogId for especificado, então o SharedLogPath também deve ser especificado. |
| CompartilhadoLogAta |Nome do caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde o ficheiro de registo partilhado utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogPath na sua configuração específica de serviço. No entanto, se o SharedLogPath for especificado, então o SharedLogId também deve ser especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB do espaço do disco para atribuir estáticamente para o registo partilhado. O valor deve ser 2048 ou maior. |

No modelo JSON da Azure ARM ou no local, o exemplo abaixo mostra como alterar o registo de transações partilhado que é criado para apoiar quaisquer cobranças fiáveis para serviços estatais.

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="sample-local-developer-cluster-manifest-section"></a>Corte de projeto local de desenvolvimento local secção
Se quiser alterar isto no seu ambiente de desenvolvimento local, tem de editar o ficheiro local clustermanifest.xml.

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
O madeireiro tem um conjunto global de memórias alocadas a partir da memória kernel não paged que está disponível para todos os serviços fiáveis em um nó para caching dados do estado antes de ser escrito para o registo dedicado associado com a réplica de serviço confiável. O tamanho da piscina é controlado pelas definições WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB especifica tanto o tamanho inicial deste pool de memória como o tamanho mais baixo a que o pool de memória pode encolher. WriteBufferMemoryPoolMaximumInKB é o tamanho mais alto ao qual o pool de memória pode crescer. Cada réplica de serviço fiável que é aberta pode aumentar o tamanho do pool de memória por um sistema determinado até writeBufferMemoryPoolMaximumInKB. Se houver mais procura de memória do conjunto de memórias do que disponível, os pedidos de memória serão adiados até que a memória esteja disponível. Portanto, se o conjunto de memória tampão de escrita for demasiado pequeno para uma determinada configuração, o desempenho pode sofrer.

As definições SharedLogId e SharedLogPath são sempre utilizadas em conjunto para definir o GUIADO e a localização para o registo partilhado padrão para todos os nós do cluster. O registo partilhado por defeito é utilizado para todos os serviços fiáveis que não especificam as definições no settings.xml para o serviço específico. Para melhor desempenho, os ficheiros de registo partilhados devem ser colocados em discos que são utilizados exclusivamente para o ficheiro de registo partilhado para reduzir a contenção.

SharedLogSizeInMB especifica a quantidade de espaço do disco para pré-lolocalização para o registo partilhado padrão em todos os nós.  O SharedLogId e o SharedLogPath não precisam de ser especificados para que o SharedLogSizeInMB seja especificado.

## <a name="service-specific-configuration"></a>Configuração específica do serviço
Pode modificar as configurações predefinitadas dos Serviços Fidedigdos, utilizando o pacote de configuração (Config) ou a implementação do serviço (código).

* **Config** - A configuração através do pacote config é realizada alterando o ficheiro Settings.xml gerado na raiz do pacote Microsoft Visual Studio sob a pasta Config para cada serviço na aplicação.
* **Código**   - A configuração via código é realizada através da criação de um ReliableStateManagerConfiguration com o conjunto de opções apropriadas.

Por predefinição, o tempo de execução do Tecido de Serviço Azure procura nomes de secção predefinidos no ficheiro Settings.xml e consome os valores de configuração enquanto cria os componentes de tempo de execução subjacentes.

> [!NOTE]
> **Não** elimine os nomes de secção das seguintes configurações no ficheiro Settings.xml gerado na solução Visual Studio, a menos que planeie configurar o seu serviço por código.
> Renomear o pacote de configuração ou nomes de secção exigirá uma alteração de código ao configurar o ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador são usadas para proteger o canal de comunicação que é utilizado durante a replicação. Isto significa que os serviços não poderão ver o tráfego de replicação uns dos outros, garantindo que os dados que são disponibilizados altamente disponíveis também são seguros. Por predefinição, uma secção de configuração de segurança vazia impede a segurança da replicação.

> [!IMPORTANT]
> Nos nós Linux, os certificados devem ser formatados por PEM. Para saber mais sobre a localização e configuração de certificados para Linux, consulte [os certificados de configuração no Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Nome da secção predefinido
ReplicatorSecurityConfig

> [!NOTE]
> Para alterar este nome de secção, substitua o parâmetro de verificação do replicadorSecuritySectionName para o construtor ReliableStateManagerConfiguration ao criar o ReliableStateManager para este serviço.
> 
> 

### <a name="replicator-configuration"></a>Configuração do replicador
As configurações do replicador configuram o replicador que é responsável por tornar o estado do Stateful Reliable Service altamente fiável replicando e persistindo o estado localmente.
A configuração padrão é gerada pelo modelo de Estúdio Visual e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para sintonizar o replicador.

### <a name="default-section-name"></a>Nome da secção predefinido
ReplicadorConfig

> [!NOTE]
> Para alterar este nome de secção, substitua o parâmetro de replicaçãoSettingsSectionName para o construtor ReliableStateManagerConfiguration ao criar o ReliableStateManager para este serviço.
> 
> 

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAckAckNownowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicador no secundário aguarda depois de receber uma operação antes de enviar de volta um aviso para o primário. Quaisquer outros avisos a serem enviados para operações processadas dentro deste intervalo são enviados como uma resposta. |
| Ponto de réplica |N/D |Sem parâmetro padrão-necessário |Endereço IP e porta que o replicador primário/secundário utilizará para comunicar com outros replicadores no conjunto de réplicas. Isto deve fazer referência a um ponto final de recurso TCP no manifesto de serviço. Consulte os [recursos manifestos do Serviço](service-fabric-service-manifest-resources.md) para ler mais sobre a definição de recursos de ponto final num manifesto de serviço. |
| MaxPrimaryReplicationQueueSize |Número de operações |8192 |Número máximo de operações na fila primária. Uma operação é libertada após o replicador primário receber um reconhecimento de todos os replicadores secundários. Este valor deve ser superior a 64 e uma potência de 2. |
| MaxSecondaryReplicationSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é libertada depois de tornar o seu estado altamente disponível através da persistência. Este valor deve ser superior a 64 e uma potência de 2. |
| CheckpointThresholdInMB |MB |50 |Quantidade de espaço de ficheiro de log após o qual o estado é posto em controlo. |
| MaxRecordsizeInkB |KB |1024 |Maior tamanho de registo que o replicador pode escrever no registo. Este valor deve ser um múltiplo de 4 e superior a 16. |
| MinLogsizeInMB |MB |0 (sistema determinado) |Tamanho mínimo do registo transacional. O registo não será permitido truncar para um tamanho inferior a esta definição. 0 indica que o replicador determinará o tamanho mínimo do registo. Aumentar este valor aumenta a possibilidade de fazer cópias parciais e cópias de segurança incrementais, uma vez que as chances de registos relevantes a serem truncados são reduzidas. |
| TruncationThresholdFactor |Fator |2 |Determina o tamanho do tronco, a truncação será desencadeada. O limiar de trunca é determinado pelo MinLogSizeInMB multiplicado por TruncationThresholdFactor. TruncationThresholdFactor deve ser superior a 1. MinLogSizeInMB * TruncationThresholdFactor deve ser inferior ao MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Fator |4 |Determina o tamanho do tronco, a réplica começará a ser estrangulada. O limiar de aceleração (em MB) é determinado por Max ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). O limiar de aceleração (em MB) deve ser maior do que o limiar de truncação (em MB). O limiar de truncação (em MB) deve ser inferior ao MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogsizeInMB |MB |800 |Max tamanho acumulado (em MB) de registos de backup numa determinada corrente de registo de backup. Um pedido de backup incremental falhará se a cópia de segurança incremental gerar um registo de backup que faria com que os registos de backup acumulados, uma vez que a cópia de segurança completa relevante fosse maior do que este tamanho. Nesses casos, o utilizador é obrigado a fazer uma cópia de segurança completa. |
| SharedLogId |GUID |"" |Especifica um GUID único para identificar o ficheiro de registo partilhado utilizado com esta réplica. Normalmente, os serviços não devem utilizar esta definição. No entanto, se o SharedLogId for especificado, então o SharedLogPath também deve ser especificado. |
| CompartilhadoLogAta |Nome do caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde será criado o ficheiro de registo partilhado para esta réplica. Normalmente, os serviços não devem utilizar esta definição. No entanto, se o SharedLogPath for especificado, então o SharedLogId também deve ser especificado. |
| SlowApiMonitoringDuration |Segundos |300 |Define o intervalo de monitorização para chamadas de API geridas. Exemplo: o utilizador forneceu a função de retorno de backup. Após o intervalo ter passado, será enviado um relatório de saúde de alerta ao Responsável de Saúde. |
| LogTruncationIntervalSeconds |Segundos |0 |Intervalo configurável em que a truncação de registo será iniciada em cada réplica. É usado para garantir que o log também é truncado com base no tempo em vez de apenas tamanho de log. Esta definição também obriga à purga de entradas eliminadas num dicionário fiável. Assim, pode ser utilizado para garantir que os itens eliminados sejam purgados em tempo útil. |
| Ativar Leituras |Booleano |Falso |Permitir leituras estáveis restringe réplicas secundárias a valores de retorno que foram quórum-acked. |

### <a name="sample-configuration-via-code"></a>Configuração da amostra via código
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
BatchAckAcknowledgementInterval controla a latência da replicação. Um valor de '0' resulta na menor latência possível, à custa da produção (uma vez que mais mensagens de reconhecimento devem ser enviadas e processadas, cada uma contendo menos reconhecimentos).
Quanto maior for o valor para BatchAckAcknowledgementInterval, maior é a produção de replicação global, à custa de uma maior latência de operação. Isto traduz-se diretamente na latência dos compromissos de transações.

O valor para CheckpointThresholdInMB controla a quantidade de espaço em disco que o replicador pode usar para armazenar informações do estado no ficheiro de registo dedicado da réplica. Aumentar isto para um valor mais elevado do que o padrão pode resultar em tempos de reconfiguração mais rápidos quando uma nova réplica é adicionada ao conjunto. Isto deve-se à transferência parcial do Estado que ocorre devido à disponibilidade de mais histórico de operações no diário de bordo. Isto pode potencialmente aumentar o tempo de recuperação de uma réplica após um acidente.

A definição MaxRecordSizeInKB define o tamanho máximo de um registo que pode ser escrito pelo replicador no ficheiro de registo. Na maioria dos casos, o tamanho de registo padrão 1024-KB é o ideal. No entanto, se o serviço está a fazer com que os itens de dados maiores sejam parte da informação do Estado, então este valor poderá ter de ser aumentado. Há poucos benefícios em fazer MaxRecordSizeInKB menor que 1024, uma vez que os registos mais pequenos usam apenas o espaço necessário para o registo menor. Esperamos que este valor tenha de ser alterado apenas em casos raros.

As definições SharedLogId e SharedLogPath são sempre usadas em conjunto para fazer com que um serviço utilize um registo partilhado separado do registo partilhado padrão para o nó. Para melhor eficiência, o maior número possível de serviços deve especificar o mesmo registo partilhado. Os ficheiros de registo partilhados devem ser colocados em discos que são utilizados exclusivamente para o ficheiro de registo partilhado para reduzir a contenção do movimento da cabeça. Esperamos que este valor tenha de ser alterado apenas em casos raros.

## <a name="next-steps"></a>Passos seguintes
* [Depurar a sua aplicação de Tecido de Serviço no Estúdio Visual](service-fabric-debugging-your-application.md)
* [Referência do programador para serviços fiáveis](/previous-versions/azure/dn706529(v=azure.100))
