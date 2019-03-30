---
title: Descrição geral de clusters autónomos do Service Fabric | Documentos da Microsoft
description: Clusters do Service Fabric executados no Windows Server e Linux, que significa que será capaz de implantar e alojar as aplicações do Service Fabric em qualquer lugar, pode executar Windows Server ou Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b4b7759d1dc23c1a1b3a9b5aeb2a181923e14d40
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670715"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Descrição geral de clusters autónomos do Service Fabric

Um cluster do Service Fabric é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Uma máquina ou VM que faça parte de um cluster é chamado de um nó de cluster. Clusters podem ser dimensionados para milhares de nós. Se adicionar novos nós ao cluster, o Service Fabric rebalances as réplicas de partição de serviço e as instâncias em aumento do número de nós. Em geral melhora o desempenho do aplicativo e reduz a contenção de acesso à memória. Se os nós do cluster não estão a ser utilizados com eficiência, pode diminuir o número de nós do cluster. Service Fabric rebalances novamente as instâncias e réplicas de partição entre o menor número de nós para garantir uma melhor utilização do hardware em cada nó.

Um tipo de nó define o tamanho, número e propriedades de um conjunto de nós no cluster. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Os tipos de nó são utilizados para definir funções para um conjunto de nós de cluster, como o "front-end" ou o "back-end". O cluster pode ter mais do que um tipo de nó, mas o tipo de nó primário tem de ter, pelo menos, cinco VMs para clusters de produção (ou, pelo menos, três VMs para clusters de teste). Os [serviços do sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados em nós do tipo de nó primário.

O processo para criar um cluster do Service Fabric no local é semelhante ao processo de criação de um cluster em qualquer cloud à sua escolha, com um conjunto de VMs. As etapas iniciais para aprovisionar as VMs são regidas pelo fornecedor de cloud ou no ambiente no local que está a utilizar. Depois de ter um conjunto de VMs com a conectividade de rede ativada entre eles, em seguida, os passos para configurar o pacote de recursos de infraestrutura do serviço, edite as definições do cluster e execute a criação de cluster e scripts de gestão são idênticas. Isto garante que seu conhecimento e experiência de operando e gerenciando os clusters do Service Fabric é transferível quando opta por novos ambientes de alojamento de destino.

## <a name="cluster-security"></a>Segurança do cluster
Um cluster do Service Fabric é um recurso que é proprietário.  É da responsabilidade do cliente para proteger os seus clusters para ajudar a impedir que os utilizadores não autorizados a ligar aos mesmos. Um cluster seguro é especialmente importante quando estiver a executar cargas de trabalho de produção no cluster.

### <a name="node-to-node-security"></a>Segurança de nó para nó
Segurança de nó para nó protege a comunicação entre VMs ou computadores num cluster. Neste cenário de segurança garante que apenas os computadores que estejam autorizados a aderir o cluster podem participar de hospedar aplicativos e serviços no cluster. O Service Fabric utiliza certificados X.509 para proteger um cluster e fornecer recursos de segurança do aplicativo.  É necessário para proteger o tráfego de cluster e disponibilizar a autenticação do cluster e servidor de um certificado de cluster.  Gestão personalizados de certificados assinados podem ser utilizados para clusters de teste, mas um certificado de uma autoridade de certificado fidedigno deve ser utilizado para proteger clusters de produção.

Segurança do Windows também pode ser ativada para um cluster autónomo do Windows. Se tiver o Windows Server 2012 R2 e do Active Directory do Windows, recomendamos que utilize a segurança do Windows com contas de serviço geridas de grupo. Caso contrário, utilize a segurança do Windows com contas do Windows.

Para obter mais informações, leia [segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança de cliente para nó
Segurança de cliente para nó autentica os clientes e ajuda a comunicação segura entre um cliente e nós individuais no cluster. Este tipo de segurança ajuda a garantir que apenas os utilizadores autorizados conseguem aceder ao cluster e as aplicações que são implementadas no cluster. Os clientes são exclusivamente identificados por meio de qualquer uma de suas credenciais de segurança do certificado X.509. Qualquer número de certificados de cliente opcional pode ser utilizado para autenticar clientes de administrador ou utilizador com o cluster.

Para além dos certificados de cliente, o Azure Active Directory também pode ser configurado para autenticar os clientes com o cluster.

Para obter mais informações, leia [segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
O Service Fabric também suporta o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais segura. Dois tipos de controle de acesso são suportados para clientes que se ligam a um cluster: Função de administrador e a função de utilizador.  

Para obter mais informações, leia [controlo de acesso baseado em funções (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Dimensionamento

Alteram pedidos de aplicações ao longo do tempo. Poderá ter de aumentar os recursos do cluster para corresponder ao tráfego de rede ou de carga de trabalho de aplicações mais ou diminuir recursos do cluster quando a procura cair. Depois de criar um cluster do Service Fabric, pode dimensionar o cluster horizontalmente (alterar o número de nós) ou na vertical (alterar os recursos de nós). Pode dimensionar o cluster em qualquer altura, mesmo quando as cargas de trabalho em execução no cluster. Como dimensiona o cluster, as aplicações são dimensionadas automaticamente também.

Para obter mais informações, leia [dimensionar clusters autónomos](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>A atualizar

Um cluster autónomo é um recurso que inteiramente próprios. É responsável por aplicação de patches de sistema operacional subjacente e o início de atualizações de recursos de infraestrutura. Pode definir o seu cluster para receber atualizações de tempo de execução automática, quando a Microsoft lança uma nova versão, ou optar por selecionar uma versão de runtime suportada que pretende. Para além das atualizações de recursos de infraestrutura, também pode aplicar o patch do SO e atualizar a configuração de cluster, tais como certificados ou as portas da aplicação. 

Para obter mais informações, leia [atualizar clusters autónomos](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
É possível criar clusters em VMs ou computadores que executam esses sistemas operacionais (Linux ainda não é suportado):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="next-steps"></a>Passos Seguintes
Leia mais sobre [protegendo](service-fabric-cluster-security.md), [dimensionamento](service-fabric-cluster-scaling-standalone.md), e [atualizar](service-fabric-cluster-upgrade-standalone.md) clusters autónomos.

Saiba mais sobre [opções de suporte do Service Fabric](service-fabric-support.md).