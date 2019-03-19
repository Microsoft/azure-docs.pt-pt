---
title: Gestão de estado de Reliable Actors | Documentos da Microsoft
description: Descreve como o estado de Reliable Actors é gerenciado, persistência e replicação para elevada disponibilidade.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 77fa369a3085a3d11d5bf03406b4ddb885a24009
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57847404"
---
# <a name="reliable-actors-state-management"></a>Gerenciamento de estado do Reliable Actors
Reliable Actors são objetos de thread único de mensagens em fila que podem encapsular a lógica e estado. Porque os atores são executados no Reliable Services, eles podem manter o estado da forma fiável com o mesmo persistência e mecanismos de replicação. Dessa forma, atores não perdem o seu estado após falhas, após a reativação após a coleta de lixo, ou quando são movidas em torno de entre nós num cluster devido ao balanceamento de recursos ou atualizações.

## <a name="state-persistence-and-replication"></a>Persistência de estado e replicação
Todos os Reliable Actors são considerados *com monitoração de estado* porque cada instância de ator é mapeado para um ID exclusivo. Isso significa que chamadas repetidas para o mesmo ID de ator são encaminhadas para a mesma instância de ator. Num sistema sem monitoração de estado, por outro lado, chamadas de cliente não são garantidas para ser encaminhado para o mesmo servidor cada vez. Por esse motivo, serviços de atores são sempre de serviços com estado.

Apesar dos atores são considerados com monitoração de estado, isso não significa que devem armazenar o estado de forma fiável. Actors podem escolher o nível de persistência do Estado e a replicação com base nos seus dados os requisitos de armazenamento:

* **Persistir estado**: Estado é persistente para disco e são replicados para três ou mais réplicas. Estado persistente é a opção de armazenamento de estado durável mais, em que estado pode prosseguir após falha completa do cluster.
* **Estado volátil**: Estado é replicado para três ou mais réplicas e apenas mantido na memória. Estado volátil fornece resiliência contra falhas de nó e de falha de ator e durante as atualizações e balanceamento de recurso. No entanto, o estado não é mantido no disco. Então, se todas as réplicas são perdidas ao mesmo tempo, o estado é perdido também.
* **Sem estado persistente**: Estado não é replicado ou escrito para o disco, utilize apenas para atores que não é necessário para manter o estado de forma fiável.

Cada nível da persistência é simplesmente um diferente *fornecedor de estado* e *replicação* configuração do seu serviço. Se pretende ou não o estado é escrito para disco depende do fornecedor de estado--o componente num serviço fiável que armazena o estado. Replicação depende de quantas réplicas de um serviço é implementado com. Como com o Reliable Services, o fornecedor de estado e a contagem de réplicas podem facilmente ser definidos manualmente. Actor framework fornece um atributo que, quando usado num ator, seleciona automaticamente um provedor de estado padrão e gera automaticamente as definições de contagem de réplicas para alcançar uma dessas três configurações de persistência. O atributo StatePersistence não sendo herdado por classe derivada, cada tipo de Ator tem de fornecer seu nível de StatePersistence.

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
Esta definição utiliza um fornecedor de estado que armazene dados no disco e define automaticamente a contagem de réplicas do serviço para 3.

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
Esta definição utiliza um fornecedor de estado na memória-só e define a contagem de réplica para 3.

### <a name="no-persisted-state"></a>Sem estado persistente
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
Esta definição utiliza um fornecedor de estado na memória-só e define a contagem de réplica para 1.

### <a name="defaults-and-generated-settings"></a>Padrões e configurações gerado
Quando estiver a utilizar o `StatePersistence` atributo, um fornecedor de estado é selecionado automaticamente para em tempo de execução quando inicia o serviço de ator. A contagem de réplicas, no entanto, é definida em tempo de compilação por ferramentas de compilação de ator do Visual Studio. As ferramentas de compilação geram automaticamente uma *serviço predefinido* para o serviço de ator em applicationmanifest. XML. Parâmetros são criados para **tamanho de conjunto de réplicas de min** e **tamanho de conjunto de réplicas de destino**.

Pode alterar manualmente esses parâmetros. Mas sempre que o `StatePersistence` atributo é alterado, os parâmetros são definidos para os valores de tamanho de conjunto de réplica predefinidos para selecionado `StatePersistence` atributo, substituindo quaisquer valores anteriores. Em outras palavras, os valores que definir no servicemanifest. XML são *apenas* substituída no momento da compilação quando altera o `StatePersistence` valor do atributo.

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

