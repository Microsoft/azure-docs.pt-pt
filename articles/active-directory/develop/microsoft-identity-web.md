---
title: Visão geral da biblioteca de autenticação da Microsoft Identity Web
titleSuffix: Microsoft identity platform
description: Conheça a Microsoft Identity Web, uma biblioteca de autenticação e autorização para aplicações core ASP.NET que se integram com o Azure Ative Directory, Azure AD B2C e Microsoft Graph e outras APIs web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4d915cee962546e355ee9d53c683581730bbbf36
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778859"
---
# <a name="microsoft-identity-web-authentication-library"></a>Biblioteca de autenticação Microsoft Identity Web

O Microsoft Identity Web é um conjunto de bibliotecas core ASP.NET que simplifica a adição de suporte de autenticação e autorização a aplicações web e APIs web integrados na plataforma de identidade da Microsoft. Fornece uma camada de conveniência API de superfície única que se liga ASP.NET Core, o seu middleware de autenticação e a Biblioteca de Autenticação da [Microsoft (MSAL) para .NET](https://github.com/azuread/microsoft-authentication-library-for-dotnet).

## <a name="supported-application-scenarios"></a>Cenários de candidatura apoiados

Se estiver a construir ASP.NET aplicações web Core ou APIs web e quiser utilizar o Azure Ative Directory (Azure AD) ou a Azure AD B2C para gestão de identidade e acesso (IAM), recomendamos a utilização da Microsoft Identity Web para todos estes cenários:

- [Aplicação web que inicia a sessão de utilizadores](scenario-web-app-sign-user-overview.md)
- [Web app que assina nos utilizadores e chama uma API web em seu nome](scenario-web-app-call-api-overview.md)
- [API web protegida que só os utilizadores autenticados podem aceder](scenario-protected-web-api-overview.md)
- [API web protegida que chama outra API web (a jusante) em nome do utilizador inscrito](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>Pegue a biblioteca

Pode obter a Microsoft Identity Web a partir de modelos de [projetos NuGet](#nuget), [.NET Core](#project-templates)e [GitHub](#github).

#### <a name="nuget"></a>NuGet

O Microsoft Identity Web está disponível no NuGet como um conjunto de pacotes que fornecem funcionalidade modular com base nas necessidades da sua aplicação. Utilize o comando do .NET CLI ou o `dotnet add` Gestor de **Pacotes NuGet** do Visual Studio para instalar os pacotes apropriados para o seu projeto:

- [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) - O pacote principal. Requerido por todas as aplicações que utilizam a Microsoft Identity Web.
- [Microsoft.Identity.web.uI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) - Opcional. Adiciona UI para o utilizador iniciar sôm e iniciar sedus e um controlador associado para aplicações web.
- [Microsoft.Identity.web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) - Opcional. Proporciona interação simplificada com a API do Gráfico microsoft.

#### <a name="project-templates"></a>Modelos de projeto

Os modelos de projeto Microsoft Identity Web estão incluídos em .NET 5.0 e estão disponíveis para download para projetos ASP.NET Core 3.1.

Se estiver a utilizar ASP.NET Core 3.1, instale os modelos com o .NET CLI:

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

O diagrama a seguir mostra uma visão de alto nível dos tipos de aplicações suportados e dos seus argumentos relevantes:

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Diagrama dos modelos de projeto CLI net de pontos disponíveis para Microsoft Identity Web&quot;:::
<br /><sup><b>*</b></sup>`MultiOrg`não é suportado com `webapi2` , mas pode ser ativado emappsettings.js*através da* definição de inquilino para `common` ou`organizations`
<br /><sup><b>**</b></sup>`--calls-graph`não é suportado para Azure AD B2C

Este comando .NET CLI, retirado do nosso [tutorial Blazor Server,](tutorial-blazor-server.md)gera um novo projeto Do Blazor Server que inclui os pacotes certos e o código de arranque (valores de espaço reservado mostrados):

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id &quot;00000000-0000-0000-0000-000000000000&quot; --tenant-id &quot;11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft Identity Web é um projeto de código aberto hospedado no GitHub: [AzureAD/microsoft-identity-web](https://github.com/AzureAD/microsoft-identity-web?azure-portal=true)

O [wiki do repositório](https://github.com/AzureAD/microsoft-identity-web/wiki) contém documentação adicional, e se precisar de ajuda ou descobrir um bug, pode [arquivar um problema](https://github.com/AzureAD/microsoft-identity-web/issues).

## <a name="features"></a>Funcionalidades

O Microsoft Identity Web inclui várias funcionalidades não fornecidas se utilizar os modelos de projeto 3.1 predefinidos ASP.NET.

| Funcionalidade                                                                                  | ASP.NET Core 3.1                                                     | Microsoft Identity Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [Inscreva-se nos utilizadores](scenario-web-app-sign-user-app-configuration.md) em aplicações web             | <li>Contas escolares ou profissionais<li>Identidades sociais (com Azure AD B2C) | <li>Contas escolares ou profissionais<li>Contas pessoais da Microsoft<li>Identidades sociais (com Azure AD B2C)     |
| [Proteja as APIs web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>Contas escolares ou profissionais<li>Identidades sociais (com Azure AD B2C) | <li>Contas escolares ou profissionais<li>Contas pessoais da Microsoft<li>Identidades sociais (com Azure AD B2C)     |
| Validação do emitente em aplicativos multi-inquilinos                                                   | Não                                                                   | Sim, para [todas as nuvens](authentication-national-cloud.md) e [Azure AD B2C](/azure/active-directory-b2c) |
| Web app/API [chama gráfico da Microsoft][scenario-api-call-graph]                             | Não                                                                   | Sim                                                                                                     |
| Web app/API [chama a API web][scenario-api-call-api]                                       | Não                                                                   | Sim                                                                                                     |
| Suporta credenciais de certificado                                                         | Não                                                                   | Sim, incluindo a Azure Key Vault.                                                                          |
| Consentimento incremental e suporte de acesso condicional em aplicações web                           | Não                                                                   | Sim, em MVC, páginas de navalha, e Blazor                                                                    |
| Certificados de encriptação token em APIs web                                                | Não                                                                   | Sim                                                                                                     |
| [Validação de funções de âmbito/aplicação] [cenário-api-validação] em APIs web                        | Não                                                                   | Sim                                                                                                     |
| `WWW-Authenticate` geração de cabeçalho em APIs web                                         | Não                                                                   | Sim                                                                                                     |

## <a name="next-steps"></a>Próximos passos

Para ver a Microsoft Identity Web em ação, experimente o nosso tutorial do Blazor Server:

[Tutorial: Criar uma aplicação Blazor Server que utiliza a plataforma de identidade da Microsoft para autenticação](tutorial-blazor-server.md)

O wiki Web da Microsoft Identity no GitHub contém documentação de referência extensiva para vários aspetos da biblioteca. Por exemplo, a utilização do certificado, o consentimento incremental e a referência de acesso condicional podem ser consultados aqui:

- [Utilização de certificados com Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates?azure-portal=true) (GitHub)
- [Consentimento incremental e acesso condicional](https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access?azure-portal=true) (GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[cenário-api-call-api]: cenário-web-api-call-api-call-api.md#option-1-call-microsoft-graph-with-the-sdk [scenario-api-call-call-gráfico]: cenário-web-api-call-api-call-api.md#option-1-call-microsoft-graph-with-the-sdk [validação do cenário-api]: scenario-protected-web-api-verification-scope-app-roles.md
