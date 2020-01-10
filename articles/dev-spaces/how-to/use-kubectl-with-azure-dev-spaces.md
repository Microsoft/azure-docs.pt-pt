---
title: Como usar o kubectl com Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Saiba como usar comandos kubectl em um espaço de desenvolvimento em um cluster do serviço kubernetes do Azure com Azure Dev Spaces habilitado
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438370"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Usar o kubectl com um espaço de desenvolvimento do Azure

Você pode acessar o cluster kubernetes dentro de um espaço de desenvolvimento do Azure e usar ferramentas de kubernetes existentes, como `kubectl`.

Executar `az aks use-dev-spaces` comando adicionará automaticamente um contexto de configuração de `kubectl` para você, portanto kubectl já deve estar conectado ao cluster Azure Dev Spaces kubernetes. Exemplos:
- Confirme o contexto atual: `kubectl config current-context`
- Listar todos os contextos disponíveis: `kubectl config get-contexts`. 
- Alterar contexto: `kubectl config use-context <context-name>`
- Exiba o painel do kubernetes: execute `kubectl proxy`e, em seguida, abra o navegador para o endereço emitido por esse comando (acrescente `/ui` à URL para navegar até o painel do kubernetes).
- Liste os serviços em execução no espaço de Azure Dev Spaces padrão denominado *padrão*: `kubectl get services --namespace=default`

