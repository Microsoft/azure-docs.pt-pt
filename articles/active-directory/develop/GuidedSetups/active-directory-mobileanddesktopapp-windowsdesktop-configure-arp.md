---
title: Azure AD v2 Windows Desktop Começando - Config
description: Como uma aplicação Windows Desktop .NET (XAML) pode obter um token de acesso e chamar uma API protegida pelo Azure Ative Directory v2 ponto final.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885774"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Adicione as informações de registo da aplicação à sua app
Neste passo, precisa adicionar o ID de aplicação ao seu projeto.

1.  Abra `App.xaml.cs` e substitua a linha que `ClientId` contém:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>O que é a seguir

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
