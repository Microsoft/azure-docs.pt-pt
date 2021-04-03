---
title: Como usar kubectl com Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Saiba como usar comandos kubectl dentro de um espaço dev em um cluster de serviço Azure Kubernetes com Azure Dev Spaces ativados
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960170"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Use kubectl com um Espaço Azure Dev

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Você pode aceder ao cluster Kubernetes dentro de um Azure Dev Space, e usar ferramentas kubernetes existentes como `kubectl` .

O comando de `az aks use-dev-spaces` execução irá adicionar automaticamente um `kubectl` contexto de configuração para si, pelo que o kubectl já deve estar ligado ao seu cluster Azure Dev Spaces Kubernetes. Exemplos:
- Confirme o contexto atual: `kubectl config current-context`
- Listar todos os contextos disponíveis: `kubectl config get-contexts` . 
- Contexto de alteração: `kubectl config use-context <context-name>`
- Ver o dashboard Kubernetes: `kubectl proxy` executar, em seguida, abrir o seu navegador para o endereço que este comando emite (anexar `/ui` ao URL para navegar no painel de instrumentos Kubernetes).
- Listar os serviços em execução no espaço padrão Azure Dev Spaces denominado *padrão:*`kubectl get services --namespace=default`

