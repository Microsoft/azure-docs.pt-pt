---
title: Modelo de aplicação Azure Service Fabric
description: Como modelar e descrever aplicações e serviços no Tecido de Serviço Azure utilizando ficheiros de manifesto de aplicação e serviço.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7179686b7d4ef2df267cb95ece8f83d5fb7682b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551884"
---
# <a name="model-an-application-in-service-fabric"></a>Modelo de uma aplicação em Tecido de Serviço
Este artigo fornece uma visão geral do modelo de aplicação Azure Service Fabric e como definir uma aplicação e serviço através de ficheiros manifestos.

## <a name="understand-the-application-model"></a>Compreender o modelo de aplicação
Uma aplicação é uma coleção de serviços constituintes que desempenham uma determinada função ou funções. Um serviço desempenha uma função completa e autónoma e pode iniciar e executar independentemente de outros serviços.  Um serviço é composto por código, configuração e dados. Para cada serviço, o código consiste nos binários executáveis, a configuração consiste em definições de serviço que podem ser carregadas no tempo de execução, e os dados consistem em dados estáticos arbitrários a serem consumidos pelo serviço. Cada componente neste modelo de aplicação hierárquica pode ser versão e atualizado de forma independente.

![O modelo de aplicação de tecido de serviço][appmodel-diagram]

Um tipo de aplicação é uma categorização de uma aplicação e consiste num pacote de tipos de serviço. Um tipo de serviço é uma categorização de um serviço. A categorização pode ter configurações e configurações diferentes, mas a funcionalidade principal permanece a mesma. As instâncias de um serviço são as diferentes variações de configuração de serviço do mesmo tipo de serviço.  

As classes (ou "tipos") de aplicações e serviços são descritas através de ficheiros XML (manifestos de aplicação e manifestos de serviço).  Os manifestos descrevem aplicações e serviços e são os modelos contra os quais as aplicações podem ser instantaneamente instantâneas a partir da loja de imagens do cluster.  Os manifestos são cobertos em detalhe nos manifestos de [aplicação e serviço.](service-fabric-application-and-service-manifests.md) A definição de esquema para o ficheiro ServiceManifest.xml e ApplicationManifest.xml está instalada com o SDK de Tecido de Serviço e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. O esquema XML está documentado na documentação de [esquema serviceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

O código para diferentes instâncias de aplicação é executado como processos separados mesmo quando hospedado pelo mesmo nó de Tecido de Serviço. Além disso, o ciclo de vida de cada instância de aplicação pode ser gerido (por exemplo, atualizado) de forma independente. O diagrama seguinte mostra como os tipos de aplicação são compostos por tipos de serviço, que por sua vez são compostos por códigos, configurações e pacotes de dados. Para simplificar o diagrama, apenas `ServiceType4` são apresentados os pacotes de código/config/dados, embora cada tipo de serviço inclua alguns ou todos esses tipos de pacotes.

![Tipos de aplicações de tecido de serviço e tipos de serviço][cluster-imagestore-apptypes]

Pode haver um ou mais casos de um tipo de serviço ativo no cluster. Por exemplo, casos de serviço sinuosos, ou réplicas, alcançam alta fiabilidade replicando o estado entre réplicas localizadas em diferentes nós do cluster. A replicação essencialmente proporciona redundância para que o serviço esteja disponível mesmo que um nó num cluster falhe. Um [serviço dividido](service-fabric-concepts-partitioning.md) divide ainda mais o seu estado (e padrões de acesso a esse estado) através de nós no cluster.

O diagrama seguinte mostra a relação entre aplicações e instâncias de serviço, divisórias e réplicas.

![Divisórias e réplicas dentro de um serviço][cluster-application-instances]

> [!TIP]
> Pode visualizar o layout de aplicações num cluster utilizando&lt;a ferramenta&gt;Service Fabric Explorer disponível em http:// seu clusteraddress :19080/Explorer. Para mais informações, consulte [Visualizar o seu cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
- Saiba mais sobre [estado](service-fabric-concepts-state.md)de serviço, [partição](service-fabric-concepts-partitioning.md)e [disponibilidade.](service-fabric-availability-services.md)
- Leia sobre como as aplicações e serviços são definidos em manifestos de [aplicação e serviço.](service-fabric-application-and-service-manifests.md)
- [Os modelos](service-fabric-hosting-model.md) de hospedagem de aplicações descrevem a relação entre réplicas (ou instâncias) de um processo de serviço implantado e de hospedagem de serviços.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


