---
title: Verifique as suas implementações de Kubernetes no Azure para implementação de práticas recomendadas
description: Saiba como verificar a existência de implementação de melhores práticas das implementações no serviço de Kubernetes do Azure utilizando o Assistente do kube
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 29f98e334b0d2527b5159e1a5394109c5041024a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465381"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Verificar a existência de práticas recomendadas do Kubernetes no seu cluster

Existem várias práticas recomendadas que deve seguir nas suas implantações do Kubernetes para garantir o melhor desempenho e resiliência para as suas aplicações. Pode utilizar a ferramenta de aconselhamento do kube para procurar as implementações que não estão a seguir essas sugestões.

## <a name="about-kube-advisor"></a>Sobre o kube advisor

O [ferramenta de aconselhamento kube] [ kube-advisor-github] é um único contentor foi concebido para ser executado no seu cluster. Ele consulta o servidor de API do Kubernetes para obter informações sobre as implementações e devolve um conjunto de melhorias sugeridas.

> [!NOTE]
> A ferramenta de aconselhamento do kube é suportada pela Microsoft numa base de melhor esforço. Problemas e sugestões devem ser arquivadas no GitHub.

## <a name="running-kube-advisor"></a>A executar o Assistente do kube

Para executar a ferramenta num cluster que está configurado para [controlo de acesso baseado em funções (RBAC)](azure-ad-integration.md), com os comandos seguintes. O primeiro comando cria uma conta de serviço do Kubernetes. O segundo comando executa a ferramenta num pod com essa conta de serviço e configura o pod para eliminação após sai. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Se não estiver a utilizar o RBAC, pode executar o comando da seguinte forma:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Dentro de alguns segundos, deverá ver uma tabela que descreve possíveis melhorias para as suas implementações.

![Saída do Kube advisor](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Verificações realizadas

A ferramenta valida várias Kubernetes melhores práticas, cada um com seus próprios remediação sugeridos.

### <a name="resource-requests-and-limits"></a>Pedidos de recursos e limites

Kubernetes compatível com a definição [pedidos de recursos e limita-se nas especificações do pod][kube-cpumem]. O pedido define o mínimo da CPU e memória necessários para executar o contentor. O limite define o máximo da CPU e memória que deve ser permitida.

Por predefinição, não existem pedidos ou os limites são definidas nas especificações do pod. Isso pode levar a que está a ser overscheduled de nós e os contentores que está a ser privados. A ferramenta de aconselhamento do kube realça os pods sem pedidos e nos limites do conjunto.

## <a name="cleaning-up"></a>Limpeza

Se o seu cluster tiver RBAC ativada, é possível limpar o `ClusterRoleBinding` depois de executar a ferramenta com o seguinte comando:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Se estiver a executar a ferramenta em relação a um cluster que não está ativado o RBAC, limpeza de não é necessária.

## <a name="next-steps"></a>Passos Seguintes

- [Resolver problemas com o Azure Kubernetes Service](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor