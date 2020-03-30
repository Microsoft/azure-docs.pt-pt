---
title: Visão geral dos clusters de tecido de serviço autónomo
description: Os clusters de tecido de serviço funcionam no Windows Server e linux, o que significa que poderá implementar e hospedar aplicações de Tecido de Serviço em qualquer lugar onde possa executar o Windows Server ou o Linux.
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: e8912ef5bc0fd6009443b736031fc9af57ab6c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465637"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Visão geral dos clusters autónomos de tecido de serviço

Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um cluster é chamado de nó de cluster. Os aglomerados podem escalar para milhares de nós. Se adicionar novos nós ao cluster, o Service Fabric reequilibra as réplicas e instâncias de partição de serviços em todo o número aumentado de nós. O desempenho global da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Service Fabric reequilibra novamente as réplicas e instâncias da partição através do número reduzido de nós para fazer melhor uso do hardware em cada nó.

Um tipo de nó define o tamanho, número e propriedades para um conjunto de nós no cluster. Cada tipo de nó pode então ser aumentado ou reduzido verticalmente de forma independente, pode ter conjuntos diferentes de portas abertas e ter métricas de capacidade diferente. Os tipos de nó são utilizados para definir funções para um conjunto de nós de cluster, como o "front-end" ou o "back-end". O cluster pode ter mais do que um tipo de nó, mas o tipo de nó primário tem de ter, pelo menos, cinco VMs para clusters de produção (ou, pelo menos, três VMs para clusters de teste). Os [serviços do sistema do Service Fabric](service-fabric-technical-overview.md#system-services) são colocados em nós do tipo de nó primário.

O processo de criação de um cluster de Tecido de Serviço no local é semelhante ao processo de criação de um cluster em qualquer nuvem à sua escolha com um conjunto de VMs. Os passos iniciais para a provisioniação dos VMs são regidos pelo fornecedor de nuvem ou pelo ambiente no local que você está usando. Uma vez que tenha um conjunto de VMs com conectividade de rede ativada entre eles, então os passos para configurar o pacote Service Fabric, editar as definições do cluster, e executar os scripts de criação e gestão de clusters são idênticos. Isto garante que o seu conhecimento e experiência de operar e gerir clusters de Tecidos de Serviço é transferível quando você escolhe direcionar novos ambientes de hospedagem.

## <a name="cluster-security"></a>Segurança do cluster
Um cluster de tecido de serviço é um recurso que possui.  É da sua responsabilidade proteger os seus clusters para ajudar a evitar que utilizadores não autorizados se conectem a eles. Um cluster seguro é especialmente importante quando se está a executar cargas de trabalho de produção no cluster.

### <a name="node-to-node-security"></a>Segurança nó-ao-nó
A segurança nó-ao-nó assegura a comunicação entre os VMs ou os computadores num cluster. Este cenário de segurança garante que apenas os computadores autorizados a aderir ao cluster podem participar no alojamento de aplicações e serviços no cluster. Service Fabric utiliza certificados X.509 para garantir um cluster e fornecer funcionalidades de segurança da aplicação.  É necessário um certificado de cluster para proteger o tráfego do cluster e fornecer a autenticação do cluster e do servidor.  Os certificados auto-assinados podem ser utilizados para agrupamentos de ensaio, mas deve ser utilizado um certificado de uma autoridade de certificados fidedignos para assegurar agrupamentos de produção.

A segurança do Windows também pode ser ativada para um cluster autónomo do Windows. Se tiver o Windows Server 2012 R2 e o Windows Ative Diretório, recomendamos que utilize a segurança do Windows com contas de serviço geridas pelo grupo. Caso contrário, utilize a segurança do Windows com contas Windows.

Para mais informações, leia [a segurança nó-ao-nó](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Segurança cliente-a-nó
A segurança cliente-a-nó autentica clientes e ajuda a garantir a comunicação entre um cliente e nós individuais no cluster. Este tipo de segurança ajuda a garantir que apenas os utilizadores autorizados podem aceder ao cluster e às aplicações que são implementadas no cluster. Os clientes são identificados de forma única através das suas credenciais de segurança de certificadox.509. Qualquer número de certificados de cliente opcionais pode ser usado para autenticar clientes administrativos ou utilizadores com o cluster.

Além dos certificados de cliente, o Azure Ative Directory também pode ser configurado para autenticar clientes com o cluster.

Para mais informações, leia [a segurança cliente-a-nó](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
O Service Fabric também suporta o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais seguro. São suportados dois tipos de controlo de acesso para clientes que se ligam a um cluster: função de administrador e função de utilizador.  

Para mais informações, leia o [Controlo de Acesso baseado em Funções (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Dimensionamento

A aplicação exige mudanças ao longo do tempo. Pode ser necessário aumentar os recursos de cluster para satisfazer o aumento da carga de trabalho das aplicações ou o tráfego de rede ou diminuir os recursos de cluster quando a procura diminui. Depois de criar um cluster de Tecido de Serviço, pode escalar o cluster horizontalmente (alterar o número de nós) ou verticalmente (alterar os recursos dos nós). Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster. À medida que o cluster escala, as suas aplicações também escalam automaticamente.

Para mais informações, leia os [clusters autónomos de Escala.](service-fabric-cluster-scaling-standalone.md)

## <a name="upgrading"></a>Upgrade

Um aglomerado autónomo é um recurso que tu és inteiramente dono. É responsável por remendar os sistemas operativos subjacentes e dar início a atualizações de tecidos. Pode configurar o seu cluster para receber atualizações automáticas de tempo de execução, quando a Microsoft lançar uma nova versão, ou optar por selecionar uma versão de tempo de execução suportada que deseja. Além de atualizações de tecido, também pode remendar o SISTEMA e atualizar a configuração do cluster, como certificados ou portas de aplicação. 

Para mais informações, leia A atualização de [clusters autónomos](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Sistemas operativos suportados
É capaz de criar clusters em VMs ou computadores que executam estes sistemas operativos (o Linux ainda não é suportado):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Passos seguintes
Leia mais sobre [a segurança,](service-fabric-cluster-security.md) [a escala](service-fabric-cluster-scaling-standalone.md)e a [modernização](service-fabric-cluster-upgrade-standalone.md) de clusters autónomos.

Saiba mais sobre as opções de suporte do [Tecido de Serviço.](service-fabric-support.md)
