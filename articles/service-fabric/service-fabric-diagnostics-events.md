---
title: Eventos de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Saiba mais sobre os eventos do Service Fabric vêm prontos para o ajudar a monitorizar o seu cluster do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: c4ce8e01b1dc819453610f68d044cc268e27eed7
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242756"
---
# <a name="service-fabric-events"></a>Eventos do Service Fabric 

A plataforma do Service Fabric escreve vários eventos estruturados para as atividades operacionais chave a acontecer no seu cluster. Estas variam desde atualizações de cluster para decisões de colocação de réplica. Cada evento que o Service Fabric expõe é mapeado para uma das seguintes entidades do cluster:
* Cluster
* Aplicação
* Serviço
* Partição
* Réplica 
* Contentor

Para ver uma lista completa dos eventos expostos pela plataforma - [eventos da lista de Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Aqui estão alguns exemplos de cenários, verá os eventos para no seu cluster. 
* Eventos de ciclo de vida de nó: como nós surgem, vão para baixo, dimensionar de entrada/saída, reiniciarem e são ativada/desativada, estes eventos vão ser expostos mostrando a o que aconteceu e ajudar a identificar se existe um problema com a própria máquina ou se tiver ocorrido uma API que foi chamado por meio de SF para modificar o estado de um nó.
* Atualização do cluster: como o cluster é atualizado (SF versão ou configuração de alteração), verá a atualização iniciar, implementar através de cada um dos seus domínios de atualização e concluir (ou reversão). 
* As atualizações de aplicações: tal como as atualizações de cluster, há um conjunto abrangente de eventos como a atualização é aplicada nos. Esses eventos podem ser útil para entender quando uma atualização foi agendada, o estado atual de uma atualização e a sequência geral de eventos. Isto é útil para voltando para ver o que as atualizações têm foi implementadas com êxito ou se foi acionada uma reversão.
* Implementação de aplicações/serviços / eliminação: há eventos para cada aplicação, o serviço e o contentor, que está a ser criada ou eliminada e útil quando o dimensionamento dentro ou para fora por exemplo, aumento do número de réplicas
* Partição move (reconfiguração): sempre que uma partição com monitoração de estado fica através de uma reconfiguração (uma alteração no conjunto de réplica), um evento é registado. Isto é útil se estiver a tentar compreender a frequência com que o seu conjunto de réplicas de partição é alterar ou efetuar a ativação pós-falha ou controlar o nó que estava a ser executada a réplica primária a qualquer momento no tempo.
* Eventos de Chaos: ao utilizar o Service Fabric [Chaos](service-fabric-controlled-chaos.md) serviço, verá eventos sempre que o serviço é iniciado ou parado, ou quando ele injeta uma falha no sistema.
* Eventos de estado de funcionamento: Service Fabric expõe eventos de estado de funcionamento sempre que é criado um aviso ou um relatório de estado de funcionamento de erro, ou uma entidade tem a ver com um Estado de funcionamento OK ou um relatório de estado de funcionamento expira. Esses eventos são muito úteis para controlar as estatísticas de históricos de estado de funcionamento de uma entidade. 

## <a name="how-to-access-events"></a>Como os eventos de acesso

Existem algumas formas diferentes através do qual os eventos do Service Fabric podem ser acessados:
* Os eventos são registados através de canais standard como logs de eventos do ETW/Windows e podem ser visualizados por qualquer ferramenta de monitoramento que suporta estes, como registos do Azure Monitor. Por predefinição, os clusters criados no portal do tem o diagnóstico ativado e têm o agente de diagnóstico do Windows Azure enviar eventos para o armazenamento de tabelas do Azure, mas ainda tem de integrar isso com o seu recurso do log analytics. Leia mais sobre como configurar o [agente do diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) para modificar a configuração de diagnósticos do seu cluster para recolher mais registos ou contadores de desempenho e a [integração de registos do Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* APIs de Rest do serviço de EventStore que permitem-lhe consultar o cluster diretamente ou por meio da biblioteca de cliente de recursos de infraestrutura do serviço. Ver [APIs de EventStore de consulta para eventos de cluster](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Passos Seguintes
* Obter mais informações na monitorização do seu cluster - [monitorizar o cluster e a plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Saiba mais sobre o serviço de EventStore - [descrição geral do serviço de EventStore](service-fabric-diagnostics-eventstore.md)
