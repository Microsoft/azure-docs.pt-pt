---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 4c4870dc0f5a423288e6cb561b985501414e8525
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203668"
---
## <a name="test-your-code"></a>Testar o seu código

Para testar o aplicativo no Visual Studio, prima **F5** para executar o seu projeto. O browser é aberto o http://<span></span>localhost: localização {porta} e ver o **iniciar sessão com a Microsoft** botão. Selecione o botão para iniciar o processo de início de sessão.

Quando estiver pronto para executar o teste, utilize uma conta do Microsoft Azure Active Directory (Azure AD) (conta escolar ou profissional) ou uma conta Microsoft pessoal (<span>live.</span> com ou <span>outlook.</span> com) para iniciar sessão.

![Inicie sessão com a Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Inicie sessão na sua conta Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, o utilizador é redirecionado para a home page do seu Web site. A home page é o URL de HTTPS, que é especificado nas suas informações de registo de aplicação no Portal de registo de aplicação do Microsoft. A página inicial inclui uma mensagem de boas-vindas *"Hello \<utilizador >,"* uma ligação para terminar sessão e uma ligação para ver as afirmações do utilizador. A ligação para afirmações do utilizador navega para o *afirmações* controlador que criou anteriormente.

### <a name="browse-to-see-the-users-claims"></a>Procurar para ver as afirmações do utilizador

Para ver as afirmações do utilizador, selecione a ligação para navegar para a vista de controlador que está disponível apenas para utilizadores autenticados.

#### <a name="view-the-claims-results"></a>Ver os resultados de afirmações

Depois que navega para a vista de controlador, deverá ver uma tabela que contém as propriedades básicas para o utilizador:

|Propriedade |Value |Descrição |
|---|---|---|
|**Nome** |Nome completo do utilizador | O utilizador do primeiro e último nome.
|**Nome de Utilizador** |user<span>@domain.com</span> | O nome de utilizador que é utilizado para identificar o utilizador.
|**Assunto** |Requerente |Uma cadeia que identifica unicamente o usuário na web.|
|**ID do inquilino** |Guid | R **guid** que representa exclusivamente a organização do utilizador do Azure AD.|

Além disso, deverá ver uma tabela de todas as afirmações que estão no pedido de autenticação. Para obter mais informações, consulte a [lista de declarações que estão num Token de ID do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testar o acesso a um método que tem um atributo Authorize (opcional)

Para testar o acesso como um usuário anônimo para um controlador protegido com o `Authorize` atributo, siga estes passos:

1. Selecione a ligação para iniciar o utilizador e concluir o processo de fim de sessão.
2. No seu browser, escreva http://<span></span>localhost: {porta} / afirmações aceder ao seu controlador que está protegido com o `Authorize` atributo.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Resultados esperados depois de acesso a um controlador protegido

Lhe for pedido para autenticar para utilizar a vista de controlador protegido.

## <a name="advanced-options"></a>Opções avançadas

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteger o seu Web site inteiro
Para proteger o seu site inteiro, no **global. asax** do ficheiro, adicione o `AuthorizeAttribute` atributo para o `GlobalFilters` filtrar no `Application_Start` método:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Restringir quem pode iniciar sessão sua aplicação

Por predefinição quando cria a aplicação criada por este guia, a aplicação irá aceitar inícios de sessão de contas pessoais (incluindo o outlook.com, live.com e outros), bem como o trabalho contas escolares ou profissionais de qualquer empresa ou organização que está integrado nas O Azure Active Directory. Esta é uma opção recomendada para aplicações SaaS.

Para restringir o início de sessão acesso de utilizador para a sua aplicação, várias opções estão disponíveis:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opção 1: Restringir os utilizadores da instância do Active Directory de apenas uma organização para iniciar sessão na aplicação (inquilino único)

Esta opção é um cenário comum para *aplicativos LOB*: Se quiser que o aplicativo para aceitar inícios de sessão apenas a partir de contas que pertencem a uma instância específica do Azure Active Directory (incluindo *contas de convidado* dessa instância) faça o seguinte:

1. No **Web. config** de ficheiros, altere o valor para o `Tenant` parâmetro de `Common` para o nome do inquilino da organização, tais como `contoso.onmicrosoft.com`.
2. No seu [classe de Startup da OWIN](#configure-the-authentication-pipeline), defina o `ValidateIssuer` argumento para `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Opção 2: Restringir o acesso à sua aplicação para os utilizadores numa lista específica de organizações

Pode restringir o acesso de início de sessão para contas de utilizador única que estão numa organização do Azure AD que está na lista de permitidos organizações:
1. No seu [classe de Startup da OWIN](#configure-the-authentication-pipeline), defina o `ValidateIssuer` argumento para `true`.
2. Defina o valor do `ValidIssuers` parâmetro à lista de permitidos organizações.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opção 3: Utilize um método personalizado para validar os emissores

Pode implementar um método personalizado para validar os emissores utilizando o **IssuerValidator** parâmetro. Para obter mais informações sobre como utilizar este parâmetro, leia sobre o [TokenValidationParameters classe](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) no MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]