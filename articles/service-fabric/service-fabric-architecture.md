---
title: Arquitetura do Tecido de Serviço Azure
description: Este artigo explica a arquitetura do Service Fabric, uma plataforma de sistemas distribuídos usada para construir aplicações escaláveis, fiáveis e facilmente geridas para a nuvem.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76024706"
---
# <a name="service-fabric-architecture"></a>Arquitetura do Service Fabric

O Tecido de Serviço é construído com subsistemas em camadas. Estes subsistemas permitem-lhe escrever aplicações que são:

* Tenha elevada disponibilidade
* Seja dimensionável
* Gerível
* Testável

O diagrama seguinte mostra os principais subsistemas de Tecido de Serviço.

![Diagrama de arquitetura de tecido de serviço](media/service-fabric-architecture/service-fabric-architecture.png)

Num sistema distribuído, a capacidade de comunicar de forma segura entre nós num cluster é crucial. Na base da pilha está o subsistema de transporte, que fornece uma comunicação segura entre nós. Acima do subsistema de transporte encontra-se o subsistema da federação, que agrupa os diferentes nós numa única entidade (agrupamentos nomeados) para que o Service Fabric possa detetar falhas, realizar a eleição de líderes e fornecer encaminhamento consistente. O subsistema de fiabilidade, em camadas em cima do subsistema da federação, é responsável pela fiabilidade dos serviços de Service Fabric através de mecanismos como replicação, gestão de recursos e failover. O subsistema da federação também está subjacente ao subsistema de hospedagem e ativação, que gere o ciclo de vida de uma aplicação num único nó. O subsistema de gestão gere o ciclo de vida de aplicações e serviços. O subsistema de testability ajuda os desenvolvedores de aplicações a testar os seus serviços através de falhas simuladas antes e depois de implementar aplicações e serviços em ambientes de produção. A Service Fabric fornece a capacidade de resolver localizações de serviço através do seu subsistema de comunicação. Os modelos de programação de aplicações expostos aos desenvolvedores estão em camadas em cima destes subsistemas juntamente com o modelo de aplicação para permitir a ferramenta.

## <a name="transport-subsystem"></a>Subsistema de transporte

O subsistema de transporte implementa um canal de comunicação de datagram ponto a ponto. Este canal é utilizado para comunicação dentro de clusters de tecidos de serviço e comunicação entre o cluster de tecidos de serviço e os clientes. Apoia padrões de comunicação unidirecionais e de resposta a pedidos, que fornecem as bases para a implementação da emissão e multicast na camada da Federação. O subsistema de transporte assegura a comunicação utilizando certificados X509 ou segurança do Windows. Este subsistema é utilizado internamente pela Service Fabric e não está diretamente acessível aos desenvolvedores para programação de aplicações.

## <a name="federation-subsystem"></a>Subsistema da Federação

Para raciocinar sobre um conjunto de nós num sistema distribuído, é necessário ter uma visão consistente do sistema. O subsistema da federação utiliza os primitivos de comunicação fornecidos pelo subsistema de transporte e cose os vários nós num único cluster unificado que possa raciocinar. Fornece os sistemas distribuídos primitivos necessários pelos outros subsistemas - deteção de falhas, eleição de líderes e encaminhamento consistente. O subsistema da federação é construído em cima de mesas de haxixe distribuídas com um espaço simbólico de 128 bits. O subsistema cria uma topologia de anel sobre os nós, com cada nó no anel a ser atribuído um subconjunto do espaço simbólico para a propriedade. Para deteção de falhas, a camada utiliza um mecanismo de locação com base no batimento cardíaco e na arbitragem. O subsistema da federação também garante através de protocolos de adesão e partida intrincados que apenas um único proprietário de um símbolo existe a qualquer momento. Isto fornece garantias de eleição de líderes e de encaminhamento consistente.

## <a name="reliability-subsystem"></a>Subsistema de fiabilidade

O subsistema de fiabilidade fornece o mecanismo para tornar o estado de um serviço de tecido de serviço altamente disponível através da utilização do *Replicador,* *Gestor de Falhas*e *Balanceador de Recursos.*

