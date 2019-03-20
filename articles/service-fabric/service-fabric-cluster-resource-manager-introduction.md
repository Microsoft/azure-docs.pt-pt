---
title: Apresentando o Gestor de recursos de Cluster do Service Fabric | Documentos da Microsoft
description: Uma introdução para o Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 75aa960ff060d74d0a579b475e4334402992b3c3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903370"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Apresentando o Gestor de recursos de cluster do Service Fabric
Tradicionalmente, gerenciamento de sistemas de TI ou serviços online significava dedicar máquinas virtuais ou físicas específicas para esses serviços específicos ou sistemas. Foram arquitetados como camadas de serviços. Deveria haver uma camada de "web" e uma camada de "dados" ou "armazenamento". Aplicativos teria uma camada de mensagens em que pedidos fluísse entrada e saída, bem como um conjunto de máquinas dedicadas a colocação em cache. Cada camada ou o tipo de carga de trabalho tinha máquinas específicas dedicadas a ele: a base de dados tem duas máquinas dedicadas a ele, os servidores web alguns. Se um determinado tipo de carga de trabalho causado máquinas era para executar muito quente, em seguida, adicionou mais máquinas com essa mesma configuração para essa camada. No entanto, nem todas as cargas de trabalho podem ser dimensionadas tão facilmente – particularmente com a camada de dados, normalmente, poderia substituir máquinas com máquinas maiores. Fácil. Se uma máquina falhou, essa parte do aplicativo global foi executada atingiu o limite inferior, até que a máquina pode ser restaurada. Ainda assim relativamente fácil (se não necessariamente divertido).

Agora no entanto o mundo da arquitetura de serviço e o software foi alterado. É mais comum que aplicativos ADOTARAM um design de escalamento horizontal. É comum a criação de aplicativos com microsserviços ou contentores (ou ambos). Agora, embora ainda possa ter apenas algumas máquinas, eles não estiverem apenas uma única instância de uma carga de trabalho. Eles poderão ainda estar em execução várias cargas de trabalho diferentes ao mesmo tempo. Agora tem dezenas de diferentes tipos de serviços (nenhum valor de uma máquina completo de recursos de consumo), talvez centenas de diferentes instâncias desses serviços. Cada instância nomeada tem um ou mais instâncias ou de réplicas para elevada disponibilidade (HA). Consoante os tamanhos dessas cargas de trabalho e o estado de disponibilidade são, talvez tenha com centenas ou milhares de máquinas. 

Não é tão simples como gerir algumas máquinas dedicadas a únicos tipos de cargas de trabalho, de repente, gestão do seu ambiente. Os servidores são virtuais e deixará de ter nomes (tiver mudado mindsets partir [animais de estimação cattle](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) Afinal de contas). A configuração é menos com as máquinas e muito mais sobre os serviços. Hardware dedicado para uma única instância de uma carga de trabalho é basicamente uma coisa do passado. Próprios serviços se tornaram pequenos sistemas distribuídos que abrangem várias partes menores de hardware do produto.

Uma vez que a aplicação já não é uma série de monolitos espalhadas em vários escalões, agora tem muitas combinações de mais de lidar com. Quem decide o que tipos de cargas de trabalho podem ser executados em qual hardware, ou quantos? Que cargas de trabalho funcionam bem no mesmo hardware, e que entram em conflito? Quando uma máquina fica para baixo como fazer sabe o que estava em execução lá nessa máquina? Quem é responsável por certificar-se de que essa carga de trabalho começa a ser executado novamente? Aguardar para a máquina (virtual)? voltar ou suas cargas de trabalho automaticamente a ativação pós-falha para outras máquinas e manter em execução? É necessária intervenção humana necessária? E sobre as atualizações neste ambiente?

Como os programadores e operadores lidar neste ambiente, vamos precisa de ajuda a gerenciar essa complexidade. Acabo de sair de contratação e a tentativa de ocultar a complexidade com pessoas provavelmente não é a resposta certa, então, o que fazemos?

## <a name="introducing-orchestrators"></a>Introdução ao orquestradores
Um "Orchestrator" é o termo geral para uma parte do software que ajuda a administradores que gerenciam esses tipos de ambientes. Orquestradores são os componentes que faça pedidos, como "Eu gostaria de cinco cópias deste serviço em execução no meu ambiente." Eles tentam fazer o ambiente com o mesmo estado pretendido, independentemente do que acontece.

Orquestradores (não humanos) são o que preciso ação quando ocorre uma falha de uma máquina ou uma carga de trabalho é encerrado por algum motivo inesperado. A maioria dos orquestradores mais do que apenas tratam de falha. Outros recursos que tenham estão a gerir novas Implantações, atualizações de manipulação e lidar com consumo de recursos e governação. Todos os orquestradores são fundamentalmente sobre como manter algum estado pretendido de configuração no ambiente. Quer ser capaz de dizer um orquestrador que pretende e tê-lo a fazer o trabalho pesado. Aurora com base no Mesos, Docker Datacenter/Docker Swarm, Kubernetes e o Service Fabric são todos os exemplos de orquestradores. Estes orquestradores estão sendo desenvolvidos ativamente para satisfazer as necessidades das cargas de trabalho real em ambientes de produção. 

