---
title: Como utilizar o kubectl com espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s '
ms.openlocfilehash: 0dfe88966deeb4dcf0196aa1f1584a06794b36a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686329"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Utilizar o kubectl com um espaço de desenvolvimento do Azure

Pode aceder ao cluster de Kubernetes dentro de um espaço de desenvolvimento do Azure e utilizar ferramentas do Kubernetes existentes como `kubectl`.

Em execução `az aks use-dev-spaces` comando irá adicionar automaticamente um `kubectl` contexto de configuração para si, para que o kubectl já deverá estar ligado ao seu cluster de Kubernetes de espaços de desenvolvimento do Azure. Exemplos:
- Confirme o contexto atual: `kubectl config current-context`
- Lista de todos os contextos disponíveis: `kubectl config get-contexts`. 
- Alterar o contexto: `kubectl config use-context <context-name>`
- Ver o dashboard do Kubernetes: executar `kubectl proxy`, em seguida, abra o browser para o endereço que este comando emite (acrescentar `/ui` para o URL para navegar para o dashboard do Kubernetes).
- Lista de serviços em execução no espaço de espaços de desenvolvimento do Azure de padrão com o nome *predefinição*: `kubectl get services --namespace=default`

