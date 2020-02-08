---
title: Considerações do navegador do sistema Xamarin Android (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao utilizar navegadores de sistema no Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.openlocfilehash: c144c6dd090669ca16c03050cbb8b59ff0cc224f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084584"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>Considerações de navegador do sistema Xamarin Android com MSAL.NET

Este artigo discute considerações específicas ao utilizar o navegador do sistema no Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).

A partir de MSAL.NET pré-visualização de 2.4.0, MSAL.NET suporta navegadores que não o Chrome e já não exige que o Chrome seja instalado no dispositivo Android para autenticação.

Recomendamos que utilize navegadores que suportem separadores personalizados, como estes:

| Navegadores com suporte personalizado para abas | Nome do pacote |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Rio Kiwi | com.kiwibrowser.browser|
|Corajoso | com.brave.browser|

Além de navegadores com suporte personalizado para abas, com base nos nossos testes, alguns navegadores que não suportam separadores personalizados também funcionarão para autenticação: Opera, Opera Mini, InBrowser e Maxthon. Para mais informações, leia [a tabela para obter resultados dos testes.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)

## <a name="known-issues"></a>Problemas conhecidos

- Se o utilizador não tiver navegador ativado no dispositivo, MSAL.NET lançará uma exceção `AndroidActivityNotFound`. 
  - **Mitigação**: Informe o utilizador de que deve ativar um navegador (de preferência um com suporte personalizado para abas) no seu dispositivo.

- Se a autenticação falhar (ex. lançamentos de autenticação com DuckDuckGo), MSAL.NET devolverá um `AuthenticationCanceled MsalClientException`. 
  - **Problema raiz**: Um navegador com suporte personalizado para abas não foi ativado no dispositivo. A autenticação foi lançada com um navegador alternativo, que não foi capaz de completar a autenticação. 
  - **Mitigação**: Informe o utilizador de que deve instalar um navegador (de preferência um com suporte personalizado para o separador) no seu dispositivo.

## <a name="devices-and-browsers-tested"></a>Dispositivos e navegadores testados
A tabela seguinte lista os dispositivos e navegadores que foram testados.

| | &ast; do navegador     |  Resultado  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | &ast; cromado | Passá|
| Huawei/One+ | &ast; de borda | Passá|
| Huawei/One+ | &ast; firefox | Passá|
| Huawei/One+ | &ast; corajosa | Passá|
| Um+ | Ecosia&ast; | Passá|
| Um+ | &ast; de Kiwi | Passá|
| Huawei/One+ | Opera | Passá|
| Huawei | OperaMini | Passá|
| Huawei/One+ | InBrowser | Passá|
| Um+ | Maxthon | Passá|
| Huawei/One+ | DuckDuckGo | Utilizador cancelado auth|
| Huawei/One+ | UC Browser | Utilizador cancelado auth|
| Um+ | Golfinho | Utilizador cancelado auth|
| Um+ | Navegador CM | Utilizador cancelado auth|
| Huawei/One+ | nenhum instalado | AndroidActivityNotFound ex|

&ast; suporta separadores personalizados

## <a name="next-steps"></a>Passos seguintes
Para obter snippets de código e informações adicionais sobre a utilização do navegador do sistema com o Xamarin Android, leia este [guia](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  