---
title: Reliable Actors no Service Fabric
description: Descreve como os Reliable Actors são em camadas em Reliable Services e usam os recursos da plataforma Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 92c717fa2c82dd147acd3c28333e37ccf8dd2e89
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75349236"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Como os Reliable Actors utilizam a plataforma Service Fabric
Este artigo explica como Reliable Actors funcionam na plataforma de Service Fabric do Azure. Reliable Actors executado em uma estrutura hospedada em uma implementação de um serviço confiável com estado chamado *serviço de ator*. O serviço de ator contém todos os componentes necessários para gerenciar o ciclo de vida e a expedição de mensagens para seus atores:

* O tempo de execução do ator gerencia o ciclo de vida, a coleta de lixo e impõe o acesso de thread único.
* Um ouvinte de comunicação remota do serviço de ator aceita chamadas de acesso remoto para atores e as envia a um Dispatcher para rotear para a instância de ator apropriada.
* O provedor de estado do ator encapsula provedores de estado (como o provedor de estado de coleções confiáveis) e fornece um adaptador para o gerenciamento de estado do ator.

Esses componentes formam a estrutura de ator confiável.

## <a name="service-layering"></a>Camadas de serviço
Como o próprio serviço de ator é um serviço confiável, todos os conceitos de [modelo de aplicativo](service-fabric-application-model.md), ciclo de vida, [empacotamento](service-fabric-package-apps.md), [implantação](service-fabric-deploy-remove-applications.md), atualização e dimensionamento de Reliable Services se aplicam da mesma maneira aos serviços de ator.

![Disposição em camadas do serviço de ator][1]

O diagrama anterior mostra a relação entre as estruturas de aplicativo Service Fabric e o código do usuário. Os elementos azuis representam a estrutura do aplicativo Reliable Services, o laranja representa a estrutura de ator confiável e o verde representa o código do usuário.

No Reliable Services, seu serviço herda a classe `StatefulService`. Essa classe é derivada de `StatefulServiceBase` (ou `StatelessService` para serviços sem estado). No Reliable Actors, você usa o serviço de ator. O serviço de ator é uma implementação diferente da classe `StatefulServiceBase` que implementa o padrão de ator em que seus atores são executados. Como o próprio serviço de ator é apenas uma implementação de `StatefulServiceBase`, você pode escrever seu próprio serviço derivado de `ActorService` e implementar recursos de nível de serviço da mesma maneira que faria ao herdar `StatefulService`, como:

* Backup e restauração de serviço.
* Funcionalidade compartilhada para todos os atores, por exemplo, um disjuntor.
* Chamadas de procedimento remoto no próprio serviço de ator e em cada ator individual.

Para obter mais informações, consulte [implementando recursos de nível de serviço em seu serviço de ator](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Modelo de aplicação
Os serviços de ator são Reliable Services, portanto, o modelo de aplicativo é o mesmo. No entanto, as ferramentas de Build da estrutura de ator geram alguns dos arquivos de modelo de aplicativo para você.

### <a name="service-manifest"></a>Manifesto do serviço
As ferramentas de Build da estrutura de ator geram automaticamente o conteúdo do arquivo Service manifest. XML do serviço de ator. Esse arquivo inclui:

* Tipo de serviço de ator. O nome do tipo é gerado com base no nome do projeto do ator. Com base no atributo de persistência em seu ator, o sinalizador HasPersistedState também é definido de acordo.
* Pacote de códigos.
* Pacote de configuração.
* Recursos e pontos de extremidade.

### <a name="application-manifest"></a>Manifesto de aplicação
As ferramentas de Build da estrutura de ator criam automaticamente uma definição de serviço padrão para seu serviço de ator. As ferramentas de compilação preenchem as propriedades padrão do serviço:

* A contagem de conjuntos de réplicas é determinada pelo atributo de persistência em seu ator. Cada vez que o atributo de persistência em seu ator é alterado, a contagem de conjuntos de réplicas na definição de serviço padrão é redefinida de acordo.
* O esquema de partição e o intervalo são definidos como Int64 uniformes com o intervalo de chave Int64 completo.

## <a name="service-fabric-partition-concepts-for-actors"></a>Conceitos de partição de Service Fabric para atores
Os serviços de ator são serviços com estado particionados. Cada partição de um serviço de ator contém um conjunto de atores. As partições de serviço são distribuídas automaticamente em vários nós no Service Fabric. As instâncias de ator são distribuídas como resultado.

![Particionamento e distribuição de ator][5]

Reliable Services pode ser criado com esquemas de partição e intervalos de chave de partição diferentes. O serviço de ator usa o esquema de particionamento Int64 com o intervalo de chaves Int64 completo para mapear atores para partições.

### <a name="actor-id"></a>ID do ator
Cada ator criado no serviço tem uma ID exclusiva associada a ele, representado pela classe `ActorId`. `ActorId` é um valor de ID opaco que pode ser usado para distribuição uniforme de atores entre as partições de serviço gerando IDs aleatórias:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Cada `ActorId` é codificada em hash para um Int64. É por isso que o serviço de ator deve usar um esquema de particionamento Int64 com o intervalo de chave Int64 completo. No entanto, os valores de ID personalizados podem ser usados para um `ActorID`, incluindo GUIDs/UUIDs, cadeias de caracteres e Int64s.

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

Quando você está usando GUIDs/UUIDs e cadeias de caracteres, os valores são codificados em hash para um Int64. No entanto, quando você estiver fornecendo explicitamente um Int64 para um `ActorId`, o Int64 será mapeado diretamente para uma partição sem hash adicional. Você pode usar essa técnica para controlar em qual partição os atores são colocados.


## <a name="next-steps"></a>Passos seguintes
* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos atores](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [Código de exemplo do .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
