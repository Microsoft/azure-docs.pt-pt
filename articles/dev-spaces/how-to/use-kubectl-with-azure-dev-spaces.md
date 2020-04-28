---
title: Como usar kubectl com espaços Azure Dev
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Saiba como usar comandos kubectl dentro de um espaço de v em um cluster de serviço Azure Kubernetes com espaços Azure Dev habilitados
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75438370"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Use kubectl com um Espaço Azure Dev

Você pode aceder ao cluster Kubernetes dentro de um Espaço Azure Dev, e usar ferramentas kubernetes existentes como `kubectl`.

O `az aks use-dev-spaces` comando de funcionamento irá automaticamente adicionar um `kubectl` contexto de configuração para si, pelo que o kubectl já deve estar ligado ao seu cluster Dev Spaces Kubernetes. Exemplos:
- Confirme o contexto atual:`kubectl config current-context`
- Enumerar todos os `kubectl config get-contexts`contextos disponíveis: . 
- Alterar o contexto:`kubectl config use-context <context-name>`
- Veja o dashboard Kubernetes: executar, `kubectl proxy`em seguida, abra o seu `/ui` navegador para o endereço que este comando emite (apêndice ao URL para navegar para o dashboard Kubernetes).
- Enumerar os serviços de funcionamento no espaço padrão do Azure Dev Spaces nomeado *por defeito:*`kubectl get services --namespace=default`

