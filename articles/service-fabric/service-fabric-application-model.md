---
title: Modelo de aplicativo Service Fabric do Azure
description: Como modelar e descrever aplicativos e serviços no Azure Service Fabric usando arquivos de manifesto de aplicativo e serviço.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7179686b7d4ef2df267cb95ece8f83d5fb7682b8
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551884"
---
# <a name="model-an-application-in-service-fabric"></a>Modelar um aplicativo no Service Fabric
Este artigo fornece uma visão geral do modelo de aplicativo Service Fabric do Azure e como definir um aplicativo e um serviço por meio de arquivos de manifesto.

## <a name="understand-the-application-model"></a>Entender o modelo de aplicativo
Um aplicativo é uma coleção de serviços constituintes que executam uma determinada função ou funções. Um serviço executa uma função completa e autônoma e pode iniciar e executar independentemente de outros serviços.  Um serviço é composto por código, configuração e dados. Para cada serviço, o código consiste em binários executáveis, a configuração consiste em configurações de serviço que podem ser carregadas em tempo de execução, e os dados consistem em dados estáticos arbitrários a serem consumidos pelo serviço. Cada componente desse modelo de aplicativo hierárquico pode ter controle de versão e ser atualizado de forma independente.

![O modelo de aplicativo Service Fabric][appmodel-diagram]

Um tipo de aplicativo é uma categorização de um aplicativo e consiste em um pacote de tipos de serviço. Um tipo de serviço é uma categorização de um serviço. A categorização pode ter definições e configurações diferentes, mas a funcionalidade básica permanece a mesma. As instâncias de um serviço são as diferentes variações de configuração de serviço do mesmo tipo de serviço.  

As classes (ou "tipos") de aplicativos e serviços são descritas por meio de arquivos XML (manifestos de aplicativo e manifestos de serviço).  Os manifestos descrevem aplicativos e serviços e são os modelos nos quais os aplicativos podem ser instanciados do repositório de imagens do cluster.  Os manifestos são abordados em detalhes em [manifestos de aplicativo e serviço](service-fabric-application-and-service-manifests.md). A definição de esquema para o arquivo manifest. xml e ApplicationManifest. xml é instalada com o SDK do Service Fabric e ferramentas para *c:\Arquivos de Programas\microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. O esquema XML está documentado na [documentação do esquema ServiceFabricServiceModel. xsd](service-fabric-service-model-schema.md).

O código para instâncias de aplicativo diferentes é executado como processos separados, mesmo quando hospedado pelo mesmo nó de Service Fabric. Além disso, o ciclo de vida de cada instância do aplicativo pode ser gerenciado (por exemplo, atualizado) de forma independente. O diagrama a seguir mostra como os tipos de aplicativo são compostos de tipos de serviço que, por sua vez, são compostos de código, configuração e pacotes de dados. Para simplificar o diagrama, somente os pacotes de código/configuração/dados para `ServiceType4` são mostrados, embora cada tipo de serviço inclua alguns ou todos os tipos de pacote.

![Tipos de aplicativos Service Fabric e tipos de serviço][cluster-imagestore-apptypes]

Pode haver uma ou mais instâncias de um tipo de serviço ativas no cluster. Por exemplo, instâncias de serviço com estado, ou réplicas, alcançam alta confiabilidade replicando o estado entre as réplicas localizadas em nós diferentes no cluster. Basicamente, a replicação fornece redundância para que o serviço esteja disponível, mesmo se um nó em um cluster falhar. Um [serviço particionado](service-fabric-concepts-partitioning.md) divide ainda mais seu estado (e padrões de acesso a esse estado) entre os nós no cluster.

O diagrama a seguir mostra a relação entre aplicativos e instâncias de serviço, partições e réplicas.

![Partições e réplicas em um serviço][cluster-application-instances]

> [!TIP]
> Você pode exibir o layout dos aplicativos em um cluster usando a ferramenta Service Fabric Explorer disponível em http://&lt;yourclusteraddress&gt;: 19080/Explorer. Para obter mais informações, consulte [visualizando seu cluster com Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre a [escalabilidade do aplicativo](service-fabric-concepts-scalability.md).
- Saiba mais sobre o [estado](service-fabric-concepts-state.md), o [particionamento](service-fabric-concepts-partitioning.md)e a [disponibilidade](service-fabric-availability-services.md)do serviço.
- Leia sobre como os aplicativos e serviços são definidos em [manifestos de aplicativo e serviço](service-fabric-application-and-service-manifests.md).
- Os [modelos de Hospedagem de aplicativos](service-fabric-hosting-model.md) descrevem a relação entre réplicas (ou instâncias) de um serviço implantado e um processo de host de serviço.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


