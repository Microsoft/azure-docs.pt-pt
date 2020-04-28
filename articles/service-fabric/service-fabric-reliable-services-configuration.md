---
title: Configure Azure Service Fabric Reliable Services
description: Aprenda sobre configurar serviços fiáveis e imponentes numa aplicação Azure Service Fabric globalmente e para um único serviço.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 9743213394b59af701b25b8be9dd48cf4310b499
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645519"
---
# <a name="configure-stateful-reliable-services"></a>Configurar serviços fiáveis e estatais
Existem dois conjuntos de configurações para serviços fiáveis. Um conjunto é global para todos os serviços fiáveis no cluster, enquanto o outro conjunto é específico de um determinado serviço fiável.

## <a name="global-configuration"></a>Configuração Global
A configuração global de serviço fiável é especificada no manifesto de cluster para o cluster sob a secção KtlLogger. Permite a configuração da localização e tamanho do registo partilhado mais os limites globais de memória utilizados pelo madeireiro. O manifesto do cluster é um único ficheiro XML que contém configurações e configurações que se aplicam a todos os nós e serviços do cluster. O ficheiro é tipicamente chamado ClusterManifest.xml. Pode ver o manifesto de cluster para o seu cluster utilizando o comando Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolminimumKB |Kilobytes |8388608 |Número mínimo de KB para alocar no modo kernel para o conjunto de memória tampão de escrita de login. Este conjunto de memória é usado para gravar informações do estado antes de escrever para o disco. |
| WriteBufferMemoryPoolMaximumInKB |Kilobytes |Sem Limite |Tamanho máximo ao qual o conjunto de memória tampão de gravação pode crescer. |
| SharedLogid |GUID |"" |Especifica um GUID único para identificar o ficheiro de registo partilhado por defeito utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogId na sua configuração específica do serviço. Se o SharedLogId for especificado, então o SharedLogPath também deve ser especificado. |
| SharedLogPath |Nome de caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde o ficheiro de registo partilhado utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogPath na sua configuração específica do serviço. No entanto, se o SharedLogPath for especificado, então o SharedLogId também deve ser especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB de espaço em disco para alocar estáticamente para o registo partilhado. O valor deve ser 2048 ou maior. |

No modelo JSON Azure ARM ou no local, o exemplo abaixo mostra como alterar o registo de transações partilhadas que é criado para apoiar quaisquer coleções fiáveis para serviços estatais.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Prove a secção de manifesto de aglomerado de desenvolvedores locais
Se quiser alterar isto no seu ambiente de desenvolvimento local, precisa editar o ficheiro clustermanifest.xml local.

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
O madeireiro tem um conjunto global de memória alocada a partir de memória de kernel não páginada que está disponível para todos os serviços confiáveis em um nó para cortar dados do estado antes de ser escrito para o registo dedicado associado à réplica de serviço confiável. O tamanho da piscina é controlado pelas definições WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumKB especifica tanto o tamanho inicial desta piscina de memória como o tamanho mais baixo para o qual o conjunto de memória pode encolher. WriteBufferMemoryPoolMaximumInKB é o tamanho mais alto para o qual o conjunto de memória pode crescer. Cada réplica de serviço fiável que é aberta pode aumentar o tamanho do pool de memória por um sistema determinado até WriteBufferMemoryPoolMaximumInKB. Se houver mais procura de memória do conjunto de memórias do que o disponível, os pedidos de memória serão adiados até que a memória esteja disponível. Portanto, se o conjunto de memória tampão de escrita for demasiado pequeno para uma configuração específica, então o desempenho pode sofrer.

As definições SharedLogId e SharedLogPath são sempre utilizadas em conjunto para definir o GUID e a localização para o registo partilhado por defeito para todos os nós do cluster. O registo partilhado por defeito é utilizado para todos os serviços fiáveis que não especificam as definições nas definições.xml para o serviço específico. Para um melhor desempenho, os ficheiros de registo partilhados devem ser colocados em discos que são utilizados exclusivamente para o ficheiro de registo partilhado para reduzir a contenção.

O SharedLogSizeInMB especifica a quantidade de espaço do disco para pré-atribuir para o registo partilhado por defeito em todos os nós.  O SharedLogId e o SharedLogPath não precisam de ser especificados para que o SharedLogSizeInMB seja especificado.

## <a name="service-specific-configuration"></a>Configuração específica do serviço
Pode modificar as configurações predefinidas dos Serviços Fiáveis, utilizando o pacote de configuração (Config) ou a implementação do serviço (código).

