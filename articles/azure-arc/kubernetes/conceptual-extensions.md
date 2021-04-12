---
title: Extensões de cluster - Azure Arc habilitado Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral conceptual da capacidade de extensões de cluster de Azure Arc habilitado Kubernetes
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451174"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Extensões de cluster em Azure Arc ativaram Kubernetes

[Os gráficos de](https://helm.sh/) leme ajudam-no a gerir as aplicações kubernetes, fornecendo os blocos de construção necessários para definir, instalar e atualizar até mesmo as aplicações kubernetes mais complexas. A funcionalidade de extensão de cluster procura construir em cima dos componentes de embalagem do Helm. Fá-lo proporcionando uma experiência impulsionada pelo Azure Resource Manager para a instalação e gestão do ciclo de vida de extensões de clusters como o Azure Monitor e o Azure Defender para Kubernetes. A funcionalidade de extensões de cluster fornece os seguintes benefícios extras para além do que já está disponível nativamente com gráficos Helm:

- Obtenha um inventário de todos os clusters e extensões instalados nesses clusters.
- Utilize a Política Azure para automatizar a implantação em escala de extensões de cluster.
- Subscreva a libertação de comboios de cada extensão.
- Configurar uma atualização automática para extensões.
- Suporte para os eventos de criação de instância de extensão e gestão do ciclo de vida de atualização e eliminação.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Arquitetura

[![Arquitetura de extensões de ](./media/conceptual-extensions.png) cluster](./media/conceptual-extensions.png#lightbox)

A instância de extensão do cluster é criada como um recurso de extensão Azure Resource Manager ( `Microsoft.KubernetesConfiguration/extensions` ) em cima do Arco Azure habilitado recurso Kubernetes (representado por ) em `Microsoft.Kubernetes/connectedClusters` Azure Resource Manager. A representação no Azure Resource Manager permite-lhe autoriar uma política que verifica todos os recursos do Arco Azure habilitados com ou sem uma extensão específica do cluster. Uma vez determinado quais os clusters que carecem de extensões de cluster com os valores de propriedade pretendidos, você pode remediar estes recursos não conformes usando a Política Azure.

A `config-agent` execução no seu cluster rastreia novos recursos de extensão ou atualizados no Arco Azure que permitiu o recurso Kubernetes. A `extensions-manager` execução no seu cluster retira o gráfico Helm do Registo de Contentores Azure ou do Registo do Contentor da Microsoft e instala-o no cluster. 

Tanto os `config-agent` `extensions-manager` componentes em execução nas atualizações da versão do cabo do cluster como a eliminação de instâncias de extensão.

> [!NOTE]
> * `config-agent` monitores para recursos de extensão novos ou atualizados a serem disponíveis no arco habilitado recurso Kubernetes. Assim, os agentes requerem conectividade para que o estado desejado seja puxado para baixo para o cluster. Se os agentes não puderem ligar-se ao Azure, a propagação do estado desejado ao cluster é retardada.
> * As definições de configuração protegidas para uma extensão são armazenadas até 48 horas nos serviços de Kubernetes ativados pelo Arco Azure. Como resultado, se o cluster permanecer desligado durante as 48 horas após a criação do recurso de extensão em Azure, a extensão transita de um `Pending` estado para `Failed` estado. Aconselhamos a colocar os clusters on-line o mais regularmente possível.

## <a name="next-steps"></a>Passos seguintes

* Use o nosso quickstart para [ligar um cluster Kubernetes ao Arco Azure.](./quickstart-connect-cluster.md)
* [Implemente extensões de cluster](./extensions.md) no seu Azure Arc ativado pelo cluster Kubernetes.