---
title: Atores confiáveis no tecido de serviço
description: Descreve como os Atores Fiáveis são em camadas em Serviços Fiáveis e usam as características da plataforma Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 92c717fa2c82dd147acd3c28333e37ccf8dd2e89
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391878"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Como os atores confiáveis usam a plataforma De Tecido de Serviço
Este artigo explica como os Atores Fiáveis trabalham na plataforma Azure Service Fabric. Os Atores Fiáveis funcionam num quadro que é hospedado numa implementação de um serviço de confiança estatal chamado serviço de *ator.* O serviço do ator contém todos os componentes necessários para gerir o ciclo de vida e o envio de mensagens para os seus atores:

* O Ator Runtime gere o ciclo de vida, a recolha de lixo e impõe o acesso de um fio único.
* Um ator que requeira escuta saqueia chamadas de acesso remoto aos atores e envia-as para um despachante para a instância apropriada do ator.
* O Ator State Provider envolve fornecedores estatais (como o provedor estatal de Coleções Fiáveis) e fornece um adaptador para a gestão do estado do ator.

Estes componentes juntos formam a estrutura do Ator Fiável.

## <a name="service-layering"></a>Camadas de serviço
Como o próprio serviço de ator é um serviço fiável, todo o modelo de aplicação , ciclo de [vida,](service-fabric-application-model.md) [embalagem,](service-fabric-package-apps.md) [implantação,](service-fabric-deploy-remove-applications.md)upgrade e conceitos de escala de Serviços Fiáveis aplicam-se da mesma forma aos serviços de ator.

![Camadas de serviço do ator][1]

O diagrama anterior mostra a relação entre as estruturas de aplicação do Tecido de Serviço e o código do utilizador. Os elementos azuis representam o quadro de aplicação de Serviços Fiáveis, laranja representa a estrutura do Ator Fiável, e o verde representa o código do utilizador.

Em Serviços Fiáveis, o seu serviço herda a classe `StatefulService`. Esta classe é derivada de `StatefulServiceBase` (ou `StatelessService` para serviços apátridas). Em "Atores Confiáveis", usas o serviço de atores. O serviço de ator é uma implementação diferente da classe `StatefulServiceBase` que implementa o padrão de ator onde os seus atores correm. Como o próprio serviço de ator é apenas uma implementação de `StatefulServiceBase`, pode escrever o seu próprio serviço que deriva de `ActorService` e implementar funcionalidades de nível de serviço da mesma forma que quando herda `StatefulService`, tais como:

* Apoio de serviço e restauro.
* Funcionalidade partilhada para todos os atores, por exemplo, um disjuntor.
* O procedimento remoto apela ao próprio serviço do ator e a cada ator individual.

Para mais informações, consulte [a implementação de funcionalidades de nível de serviço no seu serviço](service-fabric-reliable-actors-using.md)de ator .

## <a name="application-model"></a>Modelo de aplicação
Os serviços de ator esporam serviços fiáveis, por isso o modelo de aplicação é o mesmo. No entanto, as ferramentas de construção de estruturas de ator geram alguns dos ficheiros do modelo de aplicação para si.

### <a name="service-manifest"></a>Manifesto de serviço
A estrutura do ator constrói ferramentas geram automaticamente o conteúdo do ficheiro ServiceManifest.xml do seu serviço de ator. Este ficheiro inclui:

* Tipo de serviço de ator. O nome do tipo é gerado com base no nome do projeto do seu ator. Com base no atributo de persistência no seu ator, a bandeira haspersistedState também está definida em conformidade.
* Pacote de código.
* Pacote Config.
* Recursos e pontos finais.

### <a name="application-manifest"></a>Manifesto de aplicação
A estrutura do ator constrói ferramentas automaticamente criam uma definição de serviço padrão para o seu serviço de ator. As ferramentas de construção povoam as propriedades do serviço predefinido:

* A contagem de réplicas é determinada pelo atributo de persistência no seu ator. Cada vez que a persistência atribui ao seu ator é alterada, a contagem de conjuntos de réplicas na definição de serviço padrão é redefinida em conformidade.
* O regime de partição e o alcance são definidos para O Uniforme Int64 com a gama de chaves Int64 completa.

## <a name="service-fabric-partition-concepts-for-actors"></a>Conceitos de partição de tecido de serviço para atores
Os serviços de ator são serviços estatais divididos. Cada partição de um serviço de ator contém um conjunto de atores. As divisórias de serviço são distribuídas automaticamente por vários nódosos no Tecido de Serviço. As instâncias dos atores são distribuídas como resultado.

![Partição e distribuição de ator][5]

Os Serviços Fiáveis podem ser criados com diferentes esquemas de partição e gamas-chave de partição. O serviço de ator usa o esquema de partição Int64 com toda a gama de chaves Int64 para mapear atores para divisórias.

### <a name="actor-id"></a>Identificação do ator
Cada ator que é criado no serviço tem uma identificação única associada a ele, representada pela classe `ActorId`. `ActorId` é um valor de identificação opaco que pode ser usado para distribuição uniforme de atores através das divisórias de serviço, gerando iDs aleatórios:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Cada `ActorId` é hashed a um Int64. É por isso que o serviço de ator deve usar um esquema de partição Int64 com toda a gama de chaves Int64. No entanto, os valores de ID personalizados podem ser usados para uma `ActorID`, incluindo GUIDs/UUIDs, cordas e Int64s.

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

Quando se está a usar GUIDs/UUIDs e cordas, os valores são hashed a um Int64. No entanto, quando estiver a fornecer explicitamente um Int64 a uma `ActorId`, o Int64 irá mapear diretamente para uma partição sem mais hashing. Pode usar esta técnica para controlar em que divisória os atores são colocados.


## <a name="next-steps"></a>Passos seguintes
* [Gestão do Estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e recolha de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos atores](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [Código de amostra .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código da amostra java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
