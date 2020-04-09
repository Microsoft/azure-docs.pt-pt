---
title: Azure AD v2 Windows Desktop Getting Started - Config
description: Como uma aplicação Windows Desktop .NET (XAML) pode obter um sinal de acesso e chamar uma API protegida por Azure Ative Directory v2 endpoint.
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885774"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Adicione as informações de registo da aplicação à sua app
Neste passo, é necessário adicionar o Id de Aplicação ao seu projeto.

1.  Abra `App.xaml.cs` e substitua `ClientId` a linha que contém a seguinte:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>O que vem a seguir

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
