---
title: Considerações de Xamarin Android (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba mais sobre as considerações específicas ao utilizar o Xamarin Android com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544433"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considerações sobre o Xamarin. Android-específicas com MSAL.NET
Este artigo aborda considerações específicas ao utilizar o browser de sistema no Xamarin Android com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).

Começando com MSAL.NET 2.4.0-preview, MSAL.NET oferece suporte a navegadores que não seja o Chrome e já não necessita Chrome ser instalado no dispositivo Android para a autenticação.

Recomendamos que utilize browsers que suportam guias personalizadas, como os seguintes:

| Navegadores com suporte de guias personalizadas | Nome do pacote |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Corajoso | com.brave.browser|

Além de navegadores com suporte de guias personalizadas, com base no nosso teste, alguns navegadores que não suportam guias personalizadas também funciona para a autenticação: Opera, Opera Mini, InBrowser e Maxthon. Para obter mais informações, leia [tabela de resultados do teste](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Problemas conhecidos

- Se o utilizador não tiver nenhum navegador ativada no dispositivo, MSAL.NET lançará um `AndroidActivityNotFound` exceção. 
  - **Atenuação**: Informe o utilizador que deve permitem um navegador (preferencialmente, um com o suporte de guias personalizadas) no respetivo dispositivo.

- Se a autenticação falhar (por ex. autenticação é iniciado com DuckDuckGo), MSAL.NET irá devolver um `AuthenticationCanceled MsalClientException`. 
  - **Problema de raiz**: Um navegador com suporte de guias personalizadas não foi ativado no dispositivo. Autenticação iniciado com um browser alternativo, o que não foi possível concluir a autenticação. 
  - **Atenuação**: Informe o utilizador que deve instalar um navegador (preferencialmente, um com o suporte de guia personalizada) no respetivo dispositivo.

## <a name="devices-and-browsers-tested"></a>Dispositivos e browsers testados
A tabela seguinte lista os dispositivos e browsers que foram testados.

| | Browser&ast;     |  Resultado  | 
| ------------- |:-------------:|:-----:|
| Huawei / um + | Chrome&ast; | Passagem|
| Huawei / um + | Edge&ast; | Passagem|
| Huawei / um + | Firefox&ast; | Passagem|
| Huawei / um + | Brave&ast; | Passagem|
| Um + | Ecosia&ast; | Passagem|
| Um + | Kiwi&ast; | Passagem|
| Huawei / um + | Opera | Passagem|
| Huawei | OperaMini | Passagem|
| Huawei / um + | InBrowser | Passagem|
| Um + | Maxthon | Passagem|
| Huawei / um + | DuckDuckGo | Autenticação do utilizador foi cancelada|
| Huawei / um + | Browser UC | Autenticação do utilizador foi cancelada|
| Um + | Dolphin | Autenticação do utilizador foi cancelada|
| Um + | Browser de CM | Autenticação do utilizador foi cancelada|
| Huawei / um + | nenhum instalado | AndroidActivityNotFound ex|

&ast; Suporta guias personalizadas

## <a name="next-steps"></a>Passos Seguintes
Para o código de trechos de código e informações adicionais sobre como utilizar o browser do sistema com Xamarin. Android, leia este [guia](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  