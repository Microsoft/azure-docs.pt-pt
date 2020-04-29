---
title: Como o encaminhamento funciona com espaços Azure Dev
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos que alimentam os Espaços Azure Dev e como funciona o encaminhamento
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241390"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Como o encaminhamento funciona com espaços Azure Dev

A Azure Dev Spaces fornece-lhe múltiplas formas de iterar e depurar rapidamente aplicações kubernetes e colaborar com a sua equipa num cluster do Serviço Azure Kubernetes (AKS). Uma vez que o seu projeto está em execução num espaço de v, a Azure Dev Spaces oferece capacidades adicionais de networking e encaminhamento para o seu projeto.

Este artigo descreve como o encaminhamento funciona com a Dev Spaces.

## <a name="how-routing-works"></a>Como funciona o encaminhamento

Um espaço de v é construído em cima de AKS e usa os [mesmos conceitos de networking.](../aks/concepts-network.md) A Azure Dev Spaces também tem um serviço centralizado de *ingressmanager* e implanta o seu próprio Controlador De Ingress para o cluster AKS. O serviço *ingressmanager* monitoriza os clusters AKS com espaços de dev e aumenta o Controlador de Ingress os Espaços Azure Dev no cluster com objetos Ingress para encaminhamento para cápsulas de aplicação. O recipiente de procuração de espaços `azds-route-as` devespaços em cada cápsula adiciona um cabeçalho HTTP para o tráfego HTTP a um espaço de v baseado no URL. Por exemplo, um pedido *http://azureuser.s.default.serviceA.fedcba09...azds.io* para o URL `azds-route-as: azureuser`obteria um cabeçalho HTTP com . O recipiente de procuração de `azds-route-as` espaços devespaços não adicionará um cabeçalho se um já estiver presente.

Quando um pedido HTTP é feito a um serviço de fora do cluster, o pedido vai para o controlador Ingress. O controlador Ingress encaminha o pedido diretamente para a cápsula apropriada com base nos seus objetos e regras Ingress. O recipiente de procuração de espaços devespaços `azds-route-as` na cápsula recebe o pedido, adiciona o cabeçalho com base no URL e, em seguida, encaminha o pedido para o recipiente de aplicação.

Quando um pedido HTTP é feito a um serviço de outro serviço dentro do cluster, o pedido passa primeiro pelo recipiente de procuração de espaços de vespaços do serviço de chamada. O recipiente de procuração de espaços devespaços olha para o pedido http e verifica o `azds-route-as` cabeçalho. Com base no cabeçalho, o recipiente de procuração de espaços devespaços procurará o endereço IP do serviço associado ao valor do cabeçalho. Se for encontrado um endereço IP, o contentor proxy devspaces redireciona o pedido para esse endereço IP. Se não for encontrado um endereço IP, o contentor proxy devspaces encaminha o pedido para o recipiente de aplicação dos pais.

Por exemplo, o serviço de aplicações *A* e *o serviçoB* são implantados para um espaço de dev dos pais chamado *padrão*. *serviceA* depende do *serviçoB* e faz chamadas HTTP para ele. O Utilizador Azure cria um espaço de dev para crianças baseado no espaço *padrão* chamado *azureuser*. O Utilizador Azure também implementa a sua própria versão de *serviçoA* para o seu espaço infantil. Quando é feito *http://azureuser.s.default.serviceA.fedcba09...azds.io*um pedido para:

![Encaminhamento de Espaços Azure Dev](media/how-dev-spaces-works/routing.svg)

