---
title: Versões do kubernetes com suporte no serviço kubernetes do Azure
description: Entender a política de suporte da versão kubernetes e o ciclo de vida dos clusters no AKS (serviço kubernetes do Azure)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: bba4196547bda3d3ddcf3344032de5b9286639a0
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996753"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versões do kubernetes com suporte no serviço kubernetes do Azure (AKS)

A comunidade do Kubernetes lança versões secundárias a cada três meses. Estas versões incluem novas funcionalidades e melhorias. As versões de patch são mais frequentes (às vezes semanais) e destinam-se apenas a correções de erros críticos numa versão secundária. Essas versões de patch incluem correções para vulnerabilidades de segurança ou principais bugs que afetam um grande número de clientes e produtos executados em produção com base em kubernetes.

O AKS tem o objetivo de certificar e lançar novas versões do kubernetes dentro de 30 dias após uma versão de upstream, sujeito à estabilidade da versão.

## <a name="kubernetes-versions"></a>Versões do kubernetes

Kubernetes usa o esquema de controle de versão de [controle semântico](https://semver.org/) de versão padrão. Isso significa que cada versão do kubernetes segue este esquema de numeração:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Cada número na versão indica compatibilidade geral com a versão anterior:

* As versões principais são alteradas quando as alterações de API incompatíveis ou a compatibilidade com versões anteriores podem ser interrompidas.
* As versões secundárias são alteradas quando são feitas alterações de funcionalidade que são compatíveis com versões anteriores a outros lançamentos secundários.
* As versões de patch são alteradas quando são feitas correções de bugs compatíveis com versões anteriores.

Os usuários devem visar executar a versão mais recente do patch da versão secundária em execução, por exemplo, se o cluster de produção estiver em *1.12.14* e *1.12.15* for a versão de patch mais recente disponível para a série *1,12* , você deverá atualizar para o *1.12.15* assim que for possível garantir que o cluster seja totalmente corrigido e tenha suporte.

## <a name="kubernetes-version-support-policy"></a>Política de suporte da versão kubernetes

O AKS dá suporte a três versões secundárias do kubernetes:

* A versão secundária atual lançada em AKS (N)
* Duas versões secundárias anteriores. Cada versão secundária suportada também suporta dois patches estáveis.

Isso é conhecido como "N-2": (N (versão mais recente)-2 (versões secundárias)).

Por exemplo, se AKS apresenta *1.15. a* hoje, o suporte é fornecido para as seguintes versões:

Nova versão secundária    |    Lista de versões com suporte
-----------------    |    ----------------------
1.15. a               |    1.15. a, 1.15. b, 1.14. c, 1.14. d, 1.13. e, 1.13. f

Onde ". letter" é representativo das versões de patch.

Para obter detalhes sobre as comunicações relacionadas a alterações de versão e expectativas, consulte "comunicações" abaixo.

Quando uma nova versão secundária é introduzida, a versão secundária mais antiga e as versões de patch com suporte são preteridas e removidas. Por exemplo, se a lista de versões com suporte atual for:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

E AKS versões 1,16. *, isso significa que o 1,13.* as versões (todas as versões 1,13) serão removidas e estarão fora de suporte.

> [!NOTE]
> Observe que, se os clientes estiverem executando uma versão sem suporte do kubernetes, eles serão solicitados a atualizar ao solicitar suporte para o cluster. Os clusters que executam versões kubernetes sem suporte não são cobertos pelas [políticas de suporte do AKS](https://docs.microsoft.com/azure/aks/support-policies).

Além das versões secundárias acima, o AKS dá suporte às duas versões de **patch** mais recentes de uma determinada versão secundária. Por exemplo, dadas as seguintes versões com suporte:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Se upstream kubernetes lançaram 1.15.3 e 1.14.6 e AKS lançar essas versões de patch, as versões mais antigas do patch serão preteridas e removidas, e a lista de versões com suporte se tornará:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Comunicações

* Para novas versões **secundárias** do kubernetes
  * Todos os usuários são notificados publicamente sobre a nova versão e qual versão será removida.
  * Quando uma nova versão de patch é lançada, a versão mais antiga do patch é removida ao mesmo tempo.
  * Os clientes têm **60 dias** a partir da data de notificação pública para atualizar para uma versão de versão secundária com suporte.
* Para novas versões de **patch** do kubernetes
  * Todos os usuários são notificados sobre a versão do novo patch que está sendo lançada e para atualizar para a versão mais recente do patch.
  * Os usuários têm **30 dias** para atualizar para uma versão de patch mais recente e com suporte. Os usuários têm **30 dias** para atualizar para uma versão de patch com suporte antes que o mais antigo seja removido.

AKS define uma "versão liberada" como as versões disponíveis para o público geral, habilitadas em todas as medições de SLO/qualidade de serviço e disponíveis em todas as regiões. O AKS também pode oferecer suporte a versões de visualização que são rotuladas explicitamente e sujeitas a termos e condições de visualização.

#### <a name="notification-channels-for-aks-changes"></a>Canais de notificação para alterações AKS

O AKS publica atualizações de serviço regulares que resumem novas versões do kubernetes, alterações de serviço e atualizações de componentes que foram lançadas no serviço no [GitHub](https://github.com/Azure/AKS/releases).

Essas alterações são revertidas para todos os clientes como parte da manutenção regular que é oferecida como parte do serviço gerenciado, algumas exigem atualizações explícitas, enquanto outras não exigem nenhuma ação.

As notificações também são enviadas por meio de:

* [Notas de versão do AKS](https://aka.ms/aks/releasenotes)
* Notificações do portal do Azure
* [Canal de atualização do Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Exceções de política de versões com suporte

AKS reserva o direito de adicionar ou remover versões novas/existentes que foram identificadas para ter uma ou mais correções críticas que afetam bugs ou problemas de segurança sem aviso prévio.

Versões de patch específicas podem ser ignoradas ou a distribuição acelerada dependendo da severidade do bug ou do problema de segurança.

### <a name="azure-portal-and-cli-default-versions"></a>portal do Azure e versões padrão da CLI

Quando você implanta um cluster AKS no portal ou com o CLI do Azure, o cluster é padronizado para a versão secundária N-1 e o patch mais recente. Por exemplo, se AKS dá suporte a *1.15. a*, *1.15. b*, *1.14. c*, *1.14. d*, *1.13. e*e *1.13. f*, a versão padrão selecionada é *1.14. c*.

AKS escolhe o padrão de N-1 para fornecer aos clientes uma versão conhecida, estável e corrigida por padrão.

## <a name="list-currently-supported-versions"></a>Listar versões com suporte no momento

Para descobrir quais versões estão disponíveis no momento para sua assinatura e região, use o comando [AZ AKs Get-Versions][az-aks-get-versions] . O exemplo a seguir lista as versões de kubernetes disponíveis para a região *eastus* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>FAQ

**O que acontece quando um cliente atualiza um cluster kubernetes com uma versão secundária que não tem suporte?**

Se você estiver na versão *n-3* , você estará fora do suporte e será solicitado a fazer a atualização. Se a atualização da versão n-3 para o n-2 tiver sucesso, agora você estará dentro de nossas políticas de suporte. Por exemplo:

- Se a versão mais antiga do AKS com suporte for *1.13. a* e você estiver no *1.12. b* ou mais antigo, você está fora do suporte.
- Se a atualização de *1.12. b* para *1.13. a* ou superior for realizada com sucesso, você voltará ao dentro de nossas políticas de suporte.

Não há suporte para atualizações para versões anteriores à janela *N-2* com suporte. Nesses casos, recomendamos que os clientes criem novos clusters AKS e reimplantem suas cargas de trabalho com versões na janela com suporte.

**O que significa "fora do suporte"**

' Fora do suporte ' significa que a versão que você está executando está fora da lista de versões com suporte e você será solicitado a atualizar o cluster para uma versão com suporte ao solicitar suporte. Além disso, o AKS não torna nenhum tempo de execução ou outras garantias para clusters fora da lista de versões com suporte.

**O que acontece quando um cliente escala um cluster kubernetes com uma versão secundária que não tem suporte?**

Para versões secundárias sem suporte do AKS, a expansão ou saída deve continuar funcionando, mas é altamente recomendável atualizar para colocar o cluster novamente em suporte.

**Um cliente pode permanecer em uma versão kubernetes para sempre?**

Sim. No entanto, se o cluster não estiver em uma das versões com suporte do AKS, o cluster estará fora das políticas de suporte do AKS. O Azure não atualiza automaticamente o cluster ou o exclui.

**A qual versão o plano de controle dá suporte se o pool de nós não estiver em uma das versões do AKS com suporte?**

O plano de controle deve estar dentro de uma janela de versões de todos os pools de nós. Para obter detalhes sobre como atualizar o plano de controle ou pools de nós, visite a documentação sobre como [Atualizar pools de nós](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como atualizar o cluster, consulte [atualizar um cluster do AKS (serviço kubernetes do Azure)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
