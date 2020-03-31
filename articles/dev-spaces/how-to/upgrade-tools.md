---
title: Como atualizar as ferramentas Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Saiba como atualizar as ferramentas de linha de comando Azure Dev Spaces, extensão do Código do Estúdio Visual e extensão do Estúdio Visual
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contentores Azure, contentores
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265236"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Como atualizar as ferramentas Azure Dev Spaces

Se houver um novo lançamento e já estiver a utilizar o Azure Dev Spaces, poderá ter de atualizar as suas ferramentas de cliente Da Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Atualizar o Azure CLI

Ao atualizar o mais recente Azure CLI, obtém também a versão mais recente da extensão CLI dos Espaços Dev Spaces.

Não precisa de desinstalar a versão anterior, basta encontrar o download apropriado no [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Atualize as ferramentas de extensão CLI e linha de comando de Dev Spaces

Execute o seguinte comando:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Atualizar a extensão do Código VS

Uma vez instalada, a extensão atualiza-se automaticamente. Poderá ser necessário recarregar a extensão para utilizar as novas funcionalidades. No Código VS, abra o painel de **extensões,** escolha as extensões **dos Espaços Azure Dev** e escolha **recarregar**.

## <a name="update-the-visual-studio-extension"></a>Atualizar a extensão do Estúdio Visual

Tal como acontece com outras extensões e atualizações, o Visual Studio irá notificá-lo quando houver uma atualização disponível para as Ferramentas de Estúdio Visual para Kubernetes, que inclui os Espaços Azure Dev. Procure um ícone de bandeira na parte superior direita do ecrã.

Para atualizar as ferramentas no Estúdio Visual, escolha o item do menu **Tools > Extensions and Updates** e, no lado esquerdo, escolha **Atualizações**. Encontre **ferramentas de estúdio visuais para Kubernetes** e escolha o botão **Update.**

## <a name="next-steps"></a>Passos seguintes

Teste as novas ferramentas criando um novo cluster. Experimente os quickstarts e tutoriais no [Azure Dev Spaces.](/azure/dev-spaces)
