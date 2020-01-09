---
title: Introdução ao Gerenciador de recursos de Cluster Service Fabric
description: Saiba mais sobre o Gerenciador de recursos de Cluster Service Fabric, uma maneira de gerenciar a orquestração dos serviços do seu aplicativo.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563331"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introdução ao Gerenciador de recursos de Cluster Service Fabric
Tradicionalmente, gerenciar sistemas de ti ou serviços online significava máquinas físicas ou virtuais específicas para esses serviços ou sistemas específicos. Os serviços foram arquitetados como camadas. Haveria uma camada "Web" e uma camada de "dados" ou "armazenamento". Os aplicativos teriam uma camada de mensagens em que as solicitações fluiram para dentro e para fora, bem como um conjunto de computadores dedicados ao Caching. Cada camada ou tipo de carga de trabalho tinha máquinas específicas dedicadas a ela: o banco de dados tem alguns computadores dedicados a ele, os servidores Web alguns. Se um tipo específico de carga de trabalho fez com que as máquinas em que ele estava em execução muito quente, você adicionou mais computadores com essa mesma configuração a essa camada. No entanto, nem todas as cargas de trabalho podem ser expandidas com tanta facilidade, particularmente com a camada de dados que você normalmente substituiria máquinas com máquinas maiores. Isso é fácil. Se um computador falhou, essa parte do aplicativo geral foi executada com menor capacidade até que a máquina pudesse ser restaurada. Ainda é bem fácil (se não necessariamente divertido).

Agora, no entanto, o mundo da arquitetura de serviço e software mudou. É mais comum que os aplicativos tenham adotado um design de expansão. A criação de aplicativos com contêineres ou microserviços (ou ambos) é comum. Agora, embora você ainda possa ter apenas alguns computadores, eles não estão executando apenas uma única instância de uma carga de trabalho. Eles podem até mesmo estar executando várias cargas de trabalho diferentes ao mesmo tempo. Agora você tem dezenas de diferentes tipos de serviços (nenhum consumindo um grande valor de recursos do computador), talvez centenas de instâncias diferentes desses serviços. Cada instância nomeada tem uma ou mais instâncias ou réplicas para HA (alta disponibilidade). Dependendo dos tamanhos dessas cargas de trabalho e de como elas estão ocupadas, você pode se encontrar com centenas ou milhares de computadores. 

O gerenciamento inesperado do ambiente não é tão simples quanto o gerenciamento de alguns computadores dedicados a tipos únicos de cargas de trabalho. Seus servidores são virtuais e não têm mais nomes (você mudou mentalmente de [pets para burro](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) depois de todos). A configuração é menos sobre as máquinas e mais sobre os próprios serviços. O hardware dedicado a uma única instância de uma carga de trabalho é basicamente uma coisa do passado. Os próprios serviços se tornaram pequenos sistemas distribuídos que abrangem várias partes menores de hardware de mercadoria.

Como seu aplicativo não é mais uma série de monolíticos espalhados por várias camadas, agora você tem muitas combinações com as quais lidar. Quem decide quais tipos de cargas de trabalho podem ser executados em qual hardware ou quantos? Quais cargas de trabalho funcionam bem no mesmo hardware e quais conflitos? Quando um computador fica inativo como você sabe o que estava sendo executado nesse computador? Quem é responsável por garantir que a carga de trabalho comece a ser executada novamente? Você espera que o computador (virtual?) Volte ou que suas cargas de trabalho façam failover automaticamente para outros computadores e continuem em execução? A intervenção humana é necessária? E quanto às atualizações nesse ambiente?

Como desenvolvedores e operadores lidando com esse ambiente, vamos querer ajudar a gerenciar essa complexidade. Um onda de contratação e tentar ocultar a complexidade com as pessoas provavelmente não é a resposta certa, então o que fazemos?

## <a name="introducing-orchestrators"></a>Introdução aos orquestradores
Um "orquestrador" é o termo geral para uma parte do software que ajuda os administradores a gerenciar esses tipos de ambientes. Orquestradores são os componentes que recebem solicitações como "Eu gostaria de cinco cópias desse serviço em execução no meu ambiente". Eles tentam fazer com que o ambiente corresponda ao estado desejado, independentemente do que acontece.

Orquestradores (não humanos) são o que agir quando um computador falha ou uma carga de trabalho é encerrada por algum motivo inesperado. A maioria dos orquestradores faz mais do que apenas lidar com falhas. Outros recursos que eles têm estão gerenciando novas implantações, tratando de atualizações e lidando com o consumo de recursos e governança. Todos os orquestradores são basicamente sobre a manutenção de algum estado desejado de configuração no ambiente. Você deseja ser capaz de informar a um orquestrador o que você deseja e fazer com que ele faça o trabalho pesado. A Aurora em cima de mesos, Docker datacenter/Docker Swarm, kubernetes e Service Fabric são exemplos de orquestradores. Esses orquestradores estão sendo ativamente desenvolvidos para atender às necessidades de cargas de trabalho reais em ambientes de produção. 

