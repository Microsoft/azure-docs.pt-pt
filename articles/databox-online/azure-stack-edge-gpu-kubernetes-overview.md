---
title: Visão geral do cluster Kubernetes no dispositivo Microsoft Azure Stack Edge Pro Microsoft Docs
description: Descreve como kubernetes é implementado no seu dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5b96e568b57c34fec1c93727aabe8e739a2a45cd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448651"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes no seu dispositivo GPU Azure Stack Edge Pro

Kubernetes é uma plataforma popular de código aberto para orquestrar aplicações contentorizadas. Este artigo fornece uma visão geral de Kubernetes e, em seguida, descreve como Kubernetes funciona no seu dispositivo Azure Stack Edge Pro. 

## <a name="about-kubernetes"></a>Sobre Kubernetes 

A Kubernetes fornece uma plataforma fácil e fiável para gerir aplicações baseadas em contentores e os seus componentes de networking e armazenamento associados. Você pode construir, entregar e escalar aplicativos contentorizados com Kubernetes.

Como uma plataforma aberta, você pode usar Kubernetes para construir aplicações com a sua linguagem de programação preferida, bibliotecas de SO ou ônibus de mensagens. Para agendar e implementar lançamentos, a Kubernetes pode integrar-se com a integração contínua existente e as ferramentas de entrega contínua.

Para mais informações, consulte [como funciona a Kubernetes.](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s)

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Kubernetes no Azure Stack Edge Pro

No seu dispositivo Azure Stack Edge Pro, pode criar um cluster Kubernetes configurando o cálculo. Quando o papel de computação é configurado, o cluster Kubernetes, incluindo os nós mestres e operários, são todos implantados e configurados para si. Este cluster é então utilizado para a implementação da carga de trabalho através `kubectl` de IoT Edge ou Azure Arc.

O dispositivo Azure Stack Edge Pro está disponível como uma configuração de 1 nó que constitui o cluster de infraestrutura. O cluster Kubernetes é separado do cluster de infraestruturas e é implantado em cima do cluster de infraestruturas. O cluster de infraestrutura fornece o armazenamento persistente para o seu dispositivo Azure Stack Edge Pro, enquanto o cluster Kubernetes é responsável exclusivamente pela orquestração de aplicações. 

O aglomerado de Kubernetes, neste caso, tem um nó mestre e um nó de trabalhadores. Os nós Kubernetes num cluster são máquinas virtuais que executam as suas aplicações e fluxos de trabalho em nuvem. 

O nó mestre Kubernetes é responsável por manter o estado desejado para o seu cluster. O nó principal também controla o nó do trabalhador que, por sua vez, executa as aplicações contentorizadas. 

O diagrama seguinte ilustra a implementação de Kubernetes num dispositivo Azure Stack Edge Pro de 1 nó. O dispositivo de 1 nó não está altamente disponível e se o nó falhar, o dispositivo apaga-se. O aglomerado de Kubernetes também desce.

![Arquitetura Kubernetes para um dispositivo Azure Stack Edge Pro de 1 nó](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Para obter mais informações sobre a arquitetura do cluster Kubernetes, vá aos [conceitos fundamentais de Kubernetes.](https://kubernetes.io/docs/concepts/architecture/)


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Fornecimento de volume de armazenamento

Para suportar cargas de trabalho de aplicação, pode montar volumes de armazenamento para dados persistentes nas partilhas do seu dispositivo Azure Stack Edge Pro. Podem ser utilizados volumes estáticos e dinâmicos. 

Para obter mais informações, consulte opções de fornecimento de armazenamento para aplicações no [armazenamento de Kubernetes para o seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Rede

A rede Kubernetes permite-lhe configurar a comunicação dentro da sua rede Kubernetes, incluindo a rede contentor-a-contentor, a rede pod-to-pod, a rede pod-to-service e a rede Internet-to-service. Para obter mais informações, consulte o modelo de networking em [rede Kubernetes para o seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Atualizações

À medida que as novas versões Kubernetes ficam disponíveis, o seu cluster pode ser atualizado utilizando as atualizações padrão disponíveis para o seu dispositivo Azure Stack Edge Pro. Para obter etapas sobre como atualizar, consulte [aplicar atualizações para o seu Azure Stack Edge Pro](azure-stack-edge-gpu-install-update.md).

## <a name="access-monitoring"></a>Acesso, monitorização

O cluster Kubernetes no seu dispositivo Azure Stack Edge Pro permite o controlo de acesso baseado em funções de Kubernetes (Kubernetes RBAC). Para obter mais informações, consulte o [controlo de acesso baseado em funções da Kubernetes no seu dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-rbac.md).

Também pode monitorizar a saúde do seu cluster e recursos através do dashboard Kubernetes. Os registos dos contentores também estão disponíveis. Para obter mais informações, consulte [o painel kubernetes para monitorizar a saúde do cluster Kubernetes no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

O Azure Monitor também está disponível como um complemento para recolher dados de saúde de contentores, nós e controladores. Para mais informações, consulte [a visão geral do Azure Monitor](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Gestão de aplicações

Depois de um cluster Kubernetes ser criado no seu dispositivo Azure Stack Edge Pro, pode gerir as aplicações implementadas neste cluster através de qualquer um dos seguintes métodos:

- Acesso nativo via `kubectl`
- IoT Edge 
- Azure Arc

Estes métodos são explicados nas seguintes secções.


### <a name="kubernetes-and-kubectl"></a>Kubernetes e kubectl

Uma vez implantado o cluster Kubernetes, poderá gerir as aplicações implantadas no cluster localmente a partir de uma máquina cliente. Utiliza uma ferramenta nativa, como *o kubectl* através da linha de comando, para interagir com as aplicações. 

Para obter mais informações sobre a implementação do cluster Kubernetes, vá ao [Implementar um cluster Kubernetes no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Para obter informações sobre gestão, vá a [Utilizar kubectl para gerir o cluster Kubernetes no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes e IoT Edge

Kubernetes também podem ser integrados com cargas de trabalho IoT Edge no dispositivo Azure Stack Edge Pro onde kubernetes fornece escala e o ecossistema e IoT fornece o ecossistema centrado IoT. A camada Kubernetes é usada como uma camada de infraestrutura para implantar cargas de trabalho Azure IoT Edge. O equilíbrio de vida útil e carga de rede do módulo é gerido pela Kubernetes, enquanto a plataforma de aplicação edge é gerida pelo IoT Edge.

Para obter mais informações sobre a implementação de aplicações no seu cluster Kubernetes via IoT Edge, aceda a: 

- [Expor aplicações apátridas no dispositivo Azure Stack Edge Pro via IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes e Azure Arc

O Azure Arc é uma ferramenta de gestão híbrida que lhe permitirá implementar aplicações nos seus clusters Kubernetes. O Azure Arc também permite utilizar o Azure Monitor para que os recipientes vejam e monitorizem os seus aglomerados. Para mais informações, vá ao [que Azure-Arc está ativado kubernetes?](../azure-arc/kubernetes/overview.md) Para obter informações sobre os preços do Azure Arc, aceda aos preços do [Azure Arc](https://azure.microsoft.com/services/azure-arc/#pricing).


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o armazenamento de Kubernetes no [dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md).
- Compreenda o modelo de networking Kubernetes no [dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-networking.md).
- Implementar [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) no portal Azure.