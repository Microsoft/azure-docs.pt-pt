---
title: Modelo de aplicação do Service Fabric do Azure | Documentos da Microsoft
description: Como modelar e descrever as aplicações e serviços no Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 750970233cbcb14d901dbb5fa94f649f6ff8ae6c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666278"
---
# <a name="model-an-application-in-service-fabric"></a>Modelar uma aplicação no Service Fabric
Este artigo fornece uma visão geral do modelo de aplicação do Azure Service Fabric e como definir uma aplicação e o serviço por meio de arquivos de manifesto.

## <a name="understand-the-application-model"></a>Compreender o modelo de aplicativo
Um aplicativo é uma coleção de serviços constituintes que realizam uma determinada função ou funções. Um serviço executa uma função completa e autónoma e pode iniciar e executar independentemente dos outros serviços.  Um serviço é composto por código, configuração e dados. Para cada serviço, os binários executáveis consiste em código, configuração é composta por definições de serviço que podem ser carregadas no tempo de execução e dados consiste em dados estáticos arbitrários seja consumido pelo serviço. Cada componente neste modelo de aplicativo hierárquicos pode ser uma versão e atualizar independentemente.

![O modelo de aplicação do Service Fabric][appmodel-diagram]

Um tipo de aplicação a categorização de um aplicativo e consiste num pacote dos tipos de serviço. Um tipo de serviço é uma categorização de um serviço. A categorização pode ter definições e configurações diferentes, mas a funcionalidade principal permanece a mesma. As instâncias de um serviço são as variações de configuração de serviço diferentes do mesmo tipo de serviço.  

Classes (ou "tipos") de aplicações e serviços são descritas por meio de arquivos XML (manifestos da aplicação e manifestos de serviço).  Os manifestos descrevem a aplicações e serviços e são os modelos em relação aos quais aplicativos podem ser instanciados do arquivo de imagens do cluster.  Os manifestos são descritos detalhadamente [aplicação e manifestos de serviço](service-fabric-application-and-service-manifests.md). A definição de esquema para o ficheiro servicemanifest. XML e applicationmanifest. xml é instalada com o Service Fabric SDK e ferramentas para *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. O esquema XML está documentado no [documentação de esquema ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

O código para diferentes instâncias da aplicação executar como processos separados, mesmo se estiverem alojados pelo mesmo nó do Service Fabric. Além disso, o ciclo de vida de cada instância da aplicação pode ser gerido (por exemplo, atualizado) independentemente. O diagrama seguinte mostra como os tipos de aplicativos são compostos de tipos de serviço, que por sua vez, são compostos de código, configuração e pacotes de dados. Para simplificar o diagrama, apenas o código/configuração/dados de pacotes para `ServiceType4` são apresentados, que cada tipo de serviço incluem alguns ou todos os tipos de pacotes.

![Tipos de recursos de infraestrutura do serviço de aplicações e tipos de serviço][cluster-imagestore-apptypes]

Pode haver um ou mais instâncias de um tipo de serviço ativo do cluster. Por exemplo, as instâncias de serviço com estado ou réplicas, atingir alta fiabilidade através da replicação de estado entre as réplicas localizadas em diferentes nós do cluster. Replicação essencialmente fornece redundância para o serviço esteja disponível, mesmo se falha um nó num cluster. R [particionados serviço](service-fabric-concepts-partitioning.md) divide ainda mais seu estado (e padrões de acesso a esse Estado) em todos os nós do cluster.

O diagrama seguinte mostra a relação entre aplicações e instâncias de serviço, partições e réplicas.

![As partições e réplicas dentro de um serviço][cluster-application-instances]

> [!TIP]
> Pode ver o esquema de aplicações num cluster com a ferramenta de Service Fabric Explorer disponível em http://&lt;yourclusteraddress&gt;: 19080/Explorer. Para obter mais informações, consulte [visualizar o seu cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [escalabilidade do aplicativo](service-fabric-concepts-scalability.md).
- Saiba mais sobre o serviço [estado](service-fabric-concepts-state.md), [criação de partições](service-fabric-concepts-partitioning.md), e [disponibilidade](service-fabric-availability-services.md).
- Saiba mais sobre como os aplicativos e serviços são definidos na [aplicação e manifestos de serviço](service-fabric-application-and-service-manifests.md).
- [Modelos de hospedagem de aplicativos](service-fabric-hosting-model.md) descrevem a relação entre as réplicas (ou instâncias) de um serviço implementado e o processo de host de serviço.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


