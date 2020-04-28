---
title: Introdução do Gestor de Recursos de Cluster de Tecidos de Serviço
description: Conheça o Gestor de Recursos de Cluster de Tecidos de Serviço, uma forma de gerir a orquestração dos serviços da sua aplicação.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75563331"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introdução do gestor de recursos de cluster Service Fabric
Gerir tradicionalmente sistemas de TI ou serviços online significava dedicar máquinas físicas ou virtuais específicas a esses serviços ou sistemas específicos. Os serviços foram arquitetados como níveis. Existiria um nível "web" e um nível de "dados" ou "armazenamento". As aplicações teriam um nível de mensagens onde os pedidos fluíam dentro e fora, bem como um conjunto de máquinas dedicadas ao cache. Cada nível ou tipo de carga de trabalho tinha máquinas específicas dedicadas a ele: a base de dados tinha algumas máquinas dedicadas a ele, os servidores web algumas. Se um determinado tipo de carga de trabalho fez com que as máquinas estivessem demasiado quentes, então adicionava mais máquinas com essa mesma configuração a esse nível. No entanto, nem todas as cargas de trabalho poderiam ser dimensionadas tão facilmente - especialmente com o nível de dados que normalmente substituiria as máquinas por máquinas maiores. Isso é fácil. Se uma máquina falhar, essa parte da aplicação global funcionou em menor capacidade até que a máquina pudesse ser restaurada. Ainda bastante fácil (se não necessariamente divertido).

Agora, no entanto, o mundo do serviço e da arquitetura de software mudou. É mais comum que as aplicações tenham adotado um design de escala. As aplicações de construção com contentores ou microserviços (ou ambos) são comuns. Agora, embora ainda tenhas apenas algumas máquinas, elas não estão a funcionar apenas com uma única instância de carga de trabalho. Podem até estar a executar várias cargas de trabalho diferentes ao mesmo tempo. Tem agora dezenas de diferentes tipos de serviços (nenhum deles consumindo o valor de recursos de uma máquina completa), talvez centenas de diferentes instâncias desses serviços. Cada instância nomeada tem um ou mais casos ou réplicas para alta disponibilidade (HA). Dependendo do tamanho dessas cargas de trabalho, e de quão ocupados estão, pode encontrar-se com centenas ou milhares de máquinas. 

De repente, gerir o seu ambiente não é tão simples como gerir algumas máquinas dedicadas a únicos tipos de cargas de trabalho. Os seus servidores são virtuais e já não têm nomes (afinal, mudou mentalidades de animais de [estimação para gado).](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) A configuração é menos sobre as máquinas e mais sobre os próprios serviços. Hardware que é dedicado a uma única instância de uma carga de trabalho é em grande parte uma coisa do passado. Os próprios serviços tornaram-se pequenos sistemas distribuídos que abrangem várias peças menores de hardware de mercadoria.

Como a sua aplicação já não é uma série de monólitos espalhados por vários níveis, agora tem muitas mais combinações para lidar. Quem decide que tipo de cargas de trabalho podem ser executadas em que hardware, ou quantos? Que cargas de trabalho funcionam bem no mesmo hardware, e que conflito? Quando uma máquina cai, como sabes o que estava a passar naquela máquina? Quem está encarregado de garantir que a carga de trabalho recomece a funcionar? Espera que a máquina (virtual?) volte ou as suas cargas de trabalho falham automaticamente noutras máquinas e continuam a funcionar? É necessária uma intervenção humana? E os melhoramentos neste ambiente?

Como desenvolvedores e operadores que lidam neste ambiente, vamos querer ajuda para gerir esta complexidade. Contratar e tentar esconder a complexidade com as pessoas provavelmente não é a resposta certa, então o que fazemos?

## <a name="introducing-orchestrators"></a>Apresentando orquestradores
Um "Orquestrador" é o termo geral para uma peça de software que ajuda os administradores a gerir este tipo de ambientes. Os orquestradores são os componentes que recebem pedidos como "Gostaria de cinco cópias deste serviço a funcionar no meu ambiente." Tentam fazer com que o ambiente corresponda ao estado desejado, aconteça o que acontecer.

Os orquestradores (não os humanos) são o que tomam medidas quando uma máquina falha ou uma carga de trabalho termina por alguma razão inesperada. A maioria dos orquestradores faz mais do que lidar com o fracasso. Outras funcionalidades que têm são a gestão de novas implementações, o tratamento de upgrades e a gestão do consumo e governação de recursos. Todos os orquestradores são fundamentalmente sobre manter algum estado de configuração desejado no ambiente. Queres ser capaz de dizer a um orquestrador o que queres e fazê-lo fazer o trabalho pesado. Aurora em cima de Mesos, Docker Datacenter/Docker Swarm, Kubernetes e Service Fabric são todos exemplos de orquestradores. Estes orquestradores estão a ser desenvolvidos ativamente para satisfazer as necessidades de cargas reais de carga de trabalho em ambientes produtivos. 

