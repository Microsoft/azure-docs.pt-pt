---
title: Boas práticas para o Serviço Azure Kubernetes (AKS)
description: Recolha das melhores práticas do operador de cluster e do desenvolvedor para construir e gerir aplicações no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 627eeffd18a479486e5a682da06bf89cd5f8f0e1
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596322"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Operador de cluster e desenvolvedor de boas práticas para construir e gerir aplicações no Serviço Azure Kubernetes (AKS)

Para construir e executar aplicações com sucesso no Serviço Azure Kubernetes (AKS), existem algumas considerações fundamentais para compreender e implementar. Estas áreas incluem características multi-arrendamento e programador, segurança de cluster e pod, ou continuidade de negócios e recuperação de desastres. As seguintes boas práticas são agrupadas para ajudar os operadores de cluster e os desenvolvedores a compreender as considerações para cada uma destas áreas, e implementar as funcionalidades apropriadas.

Estas boas práticas e artigos conceptuais foram escritos em conjunto com o grupo de produtos AKS, equipas de engenharia e equipas de campo, incluindo cintos negros globais (GBBs).

## <a name="cluster-operator-best-practices"></a>Boas práticas do operador de cluster

Como operador de cluster, trabalhe em conjunto com os proprietários de aplicações e desenvolvedores para entender as suas necessidades. Em seguida, pode utilizar as seguintes melhores práticas para configurar os seus clusters AKS conforme necessário.

**Multi-inquilinos**

* [Melhores práticas de isolamento de cluster](operator-best-practices-cluster-isolation.md)
    * Inclui componentes centrais multi-arrendamento e isolamento lógico com espaços de nome.
* [Melhores práticas de funcionalidades básicas do Scheduler](operator-best-practices-scheduler.md)
    * Inclui a utilização de quotas de recursos e orçamentos de disrupção de cápsulas.
* [Melhores práticas de funcionalidades avançadas do Scheduler](operator-best-practices-advanced-scheduler.md)
    * Inclui a utilização de manchas e tolerâncias, selecionadores de nó e afinidade, e afinidade inter-pod e anti-afinidade.
* [Melhores práticas de autenticação e autorização](operator-best-practices-identity.md)
    * Inclui integração com o Azure Ative Directory, utilizando controlos de acesso baseados em papéis (RBAC) e identidades de pod.

**Segurança**

* [Melhores práticas para atualizações e segurança do cluster](operator-best-practices-cluster-security.md)
    * Inclui garantir o acesso ao servidor API, limitar o acesso ao contentor e gerir upgrades e reboots de nós.
* [Boas práticas para gestão e segurança de imagem de contentores](operator-best-practices-container-image-management.md)
    * Inclui a segurança da imagem e os tempos de execução e as construções automatizadas nas atualizações de imagem base.
* [Boas práticas para a segurança do casulo](developer-best-practices-pod-security.md)
    * Inclui garantir o acesso aos recursos, limitar a exposição credencial e usar identidades de pod e cofres digitais.

**Rede e armazenamento**

* [Boas práticas para conectividade de rede](operator-best-practices-network.md)
    * Inclui diferentes modelos de rede, utilizando firewalls de entrada e aplicação web (WAF), e garantindo o acesso sSH do nó.
* [Melhores práticas para armazenamento e cópias de segurança](operator-best-practices-storage.md)
    * Inclui a escolha do tipo de armazenamento apropriado e tamanho do nó, o fornecimento dinamicamente de volumes e cópias de segurança de dados.

**Funcionamento de cargas de trabalho prontas para a empresa**

* [Boas práticas para continuidade de negócios e recuperação de desastres](operator-best-practices-multi-region.md)
    * Inclui a utilização de pares de regiões, múltiplos clusters com o Gestor de Tráfego Azure e a geo-replicação de imagens de contentores.

## <a name="developer-best-practices"></a>Boas práticas de desenvolvimento

Como desenvolvedor ou proprietário de aplicações, pode simplificar a sua experiência de desenvolvimento e definir exigir necessidades de desempenho da aplicação.

* [Melhores práticas para os programadores de aplicações gerirem os recursos](developer-best-practices-resource-management.md)
    * Inclui a definição de pedidos e limites de recursos de casulo, configuração de ferramentas de desenvolvimento e verificação de problemas de aplicação.
* [Boas práticas para a segurança do casulo](developer-best-practices-pod-security.md)
    * Inclui garantir o acesso aos recursos, limitar a exposição credencial e usar identidades de pod e cofres digitais.

## <a name="kubernetes--aks-concepts"></a>Conceitos Kubernetes / AKS

Para ajudar a compreender algumas das funcionalidades e componentes destas boas práticas, também pode ver os seguintes artigos conceptuais para clusters no Serviço Azure Kubernetes (AKS):

* [Conceitos centrais de Kubernetes](concepts-clusters-workloads.md)
* [Acesso e identidade](concepts-identity.md)
* [Conceitos de segurança](concepts-security.md)
* [Conceitos de rede](concepts-network.md)
* [Opções de armazenamento](concepts-storage.md)
* [Opções de escala](concepts-scale.md)

## <a name="next-steps"></a>Passos seguintes

Se precisar de começar com a AKS, siga um dos quickstarts para implementar um cluster azure Kubernetes Service (AKS) utilizando o portal [Azure CLI](kubernetes-walkthrough.md) ou [Azure](kubernetes-walkthrough-portal.md).
