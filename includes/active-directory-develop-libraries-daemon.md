---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008008"
---
| Linguagem / enquadramento | Projeto em<br/>GitHub                                                                 | Pacote                                                                                | Ficando<br/>começou                           | Inscreva-se nos utilizadores                                            | Aceda a APIs web                                                 | Geralmente disponível (GA) *ou*<br/>Pré-visualização pública<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Início rápido](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![A biblioteca não pode solicitar fichas de identificação para o ser-in do utilizador.][n] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![A biblioteca não pode solicitar fichas de identificação para o ser-in do utilizador.][n] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
| Nó               | [Nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-nó](https://www.npmjs.com/package/@azure/msal-node)  | [Início rápido](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![A biblioteca não pode solicitar fichas de identificação para o ser-in do utilizador.][n] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA  |
| Python               | [Pitão MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Início rápido](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![A biblioteca não pode solicitar fichas de identificação para o ser-in do utilizador.][n] | ![A biblioteca pode solicitar o acesso a fichas para APIs web protegidas.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Os termos de utilização suplementares para as pré-visualizações do Microsoft Azure][preview-tos] aplicam-se às bibliotecas em *pré-visualização pública*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/