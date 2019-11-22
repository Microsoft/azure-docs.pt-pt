---
title: Como usar o kubectl com Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido do Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: fea14668c35eddcd01417b417e2239788eddd6d1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279933"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Usar o kubectl com um espaço de desenvolvimento do Azure

Você pode acessar o cluster kubernetes dentro de um espaço de desenvolvimento do Azure e usar ferramentas de kubernetes existentes, como `kubectl`.

Executar `az aks use-dev-spaces` comando adicionará automaticamente um contexto de configuração de `kubectl` para você, portanto kubectl já deve estar conectado ao cluster Azure Dev Spaces kubernetes. Exemplos:
- Confirme o contexto atual: `kubectl config current-context`
- Listar todos os contextos disponíveis: `kubectl config get-contexts`. 
- Alterar contexto: `kubectl config use-context <context-name>`
- Exiba o painel do kubernetes: execute `kubectl proxy`e, em seguida, abra o navegador para o endereço emitido por esse comando (acrescente `/ui` à URL para navegar até o painel do kubernetes).
- Liste os serviços em execução no espaço de Azure Dev Spaces padrão denominado *padrão*: `kubectl get services --namespace=default`

