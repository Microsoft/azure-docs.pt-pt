---
title: Eventos de Service Fabric do Azure
description: Saiba mais sobre os Service Fabric eventos fornecidos para ajudá-lo a monitorar seu cluster de Service Fabric do Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451718"
---
# <a name="service-fabric-events"></a>Eventos do Service Fabric 

A plataforma Service Fabric grava vários eventos estruturados para as principais atividades operacionais que ocorrem em seu cluster. Elas variam desde atualizações de cluster até decisões de posicionamento de réplicas. Cada evento que Service Fabric expõe é mapeado para uma das seguintes entidades no cluster:
* Cluster
* Candidatura
* Serviço
* Partição
* Réplica 
* Contentor

Para ver uma lista completa de eventos expostos pela [lista de plataformas de eventos de Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Aqui estão alguns exemplos de cenários para os quais você deve ver eventos em seu cluster. 
* Eventos de ciclo de vida do nó: como nós são exibidos, ficam inativos, reduzem/diminuem, reiniciam e são ativados/desativados, esses eventos serão expostos mostrando o que aconteceu e o ajudarão a identificar se há algo errado com a própria máquina ou se havia uma API que foi chamada por meio do it para modificar o status de um nó.
* Atualização do cluster: como o cluster é atualizado (versão ou alteração de configuração do it), você verá a atualização iniciar, passar por cada um dos seus domínios de atualização e concluir (ou reverter). 
* Atualizações de aplicativos: assim como as atualizações de cluster, há um conjunto abrangente de eventos conforme a atualização se acumula. Esses eventos podem ser úteis para entender quando uma atualização foi agendada, o estado atual de uma atualização e a sequência geral de eventos. Isso é útil para voltar a ver quais atualizações foram distribuídas com êxito ou se uma reversão foi disparada.
* Implantação/exclusão de aplicativo/serviço: há eventos para cada aplicativo, serviço e contêiner, sendo criados ou excluídos e úteis ao dimensionar horizontalmente ou horizontalmente, por exemplo, aumentar o número de réplicas
* Movimentações de partição (reconfiguração): sempre que uma partição com estado passa por uma reconfiguração (uma alteração no conjunto de réplicas), um evento é registrado em log. Isso será útil se você estiver tentando entender com que frequência seu conjunto de réplicas de partição está mudando ou fazendo failover, ou então controlar qual nó estava executando a réplica primária em qualquer ponto no tempo.
* Eventos de caos: ao usar o serviço de [caos](service-fabric-controlled-chaos.md) do Service Fabric, você verá eventos toda vez que o serviço for iniciado ou interrompido, ou quando ele injetar uma falha no sistema.
* Eventos de integridade: Service Fabric expõe eventos de integridade sempre que um relatório de aviso ou de integridade de erro é criado, ou uma entidade retorna para um estado de integridade OK ou um relatório de integridade expira. Esses eventos são muito úteis para acompanhar as estatísticas de integridade históricas de uma entidade. 

## <a name="how-to-access-events"></a>Como acessar eventos

Há algumas maneiras diferentes pelas quais Service Fabric eventos podem ser acessados:
* Os eventos são registrados por meio de canais padrão, como logs de eventos ETW/Windows e podem ser visualizados por qualquer ferramenta de monitoramento que ofereça suporte a esses logs de Azure Monitor. Por padrão, os clusters criados no portal têm o diagnóstico ativado e têm o agente de diagnóstico do Windows Azure enviando os eventos para o armazenamento de tabelas do Azure, mas você ainda precisa integrá-lo com o recurso do log Analytics. Leia mais sobre como configurar o [agente de diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) para modificar a configuração de diagnóstico do cluster para selecionar mais logs ou contadores de desempenho e a integração de logs de [Azure monitor](service-fabric-diagnostics-event-analysis-oms.md)
* As APIs REST do serviço EventStore que permitem consultar o cluster diretamente ou por meio da biblioteca de cliente Service Fabric. Consulte [APIs do EventStore de consulta para eventos de cluster](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Passos seguintes
* Mais informações sobre como monitorar seu cluster [monitorando o cluster e a plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Saiba mais sobre o serviço EventStore – [visão geral do serviço EventStore](service-fabric-diagnostics-eventstore.md)
