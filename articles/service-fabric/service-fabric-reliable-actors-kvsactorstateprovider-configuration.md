---
title: Alterar as definições do KVSActorStateProvider
description: Saiba configurar atores de serviço azure fabric de tipo KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609779"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configurar atores fiáveis --KVSActorStateProvider
Pode modificar a configuração padrão do KVSActorStateProvider alterando o ficheiro definições.xml geradona na raiz do pacote do Microsoft Visual Studio sob a pasta Config para o ator especificado.

O tempo de execução do Tecido de Serviço Azure procura nomes de secção predefinidos no ficheiro definições.xml e consome os valores de configuração enquanto cria os componentes de tempo de execução subjacentes.

> [!NOTE]
> Não **not** elimine ou modifique os nomes da secção das seguintes configurações no ficheiro definições.xml que é gerado na solução Estúdio Visual.
> 
> 

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador são usadas para fixar o canal de comunicação que é utilizado durante a replicação. Isto significa que os serviços não podem ver o tráfego de replicação uns dos outros, garantindo que os dados que são altamente disponibilizados também são seguros.
Por predefinição, uma secção de configuração de segurança vazia impede a segurança da replicação.

> [!IMPORTANT]
> Nos nós linux, os certificados devem ser formatados em PEM. Para saber mais sobre a localização e configuração de certificados para o Linux, consulte [os certificados de Configuração no Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nome da secção
&lt;Serviço&gt;de Nome actorReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
As configurações do replicador configuram o replicador que é responsável por tornar o Estado fornecedor de ator altamente fiável.
A configuração predefinida é gerada pelo modelo do Estúdio Visual e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para afinar o replicador.

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| Intervalo de Reconhecimento de Lote |Segundos |0.015 |Período de tempo para o qual o replicador no secundário aguarda depois de receber uma operação antes de enviar de volta um reconhecimento para as primárias. Quaisquer outros agradecimentos a serem enviados para operações processadas dentro deste intervalo são enviados como uma resposta. |
| Ponto de fim de réplica |N/D |Sem parâmetro sem necessidade de incumprimento |Endereço IP e porta que o replicador primário/secundário utilizará para comunicar com outros replicadores no conjunto de réplicas. Isto deve referir um ponto final de recurso TCP no manifesto de serviço. Consulte os [recursos manifestos do Serviço](service-fabric-service-manifest-resources.md) para ler mais sobre a definição de recursos de ponto final no manifesto de serviço. |
| Intervalo de retry |Segundos |5 |Período de tempo após o qual o replicador retransmite uma mensagem se não receber um reconhecimento para uma operação. |
| Tamanho do Mensagem de Replicação Max |Bytes |50 MB |Tamanho máximo dos dados de replicação que podem ser transmitidos numa única mensagem. |
| MaxPrimaryReplicationTionQueueSize |Número de operações |1024 |Número máximo de operações na fila principal. Uma operação é libertada depois que o replicador primário recebe um reconhecimento de todos os replicadores secundários. Este valor deve ser superior a 64 e uma potência de 2. |
| MaxSecondreplicationTionQueueSize |Número de operações |2048 |Número máximo de operações na fila secundária. Uma operação é libertada depois de tornar o seu estado altamente disponível através da persistência. Este valor deve ser superior a 64 e uma potência de 2. |

## <a name="store-configuration"></a>Configuração da loja
As configurações da loja são usadas para configurar a loja local que é usada para persistir o estado que está a ser replicado.
A configuração predefinida é gerada pelo modelo do Estúdio Visual e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para afinar a loja local.

### <a name="section-name"></a>Nome da secção
&lt;Serviço&gt;de Nome actorLocalStoreConfig

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMillisegundos |Milissegundos |200 |Define o intervalo máximo de lote para os compromissos de loja local durável. |
| Páginas MaxVer |Número de páginas |16384 |O número máximo de páginas de versões na base de dados da loja local. Determina o número máximo de transações pendentes. |

## <a name="sample-configuration-file"></a>Ficheiro de configuração de exemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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

