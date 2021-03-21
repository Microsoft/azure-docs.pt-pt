---
title: Configurações e GitOps - Azure Arc habilitado Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral conceptual de GitOps e capacidade de configurações de Azure Arc habilitado Kubernetes.
keywords: Kubernetes, Arc, Azure, contentores, configuração, GitOps
ms.openlocfilehash: 88a30876b25730e4cb0b4b1e19fac94b9e556adc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121801"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Configurações e GitOps com Arco Azure habilitados Kubernetes

Em relação a Kubernetes, GitOps é a prática de declarar o estado desejado das configurações de cluster de Kubernetes (implementações, espaços de nome, etc.) num repositório de Git. Esta declaração é seguida por uma colocação baseada em sondagens e de puxar estas configurações de cluster utilizando um operador. O repositório de Git pode conter:
* Os manifestos de formato YAML descrevem quaisquer recursos válidos de Kubernetes, incluindo Namespaces, ConfigMaps, Deployments, DaemonSets, etc.
* Mapas de leme para implantação de aplicações.

[Flux](https://docs.fluxcd.io/), uma ferramenta de código aberto popular no espaço GitOps, pode ser implantada no cluster Kubernetes para facilitar o fluxo de configurações de um repositório de Git para um cluster Kubernetes. O fluxo suporta a implantação do seu operador tanto nos âmbitos de cluster como no espaço de nome. Um operador de fluxo implantado com âmbito de espaço de nome só pode implantar objetos Kubernetes dentro desse espaço de nome específico. A capacidade de escolher entre o cluster ou o âmbito do espaço de nome ajuda-o a alcançar padrões de implantação de vários inquilinos no mesmo cluster Kubernetes.

## <a name="configurations"></a>Configurações

[![Arquitetura de configurações ](./media/conceptual-configurations.png)](./media/conceptual-configurations.png#lightbox)

A ligação entre o seu cluster e um repositório Git é criada como um recurso de configuração `Microsoft.KubernetesConfiguration/sourceControlConfigurations` () em cima do Arco Azure habilitado recurso Kubernetes (representado `Microsoft.Kubernetes/connectedClusters` por) em Azure Resource Manager. 

As propriedades dos recursos de configuração são utilizadas para implantar o operador flux no cluster com os parâmetros apropriados, tais como o repo Git a partir do qual puxar manifestos e o intervalo de votação para os puxar. Os dados de recursos de configuração são armazenados encriptados em repouso numa base de dados DB Azure Cosmos para garantir a confidencialidade dos dados.

A `config-agent` corrida no seu cluster é responsável por:
* Rastrear recursos de configuração novos ou atualizados no Arco Azure permitiu o recurso Kubernetes.
* Implantação de um operador de fluxo para observar o repositório Git para cada recurso de configuração.
* Aplicando quaisquer atualizações feitas a qualquer recurso de configuração. 

Pode criar vários recursos de configuração com âmbito de nome no mesmo Arco Azure que permitiu que o cluster Kubernetes alcançasse o multi-arrendamento.

> [!NOTE]
> * `config-agent` monitores para recursos de configuração novos ou atualizados a serem disponíveis no recurso Arc habilitado Kubernetes. Assim, os agentes requerem conectividade para que o estado desejado seja puxado para baixo para o cluster. Se os agentes não puderem ligar-se ao Azure, há um atraso na propagação do estado desejado para o cluster.
> * Entradas sensíveis do cliente como chave privada, conteúdo conhecido dos anfitriões, nome de utilizador HTTPS e token/password não são armazenadas por mais de 48 horas nos serviços de Kubernetes ativados pelo Arco Azure. Se estiver a utilizar entradas sensíveis para configurações, leve os clusters on-line o mais regularmente possível.

## <a name="apply-configurations-at-scale"></a>Aplicar configurações à escala

Uma vez que o Azure Resource Manager gere as suas configurações, pode automatizar a criação da mesma configuração em todos os recursos Azure Arc ativados pela Kubernetes utilizando a Azure Policy, no âmbito de uma subscrição ou de um grupo de recursos. 

Esta aplicação em escala garante uma configuração de base comum (contendo configurações como ClusterRoleBindings, RoleBindings e NetworkPolicy) pode ser aplicada em toda uma frota ou inventário de clusters Kubernetes habilitados pelo Arco Azure.

## <a name="next-steps"></a>Passos seguintes

* Caminhe pelo nosso quickstart para [ligar um cluster Kubernetes ao Arco Azure.](./connect-cluster.md)
* Já tem um cluster Kubernetes ligado a Azure Arc? [Crie configurações no seu cluster Kubernetes ativado pelo Arco.](./use-gitops-connected-cluster.md)
* Saiba como utilizar a [Política Azure para aplicar configurações à escala](./use-azure-policy.md).