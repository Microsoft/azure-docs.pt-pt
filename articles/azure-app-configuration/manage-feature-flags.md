---
title: 'Tutorial: Utilize a configuração da app Azure para gerir as bandeiras de recurso'
titleSuffix: Azure App Configuration
description: Neste tutorial, aprende a gerir as bandeiras de recurso separadamente da sua aplicação utilizando a Configuração de Aplicações Azure.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: ccab8014000f9f684249bf2c1f800f74c92e7369
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76899362"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Tutorial: Gerir bandeiras de recurso na configuração da app Azure

Pode armazenar todas as bandeiras de recurso na Configuração de Aplicações Azure e administrá-las a partir de um único local. A Configuração de Aplicações tem um portal uI chamado Gestor de **Recursos** que foi projetado especificamente para bandeiras de recurso. A Configuração da Aplicação também suporta de forma nativa o esquema de dados de bandeira de características .NET Core.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Defina e gerencie as bandeiras de recurso na Configuração da App.
> * O acesso apresenta bandeiras da sua aplicação.

## <a name="create-feature-flags"></a>Criar bandeiras de recurso

O Gestor de Funcionalidades no portal Azure para configuração de aplicações fornece um UI para criar e gerir as bandeiras de funcionalidade que utiliza nas suas aplicações.

Para adicionar uma nova bandeira de recurso:

1. Selecione **Gestor** > de Funcionalidades **+Adicione** para adicionar uma bandeira de recurso.

    ![Lista de bandeiras de recurso](./media/azure-app-configuration-feature-flags.png)

1. Introduza um nome chave único para a bandeira de recurso. Precisa deste nome para fazer referência à bandeira no seu código.

1. Se quiser, dê uma descrição à bandeira da característica.

1. Desloque o estado inicial da bandeira de recurso. Este estado é geralmente *desligado* ou *ligado*. O Estado *On* muda para *Condicional* se adicionar um filtro à bandeira da funcionalidade.

    ![Criação de bandeira de recurso](./media/azure-app-configuration-feature-flag-create.png)

1. Quando o estado estiver *ligado,* **selecione +Adicione o filtro** para especificar quaisquer condições adicionais para qualificar o estado. Introduza uma chave de filtro incorporada ou personalizada e, em seguida, selecione **+Adicione o parâmetro** para associar um ou mais parâmetros com o filtro. Os filtros incorporados incluem:

    | Chave | Parâmetros JSON |
    |---|---|
    | Microsoft.Percentagem | {"Valor": 0-100 por cento} |
    | Microsoft.TimeWindow | {"Iniciar": tempo UTC, "Fim": tempo UTC} |

    ![Filtro de bandeira de recurso](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Atualizar estados de bandeira de recurso

Para alterar o valor estatal de uma característica:

1. Selecione **Gestor de Funcionalidades**.

1. À direita de uma bandeira de recurso que pretende modificar, selecione a elipse (**...**) e, em seguida, selecione **Editar**.

1. Estabeleça um novo estado para a bandeira de recurso.

## <a name="access-feature-flags"></a>Bandeiras de recurso de acesso

As bandeiras de recurso criadas pelo Gestor de Funcionalidades são armazenadas e recuperadas como valores-chave regulares. São mantidos sob um prefixo `.appconfig.featureflag`especial do espaço de nome. Para ver os valores-chave subjacentes, utilize o Explorador de Configuração. A sua aplicação pode recuperar estes valores utilizando os fornecedores de configuração de aplicações, SDKs, extensões de linha de comando e APIs REST.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a gerir bandeiras de recursos e seus estados utilizando a Configuração de Aplicações. Para obter mais informações sobre o suporte à gestão de funcionalidades na Configuração de Apps e ASP.NET Core, consulte o seguinte artigo:

* [Use bandeiras de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md)