* **Config** - A configuração através do pacote config é realizada alterando o ficheiro Definições.xml que é gerado na raiz do pacote do Microsoft Visual Studio sob a pasta Config para cada serviço na aplicação.
* **Código** - A configuração via código é realizada através da criação de um ReliableStateManager utilizando um objeto ReliableStateManagerConfiguration com as opções apropriadas definidas.

Por predefinição, o tempo de execução do Tecido de Serviço Azure procura nomes de secção predefinidos no ficheiro Definições.xml e consome os valores de configuração enquanto cria os componentes de tempo de execução subjacentes.

> [!NOTE]
> Não **not** elimine os nomes da secção das seguintes configurações no ficheiro Definições.xml que é gerado na solução Estúdio Visual, a menos que planeie configurar o seu serviço através do código.
> Renomear o pacote de config ou os nomes da secção exigirão uma alteração de código ao configurar o ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador são usadas para fixar o canal de comunicação que é utilizado durante a replicação. Isto significa que os serviços não poderão ver o tráfego de replicação uns dos outros, garantindo que os dados que são disponibilizados altamente disponíveis também são seguros. Por predefinição, uma secção de configuração de segurança vazia impede a segurança da replicação.

> [!IMPORTANT]
> Nos nós linux, os certificados devem ser formatados em PEM. Para saber mais sobre a localização e configuração de certificados para o Linux, consulte [os certificados de Configuração no Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Nome da secção predefinida
ReplicatorSecurityConfig

> [!NOTE]
> Para alterar este nome de secção, substitua o parâmetro replicatorSecuritySectionName para o construtor ReliableStateManagerConfiguration ao criar o ReliableStateManager Manager para este serviço.
> 
> 

### <a name="replicator-configuration"></a>Configuração do replicador
As configurações de replicadores configuram o replicador que é responsável por tornar o estado do Serviço Fiável altamente fiável replicando e persistindo o estado localmente.
A configuração predefinida é gerada pelo modelo do Estúdio Visual e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para afinar o replicador.

### <a name="default-section-name"></a>Nome da secção predefinida
ReplicatorConfig

> [!NOTE]
> Para alterar este nome de secção, substitua o parâmetro replicatorSettingsSectionName para o construtor ReliableStateManagerConfiguration ao criar o ReliableStateManager Manager para este serviço.
> 
> 

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| Intervalo de Reconhecimento de Lote |Segundos |0.015 |Período de tempo para o qual o replicador no secundário aguarda depois de receber uma operação antes de enviar de volta um reconhecimento para as primárias. Quaisquer outros agradecimentos a serem enviados para operações processadas dentro deste intervalo são enviados como uma resposta. |
| Ponto de fim de réplica |N/D |Sem parâmetro sem necessidade de incumprimento |Endereço IP e porta que o replicador primário/secundário utilizará para comunicar com outros replicadores no conjunto de réplicas. Isto deve referir um ponto final de recurso TCP no manifesto de serviço. Consulte os [recursos manifestos do Serviço](service-fabric-service-manifest-resources.md) para ler mais sobre a definição de recursos endpoint num manifesto de serviço. |
| MaxPrimaryReplicationTionQueueSize |Número de operações |8192 |Número máximo de operações na fila principal. Uma operação é libertada depois que o replicador primário recebe um reconhecimento de todos os replicadores secundários. Este valor deve ser superior a 64 e uma potência de 2. |
| MaxSecondreplicationTionQueueSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é libertada depois de tornar o seu estado altamente disponível através da persistência. Este valor deve ser superior a 64 e uma potência de 2. |
| CheckpointThresholdinMB |MB |50 |Quantidade de espaço de ficheiro de registo após o qual o estado está posto de controlo. |
| MaxRecordSizeInKB |KB |1024 |Maior tamanho de registo que o replicador pode escrever no registo. Este valor deve ser um múltiplo de 4 e superior a 16. |
| MinLogSizeInMB |MB |0 (sistema determinado) |Tamanho mínimo do registo transacional. O registo não será permitido truncar para um tamanho abaixo desta definição. 0 indica que o replicador determinará o tamanho mínimo do tronco. O aumento deste valor aumenta a possibilidade de fazer cópias parciais e backups incrementais, uma vez que as probabilidades de registos de registos relevantes serem truncados são reduzidas. |
| TruncationThresholdFactor |Fator |2 |Determina a que tamanho do tronco, a truncação será ativada. O limiar de truncation é determinado por MinLogSizeInMB multiplicado por TruncationThresholdFactor. TruncationThresholdFactor deve ser superior a 1. MinLogSizeInMB * TruncationThresholdFactor deve ser inferior ao MaxStreamSizeInMB. |
| ThrotlingThresholdFactor |Fator |4 |Determina a que tamanho do tronco, a réplica começará a ser estrangulada. O limiar de estrangulamento (em MB) é determinado por Max ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). O limiar de estrangulamento (em MB) deve ser superior ao limiar de truncação (em MB). O limiar de truncation (em MB) deve ser inferior ao MaxStreamSizeInMB. |
| MaxAcumuladoBackupLogSizeInMB |MB |800 |Tamanho máximo acumulado (em MB) de registos de backup numa dada cadeia de registos de backup. Um pedido de backup incremental falhará se a cópia de segurança incremental gerar um registo de cópia de segurança que faria com que os registos de backup acumulados, uma vez que a cópia de segurança completa relevante fosse maior do que este tamanho. Nesses casos, o utilizador é obrigado a obter uma cópia de segurança completa. |
| SharedLogid |GUID |"" |Especifica um GUID único para identificar o ficheiro de registo partilhado utilizado com esta réplica. Normalmente, os serviços não devem utilizar esta configuração. No entanto, se o SharedLogId for especificado, então o SharedLogPath também deve ser especificado. |
| SharedLogPath |Nome de caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde será criado o ficheiro de registo partilhado para esta réplica. Normalmente, os serviços não devem utilizar esta configuração. No entanto, se o SharedLogPath for especificado, então o SharedLogId também deve ser especificado. |
| Duração da monitorização lenta |Segundos |300 |Define o intervalo de monitorização para chamadas API geridas. Exemplo: o utilizador forneceu uma função de backback de reserva. Após o intervalo ter passado, será enviado um relatório de saúde de alerta ao Gestor de Saúde. |
| LogTruncationIntervalSeconds |Segundos |0 |Intervalo configurável no qual será iniciada a truncação de registo em cada réplica. É utilizado para garantir que o registo também é truncado com base no tempo em vez de apenas tamanho de tronco. Esta definição também obriga à purga das entradas eliminadas no dicionário fiável. Por conseguinte, pode ser utilizado para garantir que os itens eliminados sejam purgados atempadamente. |
| HabilitarStableReads |Booleano |Falso |A habilitação de leituras estáveis restringe as réplicas secundárias aos valores de retorno que foram aquecidas pelo quórum. |

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
BatchRecognisementInterval controla a latência da replicação. Um valor de '0' resulta na latência mais baixa possível, à custa da entrada (uma vez que mais mensagens de reconhecimento devem ser enviadas e processadas, cada uma contendo menos reconhecimentos).
Quanto maior for o valor para o BatchRecognisementInterval, maior é o rendimento global da replicação, à custa de uma maior latência de operação. Isto traduz-se diretamente na latência das transações.

