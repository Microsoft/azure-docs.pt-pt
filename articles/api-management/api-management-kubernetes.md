---
title: Utilizar a Azure API Management com microserviços implantados no Serviço Azure Kubernetes [ Microsoft Docs
description: Este artigo descreve as opções de implementação da Gestão aPI com AKS
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
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75480999"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Utilizar a Azure API Management com microserviços implantados no Serviço Azure Kubernetes

Os microserviços são perfeitos para a construção de APIs. Com o [Serviço Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) (AKS), pode implementar rapidamente e operar uma arquitetura baseada em [microserviços](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) na nuvem. Em seguida, pode aproveitar a [Azure API Management](https://aka.ms/apimrocks) (API Management) para publicar os seus microserviços como APIs para consumo interno e externo. Este artigo descreve as opções de implementação da Gestão aPI com AKS. Assume conhecimentos básicos de Kubernetes, API Management e Azure networking. 

## <a name="background"></a>Segundo plano

Ao publicar microserviços como APIs para consumo, pode ser desafiante gerir a comunicação entre os microserviços e os clientes que os consomem. Há uma multiplicidade de preocupações transversais como a autenticação, autorização, estrangulamento, cache, transformação e monitorização. Estas preocupações são válidas independentemente de os microserviços estarem expostos a clientes internos ou externos. 

O padrão [API Gateway](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) aborda estas preocupações. Um portal DaPI serve de porta de entrada para os microserviços, dissocia clientes dos seus microserviços, adiciona uma camada adicional de segurança, e diminui a complexidade dos seus microserviços, removendo o fardo de lidar com preocupações de corte cruzado. 

[A Azure API Management](https://aka.ms/apimrocks) é uma solução chave na mão para resolver as suas necessidades de gateway API. Você pode rapidamente criar uma porta de entrada consistente e moderna para os seus microserviços e publicá-los como APIs. Como solução de gestão de API de ciclo de vida completo, também fornece capacidades adicionais, incluindo um portal de desenvolvimento de self-service para descoberta de API, gestão de ciclo de vida API e análise de API.

Quando utilizados em conjunto, a AKS e a API Management fornecem uma plataforma para a implementação, publicação, segurança, monitorização e gestão das suas APIs baseadas em microserviços. Neste artigo, vamos analisar algumas opções de implantação de AKS em conjunto com a API Management. 

## <a name="kubernetes-services-and-apis"></a>Serviços kubernetes e APIs

Num aglomerado de Kubernetes, os contentores são implantados em [Pods,](https://kubernetes.io/docs/concepts/workloads/pods/pod/)que são efémeros e têm um ciclo de vida. Quando um nó de operário morre, os Pods que correm no nó perdem-se. Portanto, o endereço IP de um Pod pode ser alterado a qualquer momento. Não podemos confiar nele para comunicar com a cápsula. 

Para resolver este problema, a Kubernetes introduziu o conceito de [Serviços.](https://kubernetes.io/docs/concepts/services-networking/service/) Um Serviço Kubernetes é uma camada de abstração que define um grupo lógico de Pods e permite exposição externa ao tráfego, equilíbrio de carga e descoberta de serviço para esses Pods. 

Quando estivermos prontos para publicar os nossos microserviços como APIs através da API Management, precisamos de pensar em como mapear os nossos Serviços em Kubernetes para APIs em Gestão de API. Não há regras definidas. Depende de como concebeu e dividiu as suas capacidades de negócio ou domínios em microserviços no início. Por exemplo, se as cápsulas por trás de um Serviço forem responsáveis por todas as operações num determinado recurso (por exemplo, Cliente), o Serviço pode ser mapeado para uma API. Se as operações num recurso forem divididas em múltiplos microserviços (por exemplo, GetOrder, PlaceOrder), então vários Serviços podem ser logicamente agregados numa única API na gestão da API (Ver Fig. 1). 

Os mapeamentos também podem evoluir. Uma vez que a API Management cria uma fachada em frente aos microserviços, permite-nos refactore e tamanho certo os nossos microserviços ao longo do tempo. 

![Serviços de mapas para APIs](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Implementar Gestão aPI em frente à AKS

Existem algumas opções de implementação da Gestão API em frente a um cluster AKS. 

Embora um cluster AKS seja sempre implantado numa rede virtual (VNet), uma instância de Gestão API não é necessária para ser implantada num VNet. Quando a API Management não reside dentro do cluster VNet, o cluster AKS tem de publicar pontos finais públicos para a API Management ligar-se. Nesse caso, é necessário assegurar a ligação entre a API Management e a AKS. Por outras palavras, temos de garantir que o cluster só pode ser acedido exclusivamente através da Gestão API. Vamos ver as opções. 

### <a name="option-1-expose-services-publicly"></a>Opção 1: Expor serviços publicamente

Os serviços num cluster AKS podem ser expostos publicamente utilizando tipos de [serviço](https://docs.microsoft.com/azure/aks/concepts-network) de NodePort, LoadBalancer ou ExternalName. Neste caso, os serviços são acessíveis diretamente da internet pública. Depois de implantar a API Management em frente ao cluster, precisamos de garantir que todo o tráfego de entrada passa pela API Management aplicando a autenticação nos microserviços. Por exemplo, a API Management pode incluir um sinal de acesso em cada pedido feito ao cluster. Cada microserviço é responsável pela validação do símbolo antes de processar o pedido. 


Esta pode ser a opção mais fácil de implementar a API Management em frente ao AKS, especialmente se já tiver a lógica de autenticação implementada nos seus microserviços. 

![Publicar serviços diretamente](./media/api-management-aks/direct.png)

Prós:
* Configuração fácil no lado da Gestão API porque não precisa de ser injetada no cluster VNet
* Nenhuma mudança do lado aks se os Serviços já estão expostos publicamente e a lógica de autenticação já existe nos microserviços

Contras:
* Risco potencial de segurança devido à visibilidade pública dos pontos finais do Serviço
* Nenhum ponto de entrada única para o tráfego de cluster de entrada
* Complica os microserviços com lógica de autenticação duplicada

### <a name="option-2-install-an-ingress-controller"></a>Opção 2: Instalar um Controlador De Entrada

Embora a opção 1 possa ser mais fácil, tem desvantagens notáveis como mencionado acima. Se uma instância de Gestão API não residir no cluster VNet, a autenticação Mútua TLS (mTLS) é uma forma robusta de garantir que o tráfego é seguro e confiável em ambas as direções entre uma instância de Gestão API e um cluster AKS. 

A autenticação Mútua TLS é [apoiada de forma nativa](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) pela API Management e pode ser ativada em Kubernetes [instalando um Controlador Ingress](https://docs.microsoft.com/azure/aks/ingress-own-tls) (Fig. 3). Como resultado, a autenticação será realizada no Controlador Ingress, que simplifica os microserviços. Além disso, pode adicionar os endereços IP da API Management à lista permitida pela Ingress para garantir que apenas a API Management tem acesso ao cluster.  

 
![Publicar através de um controlador de ingresso](./media/api-management-aks/ingress-controller.png)


Prós:
* Uma configuração fácil no lado da API Management porque não precisa de ser injetada no cluster VNet e mTLS é suportada de forma nativa
* Centraliza a proteção para o tráfego de cluster de entrada na camada do Controlador de Ingress
* Reduz o risco de segurança minimizando os pontos finais do cluster publicamente visíveis

Contras:
* Aumenta a complexidade da configuração do cluster devido ao trabalho extra para instalar, configurar e manter o Controlador Ingress e gerir os certificados utilizados para mTLS
* Risco de segurança devido à visibilidade pública do(s) ponto final do Controlador de Ingress


Quando publica APIs através da API Management, é fácil e comum garantir o acesso a essas APIs utilizando chaves de subscrição. Os desenvolvedores que necessitem de consumir as APIs publicadas devem incluir uma chave de subscrição válida nos pedidos http quando fazem chamadas para essas APIs. Caso contrário, as chamadas são imediatamente rejeitadas pela porta de entrada da API Management. Não são encaminhados para os serviços de back-end.

Para obter uma chave de subscrição para aceder a APIs, é necessária uma subscrição. Uma subscrição é essencialmente um recipiente nomeado para um par de chaves de subscrição. Os desenvolvedores que precisam de consumir as APIs publicadas podem obter subscrições. E não precisam da aprovação dos editores da API. Os editores da API também podem criar subscrições diretamente para consumidores de API.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Opção 3: Implementar a APIM dentro do cluster VNet

Em alguns casos, os clientes com restrições regulamentares ou requisitos de segurança rigorosos podem encontrar soluções não viáveis da Opção 1 e 2 devido a pontos finais expostos publicamente. Noutros, o cluster AKS e as aplicações que consomem os microserviços podem residir dentro do mesmo VNet, pelo que não há razão para expor publicamente o cluster, uma vez que todo o tráfego de API permanecerá dentro do VNet. Para estes cenários, pode implantar a API Management no cluster VNet. [O nível API Management Premium](https://aka.ms/apimpricing) suporta a implantação da VNet. 

Existem dois modos de implantação da [API Management num VNet](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) – Externo e Interno. 

Se os consumidores de API não residirem no cluster VNet, deve utilizar-se o modo Externo (Fig. 4). Neste modo, o gateway de Gestão API é injetado no cluster VNet mas acessível a partir da internet pública através de um equilibrista de carga externo. Ajuda a esconder completamente o cluster, ao mesmo tempo que permite que clientes externos consumam os microserviços. Além disso, pode utilizar capacidades de networking Azure, como Network Security Groups (NSG) para restringir o tráfego de rede.

![Modo VNet externo](./media/api-management-aks/vnet-external.png)

Se todos os consumidores de API residirem dentro do cluster VNet, então o modo Interno (Fig. 5) poderá ser utilizado. Neste modo, o gateway de Gestão API é injetado no Cluster VNET e acessível apenas a partir deste VNet através de um equilíbrio interno de carga. Não há como chegar ao portal de gestão da API ou ao cluster AKS da internet pública. 

![Modo VNet interno](./media/api-management-aks/vnet-internal.png)

 Em ambos os casos, o cluster AKS não é visível publicamente. Em comparação com a opção 2, o Controlador De Entrada pode não ser necessário. Dependendo do seu cenário e configuração, a autenticação poderá ainda ser necessária entre a API Management e os seus microserviços. Por exemplo, se for adotada uma malha de serviço, requer sempre autenticação mútua de TLS. 

Prós:
* A opção mais segura porque o cluster AKS não tem ponto final público
* Simplifica a configuração do cluster uma vez que não tem ponto final público
* Capacidade de ocultar tanto a Gestão API como o AKS dentro da VNet utilizando o modo Interno
* Capacidade de controlar o tráfego de rede utilizando capacidades de rede Azure, tais como Grupos de Segurança de Rede (NSG)

Contras:
* Aumenta a complexidade da implantação e configuração da Gestão aPI para trabalhar dentro da VNet

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [conceitos de rede para aplicações em AKS](https://docs.microsoft.com/azure/aks/concepts-network)
* Saiba mais sobre [como usar a Gestão API com redes virtuais](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)