## <a name="orchestration-as-a-service"></a>Orquestração como um serviço
O Gerenciador de recursos de cluster é o componente do sistema que manipula a orquestração no Service Fabric. O trabalho do Gerenciador de recursos de cluster é dividido em três partes:

1. Impondo regras
2. Otimizando seu ambiente
3. Ajudando com outros processos

### <a name="what-it-isnt"></a>O que não é
Em aplicativos de N camadas tradicionais, sempre há um [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Normalmente, isso era um NLB (Load Balancer de rede) ou um Load Balancer de aplicativo (ALB), dependendo de onde ele se SAT na pilha de rede. Alguns balanceadores de carga são baseados em hardware como a oferta se BigIP BigIP, outros são baseados em software, como o NLB da Microsoft. Em outros ambientes, você pode ver algo como HAProxy, Nginx, İSTİO ou Envoy nessa função. Nessas arquiteturas, o trabalho de balanceamento de carga é garantir que cargas de trabalho sem estado recebam (aproximadamente) a mesma quantidade de trabalhos. Estratégias para balancear a carga variadas. Alguns balanceadores enviariam cada chamada diferente para um servidor diferente. Outros forneceram fixação/adesão de sessão. Os balanceadores mais avançados usam estimativa ou relatório de carga real para rotear uma chamada com base no custo esperado e na carga atual do computador.

Balanceadores de rede ou roteadores de mensagem tentaram garantir que a camada Web/de trabalho permanecesse aproximadamente equilibrada. As estratégias para balancear a camada de dados foram diferentes e dependem do mecanismo de armazenamento de dados. O balanceamento da camada de dados dependia da fragmentação de dados, cache, exibições gerenciadas, procedimentos armazenados e outros mecanismos específicos do armazenamento.

Embora algumas dessas estratégias sejam interessantes, o Gerenciador de recursos de Cluster Service Fabric não é algo como um balanceador de carga de rede ou um cache. Um Load Balancer de rede equilibra os front-ends distribuindo o tráfego entre front-ends. O Gerenciador de recursos de Cluster Service Fabric tem uma estratégia diferente. Fundamentalmente, Service Fabric move os *Serviços* para onde eles fazem mais sentido, esperando o tráfego ou a carga a seguir. Por exemplo, ele pode mover serviços para nós que estão frios no momento porque os serviços que não estão fazendo muito trabalho. Os nós podem ficar frios, pois os serviços que estavam presentes foram excluídos ou movidos em outro lugar. Como outro exemplo, o Gerenciador de recursos de cluster também pode mover um serviço para fora de um computador. Talvez o computador esteja prestes a ser atualizado ou esteja sobrecarregado devido a um pico no consumo pelos serviços em execução. Como alternativa, os requisitos de recursos do serviço podem ter aumentado. Como resultado, não há recursos suficientes neste computador para continuar a executá-lo. 

Como o Gerenciador de recursos de cluster é responsável por mover os serviços, ele contém um conjunto de recursos diferente em comparação com o que você encontraria em um balanceador de carga de rede. Isso ocorre porque os balanceadores de carga de rede entregam o tráfego de rede para onde os serviços já estão, mesmo que esse local não seja ideal para executar o próprio serviço. O Service Fabric cluster Resource Manager emprega estratégias fundamentalmente diferentes para garantir que os recursos no cluster sejam utilizados com eficiência.

## <a name="next-steps"></a>Passos seguintes
- Para obter informações sobre a arquitetura e o fluxo de informações no Gerenciador de recursos de cluster, confira [Este artigo](service-fabric-cluster-resource-manager-architecture.md)
- O Gerenciador de recursos de cluster tem muitas opções para descrever o cluster. Para saber mais sobre as métricas, confira este artigo sobre como [descrever um cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Para obter mais informações sobre como configurar serviços, [saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
- As métricas são como o Gerenciador de recursos de Cluster Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [Este artigo](service-fabric-cluster-resource-manager-metrics.md)
- O Gerenciador de recursos de cluster funciona com os recursos de gerenciamento de Service Fabric. Para saber mais sobre essa integração, leia [Este artigo](service-fabric-cluster-resource-manager-management-integration.md)
- Para saber como o Gerenciador de recursos de cluster gerencia e equilibra a carga no cluster, confira o artigo sobre [balanceamento](service-fabric-cluster-resource-manager-balancing.md) de carga
