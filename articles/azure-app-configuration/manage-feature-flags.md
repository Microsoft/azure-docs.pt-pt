---
title: 'Tutorial: usar a configuração de Azure App para gerenciar sinalizadores de recursos'
titleSuffix: Azure App Configuration
description: Neste tutorial, você aprenderá a gerenciar sinalizadores de recursos separadamente do seu aplicativo usando Azure App configuração.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 620cd6e1712a89e13bf876310c0d3248c4a4d1e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75414013"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Tutorial: gerenciar sinalizadores de recurso na configuração do Azure App

Você pode armazenar todos os sinalizadores de recurso na configuração do Azure App e administrá-los de um único local. A configuração do aplicativo tem uma interface do usuário do portal denominada **Gerenciador de recursos** , projetada especificamente para sinalizadores de recursos. A configuração do aplicativo também dá suporte nativo ao esquema de dados do sinalizador de recursos do .NET Core.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Definir e gerenciar sinalizadores de recurso na configuração do aplicativo.
> * Acessar os sinalizadores de recurso do seu aplicativo.

## <a name="create-feature-flags"></a>Criar sinalizadores de recurso

O Gerenciador de recursos no portal do Azure para configuração de aplicativo fornece uma interface do usuário para criar e gerenciar os sinalizadores de recurso que você usa em seus aplicativos.

Para adicionar um novo sinalizador de recurso:

1. Selecione **Gerenciador de recursos** >  **+ Adicionar** para adicionar um sinalizador de recurso.

    ![Lista de sinalizadores de recursos](./media/azure-app-configuration-feature-flags.png)

1. Insira um nome de chave exclusivo para o sinalizador de recurso. Você precisa desse nome para fazer referência ao sinalizador em seu código.

1. Se desejar, dê ao recurso um sinalizador de descrição.

1. Defina o estado inicial do sinalizador de recurso. Esse estado é geralmente *desativado* ou *ativado*. O estado *on* mudará para *condicional* se você adicionar um filtro ao sinalizador de recurso.

    ![Criação de sinalizador de recurso](./media/azure-app-configuration-feature-flag-create.png)

1. Quando o estado estiver *ativado*, selecione **+ Adicionar filtro** para especificar quaisquer condições adicionais para qualificar o estado. Insira uma chave de filtro interna ou personalizada e, em seguida, selecione **+ Adicionar parâmetro** para associar um ou mais parâmetros ao filtro. Os filtros internos incluem:

    | Chave | Parâmetros JSON |
    |---|---|
    | Microsoft.Percentage | {"Valor": 0-100 por cento} |
    | Microsoft.TimeWindow | {"Início": hora UTC, "fim": hora UTC} |

    ![Filtro de sinalizador de recurso](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Atualizar Estados do sinalizador de recurso

Para alterar o valor de estado de um sinalizador de recurso:

1. Selecione **Gerenciador de recursos**.

1. À direita de um sinalizador de recurso que você deseja modificar, selecione as reticências ( **...** ) e, em seguida, selecione **Editar**.

1. Defina um novo estado para o sinalizador de recurso.

## <a name="access-feature-flags"></a>Sinalizadores de recurso de acesso

Os sinalizadores de recurso criados pelo Gerenciador de recursos são armazenados e recuperados como valores de chave regulares. Eles são mantidos sob um prefixo de namespace especial `.appconfig.featureflag`. Para exibir os valores de chave subjacentes, use o Gerenciador de configurações. Seu aplicativo pode recuperar esses valores usando os provedores de configuração de configuração de aplicativo, SDKs, extensões de linha de comando e APIs REST.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a gerenciar os sinalizadores de recurso e seus Estados usando a configuração do aplicativo. Para obter mais informações sobre o suporte ao gerenciamento de recursos na configuração do aplicativo e ASP.NET Core, consulte o seguinte artigo:

* [Usar sinalizadores de recurso em um aplicativo ASP.NET Core](./use-feature-flags-dotnet-core.md)
