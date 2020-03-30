---
title: Alterar as definições reliableDictionaryActorStateProvider
description: Saiba configurar atores de tecido de serviço Azure do tipo ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609745"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configurar atores fiáveis--ReliableDictionaryActorStateProvider
Pode modificar a configuração padrão do ReliableDictionaryActorStateProvider alterando o ficheiro definições.xml geradona na raiz do pacote do Estúdio Visual sob a pasta Config para o ator especificado.

O tempo de execução do Tecido de Serviço Azure procura nomes de secção predefinidos no ficheiro definições.xml e consome os valores de configuração enquanto cria os componentes de tempo de execução subjacentes.

> [!NOTE]
> Não **not** elimine ou modifique os nomes da secção das seguintes configurações no ficheiro definições.xml que é gerado na solução Estúdio Visual.
> 
> 

Existem também configurações globais que afetam a configuração do ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuração Global
A configuração global é especificada no manifesto de cluster para o cluster sob a secção KtlLogger. Permite a configuração da localização e tamanho do registo partilhado mais os limites globais de memória utilizados pelo madeireiro. Note que as mudanças no manifesto de cluster afetam todos os serviços que utilizam O ReliableDictionaryActorStateProvider e serviços estatais confiáveis.

O manifesto do cluster é um único ficheiro XML que contém configurações e configurações que se aplicam a todos os nós e serviços do cluster. O ficheiro é tipicamente chamado ClusterManifest.xml. Pode ver o manifesto de cluster para o seu cluster utilizando o comando Get-ServiceFabricClusterManifest powershell.

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolminimumKB |Kilobytes |8388608 |Número mínimo de KB para alocar no modo kernel para o conjunto de memória tampão de escrita de login. Este conjunto de memória é usado para gravar informações do estado antes de escrever para o disco. |
| WriteBufferMemoryPoolMaximumInKB |Kilobytes |Sem Limite |Tamanho máximo ao qual o conjunto de memória tampão de gravação pode crescer. |
| SharedLogid |GUID |"" |Especifica um GUID único para identificar o ficheiro de registo partilhado por defeito utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogId na sua configuração específica do serviço. Se o SharedLogId for especificado, então o SharedLogPath também deve ser especificado. |
| SharedLogPath |Nome de caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde o ficheiro de registo partilhado utilizado por todos os serviços fiáveis em todos os nós do cluster que não especificam o SharedLogPath na sua configuração específica do serviço. No entanto, se o SharedLogPath for especificado, então o SharedLogId também deve ser especificado. |
| SharedLogSizeInMB |Megabytes |8192 |Especifica o número de MB de espaço em disco para alocar estáticamente para o registo partilhado. O valor deve ser 2048 ou maior. |

### <a name="sample-cluster-manifest-section"></a>Secção de manifesto de aglomerado de amostras
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
O madeireiro tem um conjunto global de memória alocada a partir de memória de kernel não páginada que está disponível para todos os serviços confiáveis em um nó para cortar dados do estado antes de ser escrito para o registo dedicado associado à réplica de serviço confiável. O tamanho da piscina é controlado pelas definições WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumKB especifica tanto o tamanho inicial desta piscina de memória como o tamanho mais baixo para o qual o conjunto de memória pode encolher. WriteBufferMemoryPoolMaximumInKB é o tamanho mais alto para o qual o conjunto de memória pode crescer. Cada réplica de serviço fiável que é aberta pode aumentar o tamanho do pool de memória por um sistema determinado até WriteBufferMemoryPoolMaximumInKB. Se houver mais procura de memória do conjunto de memórias do que o disponível, os pedidos de memória serão adiados até que a memória esteja disponível. Portanto, se o conjunto de memória tampão de escrita for demasiado pequeno para uma configuração específica, então o desempenho pode sofrer.

As definições SharedLogId e SharedLogPath são sempre utilizadas em conjunto para definir o GUID e a localização para o registo partilhado por defeito para todos os nós do cluster. O registo partilhado por defeito é utilizado para todos os serviços fiáveis que não especificam as definições nas definições.xml para o serviço específico. Para um melhor desempenho, os ficheiros de registo partilhados devem ser colocados em discos que são utilizados exclusivamente para o ficheiro de registo partilhado para reduzir a contenção.

O SharedLogSizeInMB especifica a quantidade de espaço do disco para pré-atribuir para o registo partilhado por defeito em todos os nós.  O SharedLogId e o SharedLogPath não precisam de ser especificados para que o SharedLogSizeInMB seja especificado.

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador são usadas para fixar o canal de comunicação que é utilizado durante a replicação. Isto significa que os serviços não podem ver o tráfego de replicação uns dos outros, garantindo que os dados que são altamente disponibilizados também são seguros.
Por predefinição, uma secção de configuração de segurança vazia impede a segurança da replicação.

