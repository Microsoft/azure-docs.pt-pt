---
title: Apresentando o Gestor de Recursos do Cluster de Tecido de Serviço
description: Conheça o Service Fabric Cluster Resource Manager, uma forma de gerir a orquestração dos serviços da sua aplicação.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75563331"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Apresentando o gestor de recursos do cluster de tecido de serviço
Gerir sistemas de TI ou serviços online significava dedicar máquinas físicas ou virtuais específicas a esses serviços ou sistemas específicos. Os serviços foram arquitetados como escalões. Haveria um nível "web" e um nível de "dados" ou "armazenamento". As aplicações teriam um nível de mensagens onde os pedidos fluíam para dentro e para fora, bem como um conjunto de máquinas dedicadas ao caching. Cada nível ou tipo de carga de trabalho tinha máquinas específicas dedicadas a ele: a base de dados tem algumas máquinas dedicadas a ele, os servidores web alguns. Se um determinado tipo de carga de trabalho fez com que as máquinas funcionassem demasiado quentes, então adicionou mais máquinas com essa mesma configuração a esse nível. No entanto, nem todas as cargas de trabalho poderiam ser dimensionada tão facilmente - especialmente com o nível de dados que normalmente substituiria as máquinas por máquinas maiores. Isso é fácil. Se uma máquina falhou, essa parte da aplicação global funcionou com uma capacidade inferior até que a máquina pudesse ser restaurada. Ainda bastante fácil (se não necessariamente divertido).

Agora, no entanto, o mundo do serviço e da arquitetura de software mudou. É mais comum que as aplicações tenham adotado um design de escala. As aplicações de construção com contentores ou microserviços (ou ambos) são comuns. Agora, embora ainda possas ter apenas algumas máquinas, elas não estão a executar apenas um único exemplo de uma carga de trabalho. Podem até estar a executar várias cargas de trabalho diferentes ao mesmo tempo. Tem agora dezenas de diferentes tipos de serviços (nenhum consome recursos de uma máquina completa), talvez centenas de diferentes instâncias desses serviços. Cada instância nomeada tem uma ou mais instâncias ou réplicas para Alta Disponibilidade (HA). Dependendo do tamanho dessas cargas de trabalho, e do quão ocupados estão, pode encontrar-se com centenas ou milhares de máquinas. 

De repente, gerir o seu ambiente não é tão simples como gerir algumas máquinas dedicadas a tipos únicos de cargas de trabalho. Os seus servidores são virtuais e já não têm nomes (afinal, mudou mentalidades de animais de [estimação para gado).](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) A configuração é menos sobre as máquinas e mais sobre os próprios serviços. Hardware dedicado a um único exemplo de uma carga de trabalho é em grande parte uma coisa do passado. Os próprios serviços tornaram-se pequenos sistemas distribuídos que abrangem várias peças menores de hardware de mercadoria.

Como a sua aplicação já não é uma série de monólitos espalhados por vários níveis, agora tem muitas mais combinações para lidar. Quem decide que tipos de cargas de trabalho podem funcionar em que hardware, ou quantos? Que cargas de trabalho funcionam bem no mesmo hardware, e que conflito? Quando uma máquina se apaga, como sabes o que estava a correr naquela máquina? Quem está encarregado de garantir que a carga de trabalho recomeça a funcionar? Espera que a máquina (virtual?) volte ou as suas cargas de trabalho automaticamente falham noutras máquinas e continuam a funcionar? É necessária uma intervenção humana? E os melhoramentos neste ambiente?

Como desenvolvedores e operadores que lidam com este ambiente, vamos querer ajuda para gerir esta complexidade. Contratar e tentar esconder a complexidade com as pessoas provavelmente não é a resposta certa, então o que fazemos?

## <a name="introducing-orchestrators"></a>Apresentação de orquestradores
Um "Orquestrador" é o termo geral para uma peça de software que ajuda os administradores a gerir este tipo de ambientes. Os orquestradores são os componentes que recebem pedidos como "Gostaria de ter cinco cópias deste serviço a funcionar no meu ambiente." Tentam fazer com que o ambiente corresponda ao estado desejado, não importa o que aconteça.

Os orquestradores (não humanos) são o que tomam medidas quando uma máquina falha ou uma carga de trabalho termina por alguma razão inesperada. A maioria dos orquestradores fazem mais do que apenas lidar com o fracasso. Outras funcionalidades que têm são a gestão de novas implementações, o tratamento de atualizações e o tratamento do consumo de recursos e governação. Todos os orquestradores são fundamentalmente sobre a manutenção de algum estado de configuração desejado no ambiente. Queres poder dizer a um orquestrador o que queres e fazê-lo fazer o trabalho pesado. Aurora em cima de Mesos, Docker Datacenter/Docker Swarm, Kubernetes e Service Fabric são todos exemplos de orquestradores. Estes orquestradores estão a ser desenvolvidos ativamente para atender às necessidades de cargas de trabalho reais em ambientes produtivos. 

