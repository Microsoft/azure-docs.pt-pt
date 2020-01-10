---
title: Gerenciamento de estado Reliable Actors
description: Descreve como Reliable Actors estado é gerenciado, persistido e replicado para alta disponibilidade.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9962d4333e458243670d1005ad2ccfbc0bb7c92a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75348907"
---
# <a name="reliable-actors-state-management"></a>Gerenciamento de estado Reliable Actors
Reliable Actors são objetos de thread único que podem encapsular a lógica e o estado. Como os atores são executados em Reliable Services, eles podem manter o estado de forma confiável usando os mesmos mecanismos de persistência e replicação. Dessa forma, os atores não perdem seu estado após falhas, após a reativação após a coleta de lixo, ou quando eles são movidos entre os nós em um cluster devido ao balanceamento de recursos ou às atualizações.

## <a name="state-persistence-and-replication"></a>Persistência de estado e replicação
Todos os Reliable Actors são considerados *monitorados* porque cada instância de ator é mapeada para uma ID exclusiva. Isso significa que chamadas repetidas para a mesma ID de ator são roteadas para a mesma instância de ator. Em um sistema sem estado, por outro lado, não há garantia de que as chamadas de cliente sejam roteadas para o mesmo servidor a cada vez. Por esse motivo, os serviços de ator são sempre serviços com estado.

Embora os atores sejam considerados com estado, isso não significa que eles devem armazenar o estado de forma confiável. Os atores podem escolher o nível de persistência de estado e replicação com base em seus requisitos de armazenamento de dados:

* **Estado persistente**: o estado é persistido no disco e é replicado para três ou mais réplicas. O estado persistente é a opção de armazenamento de estado mais durável, em que o estado pode persistir por meio de uma interrupção completa do cluster.
* **Estado volátil**: o estado é replicado para três ou mais réplicas e mantidos apenas na memória. O estado volátil fornece resiliência contra falha de nó e falha de ator e durante atualizações e balanceamento de recursos. No entanto, o estado não é persistido no disco. Portanto, se todas as réplicas forem perdidas ao mesmo tempo, o estado também será perdido.
* **Nenhum estado persistente**: o estado não é replicado ou gravado no disco, use apenas para os atores que não precisam manter o estado de forma confiável.

Cada nível de persistência é simplesmente um *provedor de estado* diferente e a configuração de *replicação* do seu serviço. Se o estado ou não for gravado no disco depende do provedor de estado – o componente em um serviço confiável que armazena o estado. A replicação depende de quantas réplicas um serviço é implantado. Assim como ocorre com Reliable Services, o provedor de estado e a contagem de réplica podem ser facilmente definidos manualmente. A estrutura de ator fornece um atributo que, quando usado em um ator, seleciona automaticamente um provedor de estado padrão e gera automaticamente as configurações de contagem de réplicas para atingir uma dessas três configurações de persistência. O atributo StatePersistence não é herdado pela classe derivada, cada tipo de ator deve fornecer seu nível de StatePersistence.

### <a name="persisted-state"></a>Estado persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Essa configuração usa um provedor de estado que armazena dados em disco e define automaticamente a contagem de réplicas de serviço como 3.

### <a name="volatile-state"></a>Estado volátil
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Essa configuração usa um provedor de estado somente na memória e define a contagem de réplicas como 3.

### <a name="no-persisted-state"></a>Nenhum estado persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Essa configuração usa um provedor de estado somente na memória e define a contagem de réplicas como 1.

### <a name="defaults-and-generated-settings"></a>Padrões e configurações geradas
Quando você estiver usando o atributo `StatePersistence`, um provedor de estado será selecionado automaticamente para você em tempo de execução quando o serviço de ator for iniciado. No entanto, a contagem de réplicas é definida no momento da compilação pelas ferramentas de compilação do ator do Visual Studio. As ferramentas de compilação geram automaticamente um *serviço padrão* para o serviço de ator no ApplicationManifest. xml. São criados parâmetros para o tamanho **mínimo do conjunto de réplicas** e o **tamanho do conjunto de réplicas de destino**.

