---
title: Azure Arc via Kubernetes frequentemente fazer perguntas
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo contém uma lista de perguntas frequentes relacionadas com a Azure Arc habilitada a Kubernetes
keywords: Kubernetes, Arc, Azure, contentores, configuração, GitOps, faq
ms.openlocfilehash: 237b2629b833a63552b172636f46a1ac92e321c0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561740"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Perguntas frequentes - Azure Arc habilitado Kubernetes

Este artigo aborda perguntas frequentes sobre Azure Arc habilitado Kubernetes.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Qual é a diferença entre o Azure Arc ativado pela Kubernetes e pelo Serviço Azure Kubernetes (AKS)?

AKS é a oferta gerida de Kubernetes pela Azure. A AKS simplifica a implementação de um cluster de Kubernetes gerido em Azure, descarregando grande parte da complexidade e sobrecarga operacional para Azure. Uma vez que os mestres Kubernetes são geridos pelo Azure, só consegues e manténs os nós dos agentes.

A azure Arc habilitado a Kubernetes permite-lhe alargar as capacidades de gestão da Azure (como o Azure Monitor e a Azure Policy) ligando os clusters kubernetes ao Azure. Mantém o aglomerado de Kubernetes subjacente.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Preciso ligar os meus clusters AKS em execução em Azure a Azure Arc?

Não. Todas as funcionalidades de Kubernetes habilitados pelo Azure Arc, incluindo o Azure Monitor e a Azure Policy (Gatekeeper), estão disponíveis em AKS (um recurso nativo em Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Devo ligar o meu cluster AKS-HCI e os clusters Kubernetes no Azure Stack Hub e no Azure Stack Edge ao Azure Arc?

Sim, ligar o seu cluster AKS-HCI ou agrupamentos Kubernetes no Azure Stack Edge ou no Azure Stack Hub ao Azure Arc fornece clusters com representação de recursos no Azure Resource Manager. Esta representação de recursos estende capacidades como a Configuração do Cluster, O Monitor Azure e Azure Policy (Gatekeeper) aos clusters Kubernetes que liga.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Como tratar o Azure Arc expirado permitiu recursos kubernetes?

O certificado de identidade de serviço gerido (MSI) associado ao seu Arco Azure habilitado a Kuberenetes tem uma janela de validade de 90 dias. Uma vez expirado este certificado, o recurso é considerado `Expired` e todas as funcionalidades como configuração, monitorização e política deixam de funcionar neste cluster. Siga estes passos para que o seu cluster Kubernetes volte a trabalhar com o Azure Arc:

1. Eliminar Azure Arc habilitado recurso e agentes kubernetes no cluster 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Recriar o Arco Azure permitiu o recurso Kubernetes, implantando agentes no cluster novamente.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` também eliminará as configurações no topo do cluster. Depois de `az connectedk8s connect` correr, crie novamente as configurações do cluster, manualmente ou utilizando a Política Azure.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Se já estou a usar oleodutos CI/CD, ainda posso utilizar o Azure Arc ativado por Kubernetes e configurações?

Sim, ainda pode utilizar configurações num cluster que recebe implementações através de um pipeline CI/CD. Em comparação com os oleodutos tradicionais ci/CD, as configurações apresentam dois benefícios adicionais:

**Reconciliação à deriva**

O gasoduto CI/CD aplica alterações apenas uma vez durante o curso do gasoduto. No entanto, o operador de GitOps no cluster continuamente sonda o repositório de Git para obter o estado desejado dos recursos de Kubernetes no cluster. Se o operador do GitOps considerar que o estado de recursos desejado é diferente do estado real dos recursos no cluster, esta deriva é reconciliada.

**Aplicar GitOps à escala**

Os oleodutos CI/CD são bons para implantações conduzidas por eventos para o seu cluster Kubernetes, onde o evento pode ser um empurrão para um repositório de Git. No entanto, a implantação da mesma configuração para todos os seus clusters Kubernetes requer que o gasoduto CI/CD seja configurado com credenciais de cada um destes clusters Kubernetes manualmente. Por outro lado, no caso do Azure Arc ativado pela Kubernetes, uma vez que o Azure Resource Manager gere as suas configurações, pode utilizar a Azure Policy para automatizar a aplicação da configuração pretendida em todos os clusters Kubernetes sob uma subscrição ou âmbito de grupo de recursos de uma só vez. Esta capacidade é mesmo aplicável aos recursos de Azure Arc ativados pela Kubernetes criados após a atribuição da política.

A funcionalidade de configurações é usada para aplicar configurações de base como políticas de rede, encadernações de funções e políticas de segurança de pod em todo o inventário de clusters Kubernetes para requisitos de conformidade e governação.

## <a name="next-steps"></a>Passos seguintes

* [Ligue um cluster ao Arco de Azure](./connect-cluster.md)
* [Crie configurações no seu cluster Kubernetes ativado pelo Arco](./use-gitops-connected-cluster.md)
* [Use a Política Azure para aplicar configurações à escala](./use-azure-policy.md)
