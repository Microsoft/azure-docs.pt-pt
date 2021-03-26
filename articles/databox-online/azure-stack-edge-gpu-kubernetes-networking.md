---
title: Compreenda a rede kubernetes no dispositivo Azure Stack Edge Pro| Microsoft Docs
description: Descreve como a rede Kubernetes funciona num dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 139eeaa83629f3ef51f8eda414622f7da54dfab4
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560255"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Rede Kubernetes no seu dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

No seu dispositivo Azure Stack Edge Pro, é criado um cluster Kubernetes quando configura o papel de computação. Uma vez criado o cluster Kubernetes, as aplicações contentorizadas podem ser implantadas no cluster Kubernetes em Pods. Existem formas distintas de que o networking é usado para os Pods no seu cluster Kubernetes. 

Este artigo descreve a rede num cluster Kubernetes em geral e especificamente no contexto do seu dispositivo Azure Stack Edge Pro. 

## <a name="networking-requirements"></a>Requisitos de rede

Aqui está um exemplo de uma aplicação típica de 2 níveis que é implantada no cluster Kubernetes.

- A aplicação tem uma linha frontal do servidor web e uma aplicação de base de dados no backend. 
- Cada cápsula é atribuída a um IP, mas estes IPs podem ser alterados no reinício e falha da cápsula. 
- Cada aplicação é composta por várias cápsulas e deve haver um equilíbrio de carga do tráfego em todas as réplicas do pod. 

![Requisitos de rede kubernetes](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

O cenário acima resulta nos seguintes requisitos de ligação em rede:

 - Existe a necessidade de a aplicação virada para o exterior ser acedida por um utilizador de aplicações fora do cluster Kubernetes através de um nome ou de um endereço IP. 
 - As aplicações dentro do cluster Kubernetes, por exemplo, front end e as cápsulas de backend aqui devem ser capazes de falar entre si.

Para resolver ambas as necessidades acima, introduzimos um serviço Kubernetes. 


## <a name="networking-services"></a>Serviços de rede

Existem dois tipos de serviços Kubernetes: 

- **Cluster IP service** - pense nisto como fornecendo um ponto final constante para as cápsulas de aplicação. Qualquer cápsula associada a estes serviços não pode ser acedida a partir de fora do cluster Kubernetes. O endereço IP utilizado com estes serviços provém do espaço de endereço na rede privada. 
- **Load balancer IP** - como o serviço IP cluster, mas o IP associado vem da rede externa e pode ser acedido a partir de fora do cluster Kubernetes.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Rede Kubernetes no Azure Stack Edge Pro

Calico, Metallb e Core DNS são todos os componentes instalados para networking no seu Azure Stack Edge Pro. 

- **A Calico** atribui um endereço IP de uma gama ip privada a cada cápsula e configurar a rede para estas cápsulas para que o pod num nó possa comunicar com a cápsula noutro nó. 
- **A Metallb** funciona numa cápsula de cluster e atribui o endereço IP a serviços de balanceador de carga tipo. Os endereços IP do balançador de carga são escolhidos a partir da gama ip de serviço fornecida através da UI local. 
- **CORE DNS** - Este add-on configura o nome do serviço de mapeamento de DNS para o endereço IP do cluster.

Os endereços IP utilizados para os nós Kubernetes e os serviços externos são fornecidos através da página da **rede Compute** na UI local do dispositivo.

![Atribuição de IP de Kubernetes na UI local](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

A atribuição de IP é para:

- **Kubernetes nó IPs**: Esta gama IP é usada para os nós mestres e operários de Kubernetes. Estes IP são usados quando os nós de Kubernetes comunicam entre si.

- **IPs de serviço externo Kubernetes**: Esta gama IP é utilizada para serviços externos (também conhecidos como serviços de Balanceador de Carga) que estão expostos fora do cluster Kubernetes.  


## <a name="next-steps"></a>Passos seguintes

Para configurar a rede kubernetes no seu Azure Stack Edge Pro consulte:

- [Exponha uma aplicação apátrida externamente no seu Azure Stack Edge Pro via IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- [Exponha uma aplicação apátrida externamente no seu Azure Stack Edge Pro via kuebctl](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).