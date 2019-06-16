---
title: Arquitetura do Azure Service Fabric | Documentos da Microsoft
description: O Service Fabric é uma plataforma de sistemas distribuídos utilizada para criar aplicações escaláveis, fiáveis e facilmente geridas para a nuvem. Este artigo mostra a arquitetura do Service Fabric.
services: service-fabric
documentationcenter: .net
author: rishirsinha
manager: chackdan
editor: rishirsinha
ms.assetid: 6b554243-70cb-4c22-9b28-1a8b4703f45e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: rsinha
ms.openlocfilehash: a1e68e2e39ea6f1c8cf8669e2e02d8dacaf0f284
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097849"
---
# <a name="service-fabric-architecture"></a>Arquitetura do Service Fabric
Service Fabric baseia-se com subsistemas em camadas. Estes subsistemas permitem escrever aplicativos que são:

* Elevada disponibilidade
* Escalável
* Gerenciáveis
* Que pode ser testada

O diagrama seguinte mostra os principais subsistemas do Service Fabric.

![Diagrama da arquitetura do Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

Num sistema distribuído, a capacidade de comunicar de forma segura entre nós num cluster, é crucial. Na base da pilha é o subsistema de transporte, que fornece comunicação segura entre nós. Acima do transporte subsistema reside o subsistema de Federação, clusters diferentes nós numa única entidade (com o nome clusters) para que o Service Fabric pode detetar falhas, executar a eleição de coordenador e fornecem encaminhamento consistente. O subsistema de confiabilidade, colocado em camadas sobre o subsistema de Federação, é responsável pela fiabilidade dos serviços do Service Fabric através de mecanismos como replicação, gerenciamento de recursos e a ativação pós-falha. O subsistema de Federação também dão suporte para o subsistema de hospedagem e a ativação, que gerencia o ciclo de vida de um aplicativo num único nó. O subsistema de gestão gerencia o ciclo de vida de aplicativos e serviços. O subsistema de capacidade de teste ajuda os desenvolvedores de aplicativos testar os seus serviços por meio de falhas simulados antes e depois de implementar aplicações e serviços para ambientes de produção. O Service Fabric fornece a capacidade de resolver os locais de serviço através de seu subsistema de comunicação. Os modelos de programação de aplicativo expostos aos desenvolvedores estão em camadas sobre estes subsistemas juntamente com o modelo de aplicativo para permitir que as ferramentas.

## <a name="transport-subsystem"></a>Subsistema de transporte
O subsistema de transporte implementa um canal de comunicação de datagrama de ponto a ponto. Este canal é utilizado para comunicação dentro de clusters do service fabric e a comunicação entre o cluster do service fabric e os clientes. Ele oferece suporte à unidirecional e padrões de comunicação de solicitação-resposta, que fornece a base para a implementação de difusão e multicast na camada de Federação. O subsistema de transporte protege a comunicação utilizando X509 certificados ou de segurança do Windows. Esse subsistema é utilizado internamente pelo Service Fabric e não está acessível diretamente para os desenvolvedores para a programação de aplicativo.

## <a name="federation-subsystem"></a>Subsistema de Federação
Para justificar um conjunto de nós num sistema distribuído, tem de ter uma exibição consistente do sistema. O subsistema de Federação utiliza os primitivos da comunicação fornecidos pelo subsistema de transporte e stitches diversos nós num cluster unificado único que pode ponderar. Ele fornece os primitivos de sistemas distribuídos necessários para os outros subsistemas - deteção de falhas, eleição de coordenador e encaminhamento consistente. O subsistema de Federação é criado sobre tabelas de hash distribuída com um espaço de token de 128 bits. O subsistema de cria uma topologia em anel através de nós, com cada nó em anel que está a ser atribuído um subconjunto do espaço de token de propriedade. Para a deteção de falha, a camada usa um mecanismo de locação com base no coração bater e arbitragem. Também garante o subsistema de Federação por meio de associação intrincada e protocolos de saída apenas um único proprietário de um token de existente em qualquer altura. Isso fornece a eleição de coordenador e garantias de encaminhamento consistentes.

## <a name="reliability-subsystem"></a>Subsistema de confiabilidade
O subsistema de confiabilidade fornece o mecanismo para tornar o estado de um serviço de recursos de infraestrutura do serviço de elevada disponibilidade através da utilização do *replicador*, *Gestor de ativação pós-falha*, e *recursos Balanceador*.

* O Replicator garante que as alterações de estado na réplica do principal de serviço automaticamente serão replicadas em réplicas secundárias, manter a consistência entre as réplicas primárias e secundárias num conjunto de réplicas do serviço. O replicator é responsável pela gestão de quórum entre as réplicas no conjunto de réplicas. Ele interage com a unidade de ativação pós-falha ao obter a lista de operações para replicar e o agente de reconfiguração fornece-o com a configuração do conjunto de réplicas. Essa configuração indica quais réplicas as operações têm de ser replicadas. O Service Fabric fornece um replicador padrão chamado replicador de recursos de infraestrutura, o que pode ser utilizado pela API de modelo de programação para tornar o estado do serviço de elevada disponibilidade e fiáveis.
* O Gestor de ativação pós-falha, garante que quando nós são adicionados ou removidos do cluster, a carga é automaticamente redistribuída entre os nós disponíveis. Se um nó no cluster falhar, o cluster será automaticamente reconfigure as réplicas de serviço para manter a disponibilidade.
* O Gestor de recursos coloca as réplicas do serviço através de domínio de falha no cluster e garante que todas as unidades de ativação pós-falha estão operacionais. O Gestor de recursos também faz o balanceamento de recursos do serviço através do agrupamento partilhado subjacente de nós de cluster para alcançar a distribuição de carga uniforme ideal.

## <a name="management-subsystem"></a>Subsistema de gestão
O subsistema de gestão fornece o serviço de ponto-a-ponto e gerenciamento de ciclo de vida de aplicativos. Cmdlets do PowerShell e APIs administrativas permitem-lhe aprovisionar, implementar, corrigir, atualizar e anular o aprovisionamento aplicativos sem perda de disponibilidade. O subsistema de gestão realiza isso através dos seguintes serviços.

* **Gestor de clusters de**: Este é o principal serviço que interage com a ativação pós-falha Manager de confiabilidade para colocar as aplicações em nós com base nas restrições de posicionamento de serviço. O Gestor de recursos no subsistema de ativação pós-falha garante que as restrições nunca são quebradas. O Gestor de cluster gere o ciclo de vida dos aplicativos de aprovisionamento para anular o aprovisionamento. Ele se integra com o Gestor de estado de funcionamento para garantir que o de disponibilidade de aplicações não está perdida de uma perspectiva de estado de funcionamento semântico durante as atualizações.
* **Gestor de estado de funcionamento**: Este serviço permite a monitorização de estado de funcionamento de aplicações, serviços e entidades de cluster. Entidades de cluster (por exemplo, nós, as partições do serviço e as réplicas) podem relatar informações de estado de funcionamento, o que são agregadas, em seguida, para o arquivo de estado de funcionamento centralizado. Estas informações de estado de funcionamento fornecem um instantâneo do Estado de funcionamento geral ponto no tempo dos serviços e nós distribuídas por vários nós do cluster, que lhe permite efetuar quaisquer ações corretivas necessárias. Consulta de estado de funcionamento que APIs permitem-lhe consultar os eventos de estado de funcionamento relatados para o subsistema de estado de funcionamento. A consulta de estado de funcionamento APIs retornam os dados de estado de funcionamento não processados armazenados no arquivo de estado de funcionamento ou agregados, interpretado dados de estado de funcionamento de uma entidade de cluster específicos.
* **Imagem Store**: Este serviço fornece armazenamento e a distribuição dos binários do aplicativo. Este serviço fornece um armazenamento de ficheiros distribuído simples onde os aplicativos são carregados e transferidos a partir.

## <a name="hosting-subsystem"></a>Subsistema de alojamento
O Gestor de clusters de informa o subsistema de hospedagem (em execução em cada nó) quais os serviços que precisa para gerenciar para um determinado nó. O subsistema de alojamento, em seguida, gere o ciclo de vida do aplicativo nesse nó. Ele interage com os componentes de confiabilidade e o estado de funcionamento para garantir que as réplicas são colocadas corretamente e que estão em bom estadas.

## <a name="communication-subsystem"></a>Subsistema de comunicação
Esse subsistema fornece mensagens fiáveis dentro da deteção de cluster e o serviço através do serviço de nomenclatura. No serviço de nomes resolva nomes de serviço para uma localização do cluster e permite aos utilizadores gerir nomes de serviço e propriedades. Com o serviço de nomenclatura, os clientes podem comunicar de forma segura com qualquer nó do cluster para resolver um nome de serviço e obter os metadados de serviço. Utilizar um cliente de nomenclatura de API simple, os utilizadores do Service Fabric podem desenvolver serviços e clientes com capacidade de resolver a localização de rede atual, apesar de dinamismo de nó ou o novamente o dimensionamento do cluster.

## <a name="testability-subsystem"></a>Subsistema de capacidade de teste
Capacidade de teste é um conjunto de ferramentas projetadas especificamente para fins de teste de serviços criados no Service Fabric. As ferramentas permitem que o desenvolvedor facilmente induza falhas significativas e executar cenários de teste para testar e validar os diversos Estados e transições que um serviço irá ocorrer em toda a sua vida, tudo de forma controlada e segura. Capacidade de teste também fornece um mecanismo para executar testes de mais tempo do que é podem iterar por meio de vários possíveis falhas sem perder a disponibilidade. Isso fornece um ambiente de teste em produção.

