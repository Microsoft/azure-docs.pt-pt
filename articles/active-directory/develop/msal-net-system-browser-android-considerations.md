---
title: Considerações do navegador do sistema Xamarin Android (MSAL.NET) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para utilizar navegadores de sistema no Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8f755b42249a88a2d37117003d561f79aea6b170
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165996"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Considerações do navegador do sistema Xamarin Android para usar MSAL.NET

Este artigo discute o que deve considerar quando utiliza o navegador do sistema no Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).

A partir de MSAL.NET 2.4.0 Preview, MSAL.NET suporta navegadores que não o Chrome. Já não é necessário que o Chrome seja instalado no dispositivo Android para autenticação.

Recomendamos que utilize navegadores que suportem separadores personalizados. Aqui estão alguns exemplos destes navegadores:

| Navegadores que têm suporte a separadores personalizados | Nome do pacote |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Rio Kiwi | com.kiwibrowser.browser|
|Corajoso | com.brave.browser|

Além de identificar navegadores que oferecem suporte a separadores personalizados, os nossos testes indicam que alguns navegadores que não suportam separadores personalizados também funcionam para autenticação. Estes navegadores incluem Opera, Opera Mini, InBrowser e Maxthon. 

## <a name="tested-devices-and-browsers"></a>Dispositivos e navegadores testados
A tabela que se segue lista os dispositivos e navegadores que foram testados para compatibilidade de autenticação.

| Dispositivo | Browser     |  Resultado  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | Passe|
| Huawei/One+ | Microsoft Edge\* | Passe|
| Huawei/One+ | Firefox\* | Passe|
| Huawei/One+ | Corajoso\* | Passe|
| Um+ | Ecosia\* | Passe|
| Um+ | Rio Kiwi\* | Passe|
| Huawei/One+ | Ópera | Passe|
| Huawei | OperaMini | Passe|
| Huawei/One+ | InBrowser | Passe|
| Um+ | Maxthon | Passe|
| Huawei/One+ | Pato-do-pato | Autenticação cancelada do utilizador|
| Huawei/One+ | Navegador UC | Autenticação cancelada do utilizador|
| Um+ | Golfinho | Autenticação cancelada do utilizador|
| Um+ | Navegador CM | Autenticação cancelada do utilizador|
| Huawei/One+ | Nenhum instalado | Exceção AndroidActivityNotFound|

\* Suporta separadores personalizados

## <a name="known-issues"></a>Problemas conhecidos

Se o utilizador não tiver nenhum navegador ativado no dispositivo, MSAL.NET lançará uma `AndroidActivityNotFound` exceção.  
  - **Mitigação:** Peça ao utilizador para ativar um browser no seu dispositivo. Recomendar um navegador que suporte separadores personalizados.

Se a autenticação falhar (por exemplo, se a autenticação for lançada com DuckDuckGo), MSAL.NET regressará `AuthenticationCanceled MsalClientException` . 
  - **Problema de raiz**: Um navegador que suporta separadores personalizados não foi ativado no dispositivo. A autenticação foi lançada com um browser que não conseguiu completar a autenticação. 
  - **Mitigação:** Peça ao utilizador para ativar um browser no seu dispositivo. Recomendar um navegador que suporte separadores personalizados.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações e exemplos de código, consulte [Escolher entre um navegador web incorporado e um navegador de sistema no Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) e Incorporado versus sistema web [UI](msal-net-web-browsers.md#embedded-vs-system-web-ui).  