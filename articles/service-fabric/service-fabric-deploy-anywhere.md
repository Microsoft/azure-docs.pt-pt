---
title: Visão geral do Azure e clusters de Service Fabric autônomos
description: Você pode criar clusters Service Fabric em qualquer VM ou computadores que executam o Windows Server ou o Linux. Isso significa que você pode implantar e executar Service Fabric aplicativos em qualquer ambiente em que tenha um conjunto de computadores Windows Server ou Linux interconectados, Microsoft Azure ou com qualquer provedor de nuvem.
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751156"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Comparando clusters Service Fabric do Azure e autônomos no Windows Server e no Linux

Um Cluster Service Fabric é um conjunto de máquinas físicas ou virtuais conectadas à rede em que seus microserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó de cluster. Os clusters podem ser dimensionados para milhares de nós. Se você adicionar novos nós ao cluster, Service Fabric reequilibrará as réplicas e instâncias de partição de serviço em um número maior de nós. O desempenho geral do aplicativo melhora e a contenção de acesso à memória diminui. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. Service Fabric novamente reequilibra as réplicas e instâncias de partição em todo o número de nós reduzido para fazer melhor uso do hardware em cada nó.

Service Fabric permite a criação de clusters de Service Fabric em qualquer VM ou computadores que executam o Windows Server ou o Linux. Isso significa que você pode implantar e executar Service Fabric aplicativos em qualquer ambiente em que você tenha um conjunto de computadores Windows Server ou Linux interconectados, seja no local, Microsoft Azure ou com qualquer provedor de nuvem.

## <a name="benefits-of-clusters-on-azure"></a>Benefícios dos clusters no Azure

No Azure, fornecemos integração com outros recursos e serviços do Azure, o que torna as operações e o gerenciamento do cluster mais fáceis e confiáveis.

* **Portal do Azure:** Portal do Azure facilita a criação e o gerenciamento de clusters.
* **Azure Resource Manager:** O uso de Azure Resource Manager permite o gerenciamento fácil de todos os recursos usados pelo cluster como uma unidade e simplifica o controle de custos e a cobrança.
* **Service Fabric cluster como um recurso do Azure** Um Cluster Service Fabric é um recurso do Azure, para que você possa modelá-lo da mesma forma que faz com outros recursos no Azure.
* **Integração com a infraestrutura do Azure** Service Fabric coordena com a infraestrutura subjacente do Azure para sistema operacional, rede e outras atualizações para melhorar a disponibilidade e a confiabilidade de seus aplicativos.  
* **Diagnóstico:** No Azure, fornecemos integração com o diagnóstico do Azure e logs de Azure Monitor.
* **Dimensionamento automático:** Para clusters no Azure, fornecemos a funcionalidade de dimensionamento automático interna devido a conjuntos de escala de máquina virtual. No local e em outros ambientes de nuvem, você precisa criar seu próprio recurso de dimensionamento automático ou dimensionar manualmente usando as APIs que o Service Fabric expõe para dimensionar clusters.

## <a name="benefits-of-standalone-clusters"></a>Benefícios de clusters autônomos

* Você tem a liberdade de escolher qualquer provedor de nuvem para hospedar o cluster.
* Service Fabric aplicativos, uma vez gravados, podem ser executados em vários ambientes de hospedagem com pouca ou nenhuma alteração.
* O conhecimento da criação de aplicativos Service Fabric é transmitido de um ambiente de hospedagem para outro.
* A experiência operacional de executar e gerenciar clusters Service Fabric é transferida de um ambiente para outro.
* O alcance do cliente amplo não é limitado por restrições de ambiente de hospedagem.
* Existe uma camada extra de confiabilidade e proteção contra interrupções generalizadas porque você pode mover os serviços para outro ambiente de implantação se um data center ou um provedor de nuvem tiver um blecaute.

## <a name="next-steps"></a>Passos seguintes

* Leia a visão geral dos [clusters Service Fabric no Azure](service-fabric-azure-clusters-overview.md)
* Leia a visão geral de [Service Fabric clusters autônomos](service-fabric-standalone-clusters-overview.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)