---
title: Modelo de aplicação Azure Service Fabric
description: Como modelar e descrever aplicações e serviços no Azure Service Fabric utilizando ficheiros de manifesto de aplicação e serviço.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 84e6b2309fdb206771d4ea01aa03c7f355d6ff19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85963738"
---
# <a name="model-an-application-in-service-fabric"></a>Modelar uma aplicação em Tecido de Serviço
Este artigo fornece uma visão geral do modelo de aplicação Azure Service Fabric e como definir uma aplicação e serviço através de ficheiros manifestos.

## <a name="understand-the-application-model"></a>Compreender o modelo de aplicação
Uma aplicação é uma coleção de serviços constitutivo que desempenham uma determinada função ou funções. Um serviço executa uma função completa e autónoma e pode iniciar e funcionar independentemente de outros serviços.  Um serviço é composto por código, configuração e dados. Para cada serviço, o código consiste nos binários executáveis, a configuração consiste em configurações de serviço que podem ser carregadas no tempo de execução, e os dados consistem em dados estáticos arbitrários a serem consumidos pelo serviço. Cada componente deste modelo de aplicação hierárquica pode ser versão e atualizado de forma independente.

![O modelo de aplicação do Tecido de Serviço][appmodel-diagram]

Um tipo de aplicação é uma categorização de uma aplicação e consiste num conjunto de tipos de serviço. Um tipo de serviço é uma categorização de um serviço. A categorização pode ter configurações e configurações diferentes, mas a funcionalidade principal permanece a mesma. Os casos de um serviço são as diferentes variações de configuração de serviço do mesmo tipo de serviço.  

As classes (ou "tipos") de aplicações e serviços são descritas através de ficheiros XML (manifestos de aplicação e manifestos de serviço).  Os manifestos descrevem aplicações e serviços e são os modelos contra os quais as aplicações podem ser instantâneas a partir da loja de imagens do cluster.  Os manifestos são abordados em detalhe em [Aplicações e manifestos de serviço.](service-fabric-application-and-service-manifests.md) A definição de esquema para o ficheiro ServiceManifest.xml e ApplicationManifest.xml é instalada com o Service Fabric SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. O esquema XML está documentado na [documentação do esquema ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

O código para diferentes instâncias de aplicação funciona como processos separados mesmo quando hospedado pelo mesmo nó de Tecido de Serviço. Além disso, o ciclo de vida de cada instância de aplicação pode ser gerido (por exemplo, atualizado) de forma independente. O diagrama a seguir mostra como os tipos de aplicação são compostos por tipos de serviço, que por sua vez são compostos por pacotes de código, configuração e dados. Para simplificar o diagrama, apenas são apresentados os pacotes de código/config/dados `ServiceType4` para, embora cada tipo de serviço inclua alguns ou todos os tipos de pacotes.

![Tipos de aplicações e tipos de serviço de tecido de serviço][cluster-imagestore-apptypes]

Pode haver um ou mais casos de um tipo de serviço ativo no cluster. Por exemplo, casos de serviço estatal, ou réplicas, alcançam alta fiabilidade replicando o estado entre réplicas localizadas em diferentes nós no cluster. A replicação essencialmente proporciona redundância para o serviço estar disponível mesmo que um nó num cluster falhe. Um [serviço dividido](service-fabric-concepts-partitioning.md) divide ainda mais o seu estado (e padrões de acesso a esse estado) através de nós no cluster.

O diagrama seguinte mostra a relação entre aplicações e instâncias de serviço, divisórias e réplicas.

![Divisórias e réplicas dentro de um serviço][cluster-application-instances]

> [!TIP]
> Pode ver o layout das aplicações num cluster utilizando a ferramenta Service Fabric Explorer disponível http:// &lt; seu endereço de recluso &gt; :19080/Explorer. Para obter mais informações, consulte [visualizar o seu cluster com o Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md)
> 
> 


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
- Saiba mais sobre [o estado de](service-fabric-concepts-state.md)serviço, [partição](service-fabric-concepts-partitioning.md)e [disponibilidade.](service-fabric-availability-services.md)
- Leia sobre como as aplicações e serviços são definidos em [Candidaturas e manifestos de serviço.](service-fabric-application-and-service-manifests.md)
- [Os modelos de hospedagem](service-fabric-hosting-model.md) de aplicações descrevem a relação entre réplicas (ou instâncias) de um serviço implantado e processo de anfitrião de serviço.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


