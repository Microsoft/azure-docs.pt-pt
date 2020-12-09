---
title: 'Tutorial: Use a configuração da app Azure para gerir bandeiras de recurso'
titleSuffix: Azure App Configuration
description: Neste tutorial, aprende a gerir as bandeiras de funcionalidades separadamente da sua aplicação utilizando a Configuração da App Azure.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 0410a1cde12b9ef762d348a286d78b35f7b14bfd
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932307"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Tutorial: Gerir bandeiras de recurso na Configuração da App Azure

Pode armazenar todas as bandeiras de recurso na Configuração de Aplicações Azure e administrá-las a partir de um único local. A Configuração de Aplicativos tem um portal uI chamado **Feature Manager** que foi projetado especificamente para bandeiras de funcionalidades. A Configuração da Aplicação também suporta de forma nativa o esquema de dados de bandeira de recurso .NET Core.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Defina e gere as bandeiras de funcionalidades na Configuração da Aplicação.
> * As bandeiras de funcionalidades de acesso da sua aplicação.

## <a name="create-feature-flags"></a>Criar bandeiras de recurso

O Gestor de Funcionalidades no portal Azure para Configuração de Aplicações fornece um UI para criar e gerir as bandeiras de funcionalidades que utiliza nas suas aplicações.

Para adicionar uma nova bandeira de recurso:

1. Selecione **Feature Manager**  >  **+Adicione** para adicionar uma bandeira de recurso.

    ![Lista de bandeiras de recurso](./media/azure-app-configuration-feature-flags.png)

1. Introduza um nome chave único para a bandeira de recurso. Precisa deste nome para fazer referência à bandeira no seu código.

1. Se quiser, dê uma descrição à bandeira da característica.

1. Desa estale o estado inicial da bandeira de recurso. Este estado normalmente está *desligado* ou *ligado.* O estado *On* muda para *Condicional* se adicionar um filtro à bandeira de características.

    ![Criação de bandeira de recurso](./media/azure-app-configuration-feature-flag-create.png)

1. Quando o estado estiver *ligado*, selecione **+Adicione o filtro** para especificar quaisquer condições adicionais para qualificar o estado. Introduza uma tecla de filtro incorporada ou personalizada e, em seguida, selecione **+Adicione o parâmetro** para associar um ou mais parâmetros com o filtro. Os filtros incorporados incluem:

    | Chave | Parâmetros JSON |
    |---|---|
    | Microsoft.Percentagem | {"Valor": 0-100 por cento} |
    | Microsoft.TimeWindow | {"Start": tempo UTC, "Fim": tempo UTC} |
    | Microsoft.Targeting | { "Público": bolha JSON que define utilizadores, grupos e percentagens de lançamento. Veja um exemplo sob o `EnabledFor` elemento deste ficheiro de [definições](https://github.com/microsoft/FeatureManagement-Dotnet/blob/master/examples/FeatureFlagDemo/appsettings.json) }

    ![Filtro de bandeira de recurso](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Estados de bandeira de atualização

Para alterar o valor do estado de uma bandeira de recurso:

1. Selecione **Gestor de Recursos**.

1. À direita de uma bandeira de recurso que pretende modificar, selecione a elipse **(...)** e, em seguida, **selecione Editar**.

1. Estabeleça um novo estado para a bandeira de recurso.

## <a name="access-feature-flags"></a>Bandeiras de recurso de acesso

As bandeiras de funcionalidades criadas pelo Gestor de Funcionalidades são armazenadas e recuperadas como valores-chave regulares. São mantidos sob um prefixo especial do espaço de `.appconfig.featureflag` nome. Para visualizar os valores-chave subjacentes, utilize o Explorador de Configuração. A sua aplicação pode recuperar estes valores utilizando os fornecedores de configuração de configuração de aplicações, SDKs, extensões de linha de comando e APIs REST.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a gerir as bandeiras de recurso e os seus estados utilizando a Configuração de Aplicações. Para obter mais informações sobre o suporte à gestão de funcionalidades na Configuração de Aplicações e ASP.NET Core, consulte o seguinte artigo:

* [Use bandeiras de recurso numa aplicação core ASP.NET](./use-feature-flags-dotnet-core.md)