* O Replicador garante que as mudanças de estado na réplica do serviço primário serão automaticamente replicadas em réplicas secundárias, mantendo a consistência entre as réplicas primárias e secundárias num conjunto de réplicas de serviço. O replicador é responsável pela gestão do quórum entre as réplicas no conjunto de réplicas. Interage com a unidade de failover para obter a lista de operações para replicar, e o agente de reconfiguração fornece-lhe a configuração do conjunto de réplicas. Esta configuração indica quais as réplicas que as operações precisam de ser replicadas. O Service Fabric fornece um replicador padrão chamado Replicador de Tecido, que pode ser usado pelo modelo de programação API para tornar o estado de serviço altamente disponível e fiável.
* O Gestor failover garante que quando os nós são adicionados ou removidos do cluster, a carga é automaticamente redistribuída através dos nós disponíveis. Se um nó no cluster falhar, o cluster reconfigurará automaticamente as réplicas de serviço para manter a disponibilidade.
* O Gestor de Recursos coloca réplicas de serviço em domínios de falha no cluster e garante que todas as unidades de failover estão operacionais. O Gestor de Recursos também equilibra os recursos de serviço em todo o conjunto partilhado subjacente de nós de cluster para obter uma distribuição uniforme ideal de carga.

## <a name="management-subsystem"></a>Subsistema de gestão

O subsistema de gestão fornece gestão de ciclo de vida de serviço e aplicação de ponta a ponta. Os cmdlets powerShell e as APIs administrativas permitem-lhe provisões, implantação, correção, atualização e desaparamento de aplicações sem perda de disponibilidade. O subsistema de gestão realiza-o através dos seguintes serviços.

* **Cluster Manager**: Este é o serviço primário que interage com o Gestor failover da fiabilidade para colocar as aplicações nos nós com base nas restrições de colocação de serviço. O Gestor de Recursos no subsistema de failover garante que os constrangimentos nunca são quebrados. O gestor do cluster gere o ciclo de vida das aplicações desde a provisão até à desvisão. Integra-se com o gestor de saúde para garantir que a disponibilidade de aplicações não se perca numa perspetiva de saúde semântica durante as atualizações.
* **Gestor de Saúde**: Este serviço permite a monitorização da saúde de aplicações, serviços e entidades de cluster. As entidades de cluster (como nós, divisórias de serviço e réplicas) podem reportar informações de saúde, que são depois agregadas na loja de saúde centralizada. Esta informação de saúde fornece uma imagem geral de saúde pontual dos serviços e nós distribuídos por múltiplos nós no cluster, permitindo-lhe tomar as ações corretivas necessárias. As APIs de consulta de saúde permitem-lhe consultar os eventos de saúde comunicados ao subsistema de saúde. As APIs de consulta de saúde devolvem os dados de saúde bruto armazenados na loja de saúde ou nos dados de saúde agregados e interpretados para uma entidade específica do cluster.
* **Image Store**: Este serviço fornece armazenamento e distribuição dos binários de aplicação. Este serviço fornece uma simples loja de ficheiros distribuídos onde as aplicações são carregadas e descarregadas.

## <a name="hosting-subsystem"></a>Subsistema de hospedagem

O gestor do cluster informa o subsistema de hospedagem (em execução em cada nó) que serviços precisa de gerir para um determinado nó. O subsistema de hospedagem gere então o ciclo de vida da aplicação nesse nó. Interage com a fiabilidade e os componentes de saúde para garantir que as réplicas estão devidamente colocadas e saudáveis.

## <a name="communication-subsystem"></a>Subsistema de comunicação

Este subsistema fornece mensagens fiáveis dentro do cluster e descoberta de serviço através do serviço Naming. O serviço Naming resolve os nomes de serviço para uma localização no cluster e permite que os utilizadores gerem nomes de serviços e propriedades. Utilizando o serviço Naming, os clientes podem comunicar de forma segura com qualquer nó no cluster para resolver um nome de serviço e recuperar metadados de serviço. Utilizando uma simples API do cliente Naming, os utilizadores da Service Fabric podem desenvolver serviços e clientes capazes de resolver a localização atual da rede, apesar do dinamismo do nó ou do redimensionamento do cluster.

## <a name="testability-subsystem"></a>Subsistema de capacidade de teste

Testability é um conjunto de ferramentas especificamente concebidas para serviços de testes construídos em Tecido de Serviço. As ferramentas permitem que um desenvolvedor induz facilmente falhas significativas e execute cenários de teste para exercitar e validar os inúmeros estados e transições que um serviço irá experimentar ao longo da sua vida, tudo de forma controlada e segura. A capacidade de teste também fornece um mecanismo para realizar testes mais longos que podem iterar através de várias possíveis falhas sem perder disponibilidade. Isto proporciona-lhe um ambiente de teste em produção.
