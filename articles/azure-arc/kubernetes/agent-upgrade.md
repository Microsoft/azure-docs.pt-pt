---
title: Upgrade Azure Arc permitiu agentes kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Atualizações de agentes de controlo para Azure Arc ativadas Kubernetes
keywords: Kubernetes, Arc, Azure, K8s, contentores, agente, upgrade
ms.openlocfilehash: d81a00ed4f30f446aeed96d59a455935c652b7d5
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954552"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Upgrade Azure Arc permitiu agentes kubernetes

A Azure Arc habilitado a Kubernetes fornece capacidades de atualização automática e de atualização manual para os seus agentes. Se utilizar uma atualização automática e, em vez disso, depender de atualizações manuais, a política de suporte da versão é aplicável aos agentes Arc e ao cluster Kubernetes subjacente.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Alterne o upgrade automático ligado ou desligado ao ligar o cluster ao Arco de Azure

A Azure Arc habilitado a Kubernetes fornece aos seus agentes capacidades de atualização automática fora da caixa.

O seguinte comando liga um cluster ao Arco de Azure com a atualização automática **ativada:**

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Com a atualização automática ativada, o agente sonda a Azure por hora para a disponibilidade de uma versão mais recente dos agentes. Se o agente encontrar uma versão mais recente disponível, ativa uma atualização de gráfico Helm para os agentes do Azure Arc.

Para excluir a atualização automática, especifique o `--disable-auto-upgrade` parâmetro enquanto liga o cluster ao Arco de Azure. O seguinte comando liga um cluster ao Arco de Azure com a atualização automática **desativada:**

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Se planeia desativar a atualização automática, consulte a política de suporte da [versão](#version-support-policy) para o Azure Arc ativado por Kubernetes.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Alterne o upgrade automático ligado/desligado após a ligação do cluster ao Arco de Azure

Depois de ligar um cluster ao Arco Azure, pode alternar a capacidade de atualização automática com o `az connectedk8s update` comando, como mostra abaixo:

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Agentes de upgrade manual

Se tiver desativado a atualização automática dos agentes, pode iniciar manualmente atualizações para estes agentes utilizando o `az connectedk8s upgrade` comando como mostrado abaixo:

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Azure Arc habilitado Kubernetes segue o esquema de [versão semântica](https://semver.org/) padrão de versão dos `MAJOR.MINOR.PATCH` seus agentes. 

Cada número na versão indica compatibilidade geral com a versão anterior:

* **As principais versões** mudam quando há atualizações de API incompatíveis ou a retrocompatibilidade pode ser quebrada.
* **As versões menores** mudam quando as alterações de funcionalidade são compatíveis com o retro-compatível com outras versões menores.
* **As versões de** correção mudam quando são feitas correções de erro compatíveis com retro-volta.

## <a name="version-support-policy"></a>Política de suporte de versão

Ao criar problemas de suporte, o Azure Arc permitiu que a Kubernetes praticasse a seguinte política de suporte de versão:

* Azure Arc permitiu que os agentes kubernetes tivessem uma janela de suporte de "N-2" onde 'N' é a mais recente libertação menor de agentes. 
  * Por exemplo, se a Azure Arc ativado Kubernetes introduz 0.28.a hoje, as versões 0.28.a, 0.28.b, 0.27.c, 0.27.d, 0.26.e e 0.26.f são apoiadas por Azure Arc.

* Os clusters Kubernetes que se ligam ao Arco Azure têm uma janela de suporte de "N-2", onde 'N' é a mais recente versão menor estável de [Kubernetes a montante.](https://github.com/kubernetes/kubernetes/releases) 
  * Por exemplo, se Kubernetes introduzir 1.20.a hoje, as versões 1.20.a, 1.20.b, 1.19.c, 1.19.d, 1.18.e e 1.18.f são suportadas.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Com que frequência as versões menores do Azure Arc permitem que kubernetes estejam disponíveis?

Uma versão menor de Azure Arc habilitado agentes Kubernetes é lançada aproximadamente uma vez por mês.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>O que acontece se eu estiver a usar uma versão de agente ou uma versão Kubernetes fora da janela de suporte oficial?

'Fora do Suporte' significa que as versões que está a executar estão fora das versões suportadas "N-2" de agentes e clusters kubernetes a montante. Para prosseguir com a questão de suporte, ser-lhe-á pedido que atualize o cluster e os agentes para uma versão apoiada.

## <a name="next-steps"></a>Passos seguintes

* Caminhe pelo nosso quickstart para [ligar um cluster Kubernetes ao Arco Azure.](./quickstart-connect-cluster.md)
* Já tem um cluster Kubernetes ligado a Azure Arc? [Crie configurações no seu cluster Kubernetes ativado pelo Arco.](./tutorial-use-gitops-connected-cluster.md)
* Saiba como utilizar a [Política Azure para aplicar configurações à escala](./use-azure-policy.md).