Você pode alterar esses parâmetros manualmente. Mas sempre que o atributo `StatePersistence` for alterado, os parâmetros serão definidos como os valores de tamanho do conjunto de réplicas padrão para o atributo de `StatePersistence` selecionado, substituindo quaisquer valores anteriores. Em outras palavras, os valores definidos no manifest. XML são substituídos *apenas* no momento da compilação quando você altera o valor do atributo `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Gerenciador de estado
Cada instância de ator tem seu próprio Gerenciador de Estado: uma estrutura de dados semelhante a um dicionário que armazena de forma confiável pares de chave/valor. O Gerenciador de estado é um wrapper em um provedor de estado. Você pode usá-lo para armazenar dados, independentemente de qual configuração de persistência é usada. Ele não fornece nenhuma garantia de que um serviço de ator em execução pode ser alterado de uma configuração de estado volátil (na memória apenas) para uma configuração de estado persistente por meio de uma atualização sem interrupção, preservando os dados. No entanto, é possível alterar a contagem de réplicas para um serviço em execução.

As chaves do Gerenciador de estado devem ser cadeias de caracteres. Os valores são genéricos e podem ser qualquer tipo, incluindo tipos personalizados. Os valores armazenados no Gerenciador de estado devem ser serializáveis de contrato de dados porque podem ser transmitidos pela rede para outros nós durante a replicação e podem ser gravados em disco, dependendo da configuração de persistência de estado de um ator.

O Gerenciador de estado expõe métodos comuns de dicionário para o gerenciamento de estado, semelhante àqueles encontrados no dicionário confiável.

Para obter exemplos de gerenciamento de estado de ator, [acesso de leitura, salvamento e remoção de Reliable Actors estado](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Melhores práticas
Aqui estão algumas práticas sugeridas e dicas de solução de problemas para gerenciar seu estado de ator.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Tornar o estado do ator o mais granular possível
Isso é essencial para o desempenho e o uso de recursos do seu aplicativo. Sempre que houver qualquer gravação/atualização para o "estado nomeado" de um ator, o valor inteiro correspondente a esse "estado nomeado" será serializado e enviado pela rede para as réplicas secundárias.  Os secundários o gravam no disco local e respondem de volta à réplica primária. Quando o primário recebe confirmações de um quorum de réplicas secundárias, ele grava o estado em seu disco local. Por exemplo, suponha que o valor é uma classe que tem 20 membros e um tamanho de 1 MB. Mesmo que você tenha modificado apenas um dos membros da classe que tem tamanho de 1 KB, acabará pagando o custo da serialização e das gravações de rede e disco para os 1 MB completos. Da mesma forma, se o valor for uma coleção (como uma lista, matriz ou dicionário), você pagará o custo da coleção completa mesmo se modificar um dos membros. A interface StateManager da classe de ator é como um dicionário. Você deve sempre modelar a estrutura de dados representando o estado do ator na parte superior desse dicionário.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Gerenciar corretamente o ciclo de vida do ator
Você deve ter uma política clara sobre o gerenciamento do tamanho do estado em cada partição de um serviço de ator. Seu serviço de ator deve ter um número fixo de atores e reutilizá-los o máximo possível. Se você criar novos atores continuamente, deverá excluí-los depois que eles forem concluídos com seu trabalho. A estrutura de ator armazena alguns metadados sobre cada ator existente. Excluir todo o estado de um ator não remove metadados sobre esse ator. Você deve excluir o ator (consulte [excluindo atores e seu estado](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) para remover todas as informações sobre ele armazenadas no sistema. Como uma verificação adicional, você deve consultar o serviço de ator (Confira [enumerando atores](service-fabric-reliable-actors-enumerate.md)) uma vez em um momento para garantir que o número de atores esteja dentro do intervalo esperado.
 
Se você já viu que o tamanho do arquivo de banco de dados de um serviço de ator está aumentando além do tamanho esperado, certifique-se de que você está seguindo as diretrizes anteriores. Se você estiver seguindo essas diretrizes e ainda tiver problemas de tamanho de arquivo de banco de dados, deverá [abrir um tíquete de suporte](service-fabric-support.md) com a equipe de produto para obter ajuda.

## <a name="next-steps"></a>Passos seguintes

O estado armazenado em Reliable Actors deve ser serializado antes de ser gravado em disco e replicado para alta disponibilidade. Saiba mais sobre a [serialização de tipo de ator](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Em seguida, saiba mais sobre o [diagnóstico de ator e o monitoramento de desempenho](service-fabric-reliable-actors-diagnostics.md).