## <a name="orchestration-as-a-service"></a>Orquestração como um serviço
O Gestor de recursos do Cluster é o componente de sistema que trata da orquestração no Service Fabric. Tarefa do Gestor de recursos de Cluster é dividida em três partes:

1. Imposição de regras
2. Otimizando seu ambiente
3. Ajudando com outros processos

### <a name="what-it-isnt"></a>O que não é
Em aplicativos de camada N tradicionais, há sempre uma [Balanceador de carga](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Normalmente, esse era um balanceador de carga de rede (NLB) ou um balanceador de carga de aplicação (ALB) dependendo de onde Sentei na pilha da rede. Alguns balanceadores de carga são baseadas em Hardware como oferta de BigIP da F5, outras são baseados em software, como o Microsoft do NLB. Em outros ambientes, pode ver algo como HAProxy, nginx, Istio ou o Envoy nesta função. Estas arquiteturas, o trabalho de balanceamento de carga é Certifique-se de que as cargas de trabalho sem monitorização de estado (aproximadamente) recebem a mesma quantidade de trabalho. Estratégias para o balanceamento de carga variados. Alguns balanceadores enviaria cada chamada diferente para um servidor diferente. Outras fornecidas afixar/persistência da sessão. Balanceadores de mais avançados utilizam estimativa da carga real ou de relatório para encaminhar uma chamada com base no respetivo custo esperado e a carga atual da máquina.

Balanceadores de rede ou routers de mensagem tentaram Certifique-se de que a camada web/trabalho permaneceu equilibrada mais ou menos. Estratégias para o balanceamento de camada de dados eram diferentes e dependentes no mecanismo de armazenamento de dados. A camada de dados de balanceamento dependia de fragmentação de dados, colocação em cache, geridas vistas, procedimentos armazenados e outros mecanismos de arquivo específico.

Embora algumas dessas estratégias são interessantes, o Gestor de recursos de Cluster do Service Fabric não é nada como um balanceador de carga de rede ou de uma cache. Faz o balanceamento de um balanceador de carga de rede de front-ends ao propagar o tráfego entre front-ends. O Gestor de recursos de Cluster do Service Fabric tem uma estratégia diferente. Fundamentalmente, o Service Fabric move *serviços* para onde faz mais sentido, esperando tráfego ou carregar a seguir. Por exemplo, ele poderá mover serviços para nós que estão atualmente frio porque os serviços que existem não fazendo muito trabalho. Os nós podem ser frios, uma vez que os serviços que estavam presentes foram eliminados ou movidos em outro lugar. Como outro exemplo, o Gestor de recursos de Cluster também pode transferir um serviço para fora de uma máquina. Talvez a máquina está prestes a ser atualizados, ou está sobrecarregado devido a um pico no consumo por serviços em execução no mesmo. Em alternativa, podem ter aumentado os requisitos de recursos do serviço. Como resultado não existem recursos suficientes neste computador para continuar a executá-lo. 

Uma vez que o Gestor de recursos do Cluster é responsável por transferência dos serviços em todo, contém um conjunto de funcionalidades diferentes em comparação comparado o que encontraria num Balanceador de carga de rede. Isso ocorre porque os balanceadores de carga de rede enviar tráfego de rede para onde serviços já estão, mesmo que essa localização não é ideal para executar o serviço em si. O Gestor de recursos de Cluster do Service Fabric emprega fundamentalmente diferentes estratégias de garantir que os recursos do cluster são utilizados de forma eficiente.

## <a name="next-steps"></a>Passos Seguintes
- Para obter informações sobre o fluxo de arquitetura e as informações no Gestor de recursos do Cluster, verifique [neste artigo](service-fabric-cluster-resource-manager-architecture.md)
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para obter mais informações sobre as métricas, visite este artigo no [descrever um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Para obter mais informações sobre como configurar os serviços, [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
- As métricas são como o Service Fabric Cluster Resource Manager gere o consumo e a capacidade do cluster. Para saber mais sobre métricas e como configurá-las check-out [neste artigo](service-fabric-cluster-resource-manager-metrics.md)
- O Gestor de recursos de Cluster funciona com capacidades de gestão do Service Fabric. Para obter mais informações sobre essa integração, leia [neste artigo](service-fabric-cluster-resource-manager-management-integration.md)
- Para obter informações sobre como o Gestor de recursos de Cluster gere e faz o balanceamento de carga no cluster, consulte o artigo no [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
