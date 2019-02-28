---
title: Versões suportadas do Kubernetes no Azure Kubernetes Service
description: Compreender a política de suporte de versão do Kubernetes e o ciclo de vida de clusters no Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 02/15/2018
ms.author: saudas
ms.openlocfilehash: c55a12e6bd5ab57f790e5f161930851be10f5c2a
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985245"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versões suportadas do Kubernetes no Azure Kubernetes Service (AKS)

A comunidade do Kubernetes lança versões secundárias a cada três meses. Estas versões incluem novas funcionalidades e melhorias. As versões de patch são mais frequentes (às vezes semanais) e destinam-se apenas a correções de erros críticos numa versão secundária. Estas versões de patch incluem correções para vulnerabilidades de segurança ou bugs principais afetar um grande número de clientes e produtos em execução na produção com base no Kubernetes.

Kubernetes nova versão secundária é disponibilizada no [mecanismo de aks] [ aks-engine] no primeiro dia. O Objetivo do Nível de Serviço (SLO) do AKS é lançar a versão secundária dos clusters do AKS dentro de 30 dias, sujeito à estabilidade da versão.

## <a name="kubernetes-version-support-policy"></a>Política de suporte da versão do Kubernetes

O AKS suporta quatro versões secundárias do Kubernetes:

- A atual versão secundária que é lançado a montante (n)
- Três versões secundárias anteriores. Cada versão secundária suportada também suporta dois patches estáveis.

Por exemplo, se apresenta o AKS *1.12.x* hoje em dia, também é fornecido suporte para *1.11.a* + *1.11.b*, *1.10.c*  +  *1!d 1.10*, *1.9.e* + *1.9F* (em que as versões de patch letras são duas compilações estáveis mais recente).

Quando uma nova versão secundária é introduzida, a versão secundária mais antiga e as versões de patch suportadas são retiradas. 15 dias antes do lançamento da nova versão secundária e extinção de versão futura, é feito por meio de um anúncio a [canais de atualização do Azure][azure-update-channel]. No exemplo acima onde *1.12.x* é lançado, as versões retiradas são *1.8.g* + *1.8.h*.

Quando implementa um cluster do AKS no portal ou com a CLI do Azure, o cluster é sempre definido para a versão secundária n-1 e o patch mais recente. Por exemplo, se suportar do AKS *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +   *1.10 1!d*, *1.9.e* + *1.9F*, é a versão predefinida para novos clusters *1.11.b*.

## <a name="list-currently-supported-versions"></a>Lista de versões suportadas atualmente

Para saber quais as versões estão atualmente disponíveis para a sua subscrição e região, utilize o [az aks get-versões] [ az-aks-get-versions] comando. O exemplo seguinte lista as versões Kubernetes disponíveis para o *EastUS* região:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

O resultado é semelhante ao exemplo seguinte, que mostra que a versão Kubernetes *1.12.5* é a versão mais recente disponível:

```
KubernetesVersion    Upgrades
-------------------  -----------------------
1.12.5               None available
1.12.4               1.12.5
1.11.7               1.12.4, 1.12.5
1.11.6               1.11.7, 1.12.4, 1.12.5
1.10.12              1.11.6, 1.11.7
1.10.9               1.10.12, 1.11.6, 1.11.7
1.9.11               1.10.9, 1.10.12
1.9.10               1.9.11, 1.10.9, 1.10.12
```

## <a name="faq"></a>FAQ

**O que acontece quando um cliente é atualizado um cluster do Kubernetes com uma versão secundária que não é suportada?**

Se estiver a utilizar o *n-4* versão, está fora do SLO. Se a atualização da versão n-4 para n-3 for bem-sucedida, em seguida, está novamente no SLO. Por exemplo:

- Se forem as versões suportadas do AKS *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +  *1!d 1.10*, e *1.9.e* + *1.9F* e estiver a utilizar *1.8.g* ou *1.8.h*, está fora do SLO.
- Se a atualização a partir de *1.8.g* ou *1.8.h* para *1.9.e* ou *1.9.f* tiver êxito, está novamente no SLO.

As atualizações para versões mais antigas que *n-4* não são suportadas. Nesses casos, recomendamos aos clientes criar novos clusters do AKS e voltar a implementar as cargas de trabalho.

**O que acontece quando um cliente dimensiona um cluster de Kubernetes com uma versão secundária que não é suportada?**

Para as versões secundárias não suportadas pelo AKS, dimensionamento e reduzir continua a funcionar sem problemas.

**Um cliente manter-numa versão do Kubernetes para sempre?**

Sim. No entanto, se o cluster não estiver em uma das versões suportadas pelo AKS, o cluster está fora do SLO o AKS. Azure automaticamente não atualizar o seu cluster ou eliminá-lo.

**Qual versão é que o suporte principal se o cluster de agente não está em uma das versões suportadas do AKS?**

O mestre é atualizado automaticamente para a versão mais recente suportada.

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre como atualizar o seu cluster, consulte [atualizar um cluster do Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
