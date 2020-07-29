---
title: Visão geral dos clusters de tecido de serviço Azure e autónomos
description: Pode criar clusters de Tecido de Serviço em quaisquer VMs ou computadores que executem o Windows Server ou o Linux. Isto significa que é capaz de implementar e executar aplicações de Tecido de Serviço em qualquer ambiente onde tenha um conjunto de computadores Windows Server ou Linux que estejam interligados no local, Microsoft Azure ou com qualquer fornecedor de nuvem.
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75751156"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Comparando clusters de tecido de serviço Azure e autónomos no Windows Server e Linux

Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um cluster é chamada de nó de cluster. Os aglomerados podem escalar para milhares de nós. Se adicionar novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição de serviço e as instâncias através do aumento do número de nós. O desempenho geral da aplicação melhora e a contenção para o acesso à memória diminui. Se os nós do cluster não estiverem a ser utilizados de forma eficiente, pode diminuir o número de nós no cluster. O Tecido de Serviço reequilibra novamente as réplicas de partição e instâncias através do número reduzido de nós para melhor utilizar o hardware em cada nó.

O Service Fabric permite a criação de clusters de Tecido de Serviço em quaisquer VMs ou computadores que executem o Windows Server ou o Linux. Isto significa que é capaz de implementar e executar aplicações de Tecido de Serviço em qualquer ambiente onde tenha um conjunto de computadores Windows Server ou Linux que estejam interligados, seja no local, Microsoft Azure ou com qualquer fornecedor de nuvem.

## <a name="benefits-of-clusters-on-azure"></a>Benefícios dos clusters em Azure

No Azure, proporcionamos integração com outras funcionalidades e serviços Azure, o que torna as operações e gestão do cluster mais fáceis e fiáveis.

* **Portal Azure:** O portal Azure facilita a criação e gestão de clusters.
* **Gestor de Recursos Azure:** A utilização do Azure Resource Manager permite uma gestão fácil de todos os recursos utilizados pelo cluster como unidade e simplifica o rastreio de custos e a faturação.
* **Cluster de Tecido de Serviço como Recurso Azure** Um cluster de Tecido de Serviço é um recurso Azure, para que possa modelá-lo como se fizesse outros recursos em Azure.
* **Integração com a Infraestrutura Azure** O Service Fabric coordena com a infraestrutura Azure subjacente para o SO, rede e outras atualizações para melhorar a disponibilidade e fiabilidade das suas aplicações.  
* **Diagnósticos:** No Azure, proporcionamos integração com diagnósticos Azure e registos Azure Monitor.
* **Auto-dimensionamento:** Para clusters em Azure, fornecemos funcionalidade de escala automática incorporada devido a conjuntos de escalas de máquina virtual. No local e noutros ambientes em nuvem, tem de construir a sua própria funcionalidade de escala automática ou escala manualmente utilizando as APIs que o Service Fabric expõe para clusters de escala.

## <a name="benefits-of-standalone-clusters"></a>Benefícios dos agrupamentos autónomos

* Você é livre de escolher qualquer fornecedor de nuvem para hospedar o seu cluster.
* As aplicações de tecido de serviço, uma vez escritas, podem ser executadas em vários ambientes de hospedagem com alterações mínimas ou nenhumas.
* O conhecimento das aplicações de tecido de serviço de construção transporta de um ambiente de hospedagem para outro.
* A experiência operacional de gerir e gerir clusters de tecidos de serviço transporta de um ambiente para outro.
* O alcance amplo do cliente não é limitado por restrições ambientais de hospedagem.
* Existe uma camada extra de fiabilidade e proteção contra interrupções generalizadas porque pode mover os serviços para outro ambiente de implantação se um centro de dados ou um fornecedor de nuvem tiver um apagão.

## <a name="next-steps"></a>Próximos passos

* Leia a visão geral dos clusters de [tecido de serviço no Azure](service-fabric-azure-clusters-overview.md)
* Leia a visão geral dos [clusters autónomos do Tecido](service-fabric-standalone-clusters-overview.md) de Serviço
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)