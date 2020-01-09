---
title: Alterar configurações de ReliableDictionaryActorStateProvider
description: Saiba mais sobre como configurar atores Service Fabric com estado do Azure do tipo ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609745"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configurando Reliable Actors--ReliableDictionaryActorStateProvider
Você pode modificar a configuração padrão de ReliableDictionaryActorStateProvider alterando o arquivo Settings. XML gerado na raiz do pacote do Visual Studio na pasta config para o ator especificado.

O tempo de execução do Azure Service Fabric procura nomes de seção predefinidos no arquivo Settings. xml e consome os valores de configuração ao criar os componentes de tempo de execução subjacentes.

> [!NOTE]
> **Não** exclua nem modifique os nomes de seção das configurações a seguir no arquivo Settings. XML que é gerado na solução do Visual Studio.
> 
> 

Também há configurações globais que afetam a configuração de ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuração global
A configuração global é especificada no manifesto do cluster para o cluster na seção KtlLogger. Ele permite a configuração do local e do tamanho do log compartilhado, além dos limites de memória global usados pelo agente. Observe que as alterações no manifesto do cluster afetam todos os serviços que usam ReliableDictionaryActorStateProvider e serviços confiáveis com estado.

O manifesto do cluster é um único arquivo XML que mantém configurações e configurações que se aplicam a todos os nós e serviços no cluster. O arquivo é normalmente chamado de ClusterManifest. xml. Você pode ver o manifesto do cluster para o cluster usando o comando Get-ServiceFabricClusterManifest do PowerShell.

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Quilobytes |8388608 |Número mínimo de KB para alocar no modo kernel para o pool de memória do buffer de gravação do agente. Esse pool de memória é usado para armazenar em cache informações de estado antes da gravação no disco. |
| WriteBufferMemoryPoolMaximumInKB |Quilobytes |Sem Limite |O tamanho máximo para o qual o pool de memória do buffer de gravação do agente pode crescer. |
| SharedLogId |GUID |"" |Especifica um GUID exclusivo a ser usado para identificar o arquivo de log compartilhado padrão usado por todos os Reliable Services em todos os nós no cluster que não especificam o SharedLogId em sua configuração específica de serviço. Se SharedLogId for especificado, SharedLogPath também deverá ser especificado. |
| SharedLogPath |Nome do caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado em que o arquivo de log compartilhado usado por todos os serviços confiáveis em todos os nós no cluster que não especificam o SharedLogPath em sua configuração específica de serviço. No entanto, se SharedLogPath for especificado, SharedLogId também deverá ser especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB de espaço em disco para alocar estaticamente para o log compartilhado. O valor deve ser 2048 ou maior. |

### <a name="sample-cluster-manifest-section"></a>Seção de manifesto de cluster de exemplo
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
O agente de log tem um pool global de memória alocado de memória de kernel não paginável que está disponível para todos os Reliable Services em um nó para armazenar em cache os dados de estado antes de serem gravados no log dedicado associado à réplica de serviço confiável. O tamanho do pool é controlado pelas configurações de WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB especifica o tamanho inicial desse pool de memória e o menor tamanho para o qual o pool de memória pode ser reduzido. WriteBufferMemoryPoolMaximumInKB é o tamanho mais alto para o qual o pool de memória pode crescer. Cada réplica de serviço confiável que é aberta pode aumentar o tamanho do pool de memória por um sistema determinada quantidade de até WriteBufferMemoryPoolMaximumInKB. Se houver mais demanda de memória do pool de memória do que a disponível, as solicitações de memória serão atrasadas até que a memória esteja disponível. Portanto, se o pool de memória do buffer de gravação for muito pequeno para uma configuração específica, o desempenho poderá ser afetado.

As configurações de SharedLogId e SharedLogPath são sempre usadas juntas para definir o GUID e o local do log compartilhado padrão para todos os nós no cluster. O log compartilhado padrão é usado para todos os Reliable Services que não especificam as configurações no Settings. xml para o serviço específico. Para obter um melhor desempenho, os arquivos de log compartilhados devem ser colocados em discos que são usados somente para o arquivo de log compartilhado para reduzir a contenção.

SharedLogSizeInMB especifica a quantidade de espaço em disco a ser prealocada para o log compartilhado padrão em todos os nós.  SharedLogId e SharedLogPath não precisam ser especificados para que o SharedLogSizeInMB seja especificado.

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador são usadas para proteger o canal de comunicação usado durante a replicação. Isso significa que os serviços não podem ver o tráfego de replicação uns dos outros, garantindo que os dados que se tornam altamente disponíveis também sejam seguros.
Por padrão, uma seção de configuração de segurança vazia impede a segurança da replicação.

