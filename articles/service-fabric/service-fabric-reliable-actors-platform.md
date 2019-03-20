---
title: Reliable Actors no Service Fabric | Documentos da Microsoft
description: Descreve como os Reliable Actors são colocados em camadas no Reliable Services e utilizar as funcionalidades da plataforma do Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: f6594ccaa4d11361c3aacb8f774327600d3ffdde
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889255"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Como os Reliable Actors utilizam a plataforma Service Fabric
Este artigo explica como funcionam os Reliable Actors na plataforma do Azure Service Fabric. Executar numa estrutura que está hospedada numa implementação de um serviço fiável com monitoração de estado de Reliable Actors chamado os *serviço de ator*. O serviço de ator contém todos os componentes necessários para gerir o ciclo de vida e a mensagem de expedição para seus atores:

* O Runtime de Ator gerencia o ciclo de vida, coleta de lixo e impõe o acesso de thread único.
* Uma escuta de comunicação remota do serviço de ator aceita chamadas de acesso remoto para atores e envia-os para um dispatcher para encaminhar para a instância apropriada de ator.
* O fornecedor de estado do Ator encapsula os fornecedores de estado (por exemplo, o provedor de estado de Reliable Collections) e fornece um adaptador para o gerenciamento de estado do ator.

Esses componentes juntos formam a estrutura de Reliable Actor.

## <a name="service-layering"></a>Disposição em camadas de serviço
Uma vez que o próprio serviço de ator é um serviço fiável, todos os o [modelo de aplicativo](service-fabric-application-model.md), ciclo de vida, [empacotamento](service-fabric-package-apps.md), [implementação](service-fabric-deploy-remove-applications.md), atualizar e dimensionar os conceitos de fiáveis Serviços aplicam-se da mesma forma para serviços de atores.

![Disposição em camadas de serviço de ator][1]

O diagrama anterior mostra a relação entre as estruturas de aplicações do Service Fabric e o código do usuário. Elementos azuis representam a estrutura da aplicação de Reliable Services, laranja representa a estrutura de Reliable Actor e verde representa o código do usuário.

No Reliable Services, o seu serviço herda o `StatefulService` classe. Esta classe é derivada de `StatefulServiceBase` (ou `StatelessService` para serviços sem estado). Reliable Actors, vai utilizar o serviço de ator. O serviço de ator é uma implementação diferente a `StatefulServiceBase` classe que implementa o padrão de ator onde executar seus atores. Uma vez que o serviço de ator em si é apenas uma implementação de `StatefulServiceBase`, pode escrever seu próprio serviço, que deriva `ActorService` e implementar recursos de nível de serviço da mesma forma que faria ao herdar `StatefulService`, tais como:

* Serviço backup e restauração.
* Partilhado funcionalidade para todos os atores, por exemplo, um disjuntor automático.
* Chamadas de procedimento remoto no próprio serviço de ator e em cada ator individual.

Para obter mais informações, consulte [implementação de recursos de nível de serviço no seu serviço de ator](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Modelo de aplicação
Serviços de atores são Reliable Services, para que o modelo de aplicativo é o mesmo. No entanto, as ferramentas de compilação da estrutura de ator geram alguns dos arquivos de modelo do aplicativo para.

### <a name="service-manifest"></a>Manifesto do serviço
As ferramentas de compilação da estrutura de ator geram automaticamente o conteúdo do arquivo de servicemanifest. XML do seu serviço de ator. Este ficheiro inclui:

* Tipo de serviço de ator. O nome do tipo é gerado com base no nome do projeto de seu ator. Com base no atributo de persistência no seu ator, o sinalizador de HasPersistedState também é definido em conformidade.
* Pacote do código.
* Pacote de configuração.
* Recursos e os pontos finais.

### <a name="application-manifest"></a>Manifesto de aplicação
As ferramentas de compilação da estrutura de ator criam automaticamente uma definição de serviço padrão para o seu serviço de ator. As ferramentas de compilação preencher as propriedades do serviço predefinido:

* Contagem de conjunto de réplicas é determinada pelo atributo persistência no seu ator. Sempre que o atributo de persistência no seu ator é alterado, a contagem de conjunto de réplica na definição do serviço predefinido é reposta em conformidade.
* Esquema de partição e o intervalo são definidas como Int64 uniforme com o intervalo completo da chave de Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Conceitos de partição de recursos de infraestrutura do serviço de atores
Serviços de atores são particionados de serviços com estado. Cada partição de um serviço de ator contém um conjunto de atores. As partições do serviço são automaticamente distribuídas por vários nós do Service Fabric. Instâncias de ator são distribuídas como resultado.

![Criação de partições de ator e distribuição][5]

Reliable Services podem ser criado com intervalos de chave de partição e esquemas de partição diferentes. O serviço de ator usa o esquema de particionamento de Int64 com o intervalo de chave de Int64 completo para mapear actors para partições.

### <a name="actor-id"></a>ID de ator
Cada ator que é criado no serviço tem um ID exclusivo associado ao mesmo, representado pelo `ActorId` classe. `ActorId` é um valor de ID opaco, que pode ser utilizado para a distribuição uniforme de atores entre as partições de serviço por meio da geração aleatórias IDs:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Cada `ActorId` é protegido por hash Int64. É por isso o serviço de ator tem de utilizar um esquema de particionamento de Int64 vasta gama de chave de Int64. No entanto, os valores de ID personalizados podem ser utilizados para um `ActorID`, incluindo GUIDs/UUIDs não, as cadeias de caracteres e Int64s.

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

Quando estiver usando GUIDs/UUIDs não e cadeias de caracteres, os valores são codificados para Int64. No entanto, quando estiver explicitamente fornecendo Int64 para um `ActorId`, o Int64 mapeará diretamente para uma partição sem hash ainda mais. Pode usar essa técnica para controlar qual partição são colocados os atores em.


## <a name="next-steps"></a>Passos Seguintes
* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Coleção de ciclo de vida e libertação da memória de ator](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API de atores](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [Código de exemplo do .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
