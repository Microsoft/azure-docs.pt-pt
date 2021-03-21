---
title: Utilize a Azure API Management com microserviços implantados no Serviço Azure Kubernetes | Microsoft Docs
description: Este artigo descreve as opções de implementação da Gestão da API com a AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 293a47bc3e8499d7eda4e64bb68bc95eb4c85ab0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108403"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Utilizar a Azure API Management com microserviços implantados no Serviço Azure Kubernetes

Os microserviços são perfeitos para a construção de APIs. Com [o Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS), pode implementar e operar rapidamente uma arquitetura baseada em [microserviços](/azure/architecture/guide/architecture-styles/microservices) na nuvem. Em seguida, pode aproveitar [a Azure API Management](https://aka.ms/apimrocks) (API Management) para publicar os seus microserviços como APIs para consumo interno e externo. Este artigo descreve as opções de implementação da Gestão da API com a AKS. Assume conhecimentos básicos de Kubernetes, API Management e Azure networking. 

## <a name="background"></a>Fundo

Ao publicar microserviços como APIs para consumo, pode ser um desafio gerir a comunicação entre os microserviços e os clientes que os consomem. Há uma multiplicidade de preocupações transversais, tais como a autenticação, autorização, estrangulamento, caching, transformação e monitorização. Estas preocupações são válidas independentemente de os microserviços estarem expostos a clientes internos ou externos. 

O padrão [API Gateway](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) aborda estas preocupações. Um gateway da API serve como porta de entrada para os microserviços, separa os clientes dos seus microserviços, adiciona uma camada adicional de segurança e diminui a complexidade dos seus microserviços, removendo o fardo de lidar com preocupações de corte transversal. 

[A Azure API Management](https://aka.ms/apimrocks) é uma solução chave na mão para resolver as suas necessidades de gateway API. Pode rapidamente criar uma porta de entrada consistente e moderna para os seus microserviços e publicá-los como APIs. Como uma solução de gestão de API de ciclo de vida completo, também fornece capacidades adicionais, incluindo um portal de desenvolvedores de self-service para a descoberta de API, gestão de ciclo de vida API e análise de API.

Quando usados em conjunto, a AKS e a API Management fornecem uma plataforma para implantação, publicação, segurança, monitorização e gestão das suas APIs baseadas em microserviços. Neste artigo, passaremos por algumas opções de implantação da AKS em conjunto com a API Management. 

## <a name="kubernetes-services-and-apis"></a>Serviços Kubernetes e APIs

Num aglomerado de Kubernetes, os contentores são implantados em [Pods,](https://kubernetes.io/docs/concepts/workloads/pods/pod/)que são efémeros e têm um ciclo de vida. Quando um nó de trabalhador morre, os Pods que correm no nó perdem-se. Portanto, o endereço IP de um Pod pode ser alterado a qualquer momento. Não podemos confiar nele para comunicar com a cápsula. 

Para resolver este problema, a Kubernetes introduziu o conceito de [Serviços.](https://kubernetes.io/docs/concepts/services-networking/service/) Um Serviço Kubernetes é uma camada de abstração que define um grupo lógico de Pods e permite exposição externa ao tráfego, equilíbrio de carga e descoberta de serviço para esses Pods. 

Quando estivermos prontos para publicar os nossos microserviços como APIs através da API Management, precisamos de pensar em como mapear os nossos Serviços em Kubernetes para APIs na API Management. Não há regras definidas. Depende de como concebeu e dividiu as suas capacidades ou domínios de negócio em microserviços no início. Por exemplo, se as cápsulas por trás de um Serviço forem responsáveis por todas as operações num determinado recurso (por exemplo, Cliente), o Serviço pode ser mapeado para uma API. Se as operações num recurso forem divididas em múltiplos microserviços (por exemplo, GetOrder, PlaceOrder), então vários Serviços podem ser logicamente agregados numa única API na gestão da API (Ver Fig. 1). 

Os mapeamentos também podem evoluir. Uma vez que a API Management cria uma fachada em frente aos microserviços, permite-nos refactor e tamanho certo os nossos microserviços ao longo do tempo. 

![Serviços de mapas para APIs](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Implementar a API Management em frente à AKS

Existem algumas opções de implantação da API Management em frente a um cluster AKS. 

Enquanto um cluster AKS é sempre implantado numa rede virtual (VNet), não é necessário um caso de Gestão API para ser implantado num VNet. Quando a API Management não reside no cluster VNet, o cluster AKS tem de publicar pontos finais públicos para a API Management se conectar. Nesse caso, é necessário assegurar a ligação entre a API Management e a AKS. Por outras palavras, precisamos garantir que o cluster só pode ser acedido exclusivamente através da API Management. Vamos ver as opções. 

### <a name="option-1-expose-services-publicly"></a>Opção 1: Expor serviços publicamente

Os serviços num cluster AKS podem ser expostos publicamente utilizando tipos de [serviço](../aks/concepts-network.md) de NodePort, LoadBalancer ou ExternalName. Neste caso, os Serviços são acessíveis diretamente a partir da internet pública. Depois de implementar a API Management em frente ao cluster, precisamos garantir que todo o tráfego de entrada passa pela API Management aplicando autenticação nos microserviços. Por exemplo, a API Management pode incluir um token de acesso em cada pedido feito ao cluster. Cada microserviço é responsável pela validação do token antes de processar o pedido. 


Esta pode ser a opção mais fácil de implementar a API Management em frente à AKS, especialmente se já tiver lógica de autenticação implementada nos seus microserviços. 

![Publicar serviços diretamente](./media/api-management-aks/direct.png)

Prós:
* Configuração fácil no lado da Gestão da API porque não precisa de ser injetado no cluster VNet
* Nenhuma alteração do lado da AKS se os Serviços já estiverem expostos publicamente e a lógica de autenticação já existir em microserviços

Contras:
* Risco potencial de segurança devido à visibilidade pública dos pontos finais do Serviço
* Não há ponto de entrada única para o tráfego de agrupamentos de entrada
* Complica microserviços com lógica de autenticação duplicada

### <a name="option-2-install-an-ingress-controller"></a>Opção 2: Instalar um Controlador de Entrada

Embora a Opção 1 possa ser mais fácil, tem inconvenientes notáveis como mencionado acima. Se uma instância de Gestão da API não residir no cluster VNet, a autenticação Mutual TLS (mTLS) é uma forma robusta de garantir que o tráfego é seguro e confiável em ambos os sentidos entre uma instância de Gestão da API e um cluster AKS. 

A autenticação Mutual TLS é [suportada de forma nativa](./api-management-howto-mutual-certificates.md) pela API Management e pode ser ativada em Kubernetes [através da instalação de um Controlador Ingress](../aks/ingress-own-tls.md) (Fig. 3). Como resultado, a autenticação será realizada no Controlador Ingress, o que simplifica os microserviços. Além disso, pode adicionar os endereços IP da Gestão API à lista permitida pela Ingress para garantir que apenas a API Management tem acesso ao cluster.  

 
![Publicar através de um controlador de entrada](./media/api-management-aks/ingress-controller.png)


Prós:
* Configuração fácil no lado da Gestão da API porque não precisa de ser injetada no cluster VNet e mTLS é suportado de forma nativa
* Centraliza a proteção para o tráfego de aglomerados de entrada na camada do Controlador Ingress
* Reduz o risco de segurança minimizando os pontos finais de cluster publicamente visíveis

Contras:
* Aumenta a complexidade da configuração do cluster devido ao trabalho extra para instalar, configurar e manter o Controlador de Entrada e gerir certificados utilizados para mTLS
* Risco de segurança devido à visibilidade pública do ponto final do Controlador de Entrada


Quando publica APIs através da API Management, é fácil e comum garantir o acesso a essas APIs utilizando chaves de subscrição. Os desenvolvedores que precisam de consumir as APIs publicadas devem incluir uma chave de subscrição válida em pedidos HTTP quando fazem chamadas para essas APIs. Caso contrário, as chamadas são rejeitadas imediatamente pela porta de entrada da API Management. Não são encaminhados para os serviços de back-end.

Para obter uma chave de subscrição para aceder a APIs, é necessária uma subscrição. Uma subscrição é essencialmente um recipiente nomeado para um par de chaves de subscrição. Os desenvolvedores que precisam de consumir as APIs publicadas podem obter subscrições. E não precisam da aprovação dos editores da API. Os editores da API também podem criar subscrições diretamente para os consumidores de API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Opção 3: Implementar APIM dentro do cluster VNet

Em alguns casos, os clientes com restrições regulamentares ou requisitos de segurança rigorosos podem encontrar soluções não viáveis da Opção 1 e 2 devido a pontos finais expostos publicamente. Noutros, o cluster AKS e as aplicações que consomem os microserviços podem residir dentro do mesmo VNet, pelo que não há razão para expor publicamente o cluster, uma vez que todo o tráfego da API permanecerá dentro da VNet. Para estes cenários, pode colocar a API Management no cluster VNet. [O nível API Management Premium](https://aka.ms/apimpricing) suporta a implementação de VNet. 

Existem dois modos de [implantação da Gestão API num VNet](./api-management-using-with-vnet.md) – Externo e Interno. 

Se os consumidores da API não residirem no cluster VNet, deve utilizar-se o modo Externo (Fig. 4). Neste modo, o gateway de Gestão API é injetado no cluster VNet mas acessível a partir da internet pública através de um equilibrador de carga externo. Ajuda a esconder completamente o cluster, permitindo que clientes externos consumam os microserviços. Além disso, pode utilizar as capacidades de networking do Azure, tais como Grupos de Segurança de Rede (NSG) para restringir o tráfego de rede.

![Modo VNet externo](./media/api-management-aks/vnet-external.png)

Se todos os consumidores da API residirem dentro do cluster VNet, então o modo Interno (Fig. 5) poderia ser utilizado. Neste modo, o gateway de gestão da API é injetado no cluster VNET e acessível apenas a partir deste VNet através de um equilibrador de carga interno. Não há como chegar ao portal de gestão da API ou ao cluster AKS a partir da internet pública. 

![Modo VNet interno](./media/api-management-aks/vnet-internal.png)

 Em ambos os casos, o cluster AKS não é visível publicamente. Em comparação com a Opção 2, o Controlador De Entrada pode não ser necessário. Dependendo do seu cenário e configuração, a autenticação poderá ainda ser necessária entre a API Management e os seus microserviços. Por exemplo, se uma malha de serviço for adotada, requer sempre a autenticação mútua de TLS. 

Prós:
* A opção mais segura porque o cluster AKS não tem ponto final público
* Simplifica a configuração do cluster uma vez que não tem ponto final público
* Capacidade de ocultar tanto a Gestão da API como a AKS dentro do VNet utilizando o modo Interno
* Capacidade de controlar o tráfego de rede utilizando capacidades de rede Azure, tais como Grupos de Segurança de Rede (NSG)

Contras:
* Aumenta a complexidade da implantação e configuração da Gestão da API para trabalhar dentro do VNet

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [conceitos de rede para aplicações em AKS](../aks/concepts-network.md)
* Saiba mais sobre [como usar a API Management com redes virtuais](./api-management-using-with-vnet.md)
