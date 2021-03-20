---
title: Visão geral dos clusters de tecido de serviço autónomo
description: Os clusters de tecido de serviço funcionam no Windows Server e linux, o que significa que poderá implementar e hospedar aplicações de Tecido de Serviço em qualquer lugar que possa executar o Windows Server ou o Linux.
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: a3162a3d02510ce8efab6c5bcac0f1fdd2b2539b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94684003"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Visão geral dos clusters autónomos de tecido de serviço

Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um cluster é chamada de nó de cluster. Os aglomerados podem escalar para milhares de nós. Se adicionar novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição de serviço e as instâncias através do aumento do número de nós. O desempenho geral da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Tecido de Serviço reequilibra novamente as réplicas de partição e instâncias através do número reduzido de nós para melhor utilizar o hardware em cada nó.

Um tipo de nó define o tamanho, número e propriedades para um conjunto de nós no cluster. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Os tipos de nó são utilizados para definir funções para um conjunto de nós de cluster, como o "front-end" ou o "back-end". O cluster pode ter mais do que um tipo de nó, mas o tipo de nó primário tem de ter, pelo menos, cinco VMs para clusters de produção (ou, pelo menos, três VMs para clusters de teste). Os [serviços do sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados em nós do tipo de nó primário.

O processo de criação de um cluster de Tecido de Serviço no local é semelhante ao processo de criação de um cluster em qualquer nuvem à sua escolha com um conjunto de VMs. Os primeiros passos para a disponibilização dos VMs são regidos pelo fornecedor de nuvens ou pelo ambiente no local que está a utilizar. Uma vez que tenha um conjunto de VMs com conectividade de rede ativado entre eles, então os passos para configurar o pacote de Tecido de Serviço, editar as definições do cluster e executar os scripts de criação e gestão do cluster são idênticos. Isto garante que o seu conhecimento e experiência de operar e gerir clusters de Tecidos de Serviço é transferível quando escolhe direcionar novos ambientes de hospedagem.

## <a name="cluster-security"></a>Segurança do cluster

Um cluster de tecido de serviço é um recurso que possui.  É da sua responsabilidade proteger os seus clusters para ajudar a evitar que utilizadores não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando se está a executar cargas de trabalho de produção no cluster.

> [!NOTE]
> A autenticação do Windows é baseada em Kerberos. A NTLM não é suportada como um tipo de autenticação.
>
> Sempre que possível, utilize a autenticação do certificado X.509 para clusters de Tecido de Serviço.

### <a name="node-to-node-security"></a>Segurança nó-a-nó

A segurança nó-a-nó assegura a comunicação entre os VMs ou os computadores num cluster. Este cenário de segurança garante que apenas os computadores autorizados a aderir ao cluster podem participar no alojamento de aplicações e serviços no cluster. O Service Fabric utiliza certificados X.509 para garantir um cluster e fornecer funcionalidades de segurança da aplicação.  É necessário um certificado de cluster para garantir o tráfego de clusters e fornecer a autenticação de cluster e servidor.  Os certificados auto-assinados podem ser utilizados para agrupamentos de ensaio, mas deve ser utilizado um certificado de uma autoridade de certificados fidedignos para assegurar aglomerados de produção.

A segurança do Windows também pode ser ativada para um cluster autónomo do Windows. Se tiver o Windows Server 2012 R2 e o Windows Ative Directory, recomendamos que utilize a segurança do Windows com contas de serviço geridas do grupo. Caso contrário, utilize a segurança do Windows com contas do Windows.

Para mais informações, leia [a segurança nó-a-nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança cliente-nó

A segurança entre clientes e nó autentica os clientes e ajuda a garantir a comunicação entre um cliente e os nós individuais no cluster. Este tipo de segurança ajuda a garantir que apenas os utilizadores autorizados possam aceder ao cluster e às aplicações que são implementadas no cluster. Os clientes são identificados exclusivamente através das suas credenciais de segurança de certificadoS X.509. Qualquer número de certificados de cliente opcionais pode ser usado para autenticar a administração ou clientes utilizadores com o cluster.

Além dos certificados de cliente, o Azure Ative Directory também pode ser configurado para autenticar clientes com o cluster.

Para mais informações, leia [a segurança do Cliente-a-nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="service-fabric-role-based-access-control"></a>Controlo de acesso baseado em funções do Tecido de Serviço
O Service Fabric também suporta o controlo de acesso para limitar o acesso a certas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais seguro. Dois tipos de controlo de acesso são suportados para clientes que se conectam a um cluster: função de administrador e papel do Utilizador.  

Para obter mais informações, leia [o controlo de acesso baseado em funções do Service Fabric.](service-fabric-cluster-security.md#service-fabric-role-based-access-control)

## <a name="scaling"></a>Dimensionamento

A aplicação exige mudanças ao longo do tempo. Poderá ser necessário aumentar os recursos de cluster para responder ao aumento da carga de trabalho de aplicação ou ao tráfego de redes ou diminuir os recursos de cluster quando a procura diminui. Depois de criar um cluster de Tecido de Serviço, pode escalar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós). Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a funcionar no cluster. À medida que o cluster escala, as suas aplicações também escalam automaticamente.

Para mais informações, leia [os aglomerados autónomos de escala.](service-fabric-cluster-scaling-standalone.md)

## <a name="upgrading"></a>Atualizar

Um aglomerado autónomo é um recurso que possui inteiramente. Você é responsável por remendar o so subjacente e iniciar upgrades de tecido. Pode configurar o seu cluster para receber atualizações automáticas de tempo de execução, quando a Microsoft lançar uma nova versão, ou optar por selecionar uma versão de tempo de execução suportada que pretenda. Além das atualizações de tecidos, também pode corrigir o SISTEMA e atualizar a configuração do cluster, como certificados ou portas de aplicação. 

Para mais informações, leia [a atualização de agrupamentos autónomos.](service-fabric-cluster-upgrade-standalone.md)

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

É possível criar clusters em VMs ou computadores que executam estes sistemas operativos (o Linux ainda não está suportado):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre [a fixação,](service-fabric-cluster-security.md) [dimensionamento](service-fabric-cluster-scaling-standalone.md)e [modernização de](service-fabric-cluster-upgrade-standalone.md) aglomerados autónomos.

Saiba mais sobre [as opções de suporte do Tecido de Serviço.](service-fabric-support.md)
