---
title: Introdução ao Azure Red Hat OpenShift
description: Aprenda as funcionalidades e benefícios do Microsoft Azure Red Hat OpenShift para implementar e gerir aplicações baseadas em contentores.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2020
ms.custom: mvc
ms.openlocfilehash: 1bf3141876ee56ee1361f19a67689ca3b2f4f89a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685295"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

O serviço Microsoft *Azure Red Hat OpenShift* permite-lhe implementar clusters [OpenShift](https://www.openshift.com/) totalmente geridos.

Azure Red Hat OpenShift estende [Kubernetes](https://kubernetes.io/). A execução de contentores em produção com Kubernetes requer ferramentas e recursos adicionais. Isto inclui frequentemente a necessidade de fazer malabarismos com registos de imagem, gestão de armazenamento, soluções de networking e ferramentas de registo e monitorização - todas elas devem ser versadas e testadas em conjunto. A construção de aplicações baseadas em contentores requer ainda mais trabalho de integração com middleware, quadros, bases de dados e ferramentas ci/CD. O Azure Red Hat OpenShift combina tudo isto numa única plataforma, trazendo facilidade de operações às equipas de TI, ao mesmo tempo que dá às equipas de aplicação o que precisam para executar.

O Azure Red Hat OpenShift é criado em conjunto, operado e apoiado pela Red Hat e pela Microsoft para proporcionar uma experiência de suporte integrada. Não há máquinas virtuais para operar, e não é necessário remendar. Os nós de mestrado, infraestrutura e aplicação são remendados, atualizados e monitorizados em seu nome pela Red Hat e Microsoft. Os seus clusters Azure Red Hat OpenShift estão implantados na sua subscrição Azure e estão incluídos na sua conta Azure.

Pode escolher o seu próprio registo, soluções de networking, armazenamento e CI/CD, ou utilizar as soluções incorporadas para gestão automatizada de código fonte, construções de contentores e aplicações, implementações, escala, gestão de saúde, entre outras. O Azure Red Hat OpenShift proporciona uma experiência integrada de inscrição através do Azure Ative Directory.

Para começar, complete o tutorial de [cluster Create azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitorização

Para uma melhor segurança e gestão, o Azure Red Hat OpenShift permite-lhe integrar-se com o Azure Ative Directory (Azure AD) e utilizar o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC). Também pode monitorizar o estado de funcionamento do seu cluster e recursos.

## <a name="cluster-and-node"></a>Cluster e nó

Os nós Azure Red Hat OpenShift funcionam em máquinas virtuais Azure. Pode ligar o armazenamento a nós e cápsulas e atualizar componentes de cluster.

## <a name="service-level-agreement"></a>Acordo de Nível de Serviço

A Azure Red Hat OpenShift oferece um Acordo de Nível de Serviço para garantir que o serviço estará disponível 99,95% das vezes. Para mais detalhes sobre o SLA, consulte [Azure Red Hat OpenShift SLA](https://azure.microsoft.com/en-au/support/legal/sla/openshift/v1_0/).

## <a name="next-steps"></a>Próximos passos

Aprenda os pré-requisitos para Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Configurar o ambiente de desenvolvimento](tutorial-create-cluster.md)
