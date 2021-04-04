---
title: Verifique as implementações para as melhores práticas
titleSuffix: Azure Kubernetes Service
description: Saiba como verificar a implementação das melhores práticas nas suas implementações no Serviço Azure Kubernetes utilizando o kube-advisor
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 7730146f30487eb5d20f0d3138e9e5ba799daa99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94681521"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Verificar a existência de melhores práticas do Kubernetes no seu cluster

Existem várias boas práticas que deve seguir nas suas implementações de Kubernetes para garantir o melhor desempenho e resiliência para as suas aplicações. Pode utilizar a ferramenta kube-advisor para procurar implementações que não estejam a seguir essas sugestões.

## <a name="about-kube-advisor"></a>Sobre kube-advisor

A [ferramenta kube-advisor][kube-advisor-github] é um único recipiente projetado para ser executado no seu cluster. Consulta o servidor API da Kubernetes para obter informações sobre as suas implementações e devolve um conjunto de melhorias sugeridas.

A ferramenta kube-advisor pode reportar sobre o pedido de recursos e os limites em falta em PodSpecs para aplicações Windows, bem como aplicações Linux, mas a própria ferramenta de kube-advisor deve ser agendada num pod Linux. Pode agendar um pod para correr numa piscina de nó com um sistema operativo específico utilizando um [seletor de nó][k8s-node-selector] na configuração do pod.

> [!NOTE]
> A ferramenta kube-advisor é suportada pela Microsoft numa base de melhor esforço. As questões e sugestões devem ser apresentadas no GitHub.

## <a name="running-kube-advisor"></a>Corrida kube-advisor

Para executar a ferramenta num cluster configurado para [o controlo de acesso baseado em funções de Kubernetes (Kubernetes RBAC)](./azure-ad-integration-cli.md), utilizando os seguintes comandos. O primeiro comando cria uma conta de serviço Kubernetes. O segundo comando executa a ferramenta numa cápsula utilizando essa conta de serviço e configura a cápsula para eliminação após a sua saída. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }" --namespace default
```

Se não estiver a usar o RBAC de Kubernetes, pode executar o comando da seguinte forma:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Dentro de alguns segundos, deverá ver uma tabela descrevendo possíveis melhorias nas suas implementações.

![Saída de kube-advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Verificações realizadas

A ferramenta valida várias boas práticas de Kubernetes, cada uma com a sua própria remediação sugerida.

### <a name="resource-requests-and-limits"></a>Pedidos e limites de recursos

A Kubernetes suporta a definição de [pedidos de recursos e limites nas especificações do pod][kube-cpumem]. O pedido define o CPU mínimo e a memória necessárias para executar o contentor. O limite define o CPU máximo e a memória que deve ser permitida.

Por predefinição, não são definidos pedidos ou limites nas especificações do pod. Isto pode levar a que os nós sejam sobre-programados e os contentores sejam esfomeados. A ferramenta kube-advisor destaca as cápsulas sem pedidos e limites definidos.

## <a name="cleaning-up"></a>Limpeza

Se o seu cluster tiver o RBAC de Kubernetes ativado, pode limpar a `ClusterRoleBinding` ferramenta depois de ter executado a ferramenta utilizando o seguinte comando:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Se estiver a executar a ferramenta contra um cluster que não esteja ativado por NCC de Kubernetes, não é necessária nenhuma limpeza.

## <a name="next-steps"></a>Passos seguintes

- [Problemas de resolução de problemas com o Serviço Azure Kubernetes](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
