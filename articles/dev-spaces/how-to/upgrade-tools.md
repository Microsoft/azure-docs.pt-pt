---
title: Como atualizar as ferramentas de espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/03/2018
ms.topic: conceptual
ms.technology: azds-kubernetes
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores
ms.openlocfilehash: b56c6069c255984e480b3e6b0e673d89e6c0ee3d
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194136"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Como atualizar as ferramentas de espaços de desenvolvimento do Azure

Se existir uma nova versão e já estiver a utilizar espaços de desenvolvimento do Azure, poderá ter de atualizar as suas ferramentas de cliente do Azure Dev espaços.

## <a name="update-the-azure-cli"></a>Atualizar a CLI do Azure

Ao atualizar a CLI do Azure mais recente, também obtém a versão mais recente da extensão da CLI de espaços de desenvolvimento.

Não é necessário desinstalar a versão anterior, encontrar apenas o download apropriada em [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Atualizar a extensão da CLI de espaços de desenvolvimento e ferramentas da linha de comandos

Execute o seguinte comando:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Atualizar a extensão do VS Code

Uma vez instalado, a extensão é atualizado automaticamente. Poderá ter de recarregar a extensão para usar os novos recursos. No VS Code, abra a **extensões** painel, escolha a **espaços do Azure Dev** extensões e escolha **recarregar**.

## <a name="update-the-visual-studio-extension"></a>Atualizar a extensão do Visual Studio

Como com outras extensões e atualizações, Visual Studio será notificado quando existe uma atualização disponível para o Visual Studio Tools para o Kubernetes, que inclui espaços de desenvolvimento do Azure. Procurar por um ícone de sinalização na parte superior direita do ecrã.

Para atualizar as ferramentas do Visual Studio, selecione o **ferramentas > extensões e atualizações** menu de item e, no lado esquerdo, escolha **atualiza**. Encontrar **Visual Studio Tools para o Kubernetes** e escolha o **atualização** botão.

## <a name="next-steps"></a>Passos Seguintes

Novas ferramentas de teste através da criação de um novo cluster. Experimente os inícios rápidos e tutoriais na [do Azure Dev espaços](/azure/dev-spaces).

> [!WARNING]
> Espaços de desenvolvimento do Azure em clusters existentes será não ser imediatamente todos os patches, para que a certifique-se de que está a utilizar a versão mais recente em todas as suas implementações do Azure, crie um novo cluster depois de atualizar as ferramentas.
