---
title: Arquitetura do Azure Service Fabric
description: Este artigo explica a arquitetura do Service Fabric, uma plataforma de sistemas distribuídos usada para criar aplicativos escalonáveis, confiáveis e facilmente gerenciados para a nuvem.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024706"
---
# <a name="service-fabric-architecture"></a>Arquitetura do Service Fabric

Service Fabric é criado com subsistemas em camadas. Esses subsistemas permitem que você grave aplicativos que são:

* Elevada disponibilidade
* Escalável
* Gerenciável
* Testável

O diagrama a seguir mostra os principais subsistemas de Service Fabric.

![Diagrama de arquitetura de Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

Em um sistema distribuído, a capacidade de comunicação segura entre os nós em um cluster é crucial. Na base da pilha está o subsistema de transporte, que fornece comunicação segura entre os nós. Acima do subsistema de transporte está o subsistema de Federação, que agrupa os nós diferentes em uma única entidade (clusters nomeados) para que Service Fabric possa detectar falhas, realizar eleição de líder e fornecer roteamento consistente. O subsistema de confiabilidade, em camadas na parte superior do subsistema da Federação, é responsável pela confiabilidade dos serviços de Service Fabric por meio de mecanismos como replicação, gerenciamento de recursos e failover. O subsistema de Federação também conta com o subsistema de hospedagem e ativação, que gerencia o ciclo de vida de um aplicativo em um único nó. O subsistema de gerenciamento gerencia o ciclo de vida de aplicativos e serviços. O subsistema de capacidade de teste ajuda os desenvolvedores de aplicativos a testar seus serviços por meio de falhas simuladas antes e depois da implantação de aplicativos e serviços em ambientes de produção. Service Fabric fornece a capacidade de resolver os locais de serviço por meio de seu subsistema de comunicação. Os modelos de programação de aplicativo expostos aos desenvolvedores são dispostos em camadas sobre esses subsistemas junto com o modelo de aplicativo para habilitar as ferramentas.

## <a name="transport-subsystem"></a>Subsistema de transporte

O subsistema de transporte implementa um canal de comunicação de datagrama ponto a ponto. Esse canal é usado para comunicação entre clusters do Service Fabric e a comunicação entre o cluster do Service Fabric e os clientes. Ele dá suporte a padrões de comunicação unidirecional e de solicitação-resposta, que fornece a base para implementar difusão e multicast na camada de Federação. O subsistema de transporte protege a comunicação usando certificados X509 ou a segurança do Windows. Esse subsistema é usado internamente pelo Service Fabric e não é diretamente acessível aos desenvolvedores para programação de aplicativo.

## <a name="federation-subsystem"></a>Subsistema de Federação

Para o motivo de um conjunto de nós em um sistema distribuído, você precisa ter uma visão consistente do sistema. O subsistema de Federação usa os primitivos de comunicação fornecidos pelo subsistema de transporte e une os vários nós em um único cluster unificado com o qual ele pode ter um motivo. Ele fornece os primitivos de sistemas distribuídos necessários para os outros subsistemas – detecção de falhas, eleição de líder e roteamento consistente. O subsistema de Federação é criado sobre as tabelas de hash distribuídas com um espaço de token de 128 bits. O subsistema cria uma topologia de anel sobre os nós, sendo que cada nó no anel está alocando um subconjunto do espaço de token para propriedade. Para detecção de falha, a camada usa um mecanismo de concessão com base na pulsação e na arbitragem do coração. O subsistema de Federação também garante por meio de protocolos de junção e de partida complexos que apenas um único proprietário de um token existe a qualquer momento. Isso fornece uma eleição de líder e garantias de roteamento consistentes.

## <a name="reliability-subsystem"></a>Subsistema de confiabilidade

O subsistema de confiabilidade fornece o mecanismo para tornar o estado de um serviço de Service Fabric altamente disponível por meio do uso do *Replicator*, *Gerenciador de failover*e *balanceador de recursos*.

* O replicador garante que as alterações de estado na réplica de serviço primária serão automaticamente replicadas para réplicas secundárias, mantendo a consistência entre as réplicas primária e secundária em um conjunto de réplicas de serviço. O replicador é responsável pelo gerenciamento de quorum entre as réplicas no conjunto de réplicas. Ele interage com a unidade de failover para obter a lista de operações a serem replicadas e o agente de reconfiguração fornece isso com a configuração do conjunto de réplicas. Essa configuração indica quais réplicas as operações precisam ser replicadas. O Service Fabric fornece um replicador padrão chamado Fabric Replicator, que pode ser usado pela API do modelo de programação para tornar o estado do serviço altamente disponível e confiável.
* O Gerenciador de Failover garante que, quando os nós são adicionados ou removidos do cluster, a carga é redistribuída automaticamente entre os nós disponíveis. Se um nó no cluster falhar, o cluster reconfigurará automaticamente as réplicas de serviço para manter a disponibilidade.
* O Gerenciador de recursos coloca réplicas de serviço entre domínios de falha no cluster e garante que todas as unidades de failover estejam operacionais. O Gerenciador de recursos também equilibra os recursos de serviço no pool compartilhado subjacente de nós de cluster para obter uma distribuição de carga uniforme ideal.

## <a name="management-subsystem"></a>Subsistema de gerenciamento

O subsistema de gerenciamento fornece serviço de ponta a ponta e gerenciamento do ciclo de vida do aplicativo. Os cmdlets do PowerShell e as APIs administrativas permitem provisionar, implantar, aplicar patch, atualizar e desprovisionar aplicativos sem perda de disponibilidade. O subsistema de gerenciamento realiza isso por meio dos serviços a seguir.

* **Gerenciador de cluster**: esse é o serviço primário que interage com a Gerenciador de failover de confiabilidade para colocar os aplicativos nos nós com base nas restrições de posicionamento do serviço. O Gerenciador de recursos no subsistema de failover garante que as restrições nunca sejam interrompidas. O Gerenciador de cluster gerencia o ciclo de vida dos aplicativos de provisionar para desprovisionar. Ele se integra ao Gerenciador de integridade para garantir que a disponibilidade do aplicativo não seja perdida de uma perspectiva de integridade semântica durante as atualizações.
* **Gerenciador de integridade**: esse serviço habilita o monitoramento de integridade de aplicativos, serviços e entidades de cluster. As entidades de cluster (como nós, partições de serviço e réplicas) podem relatar informações de integridade, que, em seguida, são agregadas ao armazenamento de integridade centralizado. Essas informações de integridade fornecem um instantâneo geral de integridade pontual dos serviços e nós distribuídos em vários nós no cluster, permitindo que você execute as ações corretivas necessárias. As APIs de consulta de integridade permitem consultar os eventos de integridade relatados para o subsistema de integridade. As APIs de consulta de integridade retornam os dados de integridade brutos armazenados no repositório de integridade ou os dados de integridade agregados e interpretados para uma entidade de cluster específica.
* **Repositório de imagens**: esse serviço fornece armazenamento e distribuição dos binários do aplicativo. Esse serviço fornece um armazenamento de arquivos distribuído simples em que os aplicativos são carregados e baixados do.

## <a name="hosting-subsystem"></a>Subsistema de hospedagem

O Gerenciador de cluster informa o subsistema de hospedagem (em execução em cada nó) quais serviços precisam ser gerenciados para um nó específico. Em seguida, o subsistema de hospedagem gerencia o ciclo de vida do aplicativo nesse nó. Ele interage com os componentes de confiabilidade e integridade para garantir que as réplicas sejam colocadas corretamente e estejam íntegras.

## <a name="communication-subsystem"></a>Subsistema de comunicação

Esse subsistema fornece mensagens confiáveis no cluster e descoberta de serviço por meio do serviço de cadastramento. O serviço de nomenclatura resolve nomes de serviço para um local no cluster e permite que os usuários gerenciem nomes e propriedades de serviço. Usando o serviço de nomenclatura, os clientes podem se comunicar com segurança com qualquer nó no cluster para resolver um nome de serviço e recuperar os metadados do serviço. Usando uma API de cliente de nomenclatura simples, os usuários de Service Fabric podem desenvolver serviços e clientes capazes de resolver o local de rede atual apesar do nó dinamismo ou o redimensionamento do cluster.

## <a name="testability-subsystem"></a>Subsistema de possibilidade de teste

A capacidade de teste é um conjunto de ferramentas projetadas especificamente para testar serviços criados em Service Fabric. As ferramentas permitem que um desenvolvedor induza com facilidade falhas significativas e execute cenários de teste para exercitar e validar os inúmeros Estados e as transições que um serviço experimentará durante seu tempo de vida, tudo de uma maneira controlada e segura. A capacidade de teste também fornece um mecanismo para executar testes mais longos que podem iterar por meio de várias falhas possíveis sem perder a disponibilidade. Isso fornece um ambiente de teste em produção.
