---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 6e0175173f17ae0958522517360b94ee80f3b2f9
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544956"
---
## <a name="prerequisites"></a>Pré-requisitos

### <a name="get-sample-code"></a>Obter o código de exemplo

Este tutorial parte do princípio de que já concluiu os passos no [tutorial anterior](../articles/container-registry/container-registry-tutorial-quick-task.md) e já bifurcou e clonou o repositório de exemplo. Se ainda não o fez, conclua os passos na secção [Pré-requisitos](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) do tutorial anterior antes de continuar.

### <a name="container-registry"></a>Registo de contentor

Para concluir este tutorial tem de ter um registo de contentor do Azure na sua subscrição do Azure. Se precisar de um registo, consulte a [tutorial anterior](../articles/container-registry/container-registry-tutorial-quick-task.md), ou [início rápido: Criar um registo de contentor com a CLI do Azure](../articles/container-registry/container-registry-get-started-azure-cli.md).

## <a name="create-a-github-personal-access-token"></a>Criar um token de acesso pessoal do GitHub

Para acionar uma tarefa numa consolidação para um repositório de Git, tarefas de ACR tem um token de acesso pessoal (PAT) para acessar o repositório. Se ainda não tiver um TAPINHA, siga estes passos para gerar um no GitHub:

1. Navegue para a página de criação do PAT no GitHub em https://github.com/settings/tokens/new
1. Escrever uma **descrição** breve para o token, por exemplo, "Demonstração do ACR Tasks"
1. Em **repositório**, ative **repo:status** e **public_repo**

   ![Captura de ecrã da página de geração do Token de Acesso Pessoal no GitHub][build-task-01-new-token]

1. Selecione o botão **Gerar token** (poderá ser-lhe pedido para confirmar a palavra-passe)
1. Copie e guarde o token gerado numa  **localização segura** (irá utilizar este token na definição de uma tarefa de compilação na secção seguinte)

   ![Captura de ecrã do Token de Acesso Pessoal gerado no GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
