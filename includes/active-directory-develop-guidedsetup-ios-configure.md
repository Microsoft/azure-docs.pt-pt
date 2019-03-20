---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 9782c6c2024c5cf490f207bb12a214c93a53b813
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203528"
---
## <a name="register-your-application"></a>Registar a sua aplicação

Pode registar a sua aplicação em qualquer uma das duas formas, conforme descrito nas próximas duas secções.

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Agora precisa de registar a aplicação no *Portal de registo de aplicação do Microsoft*:

1. Registar a sua aplicação através da [Portal de registo de aplicação do Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure).
2. Introduza um nome para o seu e-mail e a sua aplicação.
3. Certifique-se de que a opção para a configuração interativa está marcada.
4. Siga as instruções para obter o ID e colá-lo no seu código.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

1. Aceda a [Portal de registo do Microsoft Application](https://apps.dev.microsoft.com/portal/register-app).
2. Introduza um nome para a aplicação.
3. Certifique-se que a opção para a configuração interativa está desmarcada.
4. Selecione `Add Platform` e, em seguida, selecione `Native Application`.
5. Selecione `Save`.
6. Volte para o Xcode. Na `ViewController.swift`, substitua a linha que começa com "`let kClientID`' com o ID da aplicação que acabou de registar:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Controlar + clique <code>Info.plist</code> para abrir o menu contextual e, em seguida, clique em: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
Sob o <code>dict</code> nó raiz, adicione o seguinte:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Substitua <i> <code>[Your_Application_Id_Here]</code> </i> com o Id da aplicação que acabou de registar
</li>
</ol>
