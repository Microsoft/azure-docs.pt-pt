---
title: 'Quickstart: Criar um modelo de gestor de recursos Azure Synapse Azure'
description: Aprenda a criar um espaço de trabalho synapse utilizando o modelo de Gestor de Recursos Azure (modelo ARM).
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 2b05220081dde4db6f07f21fb9978d9e466235ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92740363"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Quickstart: Criar um espaço de trabalho Azure Synapse usando um modelo ARM

Este modelo de Gestor de Recursos Azure (modelo ARM) criará um espaço de trabalho Azure Synapse com armazenamento subjacente do Lago de Dados. O espaço de trabalho Azure Synapse é um limite de colaboração securável para processos de análise em Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Desdobre-se para Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

Pode rever o modelo selecionando o **link Visualize.** Em seguida, selecione **o modelo de edição**.

[![Visualizar](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

O modelo define dois recursos:

- Conta de armazenamento
- Área de trabalho

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a seguinte imagem para iniciar seduca e abrir o modelo. Este modelo cria um espaço de trabalho synapse.

   [![Implantar para Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Insira ou atualize os seguintes valores:

   - **Assinatura**: Selecione uma subscrição Azure.
   - **Grupo de recursos**: Selecione **Criar novo** e insira um nome único para o grupo de recursos e selecione **OK**. Um novo grupo de recursos facilitará a limpeza dos recursos.
   - **Região**: Selecione uma região.  Por exemplo, **E.U.A. Central**.
   - **Nome**: Insira um nome para o seu espaço de trabalho.
   - **Início de entrada do administrador SQL**: Introduza o nome de utilizador do administrador para o SqL Server.
   - **Palavra-passe do administrador SQL**: Introduza a palavra-passe do administrador para o Servidor SQL.
   - **Valores da etiqueta**: Aceite o padrão.
   - **Reveja e Crie**: Selecione.
   - **Criar**: Selecione.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Azure Synapse Analytics e Azure Resource Manager, continue para os artigos abaixo.

- Leia uma [visão geral do Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