O valor para CheckpointThresholdInMB controla a quantidade de espaço em disco que o replicador pode usar para armazenar informações estatais no ficheiro de registo dedicado da réplica. Aumentar isto para um valor mais elevado do que o padrão pode resultar em tempos de reconfiguração mais rápidos quando uma nova réplica é adicionada ao conjunto. Isto deve-se à transferência parcial do Estado que ocorre devido à disponibilidade de mais histórico de operações no registo. Isto pode potencialmente aumentar o tempo de recuperação de uma réplica após um acidente.

A definição MaxRecordSizeInKB define o tamanho máximo de um disco que pode ser escrito pelo replicador no ficheiro de registo. Na maioria dos casos, o tamanho do recorde padrão de 1024-KB é ideal. No entanto, se o serviço está a causar que os itens de dados maiores façam parte da informação do Estado, então esse valor poderá ter de ser aumentado. Há pouco saem beneficiados em tornar maxRecordSizeInKB menor que 1024, uma vez que os registos mais pequenos usam apenas o espaço necessário para o disco mais pequeno. Esperamos que este valor tenha de ser alterado apenas em casos raros.

As definições SharedLogId e SharedLogPath são sempre utilizadas em conjunto para fazer com que um serviço utilize um registo partilhado separado do registo partilhado por defeito para o nó. Para uma melhor eficiência, o maior número possível de serviços deve especificar o mesmo registo partilhado. Os ficheiros de registo partilhados devem ser colocados em discos que são utilizados exclusivamente para o ficheiro de registo partilhado para reduzir a contenção do movimento da cabeça. Esperamos que este valor tenha de ser alterado apenas em casos raros.

## <a name="next-steps"></a>Passos seguintes
* [Desinvejo a sua aplicação de Tecido de Serviço no Estúdio Visual](service-fabric-debugging-your-application.md)
* [Referência do desenvolvedor para Serviços Fiáveis](https://msdn.microsoft.com/library/azure/dn706529.aspx)

