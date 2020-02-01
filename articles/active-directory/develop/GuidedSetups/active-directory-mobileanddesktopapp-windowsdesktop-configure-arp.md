---
title: Azure AD v2 Windows Desktop Getting Started - Config Microsoft Docs
description: Como uma aplicação Windows Desktop .NET (XAML) pode obter um sinal de acesso e chamar uma API protegida por Azure Ative Directory v2 endpoint.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4c757b52e7378bffe4a4954022b131b772a337cf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897679"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Adicione as informações de registo da aplicação à sua app
Neste passo, é necessário adicionar o Id de Aplicação ao seu projeto.

1.  Abra `App.xaml.cs` e substitua a linha que contém o `ClientId` com:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>O que vem a seguir

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
