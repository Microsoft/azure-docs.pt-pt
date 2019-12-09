---
title: Considerações do navegador do sistema do Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao usar navegadores do sistema no Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d3ea2554fac8654b052e3e38633af23e7c778b3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915470"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>Considerações do navegador do sistema do Xamarin Android com MSAL.NET

Este artigo discute considerações específicas ao usar o navegador do sistema no Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).

A partir do MSAL.NET 2.4.0-Preview, o MSAL.NET dá suporte a navegadores diferentes do Chrome e não requer mais que o Chrome seja instalado no dispositivo Android para autenticação.

Recomendamos que você use navegadores que dão suporte a guias personalizadas, como estas:

| Navegadores com suporte a guias personalizadas | Nome do Pacote |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Produtor kiwis | com.kiwibrowser.browser|
|Corajoso | com.brave.browser|

Além de navegadores com suporte a guias personalizados, com base em nossos testes, alguns navegadores que não dão suporte a guias personalizadas também funcionarão para autenticação: Opera, Opera Mini, inbrowser e Maxthon. Para obter mais informações, leia [tabela para resultados de teste](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Problemas conhecidos

- Se o usuário não tiver um navegador habilitado no dispositivo, o MSAL.NET gerará uma exceção de `AndroidActivityNotFound`. 
  - **Mitigação**: Informe ao usuário que ele deve habilitar um navegador (preferencialmente um com suporte a guias personalizados) em seu dispositivo.

- Se a autenticação falhar (por exemplo, a autenticação é iniciada com DuckDuckGo), o MSAL.NET retornará um `AuthenticationCanceled MsalClientException`. 
  - **Problema de raiz**: um navegador com suporte a guias personalizadas não foi habilitado no dispositivo. A autenticação foi iniciada com um navegador alternativo, que não conseguiu concluir a autenticação. 
  - **Mitigação**: Informe ao usuário que ele deve instalar um navegador (preferencialmente um com suporte de guia personalizado) em seu dispositivo.

## <a name="devices-and-browsers-tested"></a>Dispositivos e navegadores testados
A tabela a seguir lista os dispositivos e navegadores que foram testados.

| | &ast; de navegador     |  Resultado  | 
| ------------- |:-------------:|:-----:|
| Huawei/um + | &ast; Chrome | Passá|
| Huawei/um + | &ast; de borda | Passá|
| Huawei/um + | Firefox&ast; | Passá|
| Huawei/um + | &ast; corajoso | Passá|
| Um + | &ast; Ecosia | Passá|
| Um + | Kiwi&ast; | Passá|
| Huawei/um + | Opera | Passá|
| Huawei | OperaMini | Passá|
| Huawei/um + | Navegador | Passá|
| Um + | Maxthon | Passá|
| Huawei/um + | DuckDuckGo | Usuário cancelou autenticação|
| Huawei/um + | Navegador de UC | Usuário cancelou autenticação|
| Um + | Dolphin | Usuário cancelou autenticação|
| Um + | Navegador CM | Usuário cancelou autenticação|
| Huawei/um + | nenhum instalado | AndroidActivityNotFound ex|

&ast; dá suporte a guias personalizadas

## <a name="next-steps"></a>Passos seguintes
Para obter trechos de código e informações adicionais sobre como usar o navegador do sistema com o Xamarin Android, leia este [guia](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  