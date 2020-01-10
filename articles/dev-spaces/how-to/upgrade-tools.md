---
title: Como atualizar as ferramentas de Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Saiba como atualizar as ferramentas de linha de comando Azure Dev Spaces, a extensão de código do pino visual e a extensão do Visual Studio
keywords: Docker, kubernetes, Azure, AKS, serviço de contêiner do Azure, contêineres
ms.openlocfilehash: 07d55689ac94a865527f4b595765d67b28ddb97a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438408"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Como atualizar as ferramentas de Azure Dev Spaces

Se houver uma nova versão e você já estiver usando Azure Dev Spaces, talvez seja necessário atualizar suas ferramentas de cliente do Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Atualizar o CLI do Azure

Ao atualizar a CLI do Azure mais recente, você também obtém a versão mais recente da extensão da CLI de espaços de desenvolvimento.

Você não precisa desinstalar a versão anterior, basta encontrar o download apropriado em [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Atualizar a extensão da CLI de espaços de desenvolvimento e ferramentas de linha de comando

Execute o seguinte comando:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Atualizar a extensão de VS Code

Uma vez instalado, a extensão é atualizada automaticamente. Talvez seja necessário recarregar a extensão para usar os novos recursos. No VS Code, abra o painel **extensões** , escolha as extensões de **Azure dev Spaces** e escolha **recarregar**.

## <a name="update-the-visual-studio-extension"></a>Atualizar a extensão do Visual Studio

Assim como ocorre com outras extensões e atualizações, o Visual Studio o notificará quando houver uma atualização disponível para o Ferramentas do Visual Studio para Kubernetes, que inclui Azure Dev Spaces. Procure um ícone de sinalizador no canto superior direito da tela.

Para atualizar as ferramentas no Visual Studio, escolha o item de menu **ferramentas > extensões e atualizações** e, no lado esquerdo, escolha **atualizações**. Localize **Ferramentas do Visual Studio para kubernetes** e escolha o botão **Atualizar** .

## <a name="next-steps"></a>Passos seguintes

Teste as novas ferramentas criando um novo cluster. Experimente os guias de início rápido e os tutoriais em [Azure dev Spaces](/azure/dev-spaces).