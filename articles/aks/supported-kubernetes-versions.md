---
title: Versões Kubernetes suportadas no Serviço Azure Kubernetes
description: Compreender a política de suporte da versão Kubernetes e o ciclo de vida dos clusters no Serviço Azure Kubernetes (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 8c144fb0c9818e21c2ca5bd61525067b485531bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026120"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versões Kubernetes suportadas no Serviço Azure Kubernetes (AKS)

A comunidade do Kubernetes lança versões secundárias a cada três meses. Estas versões incluem novas funcionalidades e melhorias. As versões de patch são mais frequentes (às vezes semanais) e destinam-se apenas a correções de erros críticos numa versão secundária. Estas versões de patch incluem correções para vulnerabilidades de segurança ou grandes bugs com impacto num grande número de clientes e produtos em produção com base em Kubernetes.

A AKS pretende certificar e lançar novas versões Kubernetes no prazo de 30 dias após uma versão a montante, sujeita à estabilidade do lançamento.

## <a name="kubernetes-versions"></a>Versões Kubernetes

Kubernetes usa o esquema de controle de versão de [controle semântico](https://semver.org/) de versão padrão. Isto significa que cada versão de Kubernetes segue este esquema de numeração:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Cada número na versão indica compatibilidade geral com a versão anterior:

* As versões principais mudam quando alterações incompatíveis da API ou compatibilidade para trás podem ser quebradas.
* As versões menores mudam quando são feitas alterações de funcionalidade que são compatíveis com as outras pequenas versões.
* As versões de patch mudam quando são feitas correções de bugs compatíveis para trás.

Os utilizadores devem ter como objetivo executar o mais recente lançamento de patch da versão menor que estão a executar, por exemplo, se o seu cluster de produção estiver em *1.12.14* e *1.12.15* é a mais recente versão de patch disponível para a série *1.12,* deve atualizar para *1.12.15* assim que puder garantir que o seu cluster está totalmente remendado e suportado.

## <a name="kubernetes-version-support-policy"></a>Política de apoio à versão Kubernetes

A AKS suporta três versões menores de Kubernetes:

* A versão menor atual que é lançada em AKS (N)
* Duas versões menores anteriores. Cada versão secundária suportada também suporta dois patches estáveis.

Isto é conhecido como "N-2": (N (Última versão) - 2 (versões menores)).

Por exemplo, se o AKS introduzir *hoje 1.15.a,* é fornecido suporte para as seguintes versões:

Nova versão menor    |    Lista de versões suportadas
-----------------    |    ----------------------
1.15.a               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f

Onde ".carta" é representativo das versões de patch.

Para mais detalhes sobre comunicações sobre alterações de versão e expectativas, consulte "Comunicações" abaixo.

Quando uma nova versão menor é introduzida, as versões menores mais antigas e os lançamentos de patch suportados são depreciados e removidos. Por exemplo, se a lista de versão suportada atual for:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

E aks liberta 1.16. *Isto significa que a 1.13.* as versões (todas as versões 1.13) serão removidas e fora de suporte.

> [!NOTE]
> Por favor, note que se os clientes estiverem executando uma versão Kubernetes não suportada, eles serão solicitados a fazer upgrade ao solicitar apoio para o cluster. Os clusters que executam lançamentos kubernetes não apoiados não estão abrangidos pelas políticas de [apoio aks.](https://docs.microsoft.com/azure/aks/support-policies)

Além do acima em versões menores, a AKS suporta as duas últimas versões de **patch** de uma determinada versão menor. Por exemplo, tendo em conta as seguintes versões suportadas:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Se kubernetes a montante lançados 1.15.3 e 1.14.6 e AKS lançar essas versões patch, as versões de patch mais antigas são depreciadas e removidas, e a lista de versão suportada torna-se:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Comunicações

* Para novas versões **menores** de Kubernetes
  * Todos os utilizadores são notificados publicamente da nova versão e qual a versão que será removida.
  * Quando uma nova versão patch é lançada, o lançamento de patch mais antigo é removido ao mesmo tempo.
  * Os clientes têm **30 dias** a partir da data de notificação pública para fazer upgrade para uma versão secundária suportada.
* Para novas versões de **patch** de Kubernetes
  * Todos os utilizadores são notificados da versão patch que está a ser lançada e para fazer o upgrade para o mais recente lançamento do patch.
  * Os utilizadores têm **30 dias** para fazer o upgrade para um novo patch suportado. Os utilizadores têm **30 dias** para fazer o upgrade para uma versão de patch suportada antes de o mais antigo ser removido.

A AKS define uma "versão lançada" como as versões geralmente disponíveis, ativadas em todas as medições SLO/Qualidade de Serviço e disponíveis em todas as regiões. O AKS também pode suportar versões de pré-visualização que estejam explicitamente rotuladas e sujeitas a termos e condições de pré-visualização.

#### <a name="notification-channels-for-aks-changes"></a>Canais de notificação para alterações aks

A AKS publica atualizações regulares de serviços que resumem novas versões Kubernetes, alterações de serviço e atualizações de componentes que foram lançadas no serviço no [GitHub](https://github.com/Azure/AKS/releases).

Estas alterações são reroladas para todos os clientes como parte da manutenção regular que é oferecida como parte do serviço gerido, alguns requerem atualizações explícitas, enquanto outros não requerem qualquer ação.

As notificações também são enviadas através de:

* [Notas de lançamento do AKS](https://aka.ms/aks/releasenotes)
* Notificações do portal do Azure
* [Canal de atualização Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Exceções políticas de versões suportadas

A AKS reserva-se o direito de adicionar ou remover versões novas/existentes que tenham sido identificadas como tendo uma ou mais questões críticas de produção com impacto ou problemas de segurança sem aviso prévio.

As libertações específicas de patch podem ser ignoradas ou o lançamento acelerado dependendo da gravidade do bug ou problema de segurança.

### <a name="azure-portal-and-cli-default-versions"></a>Versões padrão do portal Azure e cli

Quando se implanta um cluster AKS no portal ou com o Azure CLI, o cluster é indefinido para a versão menor N-1 e patch mais recente. Por exemplo, se o AKS suportar *1.15.a*, *1.15.b*, *1.14.c*, *1.14.d*, *1.13.e*e , e *1.13.f*, a versão predefinida selecionada é *de 1.14.c*.

A AKS escolhe o padrão da N-1 para fornecer aos clientes uma versão conhecida, estável e remendada por padrão.

## <a name="list-currently-supported-versions"></a>Lista atualmente suportadas versões

Para saber quais as versões atualmente disponíveis para a sua subscrição e região, utilize o comando [az aks get-versions.][az-aks-get-versions] O exemplo seguinte lista as versões Kubernetes disponíveis para a região *de EastUS:*

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>FAQ

**O que acontece quando um cliente atualiza um cluster Kubernetes com uma versão menor que não é suportada?**

Se estiver na versão *n-3,* está fora do suporte e será solicitado a fazer upgrade. Se o seu upgrade da versão n-3 para n-2 for bem sucedido, está agora dentro das nossas políticas de apoio. Por exemplo:

- Se a versão AKS suportada mais antiga for *de 1.13.a* e estiver em *1.12.b* ou mais, está fora do suporte.
- Se a atualização de *1.12.b* para *1.13.a* ou superior tiver sucesso, está de volta às nossas políticas de apoio.

As atualizações para versões mais antigas do que a janela suportada do *N-2* não são suportadas. Nestes casos, recomendamos que os clientes criem novos clusters AKS e reimplementem as suas cargas de trabalho com versões na janela suportada.

**O que significa "Fora do Apoio"**

'Fora do Suporte' significa que a versão que está a executar está fora da lista de versões suportadas, e será-lhe pedido que atualize o cluster para uma versão suportada ao solicitar apoio. Além disso, a AKS não faz qualquer prazo de execução ou outras garantias para clusters fora da lista de versões suportadas.

**O que acontece quando um cliente escala um cluster Kubernetes com uma versão menor que não é suportada?**

Para versões menores não suportadas pela AKS, a escalação dentro ou fora deve continuar a funcionar, mas é altamente recomendado fazer upgrade para trazer o seu cluster de volta ao suporte.

**Um cliente pode ficar numa versão Kubernetes para sempre?**

Sim. No entanto, se o cluster não estiver numa das versões suportadas pela AKS, o cluster está fora das políticas de apoio da AKS. O Azure não atualiza automaticamente o seu cluster nem o apaga.

**Que versão suporta o plano de controlo se o conjunto de nós não estiver numa das versões AKS suportadas?**

O plano de controlo deve estar dentro de uma janela de versões de todas as piscinas de nós. Para mais informações sobre a modernização das piscinas de aviões de controlo ou nó, visite documentação sobre piscinas de nó de [upgrade.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como atualizar o seu cluster, consulte [Upgrade um cluster do Serviço Azure Kubernetes (AKS).][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
