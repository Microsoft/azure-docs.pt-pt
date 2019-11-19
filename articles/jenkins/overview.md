---
title: Descrição geral do Jenkins e do Azure
description: Hospede o Jenkins Build e implante o servidor de automação no Azure e use os recursos de computação e armazenamento do Azure para estender os pipelines de CI/CD (integração e implantação contínuas).
keywords: jenkins, azure, devops, overview
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: a7ac07af4d9f3c065ce033fac3982091a0b9c679
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158753"
---
# <a name="azure-and-jenkins"></a>Azure e Jenkins

O [Jenkins](https://jenkins.io/) é um popular servidor de automatização de código aberto que é utilizado para configurar a integração e implementação contínua (CI/CD) nos seus projetos de software. Pode alojar as implementações do Jenkins no Azure ou utilizar recursos do Azure para ampliar a sua configuração atual do Jenkins. Também estão disponíveis plug-ins do Jenkins para simplificar o CI/CD das suas aplicações para o Azure.

Este artigo é uma introdução à utilização do Azure com Jenkins, pormenorizando as principais funcionalidades do Azure que estão à disposição dos utilizadores do Jenkins. Para obter mais informações sobre como começar o seu próprio servidor Jenkins no Azure, veja [criar um servidor Jenkins no Azure](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Alojar os servidores do Jenkins no Azure

Aloje o Jenkins no Azure para centralizar a automatização de compilações e dimensionar a sua implementação à medida que as necessidades dos seus projetos de software aumentam. Pode implementar o Jenkins no Azure com:
 
- [O modelo de solução do Jenkins](install-jenkins-solution-template.md) no Azure Marketplace.
- [Máquinas virtuais do Azure](/azure/virtual-machines/linux/overview). Veja o nosso [tutorial](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd) sobre como criar uma instância do Jenkins numa VM.
- Num cluster do Kubernetes em execução no [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough); veja as [instruções](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Monitore e gerencie sua implantação do Azure Jenkins usando [logs de Azure monitor](/azure/log-analytics/log-analytics-overview) e o [CLI do Azure](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Dimensionar a automatização da compilação a pedido

Adicione agentes de compilação à sua implementação do Jenkins existente para dimensionar a capacidade de compilação do Jenkins à medida que o número de compilações e a complexidade dos seus trabalhos e pipelines aumentam. Você pode executar esses agentes de compilação em máquinas virtuais do Azure usando o [plug-in de agentes de VM do Azure](jenkins-azure-vm-agents.md). Veja mais detalhes no nosso [tutorial](/azure/jenkins/jenkins-azure-vm-agents).

Quando configurados com um [principal de serviço do Azure](/azure/azure-resource-manager/resource-group-overview), os trabalhos e os pipelines do Jenkins podem utilizar esta credencial para:

- Armazene e arquive artefatos de compilação com segurança no [armazenamento do Azure](/azure/storage/common/storage-introduction) usando o [plug-in de armazenamento do Azure](https://plugins.jenkins.io/windows-azure-storage). Reveja [Jenkins storage how-to](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) (Instruções de armazenamento do Jenkins) para saber mais.
- Gerir e configurar os recursos do Azure com a [CLI do Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Implementar o seu código nos serviços do Azure

Utilize os plug-ins do Jenkins para implementar as suas aplicações no Azure como parte dos pipelines de CI/CD do Jenkins. A implementação no [Serviço de Aplicações do Azure](/azure/app-service/) e no [Azure Container Service](/azure/container-service/kubernetes/) permite-lhe preparar, testar e lançar atualizações para as suas aplicações sem ter de gerir a infraestrutura adjacente.

 Estão disponíveis plug-ins para implementar nos seguintes serviços e ambientes:

- [Serviço de Azure app no Linux](/azure/app-service/containers/app-service-linux-intro). Veja o [tutorial](java-deploy-webapp-tutorial.md) para começar.
- [Serviço de Azure app](/azure/app-service/overview). Veja as [instruções](deploy-Jenkins-app-service-plugin.md) para começar.