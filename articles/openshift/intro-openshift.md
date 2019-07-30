---
title: Introdução ao Red Hat OpenShift do Azure | Microsoft Docs
description: Conheça os recursos e benefícios do Microsoft Azure Red Hat OpenShift para implantar e gerenciar aplicativos baseados em contêineres.
services: container-service
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: f76f5d4dc84d1f8827248ab8399c1ae450a643a0
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620152"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

O serviço *Red Hat OpenShift* do Microsoft Azure permite que você implante clusters totalmente gerenciados do [OpenShift](https://www.openshift.com/) .

O Azure Red Hat OpenShift estende o [kubernetes](https://kubernetes.io/). A execução de contêineres em produção com o kubernetes requer ferramentas e recursos adicionais, como um registro de imagem, gerenciamento de armazenamento, soluções de rede e ferramentas de log e monitoramento, todos os quais devem ter controle de versão e testados em conjunto. A criação de aplicativos baseados em contêiner requer ainda mais trabalho de integração com middleware, estruturas, bancos de dados e ferramentas de CI/CD. O Azure Red Hat OpenShift combina tudo isso em uma única plataforma, trazendo a facilidade de operações para as equipes de ti e, ao mesmo tempo, fornecer às equipes de aplicativos o que precisam ser executadas.

O Azure Red Hat OpenShift está conjunta, operado e com suporte da Red Hat e da Microsoft para fornecer uma experiência de suporte integrada. Não há máquinas virtuais para operar e nenhuma aplicação de patch é necessária. Os nós mestre, de infraestrutura e de aplicativo são corrigidos, atualizados e monitorados em seu nome pela Red Hat e pela Microsoft. Seus clusters do Azure Red Hat OpenShift são implantados em sua assinatura do Azure e estão incluídos na sua fatura do Azure.

Você pode escolher suas próprias soluções de registro, rede, armazenamento e CI/CD ou usar as soluções internas para o gerenciamento automatizado de código-fonte, compilações de contêineres e aplicativos, implantações, dimensionamento, gerenciamento de integridade e muito mais. O Azure Red Hat OpenShift fornece uma experiência de logon integrada por meio de Azure Active Directory.

Para começar, conclua o tutorial [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitorização

Para melhorar a segurança e o gerenciamento, o Azure Red Hat OpenShift permite que você integre com o Azure Active Directory (Azure AD) e use o RBAC (controle de acesso baseado em função) do kubernetes. Também pode monitorizar o estado de funcionamento do seu cluster e recursos.

## <a name="cluster-and-node"></a>Cluster e nó

Os nós do Azure Red Hat OpenShift são executados em máquinas virtuais do Azure. Pode ligar o armazenamento aos nós e aos pods, atualizar os componentes de cluster e utilizar GPUs.

## <a name="virtual-networks-and-ingress"></a>Redes virtuais e entrada

Você pode [conectar um cluster do Azure Red Hat OpenShift a uma rede virtual existente](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) por meio de emparelhamento. Nessa configuração, o pods pode se conectar a outros serviços em uma rede virtual emparelhada.

Consulte [conectar a rede virtual de um cluster a uma rede virtual existente](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) para obter detalhes.

## <a name="kubernetes-certification"></a>Certificação Kubernetes

O serviço Red Hat OpenShift do Azure foi CNCF certificado como kubernetes compatível.

## <a name="next-steps"></a>Passos Seguintes

Conheça os pré-requisitos para o Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Configurar o ambiente de desenvolvimento](howto-setup-environment.md)