## <a name="orchestration-as-a-service"></a>Orquestração como serviço
O Cluster Resource Manager é o componente do sistema que lida com a orquestração em Tecido de Serviço. O trabalho do Gestor de Recursos do Cluster é dividido em três partes:

1. Regras de Execução
2. Otimização do Seu Ambiente
3. Ajudar com outros processos

### <a name="what-it-isnt"></a>O que não é
Nas aplicações tradicionais de nível N, há sempre um [Balanceador de Carga.](https://en.wikipedia.org/wiki/Load_balancing_(computing)) Normalmente, tratava-se de um Balanceador de Carga de Rede (NLB) ou de um Balanceador de Carga de Aplicação (ALB), dependendo do local onde se encontrava na pilha de rede. Alguns equilibradores de carga são baseados em Hardware como a oferta bigIP da F5, outros são baseados em software, como o NLB da Microsoft. Em outros ambientes, você pode ver algo como HAProxy, nginx, Istio, ou Enviado neste papel. Nestas arquiteturas, o trabalho de equilibrar a carga é garantir que as cargas de trabalho apátridas recebam (aproximadamente) a mesma quantidade de trabalho. Estratégias para equilibrar a carga variaram. Alguns equilibradores enviariam cada chamada diferente para um servidor diferente. Outros providenciaram fixação/pegajosa de sessão. Os equilibradores mais avançados utilizam estimativas reais de carga ou reportando para encaminhar uma chamada com base no custo esperado e na carga atual da máquina.

Os equilibradores de rede ou routers de mensagens tentaram garantir que o nível web/trabalhador se mantinha aproximadamente equilibrado. As estratégias para equilibrar o nível de dados eram diferentes e dependiam do mecanismo de armazenamento de dados. O equilíbrio do nível de dados baseou-se em fragmentos de dados, caching, pontos de vista geridos, procedimentos armazenados e outros mecanismos específicos da loja.

Embora algumas destas estratégias sejam interessantes, o Service Fabric Cluster Resource Manager não é nada como um equilibrador de carga de rede ou um cache. Um Balanceador de Carga de Rede equilibra os frontends espalhando o tráfego através das frentes. O Service Fabric Cluster Resource Manager tem uma estratégia diferente. Fundamentalmente, a Service Fabric desloca *serviços* para onde fazem mais sentido, esperando que o tráfego ou a carga sigam. Por exemplo, pode mover serviços para nós que estão atualmente frios porque os serviços que lá estão não estão a fazer muito trabalho. Os nós podem estar frios uma vez que os serviços presentes foram apagados ou transferidos para outro lugar. Como outro exemplo, o Cluster Resource Manager também poderia mover um serviço para longe de uma máquina. Talvez a máquina esteja prestes a ser atualizada, ou esteja sobrecarregada devido a um aumento no consumo pelos serviços que a executam. Em alternativa, os requisitos de recursos do serviço podem ter aumentado. Como resultado, não há recursos suficientes nesta máquina para continuar a executá-la. 

Uma vez que o Cluster Resource Manager é responsável pela movimentação de serviços, contém um conjunto de funcionalidades diferente em comparação com o que se encontra num equilibrador de carga de rede. Isto porque os equilibradores de carga de rede fornecem tráfego de rede para onde os serviços já estão, mesmo que essa localização não seja ideal para executar o próprio serviço. O Service Fabric Cluster Resource Manager emprega estratégias fundamentalmente diferentes para garantir que os recursos no cluster são eficientemente utilizados.

## <a name="next-steps"></a>Passos seguintes
- Para obter informações sobre a arquitetura e o fluxo de informação dentro do Cluster Resource Manager, confira [este artigo](service-fabric-cluster-resource-manager-architecture.md)
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre métricas, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)
- Para obter mais informações sobre serviços de configuração, [Saiba mais sobre a configuração dos serviços](service-fabric-cluster-resource-manager-configure-services.md)
- As métricas são como a Manjedoura de Recursos do Cluster de Tecidos de Serviço gere o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las Confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
- O Cluster Resource Manager trabalha com as capacidades de gestão do Service Fabric. Para saber mais sobre esta integração, leia [este artigo](service-fabric-cluster-resource-manager-management-integration.md)
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre a [carga de equilíbrio](service-fabric-cluster-resource-manager-balancing.md)