> [!IMPORTANT]
> Nos nós linux, os certificados devem ser formatados em PEM. Para saber mais sobre a localização e configuração de certificados para o Linux, consulte [os certificados de Configuração no Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nome da secção
&lt;Serviço&gt;de Nome actorReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
As configurações de replicadores são usadas para configurar o replicador que é responsável por tornar o Estado fornecedor de ator altamente fiável replicando e persistindo o estado localmente.
A configuração predefinida é gerada pelo modelo do Estúdio Visual e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para afinar o replicador.

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| Intervalo de Reconhecimento de Lote |Segundos |0.015 |Período de tempo para o qual o replicador no secundário aguarda depois de receber uma operação antes de enviar de volta um reconhecimento para as primárias. Quaisquer outros agradecimentos a serem enviados para operações processadas dentro deste intervalo são enviados como uma resposta. |
| Ponto de fim de réplica |N/D |Sem parâmetro sem necessidade de incumprimento |Endereço IP e porta que o replicador primário/secundário utilizará para comunicar com outros replicadores no conjunto de réplicas. Isto deve referir um ponto final de recurso TCP no manifesto de serviço. Consulte os [recursos manifestos do Serviço](service-fabric-service-manifest-resources.md) para ler mais sobre a definição de recursos de ponto final no manifesto de serviço. |
| Tamanho do Mensagem de Replicação Max |Bytes |50 MB |Tamanho máximo dos dados de replicação que podem ser transmitidos numa única mensagem. |
| MaxPrimaryReplicationTionQueueSize |Número de operações |8192 |Número máximo de operações na fila principal. Uma operação é libertada depois que o replicador primário recebe um reconhecimento de todos os replicadores secundários. Este valor deve ser superior a 64 e uma potência de 2. |
| MaxSecondreplicationTionQueueSize |Número de operações |16384 |Número máximo de operações na fila secundária. Uma operação é libertada depois de tornar o seu estado altamente disponível através da persistência. Este valor deve ser superior a 64 e uma potência de 2. |
| CheckpointThresholdinMB |MB |200 |Quantidade de espaço de ficheiro de registo após o qual o estado está posto de controlo. |
| MaxRecordSizeInKB |KB |1024 |Maior tamanho de registo que o replicador pode escrever no registo. Este valor deve ser um múltiplo de 4 e superior a 16. |
| OptimizeLogForLowerDiskUsage |Booleano |true |Quando verdadeiro, o registo é configurado de modo a que o ficheiro de registo dedicado da réplica seja criado utilizando um ficheiro ntfs escasso. Isto reduz o uso real do espaço do disco para o ficheiro. Quando falso, o ficheiro é criado com alocações fixas, que proporcionam o melhor desempenho de escrita. |
| SharedLogid |guia |"" |Especifica um guia único a utilizar para identificar o ficheiro de registo partilhado utilizado com esta réplica. Normalmente, os serviços não devem utilizar esta configuração. No entanto, se o SharedLogId for especificado, então o SharedLogPath também deve ser especificado. |
| SharedLogPath |Nome de caminho totalmente qualificado |"" |Especifica o caminho totalmente qualificado onde será criado o ficheiro de registo partilhado para esta réplica. Normalmente, os serviços não devem utilizar esta configuração. No entanto, se o SharedLogPath for especificado, então o SharedLogId também deve ser especificado. |

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
O parâmetro BatchRecognisementInterval controla a latência da replicação. Um valor de '0' resulta na latência mais baixa possível, à custa da entrada (uma vez que mais mensagens de reconhecimento devem ser enviadas e processadas, cada uma contendo menos reconhecimentos).
Quanto maior for o valor para o BatchRecognisementInterval, maior é o rendimento global da replicação, à custa de uma maior latência de operação. Isto traduz-se diretamente na latência das transações.

O parâmetro CheckpointThresholdInMB controla a quantidade de espaço do disco que o replicador pode usar para armazenar informações estatais no ficheiro de registo dedicado da réplica. Aumentar isto para um valor mais elevado do que o padrão pode resultar em tempos de reconfiguração mais rápidos quando uma nova réplica é adicionada ao conjunto. Isto deve-se à transferência parcial do Estado que ocorre devido à disponibilidade de mais histórico de operações no registo. Isto pode potencialmente aumentar o tempo de recuperação de uma réplica após um acidente.

Se definir o OptimizeForLowerDiskUsage como verdadeiro, o espaço de ficheiros de registo será sobre-provisionado para que as réplicas ativas possam armazenar mais informações estatais nos seus ficheiros de registo, enquanto as réplicas inativas utilizarão menos espaço no disco. Isto permite alojar mais réplicas num nó. Se definir o OptimizeForLowerDiskUsage como falso, as informações do estado são escritas para os ficheiros de registo mais rapidamente.

A definição MaxRecordSizeInKB define o tamanho máximo de um disco que pode ser escrito pelo replicador no ficheiro de registo. Na maioria dos casos, o tamanho do recorde padrão de 1024-KB é ideal. No entanto, se o serviço está a causar que os itens de dados maiores façam parte da informação do Estado, então esse valor poderá ter de ser aumentado. Há pouco saem beneficiados em tornar maxRecordSizeInKB menor que 1024, uma vez que os registos mais pequenos usam apenas o espaço necessário para o disco mais pequeno. Esperamos que este valor só tenha de ser alterado em casos raros.

As definições SharedLogId e SharedLogPath são sempre utilizadas em conjunto para fazer com que um serviço utilize um registo partilhado separado do registo partilhado por defeito para o nó. Para uma melhor eficiência, o maior número possível de serviços deve especificar o mesmo registo partilhado. Os ficheiros de registo partilhados devem ser colocados em discos que são utilizados exclusivamente para o ficheiro de registo partilhado, para reduzir a contenção do movimento da cabeça. Esperamos que estes valores só sejam alterados em casos raros.

