---
title: Visão geral dos clusters Service Fabric autônomos
description: Service Fabric clusters são executados no Windows Server e no Linux, o que significa que você poderá implantar e hospedar Service Fabric aplicativos em qualquer lugar em que possa executar o Windows Server ou o Linux.
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: e8912ef5bc0fd6009443b736031fc9af57ab6c5b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465637"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Visão geral de Service Fabric clusters autônomos

Um Cluster Service Fabric é um conjunto de máquinas físicas ou virtuais conectadas à rede em que seus microserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó de cluster. Os clusters podem ser dimensionados para milhares de nós. Se você adicionar novos nós ao cluster, Service Fabric reequilibrará as réplicas e instâncias de partição de serviço em um número maior de nós. O desempenho geral do aplicativo melhora e a contenção de acesso à memória diminui. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. Service Fabric novamente reequilibra as réplicas e instâncias de partição em todo o número de nós reduzido para fazer melhor uso do hardware em cada nó.

Um tipo de nó define o tamanho, o número e as propriedades de um conjunto de nós no cluster. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Os tipos de nó são utilizados para definir funções para um conjunto de nós de cluster, como o "front-end" ou o "back-end". O cluster pode ter mais do que um tipo de nó, mas o tipo de nó primário tem de ter, pelo menos, cinco VMs para clusters de produção (ou, pelo menos, três VMs para clusters de teste). Os [serviços do sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados em nós do tipo de nó primário.

O processo para criar um cluster de Service Fabric local é semelhante ao processo de criação de um cluster em qualquer nuvem de sua escolha com um conjunto de VMs. As etapas iniciais para provisionar as VMs são governadas pelo provedor de nuvem ou pelo ambiente local que você está usando. Depois de ter um conjunto de VMs com conectividade de rede habilitada entre elas, as etapas para configurar o pacote de Service Fabric, editar as configurações de cluster e executar os scripts de gerenciamento e criação de cluster são idênticas. Isso garante que seu conhecimento e sua experiência de operação e gerenciamento de clusters de Service Fabric seja transferível quando você optar por direcionar novos ambientes de hospedagem.

## <a name="cluster-security"></a>Segurança em clusters
Um Cluster Service Fabric é um recurso que você possui.  É sua responsabilidade proteger seus clusters para ajudar a impedir que usuários não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando você está executando cargas de trabalho de produção no cluster.

### <a name="node-to-node-security"></a>Segurança de nó para nó
A segurança de nó para nó protege a comunicação entre as VMs ou os computadores em um cluster. Esse cenário de segurança garante que apenas os computadores autorizados a ingressar no cluster possam participar da Hospedagem de aplicativos e serviços no cluster. O Service Fabric usa certificados X. 509 para proteger um cluster e fornecer recursos de segurança de aplicativo.  Um certificado de cluster é necessário para proteger o tráfego de cluster e fornecer autenticação de cluster e de servidor.  Certificados autoassinados podem ser usados para clusters de teste, mas um certificado de uma autoridade de certificação confiável deve ser usado para proteger os clusters de produção.

A segurança do Windows também pode ser habilitada para um cluster autônomo do Windows. Se você tiver o Windows Server 2012 R2 e o Windows Active Directory, recomendamos que você use a segurança do Windows com contas de serviço gerenciado de grupo. Caso contrário, use a segurança do Windows com contas do Windows.

Para obter mais informações, leia [segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança de cliente para nó
A segurança de cliente para nó autentica clientes e ajuda a proteger a comunicação entre um cliente e nós individuais no cluster. Esse tipo de segurança ajuda a garantir que somente usuários autorizados possam acessar o cluster e os aplicativos que são implantados no cluster. Os clientes são identificados exclusivamente por meio das credenciais de segurança do certificado X. 509. Qualquer número de certificados de cliente opcionais pode ser usado para autenticar clientes de administrador ou de usuário com o cluster.

Além dos certificados de cliente, os Azure Active Directory também podem ser configurados para autenticar clientes com o cluster.

Para obter mais informações, leia [segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
O Service Fabric também dá suporte ao controle de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários. Isso ajuda a tornar o cluster mais seguro. Há suporte para dois tipos de controle de acesso para clientes que se conectam a um cluster: função de administrador e função de usuário.  

Para obter mais informações, leia [RBAC (controle de acesso baseado em função)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Dimensionamento

As demandas de aplicativo mudam ao longo do tempo. Talvez seja necessário aumentar os recursos de cluster para atender à carga de trabalho de aplicativo ou ao tráfego de rede aumentado ou diminuir os recursos de cluster quando a demanda cair. Depois de criar um Cluster Service Fabric, você pode dimensionar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós). Você pode dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster. À medida que o cluster é dimensionado, os aplicativos também são dimensionados automaticamente.

Para obter mais informações, leia [dimensionando clusters autônomos](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Atualizar

Um cluster autônomo é um recurso que você possui inteiramente. Você é responsável por aplicar patches no sistema operacional subjacente e iniciar atualizações de malha. Você pode definir o cluster para receber atualizações de tempo de execução automáticas, quando a Microsoft lançar uma nova versão ou optar por selecionar uma versão de tempo de execução com suporte desejada. Além das atualizações de malha, você também pode corrigir o sistema operacional e atualizar a configuração do cluster, como certificados ou portas de aplicativo. 

Para obter mais informações, leia [atualizando clusters autônomos](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
Você pode criar clusters em VMs ou computadores que executam esses sistemas operacionais (ainda não há suporte para o Linux):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Passos seguintes
Leia mais sobre como [proteger](service-fabric-cluster-security.md), [dimensionar](service-fabric-cluster-scaling-standalone.md)e [Atualizar](service-fabric-cluster-upgrade-standalone.md) clusters autônomos.

Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md).
