---
title: Visão geral dos clusters Azure e selfalone Service Fabric
description: Pode criar clusters de Tecido de Serviço em quaisquer VMs ou computadores que executem o Windows Server ou o Linux. Isto significa que é capaz de implementar e executar aplicações de Tecido de Serviço em qualquer ambiente onde tenha um conjunto de computadores Windows Server ou Linux que estejam interligados no local, Microsoft Azure ou com qualquer fornecedor de nuvem.
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75751156"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Comparando clusters de tecido de serviço azure e autónomo sintetizados no Windows Server e Linux

Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um cluster é chamado de nó de cluster. Os aglomerados podem escalar para milhares de nós. Se adicionar novos nós ao cluster, o Service Fabric reequilibra as réplicas e instâncias de partição de serviços em todo o número aumentado de nós. O desempenho global da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Service Fabric reequilibra novamente as réplicas e instâncias da partição através do número reduzido de nós para fazer melhor uso do hardware em cada nó.

O Service Fabric permite a criação de clusters de Tecido de Serviço em quaisquer VMs ou computadores que executem o Windows Server ou o Linux. Isto significa que é capaz de implementar e executar aplicações de Tecido de Serviço em qualquer ambiente onde tenha um conjunto de computadores Windows Server ou Linux que estejam interligados, seja no local, Microsoft Azure ou com qualquer fornecedor de nuvem.

## <a name="benefits-of-clusters-on-azure"></a>Benefícios dos clusters em Azure

No Azure, proporcionamos integração com outras funcionalidades e serviços do Azure, o que torna as operações e a gestão do cluster mais fáceis e fiáveis.

* **Portal Azure:** O portal Azure facilita a criação e gestão de clusters.
* **Gestor de Recursos Azure:** A utilização do Gestor de Recursos Azure permite uma gestão fácil de todos os recursos utilizados pelo cluster como uma unidade e simplifica o rastreio e faturação de custos.
* **Cluster de tecido de serviço como recurso Azure** Um cluster de tecido de serviço é um recurso Azure, para que você possa modelá-lo como você faz outros recursos em Azure.
* **Integração com infraestrutura azure** O Service Fabric coordena com a infraestrutura Azure subjacente para OS, rede e outras atualizações para melhorar a disponibilidade e fiabilidade das suas aplicações.  
* **Diagnósticos:** No Azure, proporcionamos integração com diagnósticos Azure e registos Do Monitor Azure.
* **Auto-escala:** Para clusters no Azure, fornecemos uma funcionalidade de escala automática incorporada devido a conjuntos de escala de Máquina Virtual. No local e noutros ambientes em nuvem, tem de construir a sua própria funcionalidade de escala automática ou escalar manualmente utilizando as APIs que o Tecido de Serviço expõe para agrupamentos de escala.

## <a name="benefits-of-standalone-clusters"></a>Benefícios de clusters autónomos

* Você é livre de escolher qualquer fornecedor de nuvem para hospedar o seu cluster.
* As aplicações de tecido de serviço, uma vez escritas, podem ser executadas em vários ambientes de hospedagem com mínimas ou sem alterações.
* O conhecimento das aplicações de construção de Tecidos de Serviço transporta-se de um ambiente de hospedagem para outro.
* A experiência operacional de executar e gerir clusters de tecidos de serviço transporta de um ambiente para outro.
* O alcance amplo do cliente é ilimitado através do acolhimento de restrições ambientais.
* Existe uma camada extra de fiabilidade e proteção contra interrupções generalizadas porque pode mover os serviços para outro ambiente de implantação se um centro de dados ou fornecedor de nuvem tiver um apagão.

## <a name="next-steps"></a>Passos seguintes

* Leia a visão geral dos [clusters de tecido de serviço no Azure](service-fabric-azure-clusters-overview.md)
* Leia a visão geral dos [clusters autónomos do Tecido de Serviço](service-fabric-standalone-clusters-overview.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)