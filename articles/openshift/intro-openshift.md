---
title: Introdução ao Azure Red Hat OpenShift | Documentos da Microsoft
description: Saiba os recursos e benefícios do Microsoft Azure Red Hat OpenShift para implementar e gerir aplicações baseadas em contentores.
services: container-service
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: bfa3e63a433a6948f05ece7ec3c3a6180933510b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466173"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

A Microsoft *Azure Red Hat OpenShift* service permite-lhe implementar totalmente gerido [OpenShift](https://www.openshift.com/) clusters.

Expande a Azure Red Hat OpenShift [Kubernetes](https://kubernetes.io/). Contentores em execução na produção com o Kubernetes requer ferramentas adicionais e recursos, como um registo de imagem, gestão de armazenamento, rede soluções e Registro em log e ferramentas de monitorização, todos os quais tem de ser uma versão e testado em conjunto. Criar aplicações baseadas em contentores requer mais trabalho de integração com o middleware, arquiteturas, bases de dados e ferramentas de CI/CD. Azure Red Hat OpenShift combina tudo isso numa única plataforma, género, facilidade de operações para as equipas de TI, oferecendo ao aplicativo as equipes que necessitam para executar.

Azure Red Hat OpenShift em conjunto, é projetado, operado e suportado pelo Red Hat e Microsoft para proporcionar uma experiência de suporte integrado. Não há nenhuma máquina virtual para operar e nenhuma aplicação de patches é necessária. Nós principal, infraestrutura e aplicação são corrigidos, atualizados e monitorizados em seu nome pelo Red Hat e Microsoft. Os clusters do Azure Red Hat OpenShift estão incluídos na sua fatura do Azure e são implementados na sua subscrição do Azure.

Pode escolher o seu próprio registo, funcionamento em rede, armazenamento e soluções de CI/CD, ou utilize baseia-se as soluções incorporadas para aplicações, contentores e gestão de código fonte automatizada, implementações, dimensionamento, gestão de estado de funcionamento e muito mais. Azure Red Hat OpenShift fornece uma experiência integrada início de sessão no Azure Active Directory.

Para começar a utilizar, execute o [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitorização

Para maior segurança e gestão, o Azure Red Hat OpenShift permite-lhe integrar com o Azure Active Directory (Azure AD) e utilizar o Kubernetes controlo de acesso baseado em funções (RBAC). Também pode monitorizar o estado de funcionamento do seu cluster e recursos.

## <a name="cluster-and-node"></a>Cluster e nó

Nós do Azure de Red Hat OpenShift executam em máquinas virtuais do Azure. Pode ligar o armazenamento aos nós e aos pods, atualizar os componentes de cluster e utilizar GPUs.

## <a name="virtual-networks-and-ingress"></a>Redes virtuais e entrada

Pode ligar um cluster do Azure Red Hat OpenShift a uma rede virtual existente através do peering. Nesta configuração, pods podem ligar-se para outros serviços numa rede virtual em modo de peering e redes no local através de [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) ou site a site (S2S) ligações VPN.

Ver [ligar uma rede virtual de um cluster a uma rede virtual existente](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) para obter detalhes.

## <a name="kubernetes-certification"></a>Certificação Kubernetes

Serviço do Azure de Red Hat OpenShift tem sido CNCF certificado como compatível de Kubernetes.

## <a name="next-steps"></a>Passos Seguintes

Saiba os pré-requisitos para a Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Configurar o ambiente de desenvolvimento](howto-setup-environment.md)