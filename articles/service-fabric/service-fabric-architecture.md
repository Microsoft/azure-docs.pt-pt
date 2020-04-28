---
title: Arquitetura do Tecido de Serviço Azure
description: Este artigo explica a arquitetura do Service Fabric, uma plataforma de sistemas distribuída seletiva, fiável e facilmente gerida para a nuvem.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76024706"
---
# <a name="service-fabric-architecture"></a>Arquitetura do Service Fabric

O tecido de serviço é construído com subsistemas em camadas. Estes subsistemas permitem-lhe escrever aplicações que são:

* Elevada disponibilidade
* Escalável
* Gerível
* Testável

O diagrama seguinte mostra os principais subsistemas do Tecido de Serviço.

![Diagrama da arquitetura de tecido de serviço](media/service-fabric-architecture/service-fabric-architecture.png)

Num sistema distribuído, a capacidade de comunicar com segurança entre nós num cluster é crucial. Na base da pilha encontra-se o subsistema de transporte, que proporciona uma comunicação segura entre nós. Acima do subsistema de transporte encontra-se o subsistema da federação, que agrupa os diferentes nós numa única entidade (clusters nomeados) para que o Service Fabric possa detetar falhas, realizar eleições de líder estonteantes e fornecer um encaminhamento consistente. O subsistema de fiabilidade, em camadas em cima do subsistema da federação, é responsável pela fiabilidade dos serviços de Tecido de Serviço através de mecanismos como a replicação, a gestão de recursos e a falha. O subsistema da federação também está subjacente ao subsistema de hospedagem e ativação, que gere o ciclo de vida de uma aplicação num único nó. O subsistema de gestão gere o ciclo de vida de aplicações e serviços. O subsistema de testabilidade ajuda os desenvolvedores de aplicações a testar os seus serviços através de falhas simuladas antes e depois de implementar aplicações e serviços para ambientes de produção. O Service Fabric fornece a capacidade de resolver os locais de serviço através do seu subsistema de comunicação. Os modelos de programação de aplicações expostos aos desenvolvedores são em camadas em cima destes subsistemas juntamente com o modelo de aplicação para permitir a ferramenta.

## <a name="transport-subsystem"></a>Subsistema de transporte

O subsistema de transporte implementa um canal de comunicação de datagram ponto a ponto. Este canal é utilizado para a comunicação dentro de clusters de tecidode serviço e comunicação entre o cluster de tecido de serviço e os clientes. Apoia os padrões de comunicação de resposta única e de resposta ao pedido, que fornecem a base para a implementação da difusão e do multicast na camada da Federação. O subsistema de transporte assegura a comunicação utilizando certificados X509 ou segurança do Windows. Este subsistema é utilizado internamente pelo Service Fabric e não é diretamente acessível aos programadores para programação de aplicações.

## <a name="federation-subsystem"></a>Subsistema da Federação

Para raciocinar sobre um conjunto de nós num sistema distribuído, é necessário ter uma visão consistente do sistema. O subsistema da federação utiliza os primitivos de comunicação fornecidos pelo subsistema de transporte e cosa os vários nós num único aglomerado unificado que pode raciocinar. Fornece os sistemas distribuídos primitivos necessários pelos outros subsistemas - deteção de falhas, eleição de líder e encaminhamento consistente. O subsistema da federação é construído em cima de mesas de hash distribuídas com um espaço de 128 bits token. O subsistema cria uma topologia de anel sobre os nós, com cada nó no anel sendo alocado um subconjunto do espaço simbólico para a propriedade. Para deteção de falhas, a camada utiliza um mecanismo de locação baseado no batimento cardíaco e na arbitragem. O subsistema da federação também garante através de protocolos de adesão e partida intrincados que apenas um único proprietário de um símbolo existe a qualquer momento. Isto proporciona eleições líderes e garantias de encaminhamento consistentes.

## <a name="reliability-subsystem"></a>Subsistema de fiabilidade

O subsistema de fiabilidade fornece o mecanismo para tornar o estado de um serviço de tecido de serviço altamente disponível através da utilização do *Replicador,* *Failover Manager*e *Doequilíbrio de Recursos.*

