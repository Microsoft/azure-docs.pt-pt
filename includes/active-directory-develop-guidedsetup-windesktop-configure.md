---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 220723988f349bf015d2de7633af78782bc03bac
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203214"
---
## <a name="register-your-application"></a>Registar a sua aplicação

Pode registrar seu aplicativo em qualquer uma das duas formas.

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Pode registar a sua aplicação rapidamente ao fazer o seguinte:
1. Aceda ao [Microsoft Application Registration Portal](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Selecione **adicionar uma aplicação**.

3. Na caixa **Nome da Aplicação**, introduza um nome para a sua aplicação.

4. Certifique-se de que o **configuração interativa** caixa de verificação está selecionada e, em seguida, selecione **criar**.

5. Siga as instruções para obter o ID da aplicação e colá-lo no seu código.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução, faça o seguinte:
1. Se ainda não registou a aplicação, vá para o [Portal de registo de aplicação do Microsoft](https://apps.dev.microsoft.com/portal/register-app).

2. Selecione **adicionar uma aplicação**.

3. Na caixa **Nome da Aplicação**, introduza um nome para a sua aplicação.

4. Certifique-se de que a caixa de verificação **Configuração Assistida** está desmarcada e, em seguida, selecione **Criar**.

5. Selecione **Adicionar Plataforma**, selecione **Aplicação Nativa** e, em seguida, selecione **Guardar**.

6. Na **ID da aplicação** caixa, copie o GUID.

7. Vá para o Visual Studio, abra a *App.xaml.cs* do ficheiro e, em seguida, substitua `your_client_id_here` com o ID da aplicação que acabou de registar e copiados.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
