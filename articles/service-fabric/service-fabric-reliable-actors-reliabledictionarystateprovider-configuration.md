---
title: Alterar definições reliableDictionaryActorStateProvider
description: Saiba mais sobre a configuração de atores estatais do Azure Service Fabric do tipo ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75609745"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configurar atores fiáveis--ReliableDictionaryActorStateProvider
Pode modificar a configuração padrão do ReliableDictionaryActorStateProvider alterando o ficheiro settings.xml gerado na raiz do pacote Visual Studio sob a pasta Config para o ator especificado.

O tempo de execução do Tecido de Serviço Azure procura nomes de secção predefinidos no ficheiro settings.xml e consome os valores de configuração enquanto cria os componentes de tempo de execução subjacentes.

> [!NOTE]
> **Não** elimine nem modifique os nomes de secção das seguintes configurações no ficheiro settings.xml gerado na solução Visual Studio.
> 
> 

Existem também configurações globais que afetam a configuração do ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuração Global
A configuração global é especificada no manifesto de cluster para o cluster sob a secção KtlLogger. Permite a configuração da localização e tamanho do registo partilhado, além dos limites globais de memória utilizados pelo madeirão. Note que as alterações no manifesto do cluster afetam todos os serviços que utilizam serviços reliableDictionaryActorStateProvider e serviços estatais fiáveis.

O manifesto de cluster é um único ficheiro XML que contém configurações e configurações que se aplicam a todos os nós e serviços do cluster. O ficheiro é normalmente chamado ClusterManifest.xml. Pode ver o manifesto de cluster para o seu cluster usando o comando Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Nomes de configuração
| Name | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobytes |8388608 |Número mínimo de KB para alocar no modo kernel para o logger escrever piscina de memória tampão. Este conjunto de memórias é utilizado para cache informações do estado antes de escrever no disco. |
| WriteBufferMemoryPoolMaximumInKB |Kilobytes |Sem Limite |Tamanho máximo ao qual o madeireiro escreve piscina de memória tampão pode crescer. |
| SharedLogId |GUID |"" |Especifica um GUID único para utilizar para identificar o ficheiro de registo partilhado padrão utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogId na sua configuração específica de serviço. Se o SharedLogId for especificado, então o SharedLogPath também deve ser especificado. |
| CompartilhadoLogAta |Nome do caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde o ficheiro de registo partilhado utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogPath na sua configuração específica de serviço. No entanto, se o SharedLogPath for especificado, então o SharedLogId também deve ser especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB do espaço do disco para atribuir estáticamente para o registo partilhado. O valor deve ser 2048 ou maior. |

### <a name="sample-cluster-manifest-section"></a>Secção manifesto de cluster de amostra
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Observações
O madeireiro tem um conjunto global de memórias alocadas a partir da memória kernel não paged que está disponível para todos os serviços fiáveis em um nó para caching dados do estado antes de ser escrito para o registo dedicado associado com a réplica de serviço confiável. O tamanho da piscina é controlado pelas definições WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB especifica tanto o tamanho inicial deste pool de memória como o tamanho mais baixo a que o pool de memória pode encolher. WriteBufferMemoryPoolMaximumInKB é o tamanho mais alto ao qual o pool de memória pode crescer. Cada réplica de serviço fiável que é aberta pode aumentar o tamanho do pool de memória por um sistema determinado até writeBufferMemoryPoolMaximumInKB. Se houver mais procura de memória do conjunto de memórias do que disponível, os pedidos de memória serão adiados até que a memória esteja disponível. Portanto, se o conjunto de memória tampão de escrita for demasiado pequeno para uma determinada configuração, o desempenho pode sofrer.

As definições SharedLogId e SharedLogPath são sempre utilizadas em conjunto para definir o GUIADO e a localização para o registo partilhado padrão para todos os nós do cluster. O registo partilhado por defeito é utilizado para todos os serviços fiáveis que não especificam as definições no settings.xml para o serviço específico. Para melhor desempenho, os ficheiros de registo partilhados devem ser colocados em discos que são utilizados exclusivamente para o ficheiro de registo partilhado para reduzir a contenção.

SharedLogSizeInMB especifica a quantidade de espaço do disco para pré-lolocalização para o registo partilhado padrão em todos os nós.  O SharedLogId e o SharedLogPath não precisam de ser especificados para que o SharedLogSizeInMB seja especificado.

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador são usadas para proteger o canal de comunicação que é utilizado durante a replicação. Isto significa que os serviços não podem ver o tráfego de replicação uns dos outros, garantindo que os dados que são disponibilizados altamente disponíveis também são seguros.
Por predefinição, uma secção de configuração de segurança vazia impede a segurança da replicação.

