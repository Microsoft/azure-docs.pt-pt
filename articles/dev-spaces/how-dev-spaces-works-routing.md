---
title: Como o encaminhamento funciona com a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos que alimentam a Azure Dev Spaces e como funciona o encaminhamento
keywords: Espaços Azure Dev, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: 2d2c6f336222b4ae0907d6579289a8cad8d73aa6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91977974"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Como o encaminhamento funciona com a Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

A Azure Dev Spaces fornece-lhe múltiplas formas de iteração rápida e depuração rápida de aplicações kubernetes e colaborar com a sua equipa num cluster Azure Kubernetes Service (AKS). Uma vez que o seu projeto está em execução em um espaço dev, a Azure Dev Spaces fornece capacidades adicionais de networking e encaminhamento para o seu projeto.

Este artigo descreve como o encaminhamento funciona com dev Spaces.

## <a name="how-routing-works"></a>Como funciona o encaminhamento

Um espaço dev é construído em cima de AKS e usa os [mesmos conceitos de networking.](../aks/concepts-network.md) A Azure Dev Spaces também tem um serviço de *ingresssgerager* centralizado e implanta o seu próprio Controlador Ingress para o cluster AKS. O serviço *ingresssgerager* monitoriza os clusters AKS com espaços de dev e aumenta o Controlador de Ingress Azure Dev Spaces ingress no cluster com objetos Ingress para encaminhamento para cápsulas de aplicação. O recipiente devspaces-proxy em cada pod adiciona um `azds-route-as` cabeçalho HTTP para tráfego HTTP a um espaço dev baseado no URL. Por exemplo, um pedido para o URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* obteria um cabeçalho HTTP com `azds-route-as: azureuser` . O recipiente devspaces-proxy não adiciona um `azds-route-as` cabeçalho se já estiver presente.

Quando um pedido HTTP é feito a um serviço de fora do cluster, o pedido vai para o controlador Ingress. O controlador Ingress encaminha o pedido diretamente para a cápsula apropriada com base nos seus objetos e regras Ingress. O recipiente devspaces-proxy na cápsula recebe o pedido, adiciona o `azds-route-as` cabeçalho com base no URL e, em seguida, encaminha o pedido para o recipiente de aplicação.

Quando um pedido HTTP é feito a um serviço de outro serviço dentro do cluster, o pedido passa primeiro pelo contentor de procuração de devspaces do serviço de chamadas. O recipiente devspaces-proxy olha para o pedido HTTP e verifica o `azds-route-as` cabeçalho. Com base no cabeçalho, o recipiente devspaces-proxy irá procurar o endereço IP do serviço associado ao valor do cabeçalho. Se for encontrado um endereço IP, o contentor devspaces-proxy reencaminha o pedido para esse endereço IP. Se não for encontrado um endereço IP, o contentor devspaces-proxy encaminha o pedido para o recipiente de aplicação dos pais.

Por exemplo, o serviço de *aplicaçõesA* e *serviçoB* são implantados num espaço dev dos pais chamado *predefinido*. *o serviceA* depende *do serviçoB* e faz chamadas HTTP para o mesmo. O Utilizador Azure cria um espaço dev infantil baseado no espaço *padrão* chamado *azureuser*. O Azure User também implementa a sua própria versão de *serviçoA* para o seu espaço infantil. Quando é feito um pedido *http://azureuser.s.default.serviceA.fedcba09...azds.io* para:

