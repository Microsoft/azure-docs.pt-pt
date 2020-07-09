---
title: Versões do Kubernetes suportadas no Azure Kubernetes Service
description: Compreenda a política de suporte da versão Kubernetes e o ciclo de vida dos clusters no Serviço Azure Kubernetes (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 5f396ba6ec1ecc6bd111e048ce34e3546c7364dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84345058"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Supported Kubernetes versions in Azure Kubernetes Service (AKS) (Versões do Kubernetes suportadas no Azure Kubernetes Service [AKS])

A comunidade kubernetes lança versões menores aproximadamente a cada três meses. Estes lançamentos incluem novas funcionalidades e melhorias. Os lançamentos de patch são mais frequentes (por vezes semanais) e destinam-se apenas a correções críticas de bugs numa versão menor. Estes lançamentos de patch incluem correções para vulnerabilidades de segurança ou bugs importantes com impacto num grande número de clientes e produtos que estão a ser geridos em produção com base em Kubernetes.

A AKS pretende certificar e lançar novas versões Kubernetes no prazo de 30 dias após o lançamento a montante, sujeito à estabilidade do lançamento.

## <a name="kubernetes-versions"></a>Versões Kubernetes

Kubernetes usa o esquema padrão de versão [semântica.](https://semver.org/) Isto significa que cada versão de Kubernetes segue este esquema de numeração:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Cada número na versão indica compatibilidade geral com a versão anterior:

* As versões principais mudam quando a API incompatível muda ou a retrocompatibilidade pode ser quebrada.
* As versões menores mudam quando são feitas alterações de funcionalidade que são compatíveis com as outras versões menores.
* As versões de correção mudam quando são feitas correções de erro compatíveis com retro-volta.

Os utilizadores devem ter como objetivo executar o mais recente patch de versão que estão a executar, por exemplo se o seu cluster de produção estiver em *1.12.14* e *1.12.15* é a versão mais recente disponível para o patch disponível para a série *1.12,* deve atualizar para *1.12.15* assim que puder garantir que o seu cluster está totalmente remendado e suportado.

## <a name="kubernetes-version-support-policy"></a>Política de suporte à versão Kubernetes

AKS suporta três versões menores de Kubernetes:

* A versão menor atual que é lançada em AKS (N)
* Duas versões menores anteriores. Cada versão menor suportada também suporta duas manchas estáveis.

Isto é conhecido como "N-2": (N (Última versão) - 2 (versões menores)).

Por exemplo, se a AKS introduzir *hoje 1.15.a,* é fornecido suporte para as seguintes versões:

Nova versão menor    |    Lista de versão suportada
-----------------    |    ----------------------
1.15.a               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f

Onde ".letter" é representativo das versões de patch.

Para mais detalhes sobre as alterações de versão e expectativas, consulte "Comunicações" abaixo.

Quando uma nova versão menor é introduzida, a versão menor mais antiga e os lançamentos de patch suportados são depreciados e removidos. Por exemplo, se a lista de versão suportada atual for:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

E aKS lança 1.16. *, isto significa que o 1.13.* versões (todas as versões 1.13) serão removidas e estão fora de suporte.

> [!NOTE]
> Por favor, note que se os clientes estiverem a executar uma versão Kubernetes não suportada, serão solicitados a fazer upgrade quando solicitarem suporte para o cluster. Os clusters que executam lançamentos kubernetes não suportados não são abrangidos pelas políticas de apoio da [AKS.](https://docs.microsoft.com/azure/aks/support-policies)

Além do que precede em versões menores, a AKS suporta as duas mais recentes versões de **patch** de uma determinada versão menor. Por exemplo, dadas as seguintes versões suportadas:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Se a montante kubernetes lançou 1.15.3 e 1.14.6 e AKS lança essas versões patch, as versões patch mais antigas são depreciadas e removidas, e a lista de versão suportada torna-se:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Comunicações

* Para novas **versões menores** de Kubernetes
  * Todos os utilizadores são notificados publicamente da nova versão e que versão será removida.
  * Quando uma nova versão patch é lançada, o patch mais antigo é removido ao mesmo tempo.
  * O suporte Azure fornece aos clientes **30 dias** a partir da data de notificação pública para atualizar para uma versão menor suportada. Uma vez passados 30 dias, é-lhe exigido que atualize a sua versão menor para continuar a receber suporte.
* Para novas **versões** de patch de Kubernetes
  * Todos os utilizadores são notificados da versão do novo patch que foi lançada e para atualizar para a versão mais recente do patch.
  * O suporte Azure oferece aos clientes **30 dias** para fazer o upgrade para um patch de lançamento suportado, após a remoção de uma versão patch mais antiga. Uma vez passados 30 dias, é-lhe exigido que atualize a sua versão patch para continuar a receber suporte.

A AKS define uma "versão lançada" como as versões geralmente disponíveis, ativadas em todas as medições SLO/Qualidade do Serviço e disponíveis em todas as regiões. A AKS também pode suportar versões de pré-visualização que estejam explicitamente rotuladas e sujeitas a termos e condições de pré-visualização.

#### <a name="notification-channels-for-aks-changes"></a>Canais de notificação para alterações AKS

A AKS publica atualizações regulares de serviço que resumem as novas versões Kubernetes, alterações de serviço e atualizações de componentes que foram lançadas no serviço no [GitHub](https://github.com/Azure/AKS/releases).

Estas alterações são enroladas para todos os clientes como parte da manutenção regular que é oferecida como parte do serviço gerido, alguns requerem atualizações explícitas enquanto outros não requerem nenhuma ação.

As notificações também são enviadas através de:

* [Notas de lançamento da AKS](https://aka.ms/aks/releasenotes)
* Notificações do portal do Azure
* [Canal de atualização Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Exceções políticas de versões suportadas

A AKS reserva-se o direito de adicionar ou remover versões novas/existentes que tenham sido identificadas para ter uma ou mais questões críticas de produção com impacto em bugs ou problemas de segurança sem aviso prévio.

As versões específicas do patch podem ser ignoradas ou aceleradas dependendo da gravidade do bug ou problema de segurança.

### <a name="azure-portal-and-cli-default-versions"></a>Versões padrão do portal Azure e do CLI

Quando implementa um cluster AKS no portal ou com o CLI Azure, o cluster é padrão para a versão menor N-1 e para o patch mais recente. Por exemplo, se a AKS suportar *1.15.a*, *1.15.b*, *1.14.c*, *1.14.d*, *1.13.e*, e *1.13.f*, a versão padrão selecionada é *1.14.c*.

A AKS escolhe o padrão de N-1 para fornecer aos clientes uma versão conhecida, estável e corrigida por padrão.

## <a name="list-currently-supported-versions"></a>Lista atualmente suportadas versões

Para saber quais as versões que estão atualmente disponíveis para a sua subscrição e região, utilize o comando [az aks get-verss.][az-aks-get-versions] O exemplo a seguir lista as versões Kubernetes disponíveis para a região *eastus:*

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>FAQ

**O que acontece quando um cliente atualiza um cluster Kubernetes com uma versão menor que não é suportada?**

Se estiver na versão *n-3,* está fora do suporte e será solicitado para fazer o upgrade. Se a sua atualização da versão n-3 para n-2 for bem sucedida, está agora dentro das nossas políticas de apoio. Por exemplo:

- Se a versão AKS suportada mais antiga for *1.13.a* e estiver em *1.12.b* ou mais antiga, está fora do suporte.
- Se a atualização de *1.12.b* para *1.13.a* ou superior tiver sucesso, está de volta às nossas políticas de apoio.

As atualizações para versões mais antigas do que a janela suportada de *N-2* não são suportadas. Nestes casos, recomendamos que os clientes criem novos clusters AKS e redistribuam as suas cargas de trabalho com versões na janela suportada.

**O que significa "Fora do Apoio"**

'Fora do Suporte' significa que a versão que está a executar está fora da lista de versões suportadas, e será solicitado que atualize o cluster para uma versão suportada ao solicitar suporte. Além disso, a AKS não faz qualquer tempo de execução ou outras garantias para clusters fora da lista de versões suportadas.

**O que acontece quando um cliente escala um cluster Kubernetes com uma versão menor que não é suportada?**

Para versões menores não suportadas por AKS, o dimensionamento dentro ou fora deve continuar a funcionar, mas é altamente recomendado fazer upgrade para trazer o seu cluster de volta ao suporte.

**Um cliente pode ficar numa versão Kubernetes para sempre?**

Se um cluster esteve sem suporte para mais de 3 versões menores e foi encontrado para transportar riscos de segurança, a Azure contacta-o para atualizar proativamente o seu cluster. Se não tomar mais medidas, a Azure reserva-se o direito de forçar o upgrade do seu cluster em seu nome.

**Que versão suporta o plano de controlo se o conjunto de nós não estiver numa das versões AKS suportadas?**

O plano de controlo deve estar dentro de uma janela de versões de todas as piscinas de nós. Para obter informações sobre a atualização do plano de controlo ou piscinas de nó, visite documentação sobre [a atualização das piscinas de nó.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre como atualizar o seu cluster, consulte [upgrade de um cluster Azure Kubernetes Service (AKS).][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
