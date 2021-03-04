---
title: Azure Arc permitiu o Programa de Validação de Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Descreve o programa de validação do Arco para distribuição de Kubernetes
keywords: Kubernetes, Arc, Azure, K8s, validação
ms.openlocfilehash: 819df906add6275997e01fab310fe8dd57a87b51
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121376"
---
# <a name="azure-arc-validation-program"></a>Programa de validação do Arco azul

Azure Arc permitiu que Kubernetes trabalha com qualquer cluster certificado de Kubernetes (Cloud Native Computing Foundation). A equipa da Azure Arc também trabalhou com a indústria-chave Kubernetes, oferecendo fornecedores para validar a Azure Arc habilitado a Kubernetes com as suas distribuições Kubernetes. Futuras versões principais e menores das distribuições de Kubernetes lançadas por estes fornecedores serão validadas para compatibilidade com a Azure Arc ativada kubernetes.

## <a name="validated-distributions"></a>Distribuições validadas

Os seguintes fornecedores de distribuição e infraestrutura da Kubernetes forneceram com sucesso os testes de conformidade para a Azure Arc ativada pela Kubernetes:

| Fornecedor de distribuição e infraestruturas | Versão |
| ---------------------------------------- | ------- |
| Cluster API Provider on Azure            | Versão de lançamento: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); Versão Kubernetes: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| AKS em Azure Stack HCI                   | Versão de lançamento: [Atualização de dezembro de 2020](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); Versão Kubernetes: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

Os seguintes fornecedores e as respetivas distribuições de Kubernetes passaram com sucesso nos testes de conformidade para a Azure Arc ativada pela Kubernetes:

| Nome do fornecedor | Nome de distribuição | Versão |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4.6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4.7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Rede Tanzu Kubernetes](https://tanzu.vmware.com/kubernetes-grid) | Versão Kubernetes: v1.17.5 |
| Canónico    | [Kubernetes encantados](https://ubuntu.com/kubernetes) | [1.19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Motor Rancher Kubernetes](https://rancher.com/products/rke/) | Versão RKE CLI: [v1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); Versões Kubernetes: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Rio Nutanix      | [Rio Karbon](https://www.nutanix.com/products/karbon)    | Versão 2.2.1 |

A equipa do Azure Arc também realizou os testes de conformidade e validou o Azure Arc permitiu cenários de Kubernetes nos seguintes fornecedores públicos de nuvem:

| Nome do fornecedor de nuvem pública | Nome de distribuição | Versão |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | Serviço de Kubernetes Elástico (EKS) | v1.18.9  |
| Plataforma Google Cloud      | Google Kubernetes Engine (GKE) | v1.17.15 |

## <a name="scenarios-validated"></a>Cenários validados

Os testes de conformidade realizados como parte do Arco Azure permitiram que a validação de Kubernetes cobrisse os seguintes cenários:

1. Ligue os clusters Kubernetes ao Arco de Azure: 
    * Implementar Azure Arc ativou o agente Kubernetes Helm gráfico no cluster.
    * Configurar o certificado de identidade do sistema gerido (MSI) no cluster.
    * Os agentes enviam metadados de cluster para o Azure.

2. Configuração: 
    * Crie uma configuração em cima do recurso Azure Arc ativado por Kubernetes.
    * [O fluxo](https://docs.fluxcd.io/), necessário para a criação do fluxo de trabalho gitOps, é implantado no cluster.
    * Fluxo puxa manifestos e gráficos helm da demo Git repo e implanta para cluster.

## <a name="next-steps"></a>Passos seguintes

Aprenda a ligar um cluster ao Arco de Azure.
> [!div class="nextstepaction"]
> [Ligue um cluster ao Arco de Azure](./quickstart-connect-cluster.md)
