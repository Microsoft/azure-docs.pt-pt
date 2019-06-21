---
title: Versões suportadas do Kubernetes no Azure Kubernetes Service
description: Compreender a política de suporte de versão do Kubernetes e o ciclo de vida de clusters no Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: b7910ee6f58c582b824cec834d92a24c0e184bfb
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205281"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versões suportadas do Kubernetes no Azure Kubernetes Service (AKS)

A comunidade do Kubernetes lança versões secundárias a cada três meses. Estas versões incluem novas funcionalidades e melhorias. As versões de patch são mais frequentes (às vezes semanais) e destinam-se apenas a correções de erros críticos numa versão secundária. Estas versões de patch incluem correções para vulnerabilidades de segurança ou bugs principais afetar um grande número de clientes e produtos em execução na produção com base no Kubernetes.

AKS pretende certificar e disponibilizar novas versões do Kubernetes dentro de 30 dias de um lançamento a montante, sujeita a estabilidade da versão.

## <a name="kubernetes-versions"></a>Versões do Kubernetes

Kubernetes utiliza o padrão [semântica do controle de versão](https://semver.org/) esquema de versões. Isso significa que cada versão do Kubernetes segue este esquema de numeração:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

Cada número na versão indica gerais de compatibilidade com a versão anterior:

* Alterações de API de alteração de versões principais quando incompatíveis ou para trás, compatibilidade pode estar quebrada.
* As versões secundárias alterar quando são feitas alterações de funcionalidade que são retrocompatíveis com as outras versões menores.
* São feitas alterações de versões de patch quando correções de erros de retroativamente compatível.

Em geral, os utilizadores devem esforçar executar a versão mais recente do patch da versão secundária estão a executar, por exemplo, se o cluster de produção no *1.13.6* e *1.13.7* é o patch mais recente disponível versão disponível para o *1.13* série, deve atualizar para o *1.13.7* assim que é possível garantir que o cluster está totalmente corrigido e suportado.

## <a name="kubernetes-version-support-policy"></a>Política de suporte da versão do Kubernetes

O AKS suporta quatro versões secundárias do Kubernetes:

* A atual versão secundária que é fornecida no AKS (N)
* Três versões secundárias anteriores. Cada versão secundária suportada também suporta dois patches estáveis.

Isso é conhecido como "N-3"-(N (versão mais recente) - 3 (as versões secundárias)).

Por exemplo, se apresenta o AKS *1.13.x* hoje em dia, o suporte é fornecido para as seguintes versões:

Nova versão secundária    |    Lista de versões suportadas
-----------------    |    ----------------------
1.13.x               |    1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

Em que "x" e ".a" e ".b" são versões de patch representativa.

Para obter detalhes sobre comunicações referentes a alterações de versão e as expectativas, consulte "Comunicações", abaixo.

Quando uma nova versão secundária é introduzida, as mais antigas pequenas versão e patch em versões suportadas são preteridas e removidas. Para o exemplo, se a lista de versões de suporte atual é:

<a name="supported-version-list"></a>Lista de versões suportadas
----------------------
1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b, 1.9.a, 1.9.b

E o AKS libera 1.13.x, isso significa que as versões de 1.9.x (todas as 1,9 versões) serão removidas e sem suporte.

> [!NOTE]
> Tenha em atenção, que se os clientes estão a executar uma versão não suportada do Kubernetes, serão solicitados a atualização ao pedir suporte para o cluster. Clusters a executar versões do Kubernetes não suportadas não são abrangidos pela [AKS suportam políticas de](https://docs.microsoft.com/azure/aks/support-policies).


Além do exposto acima sobre as versões secundárias, AKS suporta os dois mais recente *patch** versões de uma determinada versão secundária. Por exemplo, tendo em conta as seguintes versões suportadas:

<a name="supported-version-list"></a>Lista de versões suportadas
----------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5

Se o Kubernetes de origem a lançado 1.12.3 e 1.11.6 e versões do AKS as versões de patches, as versões mais antigas do patch são despromovidas e serão removidas e a lista de versões suportadas torna-se:

<a name="supported-version-list"></a>Lista de versões suportadas
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*

> [!NOTE]
> Os clientes não devem afixar criação do cluster, CI ou outras tarefas automatizadas para versões de patch específico. 

### <a name="communications"></a>Comunicações

* Para a nova **pequenas** versões do Kubernetes
  * Todos os utilizadores são notificados publicamente da nova versão e qual versão será removida.
  * Quando for lançada uma nova versão de patch, a versão de patch mais antiga é removida ao mesmo tempo.
  * Os clientes têm **60 dias** a contar da data notificação pública para atualizar para uma versão mais pequena suportada.
* Para a nova **patch** versões do Kubernetes
  * Todos os utilizadores são notificados da nova versão de patch que serão lançadas e para atualizar para a versão mais recente do patch.
  * Os utilizadores têm **30 dias** para atualizar para uma versão de patch mais recente, suportados. Os utilizadores têm **30 dias** para atualizar para uma versão de patch suportados antes do mais antigo é removido.

AKS define "lançados" como a disponibilidade geral, ativada em todos os SLO / qualidade das medidas de serviço e está disponível em todas as regiões.

> [!NOTE]
> Os clientes são notificados do Kubernetes, de versões, e depreciações, quando uma versão secundária é preterido/removidas os utilizadores recebem 60 dias para atualizar para uma versão suportada. No caso de versões de patch, os clientes recebem 30 dias para atualizar para uma versão suportada.

As notificações são enviadas através de:

* [Notas de versão do AKS](https://aka.ms/aks/releasenotes)
* Notificações de portal do Azure
* [Canal de atualização do Azure][azure-update-channel]

### <a name="policy-exceptions"></a>Exceções da política

AKS se reserva o direito de adicionar ou remover versões novas/existentes que tenham sido identificadas para ter uma ou mais produção críticas afetar erros ou problemas de segurança sem aviso.

Versões de patch específico podem ser ignorados ou implementação acelerada dependendo da gravidade do problema de bug ou segurança.

### <a name="azure-portal-and-cli-default-versions"></a>Portal do Azure e as versões do padrão CLI

Quando implementa um cluster do AKS no portal ou com a CLI do Azure, o cluster está sempre definido como a versão secundária de n-1 e os patches mais recentes. Por exemplo, se suportar do AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.a*  +   *1.11.b*, *1.10.a* + *1.10b*, é a versão predefinida para novos clusters *1.12.b*.

AKS o padrão é n-1 (minor.latestPatch, por exemplo, 1.12.b) para fornecer aos clientes uma tropa conhecida e corrigido versão por predefinição.

## <a name="list-currently-supported-versions"></a>Lista de versões suportadas atualmente

Para saber quais as versões estão atualmente disponíveis para a sua subscrição e região, utilize o [az aks get-versões] [ az-aks-get-versions] comando. O exemplo seguinte lista as versões Kubernetes disponíveis para o *EastUS* região:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

O resultado é semelhante ao exemplo seguinte, que mostra que a versão Kubernetes *1.13.5* é a versão mais recente disponível:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>FAQ

**O que acontece quando um cliente é atualizado um cluster do Kubernetes com uma versão secundária que não é suportada?**

Se estiver a utilizar o *n-4* versão, estão fora de suporte e será pedido para atualizar. Se a atualização da versão n-4 para n-3 tiver êxito, está agora em nossas políticas de suporte. Por exemplo:

- Se forem as versões suportadas do AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +  *1!d 1.11*, e *1.10.e* + *1.10F* e estiver a utilizar *1.9.g* ou *1.9.h*, estão fora do suporte.
- Se a atualização a partir de *1.9.g* ou *1.9.h* para *1.10.e* ou *1.10.f* tiver êxito, está novamente no dentro as nossas políticas de suporte.

As atualizações para versões mais antigas que *n-4* não são suportadas. Nesses casos, recomendamos aos clientes criar novos clusters do AKS e voltar a implementar as cargas de trabalho.

**O que significa "de suporte"**

"Fora de suporte" significa que a versão que está a executar está fora da lista de versões suportadas e será pedido para atualizar o cluster para uma versão suportada quando pedir suporte. Além disso, o AKS não faz qualquer tempo de execução ou de outras garantias para clusters de fora da lista de versões suportadas.

**O que acontece quando um cliente dimensiona um cluster de Kubernetes com uma versão secundária que não é suportada?**

Para as versões secundárias não suportadas pelo AKS, dimensionamento e reduzir continua a funcionar sem problemas.

**Um cliente manter-numa versão do Kubernetes para sempre?**

Sim. No entanto, se o cluster não estiver em uma das versões suportadas pelo AKS, o cluster está fora das políticas de suporte do AKS. Azure automaticamente não atualizar o seu cluster ou eliminá-lo.

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
