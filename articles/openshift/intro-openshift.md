---
title: Introdução ao OpenShift do Chapéu Vermelho Azure
description: Conheça as funcionalidades e benefícios do Microsoft Azure Red Hat OpenShift para implementar e gerir aplicações baseadas em contentores.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: a3bdc3673474b778aa7c1003e48e215bac6d05bf
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628525"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

O serviço *Microsoft Azure Red Hat OpenShift* permite-lhe implementar clusters [OpenShift](https://www.openshift.com/) totalmente geridos.

O Azure Red Hat OpenShift [estende-se a Kubernetes.](https://kubernetes.io/) A execução de contentores em produção com kubernetes requer ferramentas e recursos adicionais. Isto inclui frequentemente a necessidade de fazer malabarismos com registos de imagem, gestão de armazenamento, soluções de networking e ferramentas de registo e monitorização - todas elas devem ser versonizadas e testadas em conjunto. A construção de aplicações baseadas em contentores requer ainda mais trabalho de integração com middleware, quadros, bases de dados e ferramentas CI/CD. O Azure Red Hat OpenShift combina tudo isto numa única plataforma, trazendo facilidade de operações às equipas de TI, ao mesmo tempo que dá às equipas de aplicação o que precisam para executar.

O Azure Red Hat OpenShift é criado em conjunto, operado e apoiado pela Red Hat e pela Microsoft para proporcionar uma experiência de suporte integrada. Não existem máquinas virtuais para operar, e não é necessário remendar. Os nós de mestrado, infraestrutura e aplicação são remendados, atualizados e monitorizados em seu nome pela Red Hat e microsoft. Os seus clusters OpenShift do Chapéu Vermelho Azure estão implantados na sua subscrição Azure e estão incluídos na sua conta Azure.

Pode escolher as soluções de registo próprio, networking, armazenamento e CI/CD, ou utilizar as soluções incorporadas para a gestão automatizada de códigos de origem, construções de contentores e aplicações, implementações, escalagem, gestão da saúde e muito mais. O Azure Red Hat OpenShift proporciona uma experiência integrada de sign-on através do Azure Ative Directory.

Para começar, complete o tutorial de [cluster Create a Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitorização

Para uma melhor segurança e gestão, o Azure Red Hat OpenShift permite-lhe integrar-se com o Azure Ative Directory (Azure AD) e utilizar o controlo de acesso baseado em papéis da Kubernetes (RBAC). Também pode monitorizar o estado de funcionamento do seu cluster e recursos.

## <a name="cluster-and-node"></a>Cluster e nó

Os nódosos OpenShift do Chapéu Vermelho Azure funcionam em máquinas virtuais Azure. Pode ligar o armazenamento a nós e cápsulas e atualizar componentes de cluster.

## <a name="next-steps"></a>Passos seguintes

Aprenda os pré-requisitos para o Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Configurar o ambiente de desenvolvimento](tutorial-create-cluster.md)
