---
title: Alterar definições de KVSActorStateProvider
description: Saiba mais sobre a configuração de atores imponentes do Azure Service Fabric do tipo KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609779"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configurar atores fiáveis -- KVSActorStateProvider
Pode modificar a configuração padrão do KVSActorStateProvider alterando o ficheiro settings.xml gerado na raiz do pacote Microsoft Visual Studio sob a pasta Config para o ator especificado.

O tempo de execução do Tecido de Serviço Azure procura nomes de secção predefinidos no ficheiro settings.xml e consome os valores de configuração enquanto cria os componentes de tempo de execução subjacentes.

> [!NOTE]
> **Não** elimine nem modifique os nomes de secção das seguintes configurações no ficheiro settings.xml gerado na solução Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador são usadas para proteger o canal de comunicação que é utilizado durante a replicação. Isto significa que os serviços não podem ver o tráfego de replicação uns dos outros, garantindo que os dados que são disponibilizados altamente disponíveis também são seguros.
Por predefinição, uma secção de configuração de segurança vazia impede a segurança da replicação.

> [!IMPORTANT]
> Nos nós Linux, os certificados devem ser formatados por PEM. Para saber mais sobre a localização e configuração de certificados para Linux, consulte [os certificados de configuração no Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nome da secção
&lt;ActorName &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
As configurações do replicador configuram o replicador que é responsável por tornar o Estado do Ator State Provider altamente fiável.
A configuração padrão é gerada pelo modelo de Estúdio Visual e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para sintonizar o replicador.

### <a name="section-name"></a>Nome da secção
&lt;ActorName &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração
| Name | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAckAckNownowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicador no secundário aguarda depois de receber uma operação antes de enviar de volta um aviso para o primário. Quaisquer outros avisos a serem enviados para operações processadas dentro deste intervalo são enviados como uma resposta. |
| Ponto de réplica |N/D |Sem parâmetro padrão-necessário |Endereço IP e porta que o replicador primário/secundário utilizará para comunicar com outros replicadores no conjunto de réplicas. Isto deve fazer referência a um ponto final de recurso TCP no manifesto de serviço. Consulte os [recursos manifestos do Serviço](service-fabric-service-manifest-resources.md) para ler mais sobre a definição de recursos de ponto final no manifesto de serviço. |
| RetryInterval |Segundos |5 |Período de tempo após o qual o replicador transmite uma mensagem se não receber um aviso para uma operação. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamanho máximo de dados de replicação que podem ser transmitidos numa única mensagem. |
| MaxPrimaryReplicationQueueSize |Número de operações |1024 |Número máximo de operações na fila primária. Uma operação é libertada após o replicador primário receber um reconhecimento de todos os replicadores secundários. Este valor deve ser superior a 64 e uma potência de 2. |
| MaxSecondaryReplicationSize |Número de operações |2048 |Número máximo de operações na fila secundária. Uma operação é libertada depois de tornar o seu estado altamente disponível através da persistência. Este valor deve ser superior a 64 e uma potência de 2. |

## <a name="store-configuration"></a>Configuração da loja
As configurações da loja são usadas para configurar a loja local que é usada para persistir o estado que está a ser replicado.
A configuração padrão é gerada pelo modelo de Estúdio Visual e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para sintonizar a loja local.

### <a name="section-name"></a>Nome da secção
&lt;Serviço &gt; actorNameLocalStoreConfig

### <a name="configuration-names"></a>Nomes de configuração
| Name | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milissegundos |200 |Define o intervalo máximo de loteamento para a loja local durável. |
| MaxVerPages |Número de páginas |16384 |O número máximo de páginas de versão na base de dados da loja local. Determina o número máximo de transações pendentes. |

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
O parâmetro BatchAckAckNowledgementInterval controla a latência da replicação. Um valor de '0' resulta na menor latência possível, à custa da produção (uma vez que mais mensagens de reconhecimento devem ser enviadas e processadas, cada uma contendo menos reconhecimentos).
Quanto maior for o valor para BatchAckAcknowledgementInterval, maior é a produção de replicação global, à custa de uma maior latência de operação. Isto traduz-se diretamente na latência dos compromissos de transações.

