---
title: Eventos de Tecido de Serviço Azure
description: Conheça os eventos de Tecido de Serviço fornecidos fora da caixa para ajudá-lo a monitorizar o seu cluster Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451718"
---
# <a name="service-fabric-events"></a>Eventos do Service Fabric 

A plataforma Service Fabric escreve vários eventos estruturados para as principais atividades operacionais que acontecem dentro do seu cluster. Estes vão desde upgrades de cluster até decisões de colocação de réplicas. Cada evento que o Service Fabric expõe mapas a uma das seguintes entidades do cluster:
* Cluster
* Aplicação
* Serviço
* Partição
* Réplica 
* Contentor

Para ver uma lista completa de eventos expostos pela plataforma - [Lista de eventos de Tecido de Serviço](service-fabric-diagnostics-event-generation-operational.md).

Aqui estão alguns exemplos de cenários para os quais deve ver eventos no seu cluster. 
* Eventos de ciclo de vida do nó: à medida que os nós sobem, descem, escalam, reiniciam e são ativados/desativados, estes eventos serão expostos mostrando-lhe o que aconteceu, e ajudá-lo-á a identificar se há algo de errado com a própria máquina ou se houve uma API que foi chamada através de SF para modificar o estado de um nó.
* Upgrade do cluster: à medida que o seu cluster é atualizado (versão SF ou alteração de configuração), verá a atualização iniciada, rolará através de cada um dos seus Domínios de Upgrade e completará (ou reversão). 
* Atualizações de aplicações: tal como as atualizações de cluster, há um conjunto abrangente de eventos à medida que a atualização passa. Estes eventos podem ser úteis para entender quando uma atualização foi agendada, o estado atual de uma atualização, e a sequência geral de eventos. Isto é útil para olhar para trás para ver que upgrades foram lançados com sucesso ou se um reversão foi desencadeado.
* Aplicação/implantação de serviço/ eliminação: existem eventos para cada aplicação, serviço e contentor, sendo criados ou eliminados e úteis ao escalonar dentro ou fora, por exemplo, aumentando o número de réplicas
* Movimentos de partição (reconfiguração): sempre que uma divisória audada passa por uma reconfiguração (uma alteração no conjunto de réplicas), um evento é registado. Isto é útil se estiver a tentar perceber com que frequência o seu conjunto de réplicas de divisórias está a mudar ou a falhar, ou a rastrear qual o nó que estava a executar a sua réplica primária a qualquer momento.
* Eventos caos: ao utilizar o serviço Service Fabric's [Chaos,](service-fabric-controlled-chaos.md) verá eventos sempre que o serviço é iniciado ou parado, ou quando injeta uma falha no sistema.
* Eventos de Saúde: O Tecido de Serviço expõe eventos de saúde sempre que um aviso ou um relatório de saúde de erro é criado, ou uma entidade volta para um estado de saúde OK, ou um relatório de saúde expira. Estes eventos são muito úteis para acompanhar as estatísticas históricas de saúde de uma entidade. 

## <a name="how-to-access-events"></a>Como aceder a eventos

Existem algumas maneiras diferentes através das quais os eventos de Tecido de Serviço podem ser acedidos:
* Os eventos são registados através de canais padrão, como registos ETW/Windows Event e podem ser visualizados por qualquer ferramenta de monitorização que suporte estes registos, como registos do Monitor Azure. Por padrão, os clusters criados no portal têm diagnósticos ligados e têm o agente de diagnóstico do Windows Azure a enviar os eventos para o armazenamento de mesa seletiva, mas ainda precisa de o integrar com o seu recurso de análise de registo. Leia mais sobre a configuração do agente de diagnóstico sinuoso para modificar a configuração de diagnóstico do seu cluster para recolher mais registos ou contadores de desempenho e a integração de registos do [Monitor Azure](service-fabric-diagnostics-event-analysis-oms.md) [Azure Diagnostics agent](service-fabric-diagnostics-event-aggregation-wad.md)
* ApIs de descanso do serviço EventStore que lhe permitem consultar diretamente o cluster, ou através da Biblioteca de Clientes de Tecido de Serviço. Consulte [a Consulta EventStore APIs para eventos](service-fabric-diagnostics-eventstore-query.md)de cluster .

## <a name="next-steps"></a>Passos seguintes
* Mais informações sobre a monitorização do seu cluster - [Monitorização do cluster e da plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Saiba mais sobre o serviço EventStore - Visão geral do [serviço EventStore](service-fabric-diagnostics-eventstore.md)
