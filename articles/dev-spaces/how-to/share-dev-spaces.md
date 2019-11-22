---
title: Como compartilhar Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido do Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: 2c85625a4f61b701bc1e8b0a4a06f71dc0989ce0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279980"
---
# <a name="share-azure-dev-spaces"></a>Partilhar os Espaços de Programador do Azure

Com Azure Dev Spaces, você pode compartilhar seu espaço de desenvolvimento com outras pessoas em sua equipe. Cada desenvolvedor pode trabalhar em seu próprio espaço sem medo de quebrar outras pessoas. Além disso, trabalhar juntos em um espaço pode permitir que você teste o código de ponta a ponta sem precisar criar simulações ou simular dependências. Consulte o guia de [desenvolvimento de equipe saiba](../team-development-nodejs.md) mais para obter mais informações.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Configurar um espaço de desenvolvimento para vários desenvolvedores

1. Crie um espaço de desenvolvimento no Azure. Escolha [.NET Core e vs Code](../get-started-netcore.md), [.NET Core e Visual Studio](../get-started-netcore-visualstudio.md), ou [node. js e vs Code](../get-started-nodejs.md). Você precisará ter acesso de proprietário ou colaborador à assinatura do Azure selecionada.
1. Configure o **grupo de recursos** do espaço de desenvolvimento do Azure para [conceder acesso de colaborador](/azure/active-directory/role-based-access-control-configure) para cada membro da equipe. Você pode verificar o grupo de recursos de um espaço de desenvolvimento executando este comando: `azds list-up`
1. Peça aos membros da equipe para **selecionar o espaço de desenvolvimento a** fim de desenvolvê-lo.
   * **Linha de comando ou vs Code**: para ver os Azure dev Spaces existentes aos quais você tem acesso: `azds space list`. Para selecionar um espaço de desenvolvimento: `azds space select`.
   * **IDE do Visual Studio**: Abra um projeto no Visual Studio, selecione **Azure dev Spaces** na lista suspensa configurações de inicialização. Na caixa de diálogo que é aberta, selecione um cluster existente.

     ![Menu suspenso configurações de inicialização do Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Passos seguintes

Consulte [saiba mais sobre o desenvolvimento em equipe](../team-development-nodejs.md) para obter mais informações.
