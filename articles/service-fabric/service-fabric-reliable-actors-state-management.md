---
title: Gestão estatal de atores confiáveis
description: Descreve como o estado de Reliable Actors é gerido, persistido e replicado para alta disponibilidade.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: badfc490f26b71881e7970c2c0be3472abfec25a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575606"
---
# <a name="reliable-actors-state-management"></a>Gestão estatal de atores confiáveis
Os atores fiáveis são objetos de linha única que podem encapsular a lógica e o estado. Como os atores funcionam em Serviços Fiáveis, podem manter o Estado de forma fiável usando os mesmos mecanismos de persistência e replicação. Desta forma, os atores não perdem o seu estado após falhas, após a reativação após a recolha de lixo, ou quando são movidos entre nós num cluster devido ao equilíbrio de recursos ou atualizações.

## <a name="state-persistence-and-replication"></a>Persistência e replicação do Estado
Todos os Atores Fidedigdos são *considerados imponentes* porque cada exemplo de ator mapeia para uma identificação única. Isto significa que as chamadas repetidas para o mesmo ator ID são encaminhada para o mesmo caso de ator. Num sistema apátrida, pelo contrário, as chamadas dos clientes não são garantidas para serem encaminhadas para o mesmo servidor todas as vezes. Por esta razão, os serviços de ator são sempre serviços estatais.

Mesmo que os atores sejam considerados imponentes, isso não significa que devem armazenar o Estado de forma fiável. Os atores podem escolher o nível de persistência e replicação do Estado com base nos seus requisitos de armazenamento de dados:

* **Estado persistente**: O estado é persistido no disco e é replicado a três ou mais réplicas. O estado persistido é a opção de armazenamento estatal mais durável, onde o estado pode persistir através de uma completa interrupção do cluster.
* **Estado volátil**: O estado é replicado a três ou mais réplicas e apenas guardado na memória. O estado volátil proporciona resiliência contra a falha do nó e a falha do ator, e durante as atualizações e o equilíbrio de recursos. No entanto, o estado não persiste no disco. Então, se todas as réplicas são perdidas de uma só vez, o estado também está perdido.
* **Sem estado persistente**: O Estado não é replicado ou escrito em disco, apenas uso para atores que não precisam de manter o estado de forma fiável.

Cada nível de persistência é simplesmente um *fornecedor estatal* diferente e uma configuração de *replicação* do seu serviço. Se o estado é ou não escrito para o disco depende do fornecedor estatal -- o componente num serviço confiável que armazena o estado. A replicação depende de quantas réplicas um serviço é implantado. Tal como acontece com os Serviços Fiáveis, tanto o fornecedor do estado como a contagem de réplicas podem ser facilmente definidos manualmente. A estrutura do ator fornece um atributo que, quando usado num ator, seleciona automaticamente um fornecedor estatal predefinido e gera automaticamente configurações para a contagem de réplicas para alcançar uma destas três definições de persistência. O atributo StatePersistence não é herdado por classe derivada, cada tipo de ator deve fornecer o seu nível de StatePersistence.

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
Esta definição utiliza um fornecedor estatal que armazena dados em disco e define automaticamente a contagem de réplicas de serviço para 3.

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
Esta definição utiliza um fornecedor estatal só de memória e define a contagem de réplicas para 3.

### <a name="no-persisted-state"></a>Nenhum estado persistido
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
Esta definição utiliza um fornecedor estatal só de memória e define a contagem de réplicas para 1.

### <a name="defaults-and-generated-settings"></a>Predefinições e configurações geradas
Quando estiver a utilizar o `StatePersistence` atributo, um fornecedor estatal é automaticamente selecionado para si no momento de funcionação quando o serviço do ator começa. A contagem de réplicas, no entanto, é definida em tempo de compilação pelo ator do Estúdio Visual constroem ferramentas. As ferramentas de construção geram automaticamente um *serviço predefinido* para o serviço de ator em ApplicationManifest.xml. Os parâmetros são criados para **o tamanho do conjunto de réplica min** e tamanho do conjunto de **réplicas do alvo.**

