---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007902"
---
| Plataforma          | Projeto em<br/>GitHub                                                                          | Pacote                                                                               | Ficando<br/>começou                    | Inscreva-se nos utilizadores                                         | Aceda a APIs web                                                 | Geralmente disponível (GA) *ou*<br/>Pré-visualização pública<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Início rápido](../articles/active-directory/develop/quickstart-v2-android.md) | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| iOS (Swift/Obj-C) | [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Tutorial](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Os termos de utilização suplementares para as pré-visualizações do Microsoft Azure][preview-tos] aplicam-se às bibliotecas em *pré-visualização pública*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/