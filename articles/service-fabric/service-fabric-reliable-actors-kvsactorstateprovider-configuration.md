---
title: Alterar configurações de KVSActorStateProvider
description: Saiba mais sobre como configurar atores Service Fabric com estado do Azure do tipo KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609779"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configurando Reliable Actors--KVSActorStateProvider
Você pode modificar a configuração padrão de KVSActorStateProvider alterando o arquivo Settings. XML que é gerado na raiz do pacote de Microsoft Visual Studio na pasta de configuração do ator especificado.

O tempo de execução do Azure Service Fabric procura nomes de seção predefinidos no arquivo Settings. xml e consome os valores de configuração ao criar os componentes de tempo de execução subjacentes.

> [!NOTE]
> **Não** exclua nem modifique os nomes de seção das configurações a seguir no arquivo Settings. XML que é gerado na solução do Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
As configurações de segurança do replicador são usadas para proteger o canal de comunicação usado durante a replicação. Isso significa que os serviços não podem ver o tráfego de replicação uns dos outros, garantindo que os dados que se tornam altamente disponíveis também sejam seguros.
Por padrão, uma seção de configuração de segurança vazia impede a segurança da replicação.

> [!IMPORTANT]
> Em nós do Linux, os certificados devem ser formatados por PEM. Para saber mais sobre como localizar e configurar certificados para o Linux, consulte [configurar certificados no Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
As configurações do replicador configuram o replicador que é responsável por tornar o estado do provedor de estado do ator altamente confiável.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o replicador.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicador no secundário aguarda depois de receber uma operação antes de enviar de volta uma confirmação para a primária. Quaisquer outras confirmações a serem enviadas para operações processadas nesse intervalo são enviadas como uma resposta. |
| ReplicatorEndpoint |N/A |Nenhum padrão--parâmetro obrigatório |Endereço IP e porta que o replicador primário/secundário usará para se comunicar com outros replicadores no conjunto de réplicas. Isso deve fazer referência a um ponto de extremidade de recurso TCP no manifesto do serviço. Consulte [recursos de manifesto do serviço](service-fabric-service-manifest-resources.md) para ler mais sobre como definir recursos de ponto de extremidade no manifesto do serviço. |
| RetryInterval |Segundos |5 |Período após o qual o replicador retransmite uma mensagem se não receber uma confirmação para uma operação. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamanho máximo dos dados de replicação que podem ser transmitidos em uma única mensagem. |
| MaxPrimaryReplicationQueueSize |Número de operações |1024 |Número máximo de operações na fila primária. Uma operação é liberada depois que o replicador primário recebe uma confirmação de todos os replicadores secundários. Esse valor deve ser maior que 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |2048 |Número máximo de operações na fila secundária. Uma operação é liberada depois de tornar seu estado altamente disponível por meio de persistência. Esse valor deve ser maior que 64 e uma potência de 2. |

## <a name="store-configuration"></a>Configuração do repositório
As configurações de armazenamento são usadas para configurar o repositório local que é usado para persistir o estado que está sendo replicado.
A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o armazenamento local.

### <a name="section-name"></a>Nome da seção
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nomes de configuração
| Nome | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milissegundos |200 |Define o intervalo máximo de envio em lote para confirmações de repositório local durável. |
| MaxVerPages |Número de páginas |16384 |O número máximo de páginas de versão no banco de dados de repositório local. Ele determina o número máximo de transações pendentes. |

## <a name="sample-configuration-file"></a>Arquivo de configuração de exemplo
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
O parâmetro BatchAcknowledgementInterval controla a latência de replicação. Um valor de ' 0 ' resulta na menor latência possível, no custo da taxa de transferência (já que mais mensagens de confirmação devem ser enviadas e processadas, cada uma contendo menos confirmações).
Quanto maior o valor de BatchAcknowledgementInterval, maior será a taxa de transferência de replicação geral, com o custo de maior latência de operação. Isso se traduz diretamente na latência de confirmações de transação.