1. O controlador Ingress procura o IP para o casulo associado ao URL, que é *serviceA.azureuser*.
1. O controlador Ingress encontra o IP para o casulo no espaço de v do Utilizador Azure e encaminha o pedido para o *casulo serviceA.azureuser.*
1. O recipiente de procuração de espaços devespaços no *vagem serviceA.azureuser* recebe o pedido e adiciona `azds-route-as: azureuser` como cabeçalho HTTP.
1. O contentor de procuração de espaços devespaços no *vagem serviceA.azureuser* encaminha o pedido para o recipiente de aplicação *serviceA* no *vagem serviceA.azureuser.*
1. A aplicação *serviceA* no *serviçoA.azureuser* pod faz uma chamada para *o serviçoB*. A aplicação *serviceA* também contém `azds-route-as` código para preservar o `azds-route-as: azureuser`cabeçalho existente, que neste caso é .
1. O recipiente de procuração de espaços devespaços no *vagão serviceA.azureuser* recebe o pedido `azds-route-as` e analisa o IP do *serviçoB* com base no valor do cabeçalho.
1. O recipiente de procuração de espaços devespaços no *vagem serviceA.azureuser* não encontra um IP para *serviçoB.azureuser*.
1. O recipiente de procuração de espaços devespaços no *vagem serviceA.azureuser* procura o IP para *o serviçoB* no espaço principal, que é *serviceB.default*.
1. O recipiente de procuração de espaços devespaços no *vagem serviceA.azureuser* encontra o IP para *serviceB.default* e encaminha o pedido para o *serviçoB.default* pod.
1. O contentor de procuração de espaços devespaços no *vagem serviceB.default* recebe o pedido e encaminha o pedido para o recipiente de aplicação *serviceB* no *vagem padrão serviceB.predefinido.*
1. A aplicação *serviceB* no *serviçoB.default* pod devolve uma resposta ao *pod serviceA.azureuser.*
1. O contentor de procuração de espaços devespaços no *vagão serviceA.azureuser* recebe a resposta e encaminha a resposta para o recipiente de aplicação *serviceA* no *vagem serviceA.azureuser.*
1. A aplicação *serviceA* recebe a resposta e, em seguida, devolve a sua própria resposta.
1. O recipiente de procuração de espaços devespaços no *vagão serviceA.azureuser* recebe a resposta do recipiente de aplicação *serviceA* e encaminha a resposta para o chamador original fora do cluster.

Todo o tráfego de TCP que não seja HTTP passa pelo controlador Ingress e contentores de procuração de espaços devespaços não modificados.

## <a name="sharing-a-dev-space"></a>Partilhar um espaço de dev

Ao trabalhar com uma equipa, você pode [compartilhar um espaço de v em toda uma equipe](how-to/share-dev-spaces.md) e criar espaços de v derivados. Um espaço de v pode ser usado por qualquer pessoa com acesso ao grupo de recursos do espaço dev.

Você também pode criar um novo espaço de dev que é derivado de outro espaço de v. Quando se cria um espaço de v derivado, o rótulo *azds.io/parent-space=PARENT-SPACE-NAME* é adicionado ao espaço de nome do espaço de v derivado. Além disso, todas as aplicações do espaço de v dos pais são partilhadas com o espaço de v derivado. Se implementar uma versão atualizada de uma aplicação para o espaço derivado, ela só existirá no espaço de v derivado e o espaço de v dos pais permanecerá inalterado. Você pode ter um máximo de três níveis de espaços derivados de v ou espaços *de avós.*

O espaço derivado de v também irá direcionar inteligentemente os pedidos entre as suas próprias aplicações e as aplicações partilhadas com o seu progenitor. O encaminhamento funciona tentando encaminhar o pedido para uma aplicação no espaço de v derivado e recuando para a aplicação partilhada do espaço de v-mãe. O encaminhamento recairá para a aplicação partilhada no espaço dos avós se a aplicação não estiver no espaço dos pais.

Por exemplo:
* O *padrão* de espaço de v tem aplicações *serviçoA* e *serviçoB*.
* O *azureuser* espaço dev é derivado do *padrão*.
* Uma versão atualizada do *serviço A* é implantada para *azureuser*.

Ao utilizar *o azureuser,* todos os pedidos de *serviço A* serão encaminhados para a versão atualizada em *azureuser*. Um pedido de *serviçoB* tentará primeiro ser encaminhado para a versão *azureuser* do *serviçoB*. Uma vez que não existe, será encaminhado para a versão *padrão* do *serviçoB*. Se a versão *azureuser* do *serviço A* for removida, todos os pedidos de *serviçoA* voltarão a utilizar a versão *padrão* do *serviço A*.

## <a name="next-steps"></a>Passos seguintes

Para ver alguns exemplos de como o Azure Dev Spaces usa o encaminhamento para fornecer uma rápida iteração e desenvolvimento, veja [como ligar a sua máquina][how-it-works-connect]de desenvolvimento às suas obras de espaço de v , [Quão remota depuração do seu código com a Azure Dev Spaces funciona][how-it-works-remote-debugging], e as ações do [GitHub & Serviço Azure Kubernetes.][pr-flow]

Para começar a usar o encaminhamento com o Azure Dev Spaces para desenvolvimento de equipas, veja o desenvolvimento da [equipa em Azure Dev Spaces][quickstart-team] quickstart.

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md