* O Replicador garante que as alterações do Estado na réplica do serviço primário serão automaticamente replicadas em réplicas secundárias, mantendo a consistência entre as réplicas primárias e secundárias num conjunto de réplicas de serviço. O replicador é responsável pela gestão do quórum entre as réplicas do conjunto de réplicas. Interage com a unidade failover para obter a lista de operações para replicar, e o agente de reconfiguração fornece-lhe a configuração do conjunto de réplicas. Esta configuração indica quais as réplicas que as operações precisam de ser replicadas. O Service Fabric fornece um replicador predefinido chamado Replicador de Tecido, que pode ser usado pelo modelo de programação API para tornar o estado de serviço altamente disponível e fiável.
* O Failover Manager garante que quando os nós são adicionados ou removidos do cluster, a carga é automaticamente redistribuída pelos nós disponíveis. Se um nó no cluster falhar, o cluster reconfiguraautomaticamente as réplicas do serviço para manter a disponibilidade.
* O Gestor de Recursos coloca réplicas de serviço em domínios de falha no cluster e garante que todas as unidades de failover estão operacionais. O Gestor de Recursos também equilibra os recursos de serviço em todo o conjunto partilhado subjacente de nós de cluster para obter uma distribuição uniforme de carga ideal.

## <a name="management-subsystem"></a>Subsistema de gestão

O subsistema de gestão fornece serviço sem fim e gestão de ciclo de vida de aplicação. Os cmdlets powerShell e APIs administrativos permitem-lhe fornecer, implementar, patch, upgrade e desprovisionamento sem perda de disponibilidade. O subsistema de gestão realiza-o através dos seguintes serviços.

* **Gestor de Cluster**: Este é o serviço primário que interage com o Gestor de Failover da fiabilidade para colocar as aplicações nos nós com base nos constrangimentos de colocação do serviço. O Gestor de Recursos no subsistema failover garante que os constrangimentos nunca são quebrados. O gestor de cluster gere o ciclo de vida das aplicações desde a provisão à desprestação. Integra-se com o gestor de saúde para garantir que a disponibilidade de candidaturas não se perca do ponto de vista da saúde semântica durante as atualizações.
* **Health Manager**: Este serviço permite a monitorização da saúde de aplicações, serviços e entidades de cluster. As entidades de cluster (como nós, divisórias de serviço e réplicas) podem reportar informações de saúde, que são depois agregadas na loja centralizada de saúde. Esta informação de saúde fornece uma imagem geral de saúde ponto-a-tempo dos serviços e nós distribuídos por vários nós do cluster, permitindo-lhe tomar todas as ações corretivas necessárias. As APIs de consulta de saúde permitem-lhe consultar os eventos de saúde reportados ao subsistema de saúde. As APIs de consulta de saúde devolvem os dados brutos de saúde armazenados na loja de saúde ou os dados de saúde agregados e interpretados para uma entidade específica do cluster.
* **Loja de Imagens**: Este serviço fornece armazenamento e distribuição dos binários de aplicação. Este serviço fornece uma simples loja de ficheiros distribuída onde as aplicações são enviadas e descarregadas.

## <a name="hosting-subsystem"></a>Subsistema de hospedagem

O gestor do cluster informa o subsistema de hospedagem (em execução em cada nó) quais os serviços que precisa para gerir para um determinado nó. O subsistema de hospedagem gere então o ciclo de vida da aplicação naquele nó. Interage com a fiabilidade e componentes de saúde para garantir que as réplicas estão devidamente colocadas e saudáveis.

## <a name="communication-subsystem"></a>Subsistema de comunicação

Este subsistema fornece mensagens fiáveis dentro do cluster e descoberta de serviço através do serviço Naming. O serviço Naming resolve nomes de serviço para uma localização no cluster e permite que os utilizadores gerem nomes e propriedades de serviço. Utilizando o serviço Naming, os clientes podem comunicar com segurança com qualquer nó no cluster para resolver um nome de serviço e recuperar metadados de serviço. Utilizando uma Simples API do cliente de Nomeação, os utilizadores do Service Fabric podem desenvolver serviços e clientes capazes de resolver a localização atual da rede, apesar do dinamismo do nó ou da redimensionamento do cluster.

## <a name="testability-subsystem"></a>Subsistema de testabilidade

Testability é um conjunto de ferramentas especificamente concebidas para serviços de teste construídos em Tecido de Serviço. As ferramentas permitem que um desenvolvedor facilmente induzia falhas significativas e executasse cenários de teste para exercitar e validar os inúmeros estados e transições que um serviço irá experimentar ao longo da sua vida, tudo de forma controlada e segura. A testability também fornece um mecanismo para executar testes mais longos que podem iterar através de várias possíveis falhas sem perder a disponibilidade. Isto proporciona-lhe um ambiente de teste em produção.
