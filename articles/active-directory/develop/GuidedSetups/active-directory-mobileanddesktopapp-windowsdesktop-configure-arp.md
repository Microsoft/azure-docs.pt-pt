---
title: Introdução da área de trabalho do Windows do Azure AD v2-configuração | Microsoft Docs
description: Como um aplicativo .NET da área de trabalho do Windows (XAML) pode obter um token de acesso e chamar uma API protegida pelo ponto de extremidade Azure Active Directory v2.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a058024f8d6bdf7399e222c134f9f24c4ddffee8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835557"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Adicionar as informações de registro do aplicativo ao seu aplicativo
Nesta etapa, você precisa adicionar a ID do aplicativo ao seu projeto.

1.  Abra `App.xaml.cs` e substitua a linha que contém `ClientId` o por:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>O que vem a seguir

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
