---
title: Melhores práticas para o Azure Kubernetes Service (AKS)
description: Coleção dos cluster operador e desenvolvedor de melhores práticas para criar e gerir aplicações no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 12/07/2018
ms.author: iainfou
ms.openlocfilehash: c73f2fea808944847fab6a675914ee57e537560b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731332"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Operador de cluster e desenvolvedor de melhores práticas para criar e gerir aplicações no Azure Kubernetes Service (AKS)

Para criar e executar aplicações com êxito no Azure Kubernetes Service (AKS), existem algumas considerações-chave para compreender e implementar. Estas áreas incluem vários inquilinos e funcionalidades de programador, cluster e segurança de pod, ou continuidade do negócio e recuperação após desastre. As seguintes melhores práticas são agrupadas para ajudar os operadores de cluster e os desenvolvedores compreender as considerações para cada uma dessas áreas e implementam os recursos apropriados.

Estas melhores práticas e artigos conceptuais tem sido escritos em conjunto com o grupo de produtos do AKS, equipes e equipas de campo, incluindo global preto belts (GBBs) de engenharia.

## <a name="cluster-operator-best-practices"></a>Práticas recomendadas de operador de cluster

Como um operador de cluster, funcionam em conjunto com os proprietários da aplicação e os desenvolvedores a compreender suas necessidades. Em seguida, pode utilizar as seguintes melhores práticas para configurar seus clusters do AKS, conforme necessário.

**Multi-inquilinos**

* [Melhores práticas para isolamento de cluster](operator-best-practices-cluster-isolation.md)
    * Inclui os componentes principais de vários inquilinos e isolamento lógico com espaços de nomes.
* [Melhores práticas para as funcionalidades básicas do scheduler](operator-best-practices-scheduler.md)
    * Inclui a utilização de quotas de recursos e os orçamentos de interrupção de pod.
* [Práticas recomendadas para recursos avançados do agendador](operator-best-practices-advanced-scheduler.md)
    * Inclui a utilização taints e tolerations, seletores de nó e afinidade e afinidade de pod entre e antiafinidade.
* [Melhores práticas para autenticação e autorização](operator-best-practices-identity.md)
    * Inclui a integração com o Azure Active Directory, com controlos de acesso baseado em funções (RBAC) e identidades de pod.

**Segurança**

* [Melhores práticas para segurança do cluster e atualizações](operator-best-practices-cluster-security.md)
    * Inclui a proteção de acesso para o servidor de API, limitando o acesso ao contentor e gerenciamento de atualizações e reinícios do nó.
* [Melhores práticas para segurança e gerenciamento de imagens de contentor](operator-best-practices-container-image-management.md)
    * Inclui a proteção da imagem e tempos de execução, com registos confiáveis e compilações automatizadas no atualizações da imagem base....
* [Práticas recomendadas para segurança de pod](developer-best-practices-pod-security.md)
    * Inclui a proteção de acesso a recursos, limitar a exposição de credenciais e o uso de identidades de pod e cofres de chaves digitais.

**Rede e armazenamento**

* [Melhores práticas para a conectividade de rede](operator-best-practices-network.md)
    * Inclui modelos de rede diferentes, através de firewalls de aplicação de entrada e de web (WAF) e proteção de acesso SSH do nó.
* [Melhores práticas para armazenamento e cópias de segurança](operator-best-practices-storage.md)
    * Inclui escolher o tamanho de tipo e nó de armazenamento adequado, provisionamento dinâmico de volumes e cópias de segurança de dados.

**Executar cargas de trabalho prontos para empresas**

* [Melhores práticas para a recuperação de desastre e continuidade de negócio](operator-best-practices-multi-region.md)
    * Inclui o uso de pares de região, vários clusters com o Gestor de tráfego do Azure e a georreplicação de imagens de contentor.

## <a name="developer-best-practices"></a>Melhores práticas de programador

Como desenvolvedor ou proprietário da aplicação, pode simplificar a sua experiência de desenvolvimento e definir exigem as necessidades de desempenho do aplicativo.

* [Melhores práticas para os desenvolvedores de aplicativos gerir os recursos](developer-best-practices-resource-management.md)
    * Inclui a definição de pedidos de recursos de pod e limites, configuração de ferramentas de desenvolvimento e verificar a existência de problemas de aplicativos.
* [Práticas recomendadas para segurança de pod](developer-best-practices-pod-security.md)
    * Inclui a proteção de acesso a recursos, limitar a exposição de credenciais e o uso de identidades de pod e cofres de chaves digitais.

## <a name="kubernetes--aks-concepts"></a>Kubernetes / conceitos do AKS

Para ajudar a compreender alguns dos recursos e componentes destas melhores práticas, também pode ver os seguintes artigos conceptuais para clusters no Azure Kubernetes Service (AKS):

* [Principais conceitos do Kubernetes](concepts-clusters-workloads.md)
* [Acesso e identidade](concepts-identity.md)
* [Conceitos de segurança](concepts-security.md)
* [Conceitos de rede](concepts-network.md)
* [Opções de armazenamento](concepts-storage.md)
* [Opções de dimensionamento](concepts-scale.md)

## <a name="next-steps"></a>Passos Seguintes

Se precisa para começar a utilizar com o AKS, siga um dos inícios rápidos para implementar um cluster do Azure Kubernetes Service (AKS) com o [CLI do Azure](kubernetes-walkthrough.md) ou [portal do Azure](kubernetes-walkthrough-portal.md).
