---
title: Descrição geral do Azure Arc
description: Saiba o que é o Azure Arc e como ajuda os clientes a permitir a gestão e governação dos seus recursos híbridos com outros serviços e funcionalidades da Azure.
ms.date: 03/02/2021
ms.topic: overview
ms.openlocfilehash: 33c9d6ca87c3d8d2d8920ff429902f5876bbdc59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650197"
---
# <a name="azure-arc-overview"></a>Descrição geral do Azure Arc

Hoje, as empresas lutam para controlar e governar ambientes cada vez mais complexos. Estes ambientes estendem-se por centros de dados, múltiplas nuvens e borda. Cada ambiente e nuvem possui o seu próprio conjunto de ferramentas de gestão desarticuladas que você precisa aprender e operar.

Paralelamente, os novos modelos operacionais de DevOps e ITOps são difíceis de implementar, uma vez que as ferramentas existentes não fornecem suporte para novos padrões nativos em nuvem.

O Azure Arc simplifica a governação e a gestão através da entrega de uma plataforma consistente de gestão multi-nuvem e no local. O Azure Arc permite-lhe gerir todo o seu ambiente, com uma única vidraça de vidro, projetando os seus recursos existentes para o Azure Resource Manager. Agora pode gerir máquinas virtuais, clusters Kubernetes e bases de dados como se estivessem a funcionar em Azure. Independentemente do local onde vivem, você pode usar serviços e capacidades de gestão familiares da Azure. O Azure Arc permite-lhe continuar a usar itops tradicionais, ao mesmo tempo que introduz práticas de DevOps para suportar novos padrões nativos em nuvem no seu ambiente.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagrama do plano de controlo de gestão Azure Arc" border="false":::

Hoje, o Azure Arc permite-lhe gerir os seguintes tipos de recursos alojados fora de Azure:

* Servidores - máquinas físicas e virtuais que executam o Windows ou o Linux.
* Clusters Kubernetes - suportando várias distribuições de Kubernetes.
* Serviços de dados Azure - Azure SQL Database e serviços de hiperescala PostgreSQL.

## <a name="what-does-azure-arc-deliver"></a>O que a Azure Arc entrega?

As principais características do Arco Azure incluem:

* Implemente um inventário consistente, gestão, governação e segurança para os seus servidores em todo o seu ambiente.

* Configure [extensões VM Azure](./servers/manage-vm-extensions.md) para utilizar os serviços de gestão Azure para monitorizar, proteger e atualizar os seus servidores.

* Gerir e governar os aglomerados de Kubernetes em escala.

* Utilize GitOps para implementar a configuração em um ou mais aglomerados de repositórios de Git.

*  Conformidade e configuração de toque zero para os seus clusters Kubernetes usando a Política Azure.

* Executar serviços de dados Azure em qualquer ambiente Kubernetes como se funcionasse em Azure (especificamente Azure SQL Managed Instance e Azure Database for PostgreSQL Hyperscale, com benefícios como upgrades, atualizações, segurança e monitorização). Use a balança elástica e aplique atualizações sem qualquer tempo de inatividade da aplicação, mesmo sem ligação contínua ao Azure

* Uma experiência unificada de visualização do seu Arco Azure permitiu recursos quer esteja a utilizar o portal Azure, o Azure CLI, a Azure PowerShell ou a API AZure REST.

## <a name="how-much-does-azure-arc-cost"></a>Quanto custa o Azure Arc?

Seguem-se os detalhes dos preços das funcionalidades disponíveis hoje com o Azure Arc.

### <a name="arc-enabled-servers"></a>Servidores preparados para o Arc

A seguinte funcionalidade do plano de controlo Azure Arc é oferecida sem custos adicionais:

* Organização de recursos através de grupos de gestão Azure e tags.

* Pesquisa e indexação através do Gráfico de Recursos Azure.

* Acesso e segurança através do Azure RBAC e subscrições.

* Ambientes e automação através de modelos e extensões.

* Gestão de atualizações

Qualquer serviço Azure que seja utilizado em servidores ativados pelo Arc, por exemplo, O Centro de Segurança Azure ou o Azure Monitor, será cobrado de acordo com os preços desse serviço. Para mais informações, consulte a [página de preços do Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes ativado pelo Azure Arc

Qualquer serviço Azure que seja utilizado no Arc habilitado a Kubernetes, por exemplo, O Azure Security Center ou o Azure Monitor, será cobrado de acordo com os preços desse serviço. Para obter mais informações sobre os preços das configurações em cima do Arco Azure, consulte [a página de preços do Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-data-services"></a>Serviços de dados preparados para Azure Arc

Na atual fase de pré-visualização, os serviços de dados habilitados a Azure Arc são oferecidos sem custos adicionais.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os servidores ativados do Arc, consulte a [seguinte visão geral](./servers/overview.md)

* Para saber mais sobre o Arc habilitado a Kubernetes, consulte a seguinte [visão geral](./kubernetes/overview.md)

* Para saber mais sobre os serviços de dados ativados pela Arc, consulte a seguinte [visão geral](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)

* Experimente Arc permitiu serviços a partir da [prova jumpstart de conceito](https://azurearcjumpstart.io/azure_arc_jumpstart/)
