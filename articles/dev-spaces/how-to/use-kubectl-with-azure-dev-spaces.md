---
title: Como usar kubectl com Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Saiba como usar comandos kubectl dentro de um espaço dev em um cluster de serviço Azure Kubernetes com Azure Dev Spaces ativados
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75438370"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Use kubectl com um Espaço Azure Dev

Você pode aceder ao cluster Kubernetes dentro de um Azure Dev Space, e usar ferramentas kubernetes existentes como `kubectl` .

O comando de `az aks use-dev-spaces` execução irá adicionar automaticamente um `kubectl` contexto de configuração para si, pelo que o kubectl já deve estar ligado ao seu cluster Azure Dev Spaces Kubernetes. Exemplos:
- Confirme o contexto atual: `kubectl config current-context`
- Listar todos os contextos disponíveis: `kubectl config get-contexts` . 
- Contexto de alteração: `kubectl config use-context <context-name>`
- Ver o dashboard Kubernetes: `kubectl proxy` executar, em seguida, abrir o seu navegador para o endereço que este comando emite (anexar `/ui` ao URL para navegar no painel de instrumentos Kubernetes).
- Listar os serviços em execução no espaço padrão Azure Dev Spaces denominado *padrão:*`kubectl get services --namespace=default`

