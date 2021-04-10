---
title: Azure Arc via Kubernetes frequentemente fazer perguntas
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo contém uma lista de perguntas frequentes relacionadas com a Azure Arc habilitada a Kubernetes
keywords: Kubernetes, Arc, Azure, contentores, configuração, GitOps, faq
ms.openlocfilehash: 84368cc63bd9aaf1df4fb281395b47a6e886cb7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025854"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Perguntas frequentes - Azure Arc habilitado Kubernetes

Este artigo aborda perguntas frequentes sobre Azure Arc habilitado Kubernetes.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Qual é a diferença entre o Azure Arc ativado pela Kubernetes e pelo Serviço Azure Kubernetes (AKS)?

AKS é a oferta gerida de Kubernetes pela Azure. A AKS simplifica a implementação de um cluster de Kubernetes gerido em Azure, descarregando grande parte da complexidade e sobrecarga operacional para Azure. Uma vez que os mestres Kubernetes são geridos pelo Azure, só consegues e manténs os nós dos agentes.

A azure Arc habilitado a Kubernetes permite-lhe alargar as capacidades de gestão da Azure (como o Azure Monitor e a Azure Policy) ligando os clusters kubernetes ao Azure. Mantém o aglomerado de Kubernetes subjacente.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Preciso ligar os meus clusters AKS em execução em Azure a Azure Arc?

N.º Todas as funcionalidades de Kubernetes habilitados pelo Azure Arc, incluindo o Azure Monitor e a Azure Policy (Gatekeeper), estão disponíveis em AKS (um recurso nativo em Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Devo ligar o meu cluster AKS-HCI e os clusters Kubernetes no Azure Stack Hub e no Azure Stack Edge ao Azure Arc?

Sim, ligar o seu cluster AKS-HCI ou agrupamentos Kubernetes no Azure Stack Edge ou no Azure Stack Hub ao Azure Arc fornece clusters com representação de recursos no Azure Resource Manager. Esta representação de recursos estende as capacidades como Cluster Configuration, Azure Monitor e Azure Policy (Gatekeeper) a clusters kubernetes conectados.

Se o cluster Kubernetes ativado pelo Arco Azure estiver no Azure Stack Edge, AKS on Azure Stack HCI (>= atualização de abril de 2021), ou AKS no Windows Server 2019 Datacenter (>= atualização de abril de 2021), então a configuração de Kubernetes está incluída sem custos.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Como tratar o Azure Arc expirado permitiu recursos kubernetes?

O certificado de Identidade de Serviço Gerido (MSI) associado ao seu Arco Azure habilitado a Kubernetes tem uma janela de validade de 90 dias. Uma vez expirado este certificado, o recurso é considerado `Expired` e todas as funcionalidades (tais como configuração, monitorização e política) deixam de trabalhar neste cluster. Para que o seu cluster Kubernetes volte a trabalhar com o Azure Arc:

1. Eliminar O Arco Azure ativou o recurso e agentes da Kubernetes no cluster. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Recriar o Arco Azure permitiu o recurso Kubernetes implantando agentes no cluster.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` também eliminará as configurações no topo do cluster. Depois de `az connectedk8s connect` correr, recrie as configurações do cluster, manualmente ou utilizando a Política Azure.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Se já estou a usar oleodutos CI/CD, ainda posso utilizar o Azure Arc ativado por Kubernetes e configurações?

Sim, ainda pode utilizar configurações num cluster que recebe implementações através de um pipeline CI/CD. Em comparação com os oleodutos tradicionais ci/CD, as configurações apresentam dois benefícios adicionais:

**Reconciliação à deriva**

O gasoduto CI/CD aplica alterações apenas uma vez durante o curso do gasoduto. No entanto, o operador de GitOps no cluster continuamente sonda o repositório de Git para obter o estado desejado dos recursos de Kubernetes no cluster. Se o operador do GitOps considerar que o estado de recursos desejado é diferente do estado real dos recursos no cluster, esta deriva é reconciliada.

**Aplicar GitOps à escala**

Os gasodutos CI/CD são úteis para implantações orientadas para eventos no seu cluster Kubernetes (por exemplo, um empurrão para um repositório de Git). No entanto, se pretender implantar a mesma configuração em todos os seus clusters Kubernetes, terá de configurar manualmente as credenciais de cada cluster Kubernetes para o pipeline CI/CD. 

Para o Azure Arc ativou kubernetes, uma vez que o Azure Resource Manager gere as suas configurações, pode automatizar a criação da mesma configuração em todos os recursos Azure Arc habilitados a Kubernetes usando a Azure Policy, no âmbito de uma subscrição ou de um grupo de recursos. Esta capacidade é mesmo aplicável aos recursos de Azure Arc ativados pela Kubernetes criados após a atribuição da política.

Esta funcionalidade aplica configurações de base (como políticas de rede, encadernações de funções e políticas de segurança de pod) em todo o inventário de clusters kubernetes para satisfazer os requisitos de conformidade e governação.

## <a name="next-steps"></a>Passos seguintes

* Caminhe pelo nosso quickstart para [ligar um cluster Kubernetes ao Arco Azure.](./quickstart-connect-cluster.md)
* Já tem um cluster Kubernetes ligado a Azure Arc? [Crie configurações no seu cluster Kubernetes ativado pelo Arco.](./tutorial-use-gitops-connected-cluster.md)
* Saiba como utilizar a [Política Azure para aplicar configurações à escala](./use-azure-policy.md).