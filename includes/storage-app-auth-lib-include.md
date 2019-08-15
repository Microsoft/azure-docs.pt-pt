---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011984"
---
A biblioteca de cliente de [autenticação de aplicativo Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) para .net (versão prévia) simplifica o processo de aquisição e renovação de um token do seu código. A biblioteca de cliente de autenticação de aplicativo gerencia a autenticação automaticamente. A biblioteca usa as credenciais do desenvolvedor para autenticar durante o desenvolvimento local. O uso de credenciais de desenvolvedor durante o desenvolvimento local é mais seguro porque você não precisa criar credenciais do Azure AD nem compartilhar credenciais entre desenvolvedores. Quando a solução for implantada posteriormente no Azure, a biblioteca alternará automaticamente para o uso de credenciais do aplicativo.

Para usar a biblioteca de autenticação de aplicativo em um aplicativo de armazenamento do Azure, instale o pacote de visualização mais recente do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), bem como a versão mais recente da [biblioteca de cliente comum do armazenamento do Azure para .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) e a [biblioteca de cliente de armazenamento de BLOBs do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Adicione as seguintes instruções **using** ao seu código:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