## <a name="orchestration-as-a-service"></a>Orquestração como serviço
O Cluster Resource Manager é o componente do sistema que lida com a orquestração em Tecido de Serviço. O trabalho do Gestor de Recursos cluster é dividido em três partes:

1. Regras de Aplicação
2. Otimizar o seu ambiente
3. Ajudando com outros processos

### <a name="what-it-isnt"></a>O que não é
Nas aplicações tradicionais nível N, há sempre um [Balancer de Carga.](https://en.wikipedia.org/wiki/Load_balancing_(computing)) Normalmente este era um Balancer de Carga de Rede (NLB) ou um Balancer de Carga de Aplicação (ALB) dependendo de onde se sentava na pilha de rede. Alguns equilibradores de carga são baseados em hardware como a oferta BigIP da F5, outros são baseados em software, como o NLB da Microsoft. Noutros ambientes, pode ver algo como HAProxy, nginx, Istio ou Enviado neste papel. Nestas arquiteturas, o trabalho de equilíbrio de carga é garantir que as cargas de trabalho apátridas recebam (aproximadamente) a mesma quantidade de trabalho. As estratégias para equilibrar a carga variaram. Alguns equilibradores enviariam cada chamada diferente para um servidor diferente. Outros forneceram a fixação/adesão da sessão. Os equilibradores mais avançados utilizam a estimativa real da carga ou reportam para encaminhar uma chamada com base no seu custo esperado e na carga atual da máquina.

Os equilibradores de rede ou os routers de mensagens tentaram garantir que o nível web/trabalhador se mantivesse aproximadamente equilibrado. As estratégias para equilibrar o nível de dados eram diferentes e dependiam do mecanismo de armazenamento de dados. O equilíbrio do nível de dados baseava-se em dados de sharding, cache, vistas geridas, procedimentos armazenados e outros mecanismos específicos da loja.

Embora algumas destas estratégias sejam interessantes, o Gestor de Recursos de Cluster de Tecidos de Serviço não é nada como um equilibrista de carga de rede ou uma cache. Um Balancer de carga de rede equilibra as extremidades dianteiras espalhando o tráfego através das extremidades dianteiras. O Gestor de Recursos de Cluster de Tecidos de Serviço tem uma estratégia diferente. Fundamentalmente, o Service Fabric transfere *os serviços* para onde fazem mais sentido, esperando que o tráfego ou a carga sigam. Por exemplo, pode mover os serviços para nós que estão atualmente frios porque os serviços que lá estão não estão a fazer muito trabalho. Os nódosos podem estar frios, uma vez que os serviços presentes foram apagados ou deslocados para outro local. Como outro exemplo, o Cluster Resource Manager também poderia mover um serviço para longe de uma máquina. Talvez a máquina esteja prestes a ser atualizada, ou esteja sobrecarregada devido a um aumento no consumo pelos serviços que nela estão em funcionamento. Em alternativa, os requisitos de recursos do serviço podem ter aumentado. Como resultado, não há recursos suficientes nesta máquina para continuar a executá-la. 

Uma vez que o Cluster Resource Manager é responsável pela movimentação de serviços, contém um conjunto de funcionalidades diferente em comparação com o que se encontra num equilibrista de carga de rede. Isto porque os equilibradores de carga de rede fornecem tráfego de rede para onde os serviços já estão, mesmo que essa localização não seja ideal para executar o próprio serviço. O Gestor de Recursos de Cluster de Tecidos de Serviço emprega estratégias fundamentalmente diferentes para garantir que os recursos do cluster sejam utilizados de forma eficiente.

## <a name="next-steps"></a>Passos seguintes
- Para obter informações sobre a arquitetura e fluxo de informação dentro do Cluster Resource Manager, consulte [este artigo](service-fabric-cluster-resource-manager-architecture.md)
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre métricas, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)
- Para mais informações sobre a configuração de serviços, [Saiba sobre a configuração de Serviços](service-fabric-cluster-resource-manager-configure-services.md)
- As métricas são como o Manjedoura de Recursos de Cluster de Tecido de Serviço gere o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
- O Cluster Resource Manager trabalha com as capacidades de gestão do Service Fabric. Para saber mais sobre essa integração, leia [este artigo](service-fabric-cluster-resource-manager-management-integration.md)
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre [a carga](service-fabric-cluster-resource-manager-balancing.md) de equilíbrio
