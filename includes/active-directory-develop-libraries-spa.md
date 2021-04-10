---
ms.openlocfilehash: a393cf978318c39081805cae7e38c3386ff156f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010711"
---
| Linguagem / enquadramento | Projeto em<br/>GitHub                                                                                                    | Pacote                                                                      | Ficando<br/>começou                             | Inscreva-se nos utilizadores                                         | Aceda a APIs web                                                 | Geralmente disponível (GA) *ou*<br/>Pré-visualização pública<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | Pré-visualização pública                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Tutorial](../articles/active-directory/develop/tutorial-v2-angular.md)| ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | Pré-visualização pública                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Tutorial](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
|JavaScript|[MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Tutorial](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| React                | [MSAL Reagir](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![A biblioteca pode solicitar fichas de identificação para o ser-in do utilizador.][y] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | Pré-visualização pública                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Os termos de utilização suplementares para as pré-visualizações do Microsoft Azure][preview-tos] aplicam-se às bibliotecas em *pré-visualização pública*.

<sup>2</sup> [Fluxo de código Auth][auth-code-flow] apenas com PCKE (Recomendado). 

<sup>3</sup> [Fluxo de subvenção implícito][implicit-flow] apenas.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md