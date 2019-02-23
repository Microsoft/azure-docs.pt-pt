---
title: Alterar as definições de KVSActorStateProvider em atores do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre a configuração de atores com monitoração de estado do Azure Service Fabric KVSActorStateProvider typu.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 0b7c6a356812e4acd39b5164cce279b5a18eb3d5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732760"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configuração dos Reliable Actors – KVSActorStateProvider
Pode modificar a configuração predefinida de KVSActorStateProvider alterando o arquivo Settings XML que é gerado na raiz do pacote do Microsoft Visual Studio sob a pasta de configuração para o ator especificado.

O tempo de execução do Azure Service Fabric procura os nomes predefinidos em Settings XML e consome os valores de configuração ao criar os componentes de tempo de execução subjacente.

> [!NOTE]
> Fazer **não** eliminar ou modificar os nomes de seção das seguintes configurações em Settings XML que é gerado na solução do Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Configuração de segurança do replicador
Configurações de segurança do replicador são utilizadas para proteger o canal de comunicação que é utilizado durante a replicação. Isso significa que os serviços não é possível ver uns dos outros tráfego de replicação, garantindo que os dados que esteja altamente disponíveis também são seguros.
Por predefinição, uma seção de configuração de segurança vazio impede que a segurança de replicação.

> [!IMPORTANT]
> Em nós do Linux, certificados tem de ser formatada PEM. Para saber mais sobre localizar e configurar os certificados para Linux, veja [configurar certificados no Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração do replicador
Configurações do replicador configurar o replicator, que é responsável por verificar o estado de fornecedor de estado do Ator altamente confiável.
A configuração predefinida é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre as configurações adicionais que estão disponíveis para otimizar o replicator.

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração
| Name | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Segundos |0.015 |Período de tempo para o qual o replicator nas esperas secundários depois de receber uma operação antes de enviar de volta uma confirmação para o principal. Qualquer as confirmações para serem enviados para operações processadas dentro deste intervalo são enviadas como uma resposta. |
| ReplicatorEndpoint |N/A |Sem predefinição - parâmetro necessário |Endereço IP e porta que o replicator primária/secundária irá utilizar para comunicar com outros replicators na réplica definidos. Isso deve fazer referência a um ponto de final de recurso TCP no manifesto do serviço. Consulte a [recursos do manifesto do serviço](service-fabric-service-manifest-resources.md) para ler mais sobre como definir os recursos de ponto final no manifesto do serviço. |
| RetryInterval |Segundos |5 |Período de tempo após o qual o replicator novamente transmite uma mensagem se não receber uma confirmação de uma operação. |
| MaxReplicationMessageSize |Bytes |50 MB |Tamanho máximo de dados de replicação que podem ser transmitidos numa única mensagem. |
| MaxPrimaryReplicationQueueSize |Número de operações |1024 |Número máximo de operações na fila principal. Uma operação for liberada depois do replicador primário recebe uma confirmação de todos os replicators secundários. Este valor tem de ser superior a 64 e uma potência de 2. |
| MaxSecondaryReplicationQueueSize |Número de operações |2048 |Número máximo de operações na fila secundária. Uma operação for liberada depois de fazer o seu estado de elevada disponibilidade através de persistência. Este valor tem de ser superior a 64 e uma potência de 2. |

## <a name="store-configuration"></a>Configuração de Store
Configurações de Store são utilizadas para configurar o arquivo local, que é utilizado para manter o estado que está a ser replicado.
A configuração predefinida é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre as configurações adicionais que estão disponíveis para otimizar o armazenamento local.

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nomes de configuração
| Name | Unidade | Valor predefinido | Observações |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milissegundos |200 |Definir o máximo de intervalo para consolidações de arquivo local durável de criação de batches. |
| MaxVerPages |Número de páginas |16384 |O número máximo de páginas de versão no local armazena a base de dados. Determina o número máximo de transações pendentes. |

## <a name="sample-configuration-file"></a>Ficheiro de configuração de exemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
O parâmetro BatchAcknowledgementInterval controla a latência de replicação. Um valor de '0' resulta em menor latência possível, ao custo de débito (como mais mensagens de confirmação tem de ser enviadas e processadas, cada um deles contendo as confirmações menos).
Quanto maior for o valor para BatchAcknowledgementInterval, maior será a replicação produtividade geral, ao custo de latência superior de operação. Isto traduz-se diretamente para a latência de consolidações de transações.

