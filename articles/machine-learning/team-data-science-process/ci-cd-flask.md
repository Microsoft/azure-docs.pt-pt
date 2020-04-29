---
title: Criar um oleoduto CI/CD com Pipelines Azure - Team Data Science Process
description: Crie um oleoduto de integração contínua e entrega contínua para aplicações de Inteligência Artificial (IA) utilizando Docker e Kubernetes.
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
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721834"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Criar oleodutos CI/CD para aplicações de IA utilizando pipelines Azure, Docker e Kubernetes

Uma aplicação de Inteligência Artificial (IA) é código de aplicação incorporado com um modelo de aprendizagem automática (ML) pré-treinado. Há sempre dois fluxos de trabalho para uma aplicação de IA: os cientistas de dados constroem o modelo ML, e os desenvolvedores de aplicações constroem a app e expõem-na aos utilizadores finais para consumir. Este artigo descreve como implementar um oleoduto de integração contínua e entrega contínua (CI/CD) para uma aplicação de IA que incorpora o modelo ML no código fonte da aplicação. O código de amostra e tutorial usam uma aplicação web Python Flask, e obter um modelo pré-treinado de uma conta privada de armazenamento de blob Azure. Também pode utilizar uma conta de armazenamento AWS S3.

> [!NOTE]
> O processo seguinte é uma das várias formas de fazer CI/CD. Existem alternativas a esta ferramenta e aos pré-requisitos.

## <a name="source-code-tutorial-and-prerequisites"></a>Código fonte, tutorial e pré-requisitos

Você pode baixar [o código fonte](https://github.com/Azure/DevOps-For-AI-Apps) e um tutorial [detalhado](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) do GitHub. Siga os passos tutoriais para implementar um pipeline CI/CD para a sua própria aplicação.

Para utilizar o código fonte e tutorial descarregados, precisa dos seguintes pré-requisitos: 

- O [repositório de código fonte](https://github.com/Azure/DevOps-For-AI-Apps) bifurcado para a sua conta GitHub
- Uma [Organização Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [CLI do Azure](/cli/azure/install-azure-cli)
- Um serviço de [contentores Azure para o cluster Kubernetes (AKS)](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para executar comandos e buscar a configuração do cluster AKS 
- Uma conta de Registo de [Contentores Azure (ACR)](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Resumo do gasoduto CI/CD

Cada novo compromisso git dá o pontapé de saída do oleoduto Build. A construção retira de forma segura o mais recente modelo ML de uma conta de armazenamento blob, e embala-o com o código da aplicação num único recipiente. Esta dissociação do desenvolvimento de aplicações e dos fluxos de trabalho da ciência dos dados garante que a aplicação de produção está sempre a executar o mais recente código com o mais recente modelo ML. Se a aplicação passar nos testes, o gasoduto armazena de forma segura a imagem de construção num contentor do Docker em ACR. O gasoduto de libertação implanta então o contentor utilizando AKS. 

## <a name="cicd-pipeline-steps"></a>Passos do gasoduto CI/CD

O diagrama e os passos seguintes descrevem a arquitetura do gasoduto CI/CD:

![Arquitetura do gasoduto CI/CD](./media/ci-cd-flask/architecture.png)

1. Os desenvolvedores trabalham no código de aplicação no IDE à sua escolha.
2. Os desenvolvedores comprometem o código ao Azure Repos, GitHub ou a outro fornecedor de controlo de fontegit. 
3. Separadamente, os cientistas de dados trabalham no desenvolvimento do seu modelo ML.
4. Os cientistas de dados publicam o modelo acabado num repositório modelo, neste caso uma conta de armazenamento blob. 
5. A Azure Pipelines inicia uma construção baseada no compromisso de Git.
6. O pipeline Build retira o mais recente modelo ML do armazenamento de blob e cria um recipiente.
7. O oleoduto empurra a imagem de construção para o repositório de imagem privada em ACR.
8. O oleoduto Release começa com base na construção bem sucedida.
9. O oleoduto retira a mais recente imagem da ACR e implanta-a através do cluster Kubernetes na AKS.
10. Os pedidos do utilizador para a aplicação passam pelo servidor DNS.
11. O servidor DNS transmite os pedidos a um equilibrista de carga e envia respostas aos utilizadores.

## <a name="see-also"></a>Consulte também

- [Processo de Ciência de Dados da Equipa (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Aprendizagem automática azure (AML)](/azure/machine-learning/)
- [DevOps do Azure](https://azure.microsoft.com/services/devops/)
- [Serviços Azure Kubernetes (AKS)](/azure/aks/intro-kubernetes)
