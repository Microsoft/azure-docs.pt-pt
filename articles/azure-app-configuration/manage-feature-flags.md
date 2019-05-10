---
title: Tutorial para utilizar a configuração de aplicações do Azure para gerir sinalizadores de recurso | Documentos da Microsoft
description: Neste tutorial, irá aprender a gerir sinalizadores de recurso separadamente a partir da sua aplicação com a configuração de aplicações do Azure
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
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412344"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Tutorial: Gerir sinalizadores de recurso na configuração de aplicações do Azure

Pode armazenar todos os sinalizadores de recurso na configuração de aplicações do Azure e administrá-los a partir de um único local. Ele tem um portal da interface do Usuário, chamado **Gestor de recursos**, que é projetado especificamente para os sinalizadores de recurso. Além disso, a configuração de aplicação suporta nativamente esquema de dados de sinalizador de funcionalidade de .NET Core.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Definir e gerir sinalizadores de recurso na configuração de aplicações.
> * Sinalizadores de recurso de acesso da sua aplicação.

## <a name="create-feature-flags"></a>Criar os sinalizadores de recurso

O **Gestor de recursos** no portal do Azure para a configuração de aplicações fornece uma interface do Usuário para a criação e gestão funcionalidade sinaliza que usar em seu aplicativo. Siga os passos seguintes para adicionar um novo sinalizador de funcionalidade.

1. Selecione **Gestor de recursos** > **+ criar** para adicionar um sinalizador de funcionalidade.

    ![Lista de sinalizador de funcionalidade](./media/azure-app-configuration-feature-flags.png)

2. Introduza um nome exclusivo da chave para o sinalizador de funcionalidade. Precisa deste nome para referenciar o sinalizador em seu código.

3. Opcionalmente, forneça o sinalizador de funcionalidade uma descrição de mais amigável a humanos.

4. Defina o estado inicial do sinalizador de funcionalidade. Normalmente é simplesmente *nos* ou *desativar*.

    ![Criar o sinalizador de funcionalidade](./media/azure-app-configuration-feature-flag-create.png)

5. Quando o estado é *nos*, opcionalmente, especifique qualquer condição adicional para qualificá-lo com **Adicionar filtro**. Introduza uma chave de filtro internas ou personalizadas e associar o parâmetro (s). Filtros incorporados incluem:

    | Chave | Parâmetros JSON |
    |---|---|
    | Microsoft.Percentage | {"Value": 0 e 100 por cento} |
    | Microsoft.TimeWindow | {"Start": Hora UTC, "Fim": Hora UTC} |

    ![Filtro do sinalizador de funcionalidade](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Estados do sinalizador de recursos de atualização

Siga os passos seguintes para alterar o valor de estado de um sinalizador de funcionalidade.

1. Selecione **Gestor de recursos**.

2. Clique em **...**   >  **Editar** à direita de um sinalizador de funcionalidade que pretende modificar.

3. Defina um novo estado para o sinalizador de funcionalidade.

## <a name="access-feature-flags"></a>Sinalizadores de recurso de acesso

Funcionalidade sinalizadores criadas pelos **Gestor de recursos** são armazenados e recuperados como valores de chave regulares. Elas são mantidas num prefixo de espaço de nomes especiais *. appconfig.featureflag*. Pode ver os valores-chave subjacentes usando os **Explorador de configuração**. Seu aplicativo pode obtê-las com os fornecedores de configuração de configuração de aplicações, SDKs, extensões da linha de comandos e as APIs REST.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu como gerir sinalizadores de recurso e os respetivos Estados com a configuração de aplicações. Consulte os seguintes recursos para obter mais informações sobre o suporte de gestão de recursos em configuração de aplicações e o ASP.NET Core.

* [Utilizar os sinalizadores de recurso numa aplicação ASP.NET Core](./use-feature-flags-dotnet-core.md)
