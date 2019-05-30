---
title: Tutorial para utilizar a configuração de aplicações do Azure para gerir sinalizadores de recurso | Documentos da Microsoft
description: Neste tutorial, saiba como gerir sinalizadores de recurso separadamente a partir da sua aplicação ao utilizar a configuração de aplicações do Azure.
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
ms.openlocfilehash: b7fbf9add67a45c0db89fc11cee5c10bc537ab63
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393560"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Tutorial: Gerir sinalizadores de recurso na configuração de aplicações do Azure

Pode armazenar todos os sinalizadores de recurso na configuração de aplicações do Azure e administrá-los a partir de um único local. Configuração de aplicações tem um portal de interface do Usuário com o nome **Gestor de recursos** qual foi concebida especificamente para os sinalizadores de recurso. Configuração de aplicações suporta também nativamente o esquema de dados de sinalizador de funcionalidade do .NET Core.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Definir e gerir sinalizadores de recurso na configuração de aplicações.
> * Sinalizadores de recurso de acesso da sua aplicação.

## <a name="create-feature-flags"></a>Criar os sinalizadores de recurso

O Gestor de recursos no portal do Azure para a configuração de aplicação fornece uma interface do Usuário para criar e gerir os sinalizadores de recurso que usar em seus aplicativos.

Para adicionar um novo sinalizador de funcionalidade:

1. Selecione **Gestor de recursos** >  **+ adicionar** para adicionar um sinalizador de funcionalidade.

    ![Lista de sinalizador de funcionalidade](./media/azure-app-configuration-feature-flags.png)

1. Introduza um nome exclusivo da chave para o sinalizador de funcionalidade. Precisa deste nome para referenciar o sinalizador em seu código.

1. Se pretender, forneça uma descrição o sinalizador de funcionalidade.

1. Defina o estado inicial do sinalizador de funcionalidade. Este estado é normalmente *Off* ou *no*. O *nos* estado é alterado para *condicional* se adicionar um filtro para o sinalizador de funcionalidade.

    ![Criação do sinalizador de funcionalidade](./media/azure-app-configuration-feature-flag-create.png)

1. Quando o estado é *nos*, selecione **+ Adicionar filtro** para especificar as condições adicionais para qualificar o estado. Introduza uma chave de filtro internas ou personalizadas e, em seguida, selecione **+ Adicionar parâmetro** para associar um ou mais parâmetros com o filtro. Filtros incorporados incluem:

    | Chave | Parâmetros JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0 e 100 por cento} |
    | Microsoft.TimeWindow | {"Start": Hora UTC, "Fim": Hora UTC} |

    ![Filtro do sinalizador de funcionalidade](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Estados do sinalizador de recursos de atualização

Para alterar o valor de estado de um sinalizador de funcionalidade:

1. Selecione **Gestor de recursos**.

1. À direita de um sinalizador de funcionalidade que pretende modificar, selecione as reticências ( **...** ) e, em seguida, selecione **editar**.

1. Defina um novo estado para o sinalizador de funcionalidade.

## <a name="access-feature-flags"></a>Sinalizadores de recurso de acesso

Sinalizadores de recurso criados pelo Gestor de recursos são armazenados e recuperados como valores de chave regulares. Eles são mantidos num prefixo de espaço de nomes especiais `.appconfig.featureflag`. Para ver os valores de chave subjacentes, utilize o Explorador de configuração. Seu aplicativo pode obter estes valores, utilizando os fornecedores de configuração de configuração de aplicações, SDKs, extensões da linha de comandos e as APIs REST.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a gerir sinalizadores de recurso e os respetivos Estados com a configuração de aplicações. Para obter mais informações sobre o suporte de gestão de recursos na configuração de aplicações e o ASP.NET Core, consulte o artigo seguinte:

* [Utilizar os sinalizadores de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md)
