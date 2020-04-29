---
title: Como partilhar espaços Azure Dev
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Saiba como usar os Espaços Azure Dev para partilhar um espaço dev no Serviço Azure Kubernetes com outros da sua equipa
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79474412"
---
# <a name="share-azure-dev-spaces"></a>Partilhar os Espaços de Programador do Azure

Com a Azure Dev Spaces, pode partilhar o seu espaço de dev com outros da sua equipa. Cada desenvolvedor pode trabalhar no seu próprio espaço sem medo de quebrar os outros. Além disso, trabalhar em conjunto num só espaço pode permitir-lhe testar o código de ponta a ponta sem ter de criar simulações ou simular dependências. Consulte o guia [de desenvolvimento](../team-development-nodejs.md) da equipa para mais informações.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Criar um espaço de dev para vários desenvolvedores

1. Crie um Espaço Dev em Azure. Escolha [.NET Core e VS Code](../get-started-netcore.md), [.NET Core and Visual Studio,](../get-started-netcore-visualstudio.md)ou [Node.js e VS Code](../get-started-nodejs.md). Terá de ter acesso ao Proprietário ou Colaborador à subscrição azure selecionada.
1. Certifique-se de que cada membro da equipa tem [as permissões adequadas para aceder ao controlador Azure Dev Spaces](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Por exemplo, pode configurar o grupo de **recursos** do Azure Dev Space para conceder acesso ao [Colaborador](/azure/active-directory/role-based-access-control-configure) para cada membro da equipa. Você pode verificar o grupo de recursos de um espaço de v executando este comando:`azds show-context`
1. Peça aos membros da equipa que **selecionem o espaço de dev** para se desenvolverem nele.
   * **Linha de comando ou Código VS**: Para ver os `azds space list`espaços Azure Dev existentes tem acesso a: . Para selecionar um espaço `azds space select`de v: .
   * **Visual Studio IDE**: Abra um projeto no Estúdio Visual, selecione **Azure Dev Spaces** a partir das definições de lançamento drop-down. No diálogo que se abre, selecione um cluster existente.

     ![Definições de lançamento do Estúdio Visual drop-down](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Passos seguintes

Consulte [o desenvolvimento](../team-development-nodejs.md) da equipa para obter mais informações.
