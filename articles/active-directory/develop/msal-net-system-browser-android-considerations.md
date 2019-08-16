---
title: Considerações sobre o Xamarin Android (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba mais sobre considerações específicas ao usar o Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b0c810097913e896027245b15600ed75aabcd25
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532569"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considerações específicas do Xamarin Android com MSAL.NET
Este artigo discute considerações específicas ao usar o navegador do sistema no Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).

A partir do MSAL.NET 2.4.0-Preview, o MSAL.NET dá suporte a navegadores diferentes do Chrome e não requer mais que o Chrome seja instalado no dispositivo Android para autenticação.

Recomendamos que você use navegadores que dão suporte a guias personalizadas, como estas:

| Navegadores com suporte a guias personalizadas | Nome do pacote |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Produtor kiwis | com.kiwibrowser.browser|
|Corajoso | com.brave.browser|

Além dos navegadores com suporte a guias personalizados, com base em nossos testes, alguns navegadores que não dão suporte a guias personalizadas também funcionarão para autenticação: Opera, Opera Mini, inbrowser e Maxthon. Para obter mais informações, leia [tabela para resultados de teste](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Problemas conhecidos

- Se o usuário não tiver um navegador habilitado no dispositivo, o MSAL.NET gerará `AndroidActivityNotFound` uma exceção. 
  - **Mitigação**: Informe ao usuário que ele deve habilitar um navegador (preferencialmente um com suporte a guias personalizados) em seu dispositivo.

- Se a autenticação falhar (por exemplo, a autenticação é iniciada com DuckDuckGo), o `AuthenticationCanceled MsalClientException`MSAL.net retornará um. 
  - **Problema raiz**: Um navegador com suporte a guias personalizado não foi habilitado no dispositivo. A autenticação foi iniciada com um navegador alternativo, que não conseguiu concluir a autenticação. 
  - **Mitigação**: Informe ao usuário que ele deve instalar um navegador (preferencialmente um com suporte à guia personalizada) em seu dispositivo.

## <a name="devices-and-browsers-tested"></a>Dispositivos e navegadores testados
A tabela a seguir lista os dispositivos e navegadores que foram testados.

| | Navegador&ast;     |  Resultado  | 
| ------------- |:-------------:|:-----:|
| Huawei/um + | Monocromático&ast; | Passagem|
| Huawei/um + | Perímetro&ast; | Passagem|
| Huawei/um + | Firefox&ast; | Passagem|
| Huawei/um + | Corajoso&ast; | Passagem|
| Um + | Ecosia&ast; | Passagem|
| Um + | Kiwi&ast; | Passagem|
| Huawei/um + | Opera | Passagem|
| Huawei | OperaMini | Passagem|
| Huawei/um + | Navegador | Passagem|
| Um + | Maxthon | Passagem|
| Huawei/um + | DuckDuckGo | Usuário cancelou autenticação|
| Huawei/um + | Navegador de UC | Usuário cancelou autenticação|
| Um + | Dolphin | Usuário cancelou autenticação|
| Um + | Navegador CM | Usuário cancelou autenticação|
| Huawei/um + | nenhum instalado | AndroidActivityNotFound ex|

&ast;Dá suporte a guias personalizadas

## <a name="next-steps"></a>Passos seguintes
Para obter trechos de código e informações adicionais sobre como usar o navegador do sistema com o Xamarin Android, leia este [guia](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  