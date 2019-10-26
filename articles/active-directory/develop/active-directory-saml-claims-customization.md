---
title: Personalizar declarações de token SAML para aplicativos empresariais no Azure AD
titleSuffix: Microsoft identity platform
description: Saiba como personalizar as declarações emitidas no token SAML para aplicativos empresariais no Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4307c9036db45145a7c0e95cb5e55a667c6851eb
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893394"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Como: Personalizar declarações emitidas no token SAML para aplicativos empresariais

Hoje, o Azure Active Directory (Azure AD) dá suporte ao SSO (logon único) com a maioria dos aplicativos corporativos, incluindo os aplicativos previamente integrados na Galeria de aplicativos do Azure AD, bem como os personalizados. Quando um usuário é autenticado em um aplicativo por meio do Azure AD usando o protocolo SAML 2,0, o Azure AD envia um token para o aplicativo (por meio de um HTTP POST). Em seguida, o aplicativo valida e usa o token para registrar o usuário em vez de solicitar um nome de usuário e uma senha. Esses tokens SAML contêm informações sobre o usuário conhecido como *declarações*.

Uma *declaração* é uma informação que um provedor de identidade declara sobre um usuário dentro do token que ele emite para esse usuário. No [token SAML](https://en.wikipedia.org/wiki/SAML_2.0), esses dados normalmente estão contidos na instrução de atributo SAML. A ID exclusiva do usuário normalmente é representada na entidade SAML também chamada de identificador de nome.

Por padrão, o Azure AD emite um token SAML para seu aplicativo que contém uma declaração `NameIdentifier` com um valor de nome de usuário (também conhecido como o nome UPN) no Azure AD, que pode identificar exclusivamente o usuário. O token SAML também contém declarações adicionais contendo o endereço de email do usuário, o nome e o sobrenome.

Para exibir ou editar as declarações emitidas no token SAML para o aplicativo, abra o aplicativo no portal do Azure. Em seguida, abra a seção **atributos de usuário & declarações** .

![Abra a seção atributos de usuário & declarações no portal do Azure](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Há dois motivos possíveis para que você precise editar as declarações emitidas no token SAML:

* O aplicativo exige que a declaração `NameIdentifier` ou NameID seja algo diferente do nome de usuário (ou nome UPN) armazenado no Azure AD.
* O aplicativo foi gravado para exigir um conjunto diferente de URIs de declaração ou valores de declaração.

## <a name="editing-nameid"></a>Editando NameID

Para editar NameID (valor do identificador de nome):

1. Abra a página **valor do identificador de nome** .
1. Selecione o atributo ou a transformação que você deseja aplicar ao atributo. Opcionalmente, você pode especificar o formato que deseja que a declaração NameID tenha.

   ![Editar o valor NameID (identificador de nome)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato NameID

Se a solicitação SAML contiver o elemento NameIDPolicy com um formato específico, o Azure AD honrará o formato na solicitação.

Se a solicitação SAML não contiver um elemento para NameIDPolicy, o Azure AD emitirá o NameID com o formato que você especificar. Se nenhum formato for especificado, o Azure AD usará o formato de origem padrão associado à fonte de declaração selecionada.

Na lista suspensa **escolher formato do identificador de nome** , você pode selecionar uma das opções a seguir.

| Formato NameID | Descrição |
|---------------|-------------|
| **Predefinição** | O Azure AD usará o formato de origem padrão. |
| **Persistente** | O Azure AD usará persistente como o formato NameID. |
| **EmailAddress** | O Azure AD usará EmailAddress como o formato NameID. |
| **Não especificado** | O AD do Azure usará não especificado como o formato NameID. |

A NameID transitória também tem suporte, mas não está disponível na lista suspensa e não pode ser configurada no lado do Azure. Para saber mais sobre o atributo NameIDPolicy, consulte [protocolo SAML de logon único](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributos

Selecione a origem desejada para a declaração `NameIdentifier` (ou NameID). Você pode selecionar uma das opções a seguir.

| Nome | Descrição |
|------|-------------|
| E-mail | Endereço de email do usuário |
| userprincipalName | UPN (nome principal do usuário) do usuário |
| onpremisessamaccount | Nome da conta SAM que foi sincronizado do Azure AD local |
| ObjectID | ObjectID do usuário no Azure AD |
| funcionário | ID de funcionário do usuário |
| Extensões de diretórios | Extensões [de diretório sincronizadas do Active Directory local usando a sincronização Azure ad Connect](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributos de extensão 1-15 | Atributos de extensão locais usados para estender o esquema do Azure AD |

Para obter mais informações, consulte a [tabela 3: valores de ID válidos por origem](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Você também pode atribuir qualquer valor constante (estático) a qualquer declaração que você definir no Azure AD. Siga as etapas abaixo para atribuir um valor constante:

1. Na [portal do Azure](https://portal.azure.com/), na seção **atributos de usuário & declarações** , clique no ícone **Editar** para editar as declarações.

1. Clique na declaração necessária que você deseja modificar.

1. Insira o valor constante sem aspas no **atributo de origem** de acordo com sua organização e clique em **salvar**.

    ![Abra a seção atributos de usuário & declarações no portal do Azure](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. O valor constante será exibido como abaixo.

    ![Abra a seção atributos de usuário & declarações no portal do Azure](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Declarações especiais – transformações

Você também pode usar as funções de transformações de declarações.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix ()** | Remove o sufixo de domínio do endereço de email ou do nome principal do usuário. Isso extrai apenas a primeira parte do nome de usuário que está sendo passado (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Junção ()** | Une um atributo a um domínio verificado. Se o valor do identificador de usuário selecionado tiver um domínio, ele extrairá o nome de usuário para acrescentar o domínio verificado selecionado. Por exemplo, se você selecionar o email (joe_smith@contoso.com) como o valor do identificador de usuário e selecionar contoso.onmicrosoft.com como o domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com. |
| **ToLower ()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **ToUpper ()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |

## <a name="adding-application-specific-claims"></a>Adicionando declarações específicas do aplicativo

Para adicionar declarações específicas do aplicativo:

1. Em **atributos de usuário & declarações**, selecione **Adicionar nova declaração** para abrir a página **gerenciar declarações do usuário** .
1. Insira o **nome** das declarações. O valor não precisa seguir estritamente um padrão de URI, de acordo com a especificação SAML. Se você precisar de um padrão de URI, poderá colocá-lo no campo **namespace** .
1. Selecione a **origem** na qual a declaração vai recuperar seu valor. Você pode selecionar um atributo de usuário na lista suspensa atributo de origem ou aplicar uma transformação ao atributo de usuário antes de emiti-lo como uma declaração.

### <a name="claim-transformations"></a>Transformações de declaração

Para aplicar uma transformação a um atributo de usuário:

1. Em **gerenciar declaração**, selecione *transformação* como a origem da declaração para abrir a página **gerenciar transformação** .
2. Selecione a função na lista suspensa transformação. Dependendo da função selecionada, você precisará fornecer parâmetros e um valor constante a ser avaliado na transformação. Consulte a tabela abaixo para obter mais informações sobre as funções disponíveis.
3. Para aplicar várias transformações, clique em **Adicionar transformação**. Você pode aplicar um máximo de duas transformações a uma declaração. Por exemplo, você pode primeiro extrair o prefixo de email do `user.mail`. Em seguida, torne a cadeia de caracteres maiúsculas.

   ![Editar o valor NameID (identificador de nome)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Você pode usar as funções a seguir para transformar declarações.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix ()** | Remove o sufixo de domínio do endereço de email ou do nome principal do usuário. Isso extrai apenas a primeira parte do nome de usuário que está sendo passado (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Junção ()** | Cria um novo valor unindo dois atributos. Opcionalmente, você pode usar um separador entre os dois atributos. Para a transformação declaração NameID, a junção é restrita a um domínio verificado. Se o valor do identificador de usuário selecionado tiver um domínio, ele extrairá o nome de usuário para acrescentar o domínio verificado selecionado. Por exemplo, se você selecionar o email (joe_smith@contoso.com) como o valor do identificador de usuário e selecionar contoso.onmicrosoft.com como o domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com. |
| **ToLower ()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **ToUpper ()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |
| **Contains ()** | Gera um atributo ou constante se a entrada corresponde ao valor especificado. Caso contrário, você poderá especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma declaração em que o valor é o endereço de email do usuário, se ele contiver o domínio "@contoso.com", caso contrário, você desejará gerar o nome principal do usuário. Para fazer isso, configure os seguintes valores:<br/>*Parâmetro 1 (entrada)* : user. email<br/>*Valor*: "@contoso.com"<br/>Parâmetro 2 (saída): user. email<br/>Parâmetro 3 (saída se não houver correspondência): user. UserPrincipalName |
| **Endcom ()** | Gera um atributo ou constante se a entrada termina com o valor especificado. Caso contrário, você poderá especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma declaração em que o valor é a ID de funcionário do usuário, se a ID de funcionário terminar com "000", caso contrário, você deseja gerar um atributo de extensão. Para fazer isso, configure os seguintes valores:<br/>*Parâmetro 1 (entrada)* : user. EmployeeID<br/>*Valor*: "000"<br/>Parâmetro 2 (saída): user. EmployeeID<br/>Parâmetro 3 (saída se não houver correspondência): user. extensionAttribute1 |
| **StartWith()** | Gera um atributo ou constante se a entrada começa com o valor especificado. Caso contrário, você poderá especificar outra saída se não houver correspondência.<br/>Por exemplo, se você quiser emitir uma declaração em que o valor é a ID de funcionário do usuário, se o país/região começar com "US", caso contrário, você deseja gerar um atributo de extensão. Para fazer isso, configure os seguintes valores:<br/>*Parâmetro 1 (entrada)* : user. Country<br/>*Valor*: "US"<br/>Parâmetro 2 (saída): user. EmployeeID<br/>Parâmetro 3 (saída se não houver correspondência): user. extensionAttribute1 |
| **Extract ()-após correspondência** | Retorna a subcadeia de caracteres depois que ela corresponde ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon", o valor correspondente será "Finance_", então a saída da declaração será "BSimon". |
| **Extrair ()-antes da correspondência** | Retorna a subcadeia de caracteres até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "BSimon_US", o valor correspondente será "_US", então a saída da declaração será "BSimon". |
| **Extract ()-entre correspondência** | Retorna a subcadeia de caracteres até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon_US", o primeiro valor correspondente é "Finance_", o segundo valor correspondente é "_US", então a saída da declaração é "BSimon". |
| **ExtractAlpha ()-prefixo** | Retorna a parte alfabética do prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "BSimon_123", ele retornará "BSimon". |
| **ExtractAlpha ()-sufixo** | Retorna a parte alfabética do sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "123_Simon", ele retornará "Simon". |
| **ExtractNumeric ()-prefixo** | Retorna a parte numérica do prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "123_BSimon", ele retornará "123". |
| **ExtractNumeric ()-sufixo** | Retorna a parte numérica do sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada for "BSimon_123", ele retornará "123". |
| **IfEmpty()** | Gera um atributo ou constante se a entrada é nula ou está vazia.<br/>Por exemplo, se você quiser gerar um atributo armazenado em um ExtensionAttribute, se a ID de funcionário de um determinado usuário estiver vazia. Para fazer isso, configure os seguintes valores:<br/>Parâmetro 1 (entrada): user. EmployeeID<br/>Parâmetro 2 (saída): user. extensionAttribute1<br/>Parâmetro 3 (saída se não houver correspondência): user. EmployeeID |
| **IfNotEmpty()** | Gera um atributo ou constante se a entrada não for nula ou vazia.<br/>Por exemplo, se você quiser gerar um atributo armazenado em um ExtensionAttribute, se a ID de funcionário de um determinado usuário não estiver vazia. Para fazer isso, configure os seguintes valores:<br/>Parâmetro 1 (entrada): user. EmployeeID<br/>Parâmetro 2 (saída): user. extensionAttribute1 |

Se você precisar de transformações adicionais, envie sua ideia no [Fórum de comentários no Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) na categoria de *aplicativo SaaS* .

## <a name="emitting-claims-based-on-conditions"></a>Emitindo declarações com base em condições

Você pode especificar a origem de uma declaração com base no tipo de usuário e o grupo ao qual o usuário pertence. 

O tipo de usuário pode ser:
- **Any**: todos os usuários têm permissão para acessar o aplicativo.
- **Membros**: membro nativo do locatário
- **Todos os convidados**: o usuário é trazido de uma organização externa com ou sem o Azure AD.
- **Convidados do AAD**: o usuário convidado pertence a outra organização usando o Azure AD.
- **Convidados externos**: o usuário convidado pertence a uma organização externa que não tem o Azure AD.


Um cenário em que isso é útil é quando a origem de uma declaração é diferente para um convidado e um funcionário acessando um aplicativo. Talvez você queira especificar que, se o usuário for um funcionário, a NameID será originada de User. email, mas se o usuário for um convidado, o NameID será originado de User. extensionAttribute1.

Para adicionar uma condição de declaração:

1. Em **gerenciar declaração**, expanda as condições de declaração.
2. Selecione o tipo de usuário.
3. Selecione os grupos aos quais o usuário deve pertencer. Você pode selecionar até 10 grupos exclusivos em todas as declarações para um determinado aplicativo. 
4. Selecione a **origem** na qual a declaração vai recuperar seu valor. Você pode selecionar um atributo de usuário na lista suspensa atributo de origem ou aplicar uma transformação ao atributo de usuário antes de emiti-lo como uma declaração.

A ordem na qual você adiciona as condições é importante. O Azure AD avalia as condições de cima para baixo para decidir qual valor emitir na declaração. 

Por exemplo, Brenda Simon é um usuário convidado no locatário da contoso. Ela pertence a outra organização que também usa o Azure AD. Dada a configuração abaixo para o aplicativo Fabrikam, quando o Brenda tenta entrar na Fabrikam, o Azure AD avaliará as condições como a seguir.

Primeiro, o Azure AD verifica se o tipo de usuário do Brenda é `All guests`. Como isso é verdadeiro, o Azure AD atribui a origem para a declaração para `user.extensionattribute1`. Em segundo lugar, o Azure AD verifica se o tipo de usuário do Brenda é `AAD guests`, já que isso também é verdadeiro, o Azure AD atribui a origem da declaração a `user.mail`. Por fim, a declaração é emitida com o valor `user.email` para Brenda.

![Configuração condicional de declarações](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Passos seguintes

* [Gerenciamento de aplicativos no Azure AD](../manage-apps/what-is-application-management.md)
* [Configurar o logon único em aplicativos que não estão na Galeria de aplicativos do Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Solucionar problemas de logon único baseado em SAML](howto-v1-debug-saml-sso-issues.md)
