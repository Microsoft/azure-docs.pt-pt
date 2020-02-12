---
title: Considerações do navegador do sistema Xamarin Android (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para usar navegadores de sistema no Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132602"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Considerações do navegador do sistema Xamarin Android para usar MSAL.NET

Este artigo discute o que deve considerar quando utiliza o navegador do sistema no Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).

Começando com MSAL.NET pré-visualização 2.4.0, MSAL.NET suporta navegadores que não o Chrome. Já não é necessário que o Chrome seja instalado no dispositivo Android para autenticação.

Recomendamos que utilize navegadores que suportem separadores personalizados. Aqui estão alguns exemplos destes navegadores:

| Navegadores que têm suporte personalizado para abas | Nome do pacote |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Rio Kiwi | com.kiwibrowser.browser|
|Corajoso | com.brave.browser|

Além de identificar os navegadores que oferecem suporte personalizado para separadores, os nossos testes indicam que alguns navegadores que não suportam separadores personalizados também funcionam para autenticação. Estes navegadores incluem Opera, Opera Mini, InBrowser e Maxthon. 

## <a name="tested-devices-and-browsers"></a>Dispositivos e navegadores testados
A tabela seguinte lista os dispositivos e navegadores que foram testados para compatibilidade de autenticação.

| Dispositivo | Browser     |  Resultado  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | \* cromado | Passá|
| Huawei/One+ | \* de borda | Passá|
| Huawei/One+ | \* firefox | Passá|
| Huawei/One+ | \* corajosa | Passá|
| Um+ | Ecosia\* | Passá|
| Um+ | \* de Kiwi | Passá|
| Huawei/One+ | Opera | Passá|
| Huawei | OperaMini | Passá|
| Huawei/One+ | InBrowser | Passá|
| Um+ | Maxthon | Passá|
| Huawei/One+ | DuckDuckGo | Autenticação cancelada do utilizador|
| Huawei/One+ | UC Browser | Autenticação cancelada do utilizador|
| Um+ | Golfinho | Autenticação cancelada do utilizador|
| Um+ | Navegador CM | Autenticação cancelada do utilizador|
| Huawei/One+ | nenhum instalado | AndroidActivityNotFound exceção|

\* suporta separadores personalizados

## <a name="known-issues"></a>Problemas conhecidos

Se o utilizador não tiver navegador ativado no dispositivo, MSAL.NET irá lançar uma exceção `AndroidActivityNotFound`.  
  - **Mitigação**: Peça ao utilizador que ative um navegador no seu dispositivo. Recomende um navegador que suporte separadores personalizados.

Se a autenticação falhar (por exemplo, se a autenticação for lançada com duckDuckGo), MSAL.NET devolverá `AuthenticationCanceled MsalClientException`. 
  - **Problema de raiz**: Um navegador que suporta separadores personalizados não estava ativado no dispositivo. A autenticação foi lançada com um navegador que não conseguiu completar a autenticação. 
  - **Mitigação**: Peça ao utilizador que ative um navegador no seu dispositivo. Recomende um navegador que suporte separadores personalizados.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações e exemplos de código, consulte A Escolha entre um navegador web incorporado e um navegador de [sistema em Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) e [Incrustado versus sistema web UI](msal-net-web-browsers.md#embedded-vs-system-web-ui).  