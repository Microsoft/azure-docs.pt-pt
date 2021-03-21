---
title: Criar um pipeline CI/CD com Pipelines Azure - Processo de Ciência de Dados de Equipa
description: Crie um pipeline de integração contínua e entrega contínua para aplicações de Inteligência Artificial (IA) utilizando Docker e Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: af7900faff18d526686b80f23b9de3b3bce5ec5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93309605"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Criar oleodutos CI/CD para aplicações de IA utilizando Pipelines Azure, Docker e Kubernetes

Uma aplicação de Inteligência Artificial (IA) é um código de aplicação incorporado com um modelo de aprendizagem automática pré-treinado (ML). Existem sempre dois fluxos de trabalho para uma aplicação de IA: os cientistas de dados constroem o modelo ML, e os desenvolvedores de aplicações constroem a app e expõem-na aos utilizadores finais para consumir. Este artigo descreve como implementar um pipeline de integração contínua e entrega contínua (CI/CD) para uma aplicação de IA que incorpora o modelo ML no código fonte da aplicação. O código de amostra e tutorial usam uma aplicação web Python Flask e recebem um modelo pré-treinado a partir de uma conta privada de armazenamento de bolhas Azure. Também pode utilizar uma conta de armazenamento AWS S3.

> [!NOTE]
> O processo a seguir é uma das várias formas de fazer CI/CD. Existem alternativas a esta ferramenta e aos pré-requisitos.

## <a name="source-code-tutorial-and-prerequisites"></a>Código fonte, tutorial e pré-requisitos

Você pode baixar [código fonte](https://github.com/Azure/DevOps-For-AI-Apps) e um [tutorial detalhado](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) do GitHub. Siga os passos tutoriais para implementar um pipeline CI/CD para a sua própria aplicação.

Para utilizar o código fonte descarregado e tutorial, precisa dos seguintes pré-requisitos: 

- O [repositório de código fonte](https://github.com/Azure/DevOps-For-AI-Apps) forcado à sua conta GitHub
- Uma [Organização Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [CLI do Azure](/cli/azure/install-azure-cli)
- Um [serviço de contentores Azure para o cluster Kubernetes (AKS)](/previous-versions/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para executar comandos e obter configuração do cluster AKS 
- Uma [conta do Registo de Contentores Azure (ACR)](../../container-registry/container-registry-get-started-portal.md)

## <a name="cicd-pipeline-summary"></a>Resumo do gasoduto CI/CD

Cada novo compromisso de Git dá o pontapé de saída do oleoduto Build. A construção retira de forma segura o mais recente modelo ML de uma conta de armazenamento de bolhas e embala-o com o código de aplicação num único recipiente. Esta dissociação do desenvolvimento de aplicações e dos fluxos de trabalho da ciência dos dados garante que a aplicação de produção está sempre a executar o mais recente código com o mais recente modelo ML. Se a aplicação passar nos testes, o gasoduto armazena de forma segura a imagem de construção num recipiente Docker em ACR. Em seguida, o gasoduto de desbloqueio implanta o recipiente utilizando aKS. 

## <a name="cicd-pipeline-steps"></a>Etapas do gasoduto CI/CD

O diagrama e os passos seguintes descrevem a arquitetura do gasoduto CI/CD:

![Arquitetura de gasodutos CI/CD](./media/ci-cd-flask/architecture.png)

1. Os desenvolvedores trabalham no código de aplicação no IDE à sua escolha.
2. Os desenvolvedores comprometem o código com Azure Repos, GitHub ou outro fornecedor de controlo de fontes Git. 
3. Separadamente, os cientistas de dados trabalham no desenvolvimento do seu modelo ML.
4. Os cientistas de dados publicam o modelo acabado num repositório de modelos, neste caso uma conta de armazenamento de bolhas. 
5. Azure Pipelines dá início a uma construção baseada no compromisso git.
6. O gasoduto Build retira o mais recente modelo ML do armazenamento de bolhas e cria um recipiente.
7. O oleoduto empurra a imagem de construção para o repositório de imagem privada em ACR.
8. O gasoduto Release arranca com base no sucesso da construção.
9. O oleoduto retira a mais recente imagem da ACR e implanta-a através do cluster Kubernetes na AKS.
10. Os pedidos dos utilizadores para a aplicação passam pelo servidor DNS.
11. O servidor DNS transmite os pedidos a um equilibrador de carga e envia respostas para os utilizadores.

## <a name="see-also"></a>Ver também

- [Processo de ciência de dados da equipa (TDSP)](./index.yml)
- [Azure Machine Learning (AML)](../index.yml)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Services (AKS)](../../aks/intro-kubernetes.md)