## <a name="state-manager"></a>Gestor de estado
Todas as instâncias de ator tem seu próprio Gerenciador de estado: pares chave/valor uma estrutura de dicionário de dados que armazena de forma fiável. O Gestor de estado é um wrapper em torno de um fornecedor de estado. Pode usá-lo para armazenar dados, independentemente do que a definição de persistência é utilizada. Ele não fornece quaisquer garantias de que um serviço de ator em execução pode ser alterado de uma definição de estado volátil, (na memória-só) para uma definição de estado persistente através de uma atualização sem interrupção, preservando os dados. No entanto, é possível alterar a contagem de réplicas para um serviço em execução.

Chaves de Gestor de estado tem de ser cadeias de caracteres. Valores são genéricos e pode ser qualquer tipo, incluindo tipos personalizados. Valores armazenados no Gestor de estado tem de ser serializável de contrato de dados porque eles podem ser transmitidos pela rede a outros nós durante a replicação e podem ser gravados em disco, consoante a definição de persistência do Estado de um ator.

O Gestor de estado expõe métodos de dicionário comuns para gerir o estado, semelhante àquelas encontradas no dicionário fiável.

Para obter exemplos de gerenciamento de estado de ator, leia [acesso, guardar e remover o estado de Reliable Actors](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Melhores práticas
Eis algumas sugestões práticas e resolução de problemas para gerir o seu estado de ator.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Verifique o estado do ator como granular possível
Isto é fundamental para o desempenho e utilização de recursos de seu aplicativo. Sempre que houver qualquer escrita/atualizar para o "Estado o nome" de um ator, o valor inteiro correspondente a esse Estado"com o nome" é serializado e enviado através da rede para as réplicas secundárias.  As bases de dados secundárias escrevê-lo para o disco local e de resposta de volta para a réplica primária. Quando o principal recebe as confirmações de um quórum de réplicas secundárias, escreve o estado do seu disco local. Por exemplo, suponha que o valor é uma classe que tem 20 membros e um tamanho de 1 MB. Mesmo que apenas modificado um dos membros de classe que é de tamanho de 1 KB, final pelo custo de serialização e a escritas de disco e rede de 1 MB completo. Da mesma forma, se o valor é uma coleção (por exemplo, uma lista, uma matriz ou um dicionário), paga o custo pela coleção completa, mesmo se modificar um dos membros do mesmo. A interface de StateManager da classe de ator é como um dicionário. Sempre deve modelar a estrutura de dados que representa o estado do ator sobre nesse dictionary.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Gerir corretamente o ciclo de vida do ator
Deve ter uma política clara sobre como gerir o tamanho do Estado de cada partição de um serviço de ator. O serviço de ator deve ter um número fixo de atores e reutilizá-los tanto quanto possível. Se criar continuamente novas actors, tem de as eliminar assim que elas são feitas com o seu trabalho. Actor framework armazena alguns metadados sobre cada ator que existe. A eliminar todos os Estados de um ator não remove os metadados sobre esse ator. Tem de eliminar o ator (consulte [eliminar atores e o respetivo estado](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) para remover todas as informações cerca ele armazenado no sistema. Como uma verificação adicional, deve consultar o serviço de ator (consulte [enumerar atores](service-fabric-reliable-actors-enumerate.md)) uma vez em quando para se certificar de que o número de atores está dentro do intervalo esperado.
 
Se já viu que o tamanho de ficheiro de base de dados de um serviço de Ator é aumentar o tamanho esperado, certifique-se de que está a seguir as diretrizes anteriores. Se estiver a seguir estas diretrizes e continuam a base de dados problemas de tamanho de arquivo, deve [abra um pedido de suporte](service-fabric-support.md) com a equipe de produto para obter ajuda.

## <a name="next-steps"></a>Passos Seguintes

Estado que é armazenado no Reliable Actors deve ser serializado antes de sua gravada em disco e replicados para elevada disponibilidade. Saiba mais sobre [serialização do tipo de Ator](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Em seguida, saiba mais sobre [monitorização de desempenho e diagnóstico de Ator](service-fabric-reliable-actors-diagnostics.md).