![Encaminhamento Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. O controlador Ingress procura o IP para o pod associado ao URL, que é *serviceA.azureuser*.
1. O controlador Ingress encontra o IP para o pod no espaço dev do Utilizador Azure e encaminha o pedido para o *serviçoA.azureuser* pod.
1. O recipiente devspaces-proxy no *pod serviceA.azureuser* recebe o pedido e adiciona `azds-route-as: azureuser` como cabeçalho HTTP.
1. O recipiente devspaces-proxy no *serviçoA.azureuser* pod encaminha o pedido para o serviço *Um* recipiente de aplicação no *serviçoA.azureuser* pod.
1. A *aplicação serviceA* no *serviçoA.azureuser* pod faz uma chamada para *o serviçoB*. A *aplicação serviceA* também contém código para preservar o `azds-route-as` cabeçalho existente, que neste caso é `azds-route-as: azureuser` .
1. O recipiente devspaces-proxy no *serviçoA.azureuser* pod recebe o pedido e olha para o IP do *serviçoB* com base no valor do `azds-route-as` cabeçalho.
1. O recipiente devspaces-proxy no *pod serviceA.azureuser* não encontra um IP para *o serviçoB.azureuser*.
1. O recipiente devspaces-proxy no *pod de serviçoA.azureuser* procura o IP para *o serviçoB* no espaço dos pais, que é *serviçoB.predefinido*.
1. O recipiente devspaces-proxy no *pod de serviçoA.azureuser* encontra o IP para *o serviçoB.predefinido* e encaminha o pedido para o *pod de serviçoB.default.*
1. O recipiente devspaces-proxy no *bloco de serviçoB.predefinido* recebe o pedido e encaminha o pedido para o recipiente de aplicação *de serviçoB* na cápsula *de serviçoB.predefinido.*
1. A aplicação *serviceB* no *serviçoB.default* pod devolve uma resposta ao *serviçoA.azureuser* pod.
1. O recipiente devspaces-proxy no *serviçoA.azureuser* pod recebe a resposta e encaminha a resposta para o recipiente de aplicação *de serviçoA* na cápsula *de serviçoA.azureuser.*
1. A aplicação *serviceA* recebe a resposta e, em seguida, devolve a sua própria resposta.
1. O recipiente devspaces-proxy no *serviçoA.azureuser* pod recebe a resposta do recipiente de aplicação *do serviçoA* e encaminha a resposta para o chamador original fora do cluster.

Todo o tráfego TCP que não seja HTTP passa pelo controlador Ingress e contentores de devspaces-proxy não modificados.

## <a name="sharing-a-dev-space"></a>Partilhando um espaço dev

Ao trabalhar com uma equipa, você pode compartilhar um espaço dev em toda uma equipe e criar espaços dev derivados. Um espaço dev pode ser usado por qualquer pessoa com acesso ao grupo de recursos do espaço de dev.

Você também pode criar um novo espaço dev que é derivado de outro espaço dev. Quando cria um espaço dev derivado, a etiqueta *azds.io/parent-space=PARENT-SPACE-NAME* é adicionada ao espaço de identificação derivado. Além disso, todas as aplicações do espaço dev dos pais são partilhadas com o espaço dev derivado. Se implementar uma versão atualizada de uma aplicação para o espaço dev derivado, ela só existirá no espaço dev derivado e o espaço dev dos pais permanecerá inalterado. Você pode ter um máximo de três níveis de espaços dev derivados ou espaços *de avós.*

O espaço dev derivado também irá inteligentemente encaminhar pedidos entre as suas próprias aplicações e as aplicações partilhadas do seu progenitor. O encaminhamento funciona tentando encaminhar o pedido para uma aplicação no espaço dev derivado e recuando para a aplicação partilhada do espaço dev dos pais. O encaminhamento vai recair sobre a aplicação partilhada no espaço dos avós se a aplicação não estiver no espaço dos pais.

Por exemplo:
* O dev space *predefinido* tem *o serviço de aplicaçõesA* e *serviçoB*.
* O *azureuser* do espaço dev é derivado do *padrão*.
* Uma versão atualizada do *serviçoA* é implantada para *o azureuser*.

Ao utilizar *o azureuser,* todos os pedidos de *serviço A* serão encaminhados para a versão atualizada em *azureuser*. Um pedido de *serviçoB* tentará primeiro ser encaminhado para a versão *azureuser* do *serviçoB*. Uma vez que não existe, será encaminhado para a versão *padrão* do *serviçoB*. Se a versão *azureuser* do *serviçoA* for removida, todos os pedidos de *serviçoA* voltarão a utilizar a versão *padrão* do *serviçoA*.

## <a name="next-steps"></a>Passos seguintes

Para ver um exemplo de como a Azure Dev Spaces utiliza o encaminhamento para proporcionar uma rápida iteração e desenvolvimento, veja [como funciona a depuração remota do seu código com a Azure Dev Spaces][how-it-works-remote-debugging].


[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md