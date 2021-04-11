---
title: Eventos de tecido de serviço Azure
description: Conheça os eventos de Tecido de Serviço fornecidos fora da caixa para ajudá-lo a monitorizar o seu cluster de Tecido de Serviço Azure.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 581bb3f16941fce660e0f464ed8ea9389b034b12
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628817"
---
# <a name="service-fabric-events"></a>Eventos do Service Fabric 

A plataforma Service Fabric escreve vários eventos estruturados para as principais atividades operacionais que ocorram dentro do seu cluster. Estes vão desde upgrades de clusters a decisões de colocação de réplicas. Cada evento que a Service Fabric expõe mapas a uma das seguintes entidades do cluster:
* Cluster
* Aplicação
* Serviço
* Partição
* Réplica 
* Contentor

Para ver uma lista completa de eventos expostos pela plataforma - [Lista de eventos de Tecido de Serviço](service-fabric-diagnostics-event-generation-operational.md).

Aqui estão alguns exemplos de cenários para os quais deve ver eventos no seu cluster. 
* Eventos de ciclo de vida de nó: à medida que os nós sobem, descem, escalam para dentro/para fora, reiniciam e são ativados/desativados, estes eventos serão expostos mostrando-lhe o que aconteceu, e ajudá-lo a identificar se há algo de errado com a própria máquina ou se houve uma API que foi chamada através de SF para modificar o estado de um nó.
* Atualização do cluster: à medida que o seu cluster é atualizado (versão SF ou alteração de configuração), verá a atualização iniciar, passar por cada um dos seus Domínios de Atualização e completar (ou reverter). 
* Atualizações de aplicações: tal como as atualizações do cluster, há um conjunto abrangente de eventos à medida que a atualização avança. Estes eventos podem ser úteis para entender quando uma atualização foi programada, o estado atual de uma atualização, e a sequência geral de eventos. Isto é útil para olhar para trás para ver que atualizações foram lançadas com sucesso ou se um rollback foi desencadeado.
* Aplicação/Implantação/Eliminação de Serviço: há eventos para cada aplicação, serviço e contentor, sendo criados ou eliminados e úteis ao escalar dentro ou fora, por exemplo, aumentar o número de réplicas
* Movimentos de partição (reconfiguração): sempre que uma partição imponente passa por uma reconfiguração (uma alteração no conjunto de réplica), um evento é registado. Isto é útil se estiver a tentar perceber com que frequência o seu conjunto de réplicas de partição está a mudar ou a falhar, ou a rastrear qual o nó que estava a executar a sua réplica primária a qualquer momento.
* Eventos caos: ao utilizar o serviço [Desemaris](service-fabric-controlled-chaos.md) do Service Fabric, verá eventos sempre que o serviço é iniciado ou parado, ou quando injeta uma falha no sistema.
* Eventos de Saúde: O Tecido de Serviço expõe eventos de saúde sempre que um relatório de saúde de Advertência ou erro é criado, ou uma entidade volta para um estado de saúde OK, ou um relatório de saúde expira. Estes eventos são muito úteis para rastrear estatísticas históricas de saúde para uma entidade. 

## <a name="how-to-access-events"></a>Como aceder a eventos

Existem algumas maneiras diferentes através das quais os eventos de Service Fabric podem ser acedidos:
* Os eventos são registados através de canais padrão, tais como registos de Eventos ETW/Windows e podem ser visualizados por qualquer ferramenta de monitorização que suporte estes, tais como registos do Azure Monitor. Por padrão, os clusters criados no portal têm diagnósticos ligados e têm o agente de diagnóstico Do Windows Azure a enviar os eventos para o armazenamento da mesa Azure, mas ainda assim precisa de integrá-lo com o seu recurso de análise de log. Leia mais sobre a configuração do [agente Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) para modificar a configuração de diagnóstico do seu cluster para recolher mais registos ou contadores de desempenho e a integração de registos do [Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* As APIs de Repouso do serviço EventStore que lhe permitem consultar o cluster diretamente, ou através da Biblioteca do Cliente do Tecido de Serviço. Consulte [as APIs da Loja de Eventos de Consulta para eventos de cluster](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Passos seguintes
* Mais informações sobre a monitorização do seu cluster - [Monitorização do cluster e da plataforma.](service-fabric-diagnostics-event-generation-infra.md)
* Saiba mais sobre o serviço EventStore - [EventStore service overview](service-fabric-diagnostics-eventstore.md)
