---
title: Consulte as suas implementações da Kubernetes no Azure para implementar as melhores práticas
description: Saiba como verificar a implementação das melhores práticas nas suas implementações no Serviço Azure Kubernetes utilizando kube-advisor
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 29ea7dba1df8bc7c68e3d17563a51b784ce4a561
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595438"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Verificar as melhores práticas da Kubernetes no seu cluster

Existem várias boas práticas que deve seguir nas suas implementações kubernetes para garantir o melhor desempenho e resiliência para as suas aplicações. Pode utilizar a ferramenta kube-advisor para procurar implementações que não seguem essas sugestões.

## <a name="about-kube-advisor"></a>Sobre kube-advisor

A [ferramenta kube-advisor][kube-advisor-github] é um único recipiente projetado para ser executado no seu cluster. Consulta o servidor Kubernetes API para obter informações sobre as suas implementações e devolve um conjunto de melhorias sugeridas.

A ferramenta kube-advisor pode reportar sobre o pedido de recursos e os limites em falta em PodSpecs para aplicações Windows, bem como aplicações Linux, mas a própria ferramenta de kube-advisor deve ser agendada num casulo Linux. Você pode agendar uma cápsula para correr em uma piscina de nó com um SISTEMA específico usando um [seletor][k8s-node-selector] de nó na configuração do casulo.

> [!NOTE]
> A ferramenta kube-advisor é suportada pela Microsoft numa base de melhor esforço. As questões e sugestões devem ser arquivadas no GitHub.

## <a name="running-kube-advisor"></a>Running kube-advisor

Para executar a ferramenta num cluster configurado para [o controlo de acesso baseado em funções (RBAC),](azure-ad-integration.md)utilizando os seguintes comandos. O primeiro comando cria uma conta de serviço Kubernetes. O segundo comando executa a ferramenta numa cápsula utilizando essa conta de serviço e configura a cápsula para eliminação após a sua saída. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Se não estiver a utilizar o RBAC, pode executar o comando da seguinte forma:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Dentro de alguns segundos, deve ver uma tabela descrevendo potenciais melhorias nas suas implementações.

![Saída de kube-advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Verificações realizadas

A ferramenta valida várias boas práticas kubernetes, cada uma com a sua própria reparação sugerida.

### <a name="resource-requests-and-limits"></a>Pedidos e limites de recursos

A Kubernetes suporta a definição de [pedidos de recursos e limites nas especificações][kube-cpumem]do pod . O pedido define o CPU mínimo e a memória necessária para executar o recipiente. O limite define o CPU máximo e a memória que deve ser permitida.

Por predefinição, não são definidos pedidos ou limites nas especificações do casulo. Isto pode levar a que os nós sejam sobreprogramados e os contentores esfomeados. A ferramenta kube-advisor destaca as cápsulas sem pedidos e limites definidos.

## <a name="cleaning-up"></a>Limpeza

Se o seu cluster tiver RBAC ativado, pode limpar o `ClusterRoleBinding` depois de ter executado a ferramenta utilizando o seguinte comando:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Se estiver a executar a ferramenta contra um cluster que não esteja ativado por RBAC, não é necessária qualquer limpeza.

## <a name="next-steps"></a>Passos seguintes

- [Problemas de resolução de problemas com o Serviço Azure Kubernetes](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors