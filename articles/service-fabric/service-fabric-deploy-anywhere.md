---
title: Descrição geral do Azure e autónomos do Service Fabric clusters | Documentos da Microsoft
description: Pode criar clusters do Service Fabric em quaisquer VMs ou computadores que executam o Windows Server ou Linux. Isso significa que pode implementar e executar aplicações do Service Fabric em qualquer ambiente em que tem um conjunto do Windows Server ou de computadores Linux que estão interligados no local, Microsoft Azure, ou com qualquer fornecedor de cloud.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 8c344999839b3dcde98536b3d8d196a434f4cde8
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244915"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Comparar o Azure e autónomos do Service Fabric clusters no Windows Server e Linux
Um cluster do Service Fabric é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos. Uma máquina ou VM que faça parte de um cluster é chamado de um nó de cluster. Clusters podem ser dimensionados para milhares de nós. Se adicionar novos nós ao cluster, o Service Fabric rebalances as réplicas de partição de serviço e as instâncias em aumento do número de nós. Em geral melhora o desempenho do aplicativo e reduz a contenção de acesso à memória. Se os nós do cluster não estão a ser utilizados com eficiência, pode diminuir o número de nós do cluster. Service Fabric rebalances novamente as instâncias e réplicas de partição entre o menor número de nós para garantir uma melhor utilização do hardware em cada nó.

Service Fabric permite a criação de clusters do Service Fabric em quaisquer VMs ou computadores que executam o Windows Server ou Linux. Isso significa que pode implementar e executar aplicações do Service Fabric em qualquer ambiente em que tem um conjunto de computadores Windows Server ou Linux que estão interligados, seja no local, Microsoft Azure, ou com qualquer fornecedor de cloud.

## <a name="benefits-of-clusters-on-azure"></a>Benefícios de clusters no Azure
No Azure, podemos fornecer integração com outros serviços, o que torna as operações e gerenciamento de cluster mais fácil e mais confiável e funcionalidades do Azure.

* **Portal do Azure:** Portal do Azure torna mais fácil criar e gerir clusters.
* **Azure Resource Manager:** A utilização do Gestor de recursos do Azure permite a gestão fácil de todos os recursos utilizados pelo cluster, como uma unidade e simplifica o controlo de custos e a faturação.
* **Cluster do Service Fabric como um recurso do Azure** cluster A Service Fabric é um recurso do Azure, para que pode modelar tal como faz a outros recursos no Azure.
* **Integração com a infraestrutura do Azure** Service Fabric coordena com a infraestrutura do Azure subjacente para o sistema operacional, rede e outras atualizações melhorar a disponibilidade e fiabilidade das suas aplicações.  
* **Diagnósticos:** No Azure, fornecemos a integração com o diagnóstico do Azure e registos do Azure Monitor.
* **Dimensionamento automático:** Para os clusters no Azure, podemos fornecer funcionalidade de dimensionamento automático incorporada devido a conjuntos de dimensionamento de Máquina Virtual. Em outros ambientes de cloud e no local, terá de criar sua própria funcionalidade ou escala manualmente usando as APIs que expõe o Service Fabric para o dimensionamento de clusters de dimensionamento automático.

## <a name="benefits-of-standalone-clusters"></a>Vantagens de clusters autónomos
* É livre para escolher qualquer fornecedor de cloud para alojar o seu cluster.
* Aplicações do Service Fabric, uma vez escritas, podem ser executadas em vários ambientes de alojamento com o mínimo para sem alterações.
* Dados de conhecimento da criação de aplicações do Service Fabric transporta ao longo de um ambiente de alojamento para outro.
* Experiência operacional de execução e gestão do Service Fabric clusters executa diversas através de um ambiente para outro.
* Base de clientes ampla é não vinculado por restrições de ambiente de alojamento.
* Uma camada extra de confiabilidade e a proteção contra falhas ampla existe porque pode mover os serviços ao longo para outro ambiente de implantação se um fornecedor de center ou na cloud de dados tem uma indisponibilidade.

## <a name="next-steps"></a>Passos Seguintes

* Leia a visão geral de [clusters do Service Fabric no Azure](service-fabric-azure-clusters-overview.md)
* Leia a visão geral de [clusters autónomos do Service Fabric](service-fabric-standalone-clusters-overview.md)
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)