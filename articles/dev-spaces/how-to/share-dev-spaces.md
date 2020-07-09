---
title: Como partilhar espaços Azure Dev
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Saiba como usar a Azure Dev Spaces para partilhar um espaço dev no Serviço Azure Kubernetes com outros da sua equipa
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79474412"
---
# <a name="share-azure-dev-spaces"></a>Partilhar os Espaços de Programador do Azure

Com a Azure Dev Spaces, pode partilhar o seu espaço dev com outros da sua equipa. Cada desenvolvedor pode trabalhar no seu próprio espaço sem medo de quebrar os outros. Além disso, trabalhar em conjunto num espaço pode permitir-lhe testar código de ponta a ponta sem ter de criar simulações ou simular dependências. Consulte o guia [de desenvolvimento de equipas](../team-development-nodejs.md) para obter mais informações.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Crie um espaço dev para vários desenvolvedores

1. Crie um Espaço Dev em Azure. Escolha [.NET Core e VS Code](../get-started-netcore.md), [.NET Core and Visual Studio](../get-started-netcore-visualstudio.md), ouNode.js e VS [Code](../get-started-nodejs.md). Terá de ter acesso ao Proprietário ou Colaborador à subscrição Azure selecionada.
1. Certifique-se de que cada membro da equipa tem as [permissões adequadas para aceder ao controlador Azure Dev Spaces](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Por exemplo, pode configurar o grupo de **recursos** do Azure Dev Space para [conceder acesso](/azure/active-directory/role-based-access-control-configure) ao Colaborador para cada membro da equipa. Pode verificar o grupo de recursos de um espaço de dev executando este comando:`azds show-context`
1. Peça aos membros da equipa que **selecionem o espaço dev** para se desenvolverem nele.
   * **Linha de comando ou Código VS**: Para ver os espaços Azure Dev existentes, tem acesso a: `azds space list` . Para selecionar um espaço dev: `azds space select` .
   * **Visual Studio IDE**: Abra um projeto em Visual Studio, selecione **Azure Dev Spaces** a partir das definições de lançamento drop-down. No diálogo que se abre, selecione um cluster existente.

     ![Definições de lançamento do Estúdio Visual drop-down](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Próximos passos

Consulte [o desenvolvimento da equipa](../team-development-nodejs.md) para obter mais informações.