> [!IMPORTANT]
> Em nós do Linux, os certificados devem ser formatados por PEM. Para saber mais sobre como localizar e configurar certificados para o Linux, consulte [configurar certificados no Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
As configurações do replicador são usadas para configurar o replicador que é responsável por tornar o estado do provedor de estado do ator altamente confiável ao replicar e persistir o estado localmente.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o replicador.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicador no secundário aguarda depois de receber uma operação antes de enviar de volta uma confirmação para a primária. Quaisquer outras confirmações a serem enviadas para operações processadas nesse intervalo são enviadas como uma resposta. |
| ReplicatorEndpoint |N/A |Nenhum padrão--parâmetro obrigatório |Endereço IP e porta que o replicador primário/secundário usará para se comunicar com outros replicadores no conjunto de réplicas. Isso deve fazer referência a um ponto de extremidade de recurso TCP no manifesto do serviço. Consulte [recursos de manifesto do serviço](service-fabric-service-manifest-resources.md) para ler mais sobre como definir recursos de ponto de extremidade no manifesto do serviço. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamanho máximo dos dados de replicação que podem ser transmitidos em uma única mensagem. |
| MaxPrimaryReplicationQueueSize |Número de operações |8192 |Número máximo de operações na fila primária. Uma operação é liberada depois que o replicador primário recebe uma confirmação de todos os replicadores secundários. Esse valor deve ser maior que 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é liberada depois de tornar seu estado altamente disponível por meio de persistência. Esse valor deve ser maior que 64 e uma potência de 2. |
| CheckpointThresholdInMB |MB |200 |Quantidade de espaço do arquivo de log após o qual o estado é marcado. |
| MaxRecordSizeInKB |KB |1024 |O maior tamanho de registro que o replicador pode gravar no log. Esse valor deve ser um múltiplo de 4 e maior que 16. |
| OptimizeLogForLowerDiskUsage |Booleano |true |Quando true, o log é configurado para que o arquivo de log dedicado da réplica seja criado usando um arquivo esparso NTFS. Isso reduz o uso real do espaço em disco para o arquivo. Quando false, o arquivo é criado com alocações fixas, que fornecem o melhor desempenho de gravação. |
| SharedLogId |guid |"" |Especifica um GUID exclusivo a ser usado para identificar o arquivo de log compartilhado usado com esta réplica. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogId for especificado, SharedLogPath também deverá ser especificado. |
| SharedLogPath |Nome do caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado em que o arquivo de log compartilhado para esta réplica será criado. Normalmente, os serviços não devem usar essa configuração. No entanto, se SharedLogPath for especificado, SharedLogId também deverá ser especificado. |

## <a name="sample-configuration-file"></a>Arquivo de configuração de exemplo
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
O parâmetro BatchAcknowledgementInterval controla a latência de replicação. Um valor de ' 0 ' resulta na menor latência possível, no custo da taxa de transferência (já que mais mensagens de confirmação devem ser enviadas e processadas, cada uma contendo menos confirmações).
Quanto maior o valor de BatchAcknowledgementInterval, maior será a taxa de transferência de replicação geral, com o custo de maior latência de operação. Isso se traduz diretamente na latência de confirmações de transação.

O parâmetro CheckpointThresholdInMB controla a quantidade de espaço em disco que o replicador pode usar para armazenar informações de estado no arquivo de log dedicado da réplica. Aumentar isso para um valor mais alto do que o padrão pode resultar em tempos de reconfiguração mais rápidos quando uma nova réplica é adicionada ao conjunto. Isso ocorre devido à transferência de estado parcial que ocorre devido à disponibilidade de mais histórico de operações no log. Isso pode aumentar potencialmente o tempo de recuperação de uma réplica após uma falha.

Se você definir OptimizeForLowerDiskUsage como true, o espaço do arquivo de log será excessivamente provisionado para que as réplicas ativas possam armazenar mais informações de estado em seus arquivos de log, enquanto as réplicas inativas usarão menos espaço em disco. Isso possibilita hospedar mais réplicas em um nó. Se você definir OptimizeForLowerDiskUsage como false, as informações de estado serão gravadas mais rapidamente nos arquivos de log.

A configuração MaxRecordSizeInKB define o tamanho máximo de um registro que pode ser gravado pelo replicador no arquivo de log. Na maioria dos casos, o tamanho de registro padrão de 1024-KB é ideal. No entanto, se o serviço estiver fazendo com que itens de dados maiores façam parte das informações de estado, esse valor poderá precisar ser aumentado. Há pouco benefício em tornar o MaxRecordSizeInKB menor que 1024, pois registros menores usam apenas o espaço necessário para o registro menor. Esperamos que esse valor precise ser alterado apenas em casos raros.

As configurações de SharedLogId e SharedLogPath são sempre usadas juntas para fazer com que um serviço use um log compartilhado separado do log compartilhado padrão para o nó. Para obter a melhor eficiência, o máximo de serviços possível deve especificar o mesmo log compartilhado. Os arquivos de log compartilhados devem ser colocados em discos que são usados exclusivamente para o arquivo de log compartilhado, para reduzir a contenção de movimento de cabeçalho. Esperamos que esses valores precisem ser alterados apenas em casos raros.

