---
title: Como atualizar as ferramentas Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
ms.custom: devx-track-azurecli
description: Saiba como atualizar as ferramentas de linha de comando Azure Dev Spaces, extensão visual Studio Code e extensão visual Studio
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores
ms.openlocfilehash: aebe81d58a1bcd44f5766d368dbafa53c81ed2b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87504335"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Como atualizar as ferramentas Azure Dev Spaces

Se houver um novo lançamento e já estiver a utilizar a Azure Dev Spaces, poderá ter de atualizar as suas ferramentas de clienteS Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Atualizar o Azure CLI

Ao atualizar o mais recente Azure CLI, obtém também a versão mais recente da extensão CLI de Dev Spaces.

Não precisa de desinstalar a versão anterior, basta encontrar o download apropriado no [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Atualizar as ferramentas de extensão CLI e linha de comando de Dev Spaces

Execute o seguinte comando:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Atualizar a extensão do Código VS

Uma vez instalado, a extensão atualiza-se automaticamente. Poderá ser necessário recarregar a extensão para utilizar as novas funcionalidades. No Código VS, abra o painel **de extensões,** escolha as extensões **Azure Dev Spaces** e escolha **Recarregar**.

## <a name="update-visual-studio"></a>Atualização Estúdio Visual

Azure Dev Spaces faz parte da carga de trabalho do Azure Development e está incluído em todas as atualizações do Visual Studio.

## <a name="next-steps"></a>Passos seguintes

Teste as novas ferramentas criando um novo cluster. Experimente os quickstarts e tutoriais no [Azure Dev Spaces.](../index.yml)
