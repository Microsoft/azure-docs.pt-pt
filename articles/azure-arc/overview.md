---
title: Descrição geral do Azure Arc
description: Saiba o que é o Azure Arc e como ajuda os clientes a permitir a gestão e governação dos seus recursos híbridos com outros serviços e funcionalidades da Azure.
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 3a8b054d9b7fd8f48dc667a5bd49a2c6994de1a3
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616529"
---
# <a name="azure-arc-overview"></a>Descrição geral do Azure Arc

Hoje em dia, as empresas lutam para controlar e governar um ambiente que se torna cada vez mais complexo. Estes ambientes estendem-se por centros de dados, múltiplas nuvens e borda. Cada ambiente e nuvem tem o seu próprio conjunto de ferramentas de gestão desarticuladas que você precisa aprender e operar.

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

* Utilize a configuração baseada em GitOps como gestão de códigos para implementar aplicações e configuração em um ou mais clusters diretamente do controlo de origem, como o GitHub.

* Conformidade e configuração de toque zero para os seus clusters Kubernetes usando a Política Azure.

* Executar serviços de dados Azure em qualquer ambiente Kubernetes, especificamente Azure SQL Managed Instance e Azure Database for PostgreSQL Hyperscale, com benefícios como atualizações/atualizações, segurança e monitorização como se funcionasse em Azure. Alavancar a escala elástica, aplicar atualizações, sem qualquer tempo de inatividade da aplicação, mesmo que não tenha uma ligação contínua ao Azure.

* Uma experiência unificada de visualização do seu Arco Azure permitiu recursos quer esteja a utilizar o portal Azure, o Azure CLI, a Azure PowerShell ou a API AZure REST.

## <a name="how-much-does-azure-arc-cost"></a>Quanto custa o Azure Arc?

Seguem-se os detalhes dos preços das funcionalidades disponíveis hoje com o Azure Arc.

### <a name="arc-enabled-servers"></a>Servidores preparados para o Arc

A funcionalidade do plano de controlo Azure Arc é oferecida sem custos adicionais. O que está incluído:

* Organização de recursos através de grupos de gestão Azure e tags.

* Pesquisa e indexação através do Gráfico de Recursos Azure.

* Acesso e segurança através do Azure RBAC e subscrições.

* Ambientes e automação através de modelos e extensões.

* Gestão de atualizações

Qualquer serviço Azure que seja utilizado em servidores ativados pelo Arc, por exemplo, O Centro de Segurança Azure ou o Azure Monitor, será cobrado de acordo com os preços desse serviço. Para mais informações, consulte [a página de preços do Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes ativado pelo Azure Arc

Na atual fase de pré-visualização, a Azure Arc permitiu que kubernetes são oferecidos sem custos adicionais.

### <a name="azure-arc-enabled-data-services"></a>Serviços de dados preparados para Azure Arc

Na atual fase de pré-visualização, os serviços de dados habilitados a Azure Arc são oferecidos sem custos adicionais.

## <a name="next-steps"></a>Passos seguintes

* Comece a experimentar serviços ativados pela Arc a partir dos [cenários Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/)

* Para saber mais sobre os servidores ativados do Arc, consulte a [seguinte visão geral](./servers/overview.md)

* Para saber mais sobre o Arc habilitado a Kubernetes, consulte a seguinte [visão geral](./kubernetes/overview.md)

* Para saber mais sobre os serviços de dados ativados pela Arc, consulte a seguinte [visão geral](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)