Pode alterar estes parâmetros manualmente. Mas cada vez que o `StatePersistence` atributo é alterado, os parâmetros são definidos para os valores de tamanho definidos de réplica padrão para o `StatePersistence` atributo selecionado, sobreprimulando quaisquer valores anteriores. Por outras palavras, os valores que definiu em ServiceManifest.xml *só* são ultrapassados no momento da construção quando altera o valor do `StatePersistence` atributo.

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

## <a name="state-manager"></a>Gestor estatal
Cada instância de ator tem o seu próprio gestor estatal: uma estrutura de dados semelhante a um dicionário que armazena de forma fiável os pares chave/valor. O gerente do Estado é um invólucro em torno de um fornecedor estatal. Pode usá-lo para armazenar dados independentemente da definição de persistência. Não oferece quaisquer garantias de que um serviço de ator em execução pode ser alterado de uma definição de estado volátil (apenas na memória) para uma definição de estado persistente através de uma atualização rolante, preservando os dados. No entanto, é possível alterar a contagem de réplicas para um serviço de execução.

As chaves do gerente do estado devem ser cordas. Os valores são genéricos e podem ser qualquer tipo, incluindo tipos personalizados. Os valores armazenados no gestor do estado devem ser serializáveis por poderem ser transmitidos através da rede a outros nós durante a replicação e podem ser escritos em disco, dependendo da definição de persistência do estado de um ator.

O gestor do estado expõe métodos de dicionário comuns para gerir o estado, semelhantes aos encontrados no Dicionário Fiável.

Por exemplo, gestão do estado do ator, leia [Access, save e remova o estado de Atores Fidedigtos](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Melhores práticas
Aqui estão algumas práticas sugeridas e dicas de resolução de problemas para gerir o seu estado de ator.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Faça o estado do ator o mais granular possível
Isto é fundamental para o desempenho e utilização de recursos da sua aplicação. Sempre que há qualquer escrita/atualização para o "estado nomeado" de um ator, todo o valor correspondente a esse "estado nomeado" é serializado e enviado através da rede para as réplicas secundárias.  Os secundários escrevem-no ao disco local e respondem à réplica primária. Quando as primárias recebem reconhecimentos de um quórum de réplicas secundárias, escreve o estado para o seu disco local. Por exemplo, suponha que o valor é uma classe que tem 20 membros e um tamanho de 1 MB. Mesmo que tenha modificado apenas um dos membros da classe que é de tamanho 1 KB, acaba por pagar o custo da serialização e da rede e escreves disco para o total de 1 MB. Da mesma forma, se o valor for uma coleção (como uma lista, matriz ou dicionário), você paga o custo da coleção completa mesmo que modifique um dos seus membros. A interface statemanager da classe ator é como um dicionário. Deve sempre modelar a estrutura de dados que representa o estado do ator em cima deste dicionário.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Gerir corretamente o ciclo de vida do ator
Deve ter uma política clara sobre a gestão do tamanho do estado em cada divisão de um serviço de ator. O seu serviço de ator deve ter um número fixo de atores e reutilizá-los o máximo possível. Se criar continuamente novos atores, deve eliminá-los assim que terminarem o seu trabalho. O ator framework armazena alguns metadados sobre cada ator que existe. Apagar todo o estado de um ator não remove metadados sobre aquele ator. Deve eliminar o ator (ver [apagar atores e o seu estado)](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)para remover toda a informação armazenada no sistema. Como verificação extra, deve consultar o serviço de atores (ver [atores enumerantes)](service-fabric-reliable-actors-enumerate.md)de vez em quando para garantir que o número de atores está dentro do alcance esperado.
 
Se alguma vez vir que o tamanho do ficheiro de base de dados de um Serviço de Ator está a aumentar para além do tamanho esperado, certifique-se de que está a seguir as diretrizes anteriores. Se estiver a seguir estas diretrizes e ainda estiver a ter problemas de tamanho de ficheiro de base de dados, deverá [abrir um bilhete de apoio](service-fabric-support.md) com a equipa de produtos para obter ajuda.

## <a name="next-steps"></a>Passos seguintes

O estado que está armazenado em Reliable Actors deve ser serializado antes de ser escrito em disco e replicado para alta disponibilidade. Saiba mais sobre [a serialização do tipo ator.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

Em seguida, saiba mais sobre [diagnósticos de ator e monitorização de desempenho.](service-fabric-reliable-actors-diagnostics.md)
