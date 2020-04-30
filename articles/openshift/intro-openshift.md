---
title: Introdução ao OpenShift do Chapéu Vermelho Azure
description: Conheça as funcionalidades e benefícios do Microsoft Azure Red Hat OpenShift para implementar e gerir aplicações baseadas em contentores.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: 87126d1beca97a905f0f3b09b565c12c37a4774d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82203918"
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

Os nódosos OpenShift do Chapéu Vermelho Azure funcionam em máquinas virtuais Azure. Pode ligar o armazenamento aos nós e aos pods, atualizar os componentes de cluster e utilizar GPUs.

## <a name="virtual-networks-and-ingress"></a>Redes virtuais e entrada

Pode ligar a [rede virtual de um cluster a uma rede virtual existente ou criar uma rede virtual quando criar um cluster](tutorial-create-cluster.md#create-a-virtual-network-containing-two-empty-subnets).

## <a name="kubernetes-certification"></a>Certificação Kubernetes

O serviço Azure Red Hat OpenShift foi certificado pela CNCF como conformante kubernetes.

## <a name="next-steps"></a>Passos seguintes

Aprenda os pré-requisitos para o Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Configurar o ambiente de desenvolvimento](howto-setup-environment.md)