> [!IMPORTANT]
> Nos nós Linux, os certificados devem ser formatados por PEM. Para saber mais sobre a localização e configuração de certificados para Linux, consulte [os certificados de configuração no Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nome da secção
&lt;ActorName &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
As configurações do replicador são usadas para configurar o replicador que é responsável por tornar o estado do Fornecedor estatal do ator altamente fiável replicando e persistindo o estado localmente.
A configuração padrão é gerada pelo modelo de Estúdio Visual e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para sintonizar o replicador.

### <a name="section-name"></a>Nome da secção
&lt;ActorName &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração
| Name | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAckAckNownowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicador no secundário aguarda depois de receber uma operação antes de enviar de volta um aviso para o primário. Quaisquer outros avisos a serem enviados para operações processadas dentro deste intervalo são enviados como uma resposta. |
| Ponto de réplica |N/D |Sem parâmetro padrão-necessário |Endereço IP e porta que o replicador primário/secundário utilizará para comunicar com outros replicadores no conjunto de réplicas. Isto deve fazer referência a um ponto final de recurso TCP no manifesto de serviço. Consulte os [recursos manifestos do Serviço](service-fabric-service-manifest-resources.md) para ler mais sobre a definição de recursos de ponto final no manifesto de serviço. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamanho máximo de dados de replicação que podem ser transmitidos numa única mensagem. |
| MaxPrimaryReplicationQueueSize |Número de operações |8192 |Número máximo de operações na fila primária. Uma operação é libertada após o replicador primário receber um reconhecimento de todos os replicadores secundários. Este valor deve ser superior a 64 e uma potência de 2. |
| MaxSecondaryReplicationSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é libertada depois de tornar o seu estado altamente disponível através da persistência. Este valor deve ser superior a 64 e uma potência de 2. |
| CheckpointThresholdInMB |MB |200 |Quantidade de espaço de ficheiro de log após o qual o estado é posto em controlo. |
| MaxRecordsizeInkB |KB |1024 |Maior tamanho de registo que o replicador pode escrever no registo. Este valor deve ser um múltiplo de 4 e superior a 16. |
| OptimizeLogForLowerDiskUsage |Booleano |true |Quando verdadeiro, o registo é configurado de modo a que o ficheiro de registo dedicado da réplica seja criado utilizando um ficheiro escasso NTFS. Isto reduz a utilização real do espaço do disco para o ficheiro. Quando falso, o ficheiro é criado com alocações fixas, que fornecem o melhor desempenho de escrita. |
| SharedLogId |guid |"" |Especifica um guia único para identificar o ficheiro de log partilhado utilizado com esta réplica. Normalmente, os serviços não devem utilizar esta definição. No entanto, se o SharedLogId for especificado, então o SharedLogPath também deve ser especificado. |
| CompartilhadoLogAta |Nome do caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde será criado o ficheiro de registo partilhado para esta réplica. Normalmente, os serviços não devem utilizar esta definição. No entanto, se o SharedLogPath for especificado, então o SharedLogId também deve ser especificado. |

## <a name="sample-configuration-file"></a>Ficheiro de configuração de exemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
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

## <a name="remarks"></a>Observações
O parâmetro BatchAckAckNowledgementInterval controla a latência da replicação. Um valor de '0' resulta na menor latência possível, à custa da produção (uma vez que mais mensagens de reconhecimento devem ser enviadas e processadas, cada uma contendo menos reconhecimentos).
Quanto maior for o valor para BatchAckAcknowledgementInterval, maior é a produção de replicação global, à custa de uma maior latência de operação. Isto traduz-se diretamente na latência dos compromissos de transações.

O parâmetro CheckpointThresholdInMB controla a quantidade de espaço em disco que o replicador pode usar para armazenar informações do estado no ficheiro de registo dedicado da réplica. Aumentar isto para um valor mais elevado do que o padrão pode resultar em tempos de reconfiguração mais rápidos quando uma nova réplica é adicionada ao conjunto. Isto deve-se à transferência parcial do Estado que ocorre devido à disponibilidade de mais histórico de operações no diário de bordo. Isto pode potencialmente aumentar o tempo de recuperação de uma réplica após um acidente.

Se definir o OptimizeForLowerDiskUsage para ser verdadeiro, o espaço do ficheiro de registo será sobre-a provisionado para que as réplicas ativas possam armazenar mais informações do estado nos seus ficheiros de registo, enquanto as réplicas inativas utilizarão menos espaço em disco. Isto permite hospedar mais réplicas num nó. Se definir o OptimizeForLowerDiskUsage em falso, as informações do estado são escritas para os ficheiros de registo mais rapidamente.

A definição MaxRecordSizeInKB define o tamanho máximo de um registo que pode ser escrito pelo replicador no ficheiro de registo. Na maioria dos casos, o tamanho de registo padrão 1024-KB é o ideal. No entanto, se o serviço está a fazer com que os itens de dados maiores sejam parte da informação do Estado, então este valor poderá ter de ser aumentado. Há poucos benefícios em fazer MaxRecordSizeInKB menor que 1024, uma vez que os registos mais pequenos usam apenas o espaço necessário para o registo menor. Esperamos que este valor só tenha de ser alterado em casos raros.

As definições SharedLogId e SharedLogPath são sempre usadas em conjunto para fazer com que um serviço utilize um registo partilhado separado do registo partilhado padrão para o nó. Para melhor eficiência, o maior número possível de serviços deve especificar o mesmo registo partilhado. Os ficheiros de registo partilhados devem ser colocados em discos utilizados exclusivamente para o ficheiro de registo partilhado, para reduzir a contenção do movimento da cabeça. Esperamos que estes valores só precisem de ser alterados em casos raros.

