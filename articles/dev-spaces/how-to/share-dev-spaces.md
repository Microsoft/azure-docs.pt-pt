---
title: Como compartilhar espaços de desenvolvimento do Azure | Documentos da Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: article
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 57ca0f7b7704b179a55ac75fea99d35e1024ee8e
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706420"
---
# <a name="share-azure-dev-spaces"></a>Espaços de desenvolvimento do Azure de partilha

Com os espaços de desenvolvimento do Azure, pode partilhar o seu espaço de desenvolvimento com outras pessoas na sua equipa. Cada desenvolvedor pode trabalhar em seu próprio espaço sem medo de quebrar outras pessoas. Além disso, a trabalhar em conjunto num único espaço pode permitir que testar código ponto-a-ponto sem ter de criar elementos fictícios ou simular as dependências. Consulte a [Saiba mais sobre desenvolvimento em equipe](../team-development-nodejs.md) guia para obter mais informações.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Configurar um espaço de desenvolvimento para vários desenvolvedores

1. Crie um espaço de desenvolvimento no Azure. Escolher [código VS e .NET Core](../get-started-netcore.md), [Visual Studio e .NET Core](../get-started-netcore-visualstudio.md), ou [node. js e o código de VS](../get-started-nodejs.md). Terá de ter acesso de proprietário ou contribuinte à subscrição do Azure selecionada.
1. Configurar o Azure Dev espaço **grupo de recursos** ao [conceder acesso de contribuinte](/azure/active-directory/role-based-access-control-configure) para cada membro da equipe. Pode verificar o grupo de recursos de um espaço de desenvolvimento ao executar este comando: `azds list-up`
1. Faça os membros da Equipe **selecione o espaço de desenvolvimento** para poder desenvolver nela.
     * **Linha de comandos ou código VS**: para ver os espaços de desenvolvimento do Azure existente, terá acesso ao: `azds space list`. Para selecionar um espaço de desenvolvimento: `azds space select`.
     * **IDE do Visual Studio**: abra um projeto no Visual Studio, selecione **do Azure Dev espaços** o lançamento definições lista pendente. Na caixa de diálogo que se abre, selecione um cluster existente.

    ![Visual Studio lançamento definições pendente](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Passos Seguintes

Ver [Saiba mais sobre desenvolvimento em equipe](../team-development-nodejs.md) para obter mais informações.