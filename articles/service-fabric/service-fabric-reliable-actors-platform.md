---
title: Atores confiáveis no tecido de serviço
description: Descreve como os Atores Fiáveis são em camadas em Serviços Fiáveis e utilizam as funcionalidades da plataforma De Tecido de Serviço.
ms.topic: conceptual
ms.date: 3/9/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e204b47b7fa9f528341507c315c926159d524e13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789587"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Como os atores fiáveis usam a plataforma de tecido de serviço
Este artigo explica como os Atores Fiáveis funcionam na plataforma Azure Service Fabric. Os Atores Fiáveis funcionam num quadro que é hospedado numa implementação de um serviço de confiança estatal chamado serviço de *ator.* O serviço de ator contém todos os componentes necessários para gerir o ciclo de vida e o envio de mensagens para os seus atores:

* O Ator Runtime gere o ciclo de vida, a recolha de lixo e impõe o acesso de linha única.
* Um ouvinte de serviço de ator aceita chamadas de acesso remoto a atores e envia-as para um despachante para encaminhar para a instância do ator apropriado.
* O Ator State Provider envolve fornecedores estatais (como o fornecedor estatal Reliable Collections) e fornece um adaptador para a gestão do estado do ator.

Estes componentes juntos formam a estrutura do Ator Fiável.

## <a name="service-layering"></a>Camada de serviço
Como o próprio serviço de ator é um serviço fiável, todo o modelo de [aplicação,](service-fabric-application-model.md)ciclo de vida, [embalagem,](service-fabric-package-apps.md) [implementação,](service-fabric-deploy-remove-applications.md)upgrade e conceitos de escala de Reliable Services aplicam-se da mesma forma aos serviços de ator.

![Camada de serviço do ator][1]

O diagrama anterior mostra a relação entre os quadros de aplicação do Tecido de Serviço e o código do utilizador. Os elementos azuis representam o quadro de aplicação dos Serviços Fiáveis, o laranja representa o quadro do Ator Fiável e o verde representa o código do utilizador.

Na Reliable Services, o seu serviço herda a `StatefulService` aula. Esta classe é derivada `StatefulServiceBase` (ou `StatelessService` para serviços apátridas). Em "Reliable Actors", usas o serviço de atores. O serviço de ator é uma implementação diferente da `StatefulServiceBase` classe que implementa o padrão de ator onde os seus atores correm. Como o próprio serviço de ator é apenas uma implementação `StatefulServiceBase` de, você pode escrever o seu próprio serviço que deriva e implementar características de `ActorService` nível de serviço da mesma forma que você faria ao herdar `StatefulService` , tais como:

* Assistência e restauro.
* Funcionalidade partilhada para todos os atores, por exemplo, um disjuntor.
* O procedimento remoto requer o próprio serviço do ator e de cada ator individual.

Para obter mais informações, consulte [funcionalidades de nível de serviço de implementação no seu serviço de ator.](service-fabric-reliable-actors-using.md)

## <a name="application-model"></a>Modelo de aplicação
Os serviços de ator são Serviços Fidedigtos, por isso o modelo de aplicação é o mesmo. No entanto, as ferramentas de construção de quadros de ator geram alguns dos ficheiros de modelos de aplicação para si.

### <a name="service-manifest"></a>Manifesto de serviço
A estrutura do ator constrói ferramentas geram automaticamente o conteúdo do ficheiro de ServiceManifest.xml do seu serviço de ator. Este ficheiro inclui:

* Tipo de serviço de ator. O nome do tipo é gerado com base no nome do projeto do seu ator. Com base no atributo de persistência no seu ator, a bandeira haspersistedState também é definida em conformidade.
* Pacote de código.
* Pacote Config.
* Recursos e pontos finais.

### <a name="application-manifest"></a>Manifesto de aplicação
A estrutura do ator constrói ferramentas automaticamente para criar uma definição de serviço padrão para o seu serviço de ator. As ferramentas de construção povoam as propriedades de serviço predefinidos:

* A contagem de conjuntos de réplicas é determinada pelo atributo de persistência no seu ator. Cada vez que o atributo de persistência no seu ator é alterado, a contagem de conjunto de réplica na definição de serviço padrão é reposta em conformidade.
* O esquema de partição e o alcance estão definidos para Uniform Int64 com toda a gama de chaves Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Conceitos de partição de tecido de serviço para atores
Os serviços de ator são serviços estatais divididos. Cada divisão de um serviço de ator contém um conjunto de atores. As divisórias de serviço são distribuídas automaticamente por vários nós no Tecido de Serviço. As instâncias dos atores são distribuídas como resultado.

![Partição e distribuição de atores][5]

Os Serviços Fiáveis podem ser criados com diferentes esquemas de partição e gamas-chave de partição. O serviço de ator usa o esquema de partição Int64 com toda a gama de chaves Int64 para mapear atores para divisórias.

### <a name="actor-id"></a>ID do ator
Cada ator que é criado no serviço tem uma identificação única associada a ele, representada pela `ActorId` classe. `ActorId` é um valor de ID opaco que pode ser usado para distribuição uniforme de atores através das divisórias de serviço, gerando IDs aleatórios:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Cada `ActorId` um é haxixe para um Int64. É por isso que o serviço de ator deve usar um esquema de partição Int64 com toda a gama de chaves Int64. No entanto, os valores de ID personalizados podem ser usados para `ActorID` um, incluindo GUIDs/UUIDs, cordas e Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Quando se usa GUIDs/UUIDs e cordas, os valores são hashed para um Int64. No entanto, quando você está fornecendo explicitamente um Int64 para um `ActorId` , o Int64 irá mapear diretamente para uma partição sem mais haxixe. Pode usar esta técnica para controlar em que partição os atores são colocados.


## <a name="next-steps"></a>Passos seguintes
* [Gestão do estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleção de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos atores](/dotnet/api/microsoft.servicefabric.actors)
* [.NET código de amostra](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de amostra de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
