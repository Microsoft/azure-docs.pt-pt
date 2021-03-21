---
title: Personalizar pedidos de ficha SAML
titleSuffix: Microsoft identity platform
description: Saiba como personalizar as alegações emitidas pela plataforma de identidade Microsoft no token SAML para aplicações empresariais.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 0cccf45037320b476b1a44cafa8074bacadacbc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103600954"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Como: personalizar reclamações emitidas no token SAML para aplicações empresariais

Hoje em dia, a plataforma de identidade da Microsoft suporta um único sign-on (SSO) com a maioria das aplicações empresariais, incluindo ambas as aplicações pré-integradas na galeria de aplicações AD AZure, bem como aplicações personalizadas. Quando um utilizador autentica uma aplicação através da plataforma de identidade da Microsoft utilizando o protocolo SAML 2.0, a plataforma de identidade da Microsoft envia um símbolo para a aplicação (através de um HTTP POST). E, em seguida, a aplicação valida e utiliza o token para iniciar sessão no utilizador em vez de solicitar um nome de utilizador e senha. Estes tokens SAML contêm peças de informação sobre o utilizador conhecido como *sinistros.*

Uma *alegação* é informação que um fornecedor de identidade afirma sobre um utilizador dentro do token que emite para esse utilizador. No [token SAML,](https://en.wikipedia.org/wiki/SAML_2.0)estes dados estão tipicamente contidos na Declaração de Atributos SAML. O ID único do utilizador é tipicamente representado no Sujeito SAML também chamado de Identificador de Nome.

Por predefinição, a plataforma de identidade da Microsoft emite um token SAML na sua aplicação que contém uma `NameIdentifier` reclamação com um valor do nome de utilizador do utilizador (também conhecido como nome principal do utilizador) em Azure AD, que pode identificar exclusivamente o utilizador. O token SAML também contém reclamações adicionais que contêm o endereço de e-mail do utilizador, o primeiro nome e o apelido.

Para visualizar ou editar as reclamações emitidas no token SAML para a aplicação, abra a aplicação no portal Azure. Em seguida, abra a secção **Atributos do Utilizador & Reclamações.**

![Abra a secção atributos & do utilizador no portal Azure](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Existem duas razões possíveis para que possa ter de editar as reclamações emitidas no token SAML:

* A aplicação requer que `NameIdentifier` a alegação ou NameID seja outra coisa que não o nome de utilizador (ou nome principal do utilizador) armazenado em Azure AD.
* O pedido foi escrito para exigir um conjunto diferente de URIs de reclamação ou valores de reclamação.

## <a name="editing-nameid"></a>Nome de ediçãoID

Para editar o NameID (valor do identificador de nome):

1. Abra a página **de valor do identificador nome.**
1. Selecione o atributo ou a transformação que pretende aplicar ao atributo. Opcionalmente, pode especificar o formato que pretende que a alegação NameID tenha.

   ![Editar o valor NameID (identificador de nomes)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato NameID

Se o pedido SAML contiver o elemento NameIDPolicy com um formato específico, então a plataforma de identidade da Microsoft honrará o formato no pedido.

Se o pedido DE SAML não contiver um elemento para NameIDPolicy, então a plataforma de identidade da Microsoft emitirá o NameID com o formato especificado. Se nenhum formato for especificado, a plataforma de identidade da Microsoft utilizará o formato de origem padrão associado à fonte de reclamação selecionada.

A partir do dropdown do **formato do identificador de nome Escolher,** pode selecionar uma das seguintes opções.

| Formato NameID | Description |
|---------------|-------------|
| **Predefinição** | A plataforma de identidade da Microsoft utilizará o formato de origem padrão. |
| **Persistente** | A plataforma de identidade da Microsoft utilizará o Persistente como formato NameID. |
| **EmailAddress** | A plataforma de identidade da Microsoft utilizará o EmailAddress como o formato NameID. |
| **Não especificado** | A plataforma de identidade da Microsoft utilizará o formato NameID não especificado como. |

O NameID transitório também é suportado, mas não está disponível no dropdown e não pode ser configurado do lado de Azure. Para saber mais sobre o atributo NameIDPolicy, consulte [o protocolo Single Sign-On SAML](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributos

Selecione a fonte desejada para a `NameIdentifier` reclamação (ou NameID). Pode selecionar entre as seguintes opções.

| Nome | Descrição |
|------|-------------|
| E-mail | Endereço de e-mail do utilizador |
| userprincipalName | Nome principal do utilizador (UPN) do utilizador |
| nome onpremisessamaccountname | Nome da conta SAM que foi sincronizado a partir de Azure AD no local |
| objectid | Objectid do utilizador em Azure AD |
| empregado | ID do funcionário do utilizador |
| Extensões de diretórios | Extensões [de diretório sincronizadas a partir de diretório ativo no local usando Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributos de extensão 1-15 | Atributos de extensão no local usados para estender o esquema AZure AD |

Para mais informações, consulte [o Quadro 3: Valores de identificação válidos por fonte.](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)

Também pode atribuir qualquer valor constante (estático) a quaisquer reclamações que defina no Azure AD. Siga os passos abaixo para atribuir um valor constante:

1. No <a href="https://portal.azure.com/" target="_blank">portal Azure</a>, na secção **Atributos & Reclamações** do Utilizador, clique no ícone **Editar** para editar as reclamações.
1. Clique na reclamação necessária que pretende modificar.
1. Introduza o valor constante sem cotações no **atributo Fonte** conforme a sua organização e clique em **Guardar**.

    ![Org Atribui & Seção reivindicações no portal Azure](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. O valor constante será apresentado como abaixo.

    ![Editar Atributos & Reclamações no portal Azure](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Reivindicações especiais - transformações

Também pode utilizar as funções de transformação de sinistros.

| Função | Descrição |
|----------|-------------|
| **ExtractoMailPrefixo()** | Remove o sufixo de domínio do endereço de e-mail ou do nome principal do utilizador. Isto extrai apenas a primeira parte do nome de utilizador que está a ser passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com ). |
| **Junte-se a nós()** | Junta-se a um atributo com um domínio verificado. Se o valor do identificador de utilizador selecionado tiver um domínio, extrairá o nome de utilizador para anexar o domínio verificado selecionado. Por exemplo, se selecionar o e-mail ( joe_smith@contoso.com ) como o valor do identificador do utilizador e selecionar contoso.onmicrosoft.com como domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com . |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **Toupper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculas. |

## <a name="adding-application-specific-claims"></a>Adicionar reclamações específicas da aplicação

Para adicionar reclamações específicas da aplicação:

1. Em **Atributos de Utilizador & Reclamações**, selecione **Adicionar nova reclamação** para abrir a página de reclamações do utilizador **Gerir.**
1. Insira o **nome** das reclamações. O valor não precisa estritamente de seguir um padrão URI, de acordo com a especificação SAML. Se precisares de um padrão URI, podes pôr isso no campo **Namespace.**
1. Selecione a **Fonte** onde a reclamação vai recuperar o seu valor. Pode selecionar um atributo de utilizador a partir do atributo de origem ou aplicar uma transformação no atributo do utilizador antes de emissão como uma reclamação.

### <a name="claim-transformations"></a>Reclamações transformações

Para aplicar uma transformação a um atributo do utilizador:

1. In **Manage claim**, selecione *Transformation* as the claim source to open the Manage **transformation** page.
2. Selecione a função a partir da redução da transformação. Dependendo da função selecionada, terá de fornecer parâmetros e um valor constante para avaliar na transformação. Consulte a tabela abaixo para obter mais informações sobre as funções disponíveis.
3. Para aplicar a transformação múltipla, clique na **transformação de Adicionar**. Pode aplicar um máximo de duas transformações a uma reivindicação. Por exemplo, primeiro pode extrair o prefixo de e-mail do `user.mail` . Então, faça a parte superior da corda.

   ![Transformação de múltiplas reclamações](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Pode utilizar as seguintes funções para transformar reclamações.

| Função | Descrição |
|----------|-------------|
| **ExtractoMailPrefixo()** | Remove o sufixo de domínio do endereço de e-mail ou do nome principal do utilizador. Isto extrai apenas a primeira parte do nome de utilizador que está a ser passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com ). |
| **Junte-se a nós()** | Cria um novo valor juntando dois atributos. Opcionalmente, pode utilizar um separador entre os dois atributos. Para a transformação da reclamação nameID, a junção é restrita a um domínio verificado. Se o valor do identificador de utilizador selecionado tiver um domínio, extrairá o nome de utilizador para anexar o domínio verificado selecionado. Por exemplo, se selecionar o e-mail ( joe_smith@contoso.com ) como o valor do identificador do utilizador e selecionar contoso.onmicrosoft.com como domínio verificado, isso resultará em joe_smith@contoso.onmicrosoft.com . |
| **ToLowercase()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **Touppercase()** | Converte os caracteres do atributo selecionado em caracteres maiúsculas. |
| **Contém()** | Produz um atributo ou constante se a entrada corresponder ao valor especificado. Caso contrário, pode especificar outra saída se não houver correspondência.<br/>Por exemplo, se pretender emitir uma reclamação em que o valor seja o endereço de e-mail do utilizador se contiver o domínio " @contoso.com ", caso contrário pretender desafetar o nome principal do utilizador. Para tal, configurar os seguintes valores:<br/>*Parâmetro 1(entrada)*: user.email<br/>*Valor:* @contoso.com "<br/>Parâmetro 2 (saída): user.email<br/>Parâmetro 3 (saída se não houver correspondência): user.userprincipalname |
| **EndWith()** | Produz um atributo ou constante se a entrada terminar com o valor especificado. Caso contrário, pode especificar outra saída se não houver correspondência.<br/>Por exemplo, se quiser emitir uma reclamação em que o valor seja o ID do funcionário do utilizador se o ID do empregado terminar com "000", caso contrário, pretender fazer um atributo de extensão. Para tal, configurar os seguintes valores:<br/>*Parâmetro 1(entrada)*: user.employeeid<br/>*Valor*: "000"<br/>Parâmetro 2 (saída): user.employeeid<br/>Parâmetro 3 (saída se não houver correspondência): user.extensionattribute1 |
| **StartWith()** | Produz um atributo ou constante se a entrada começar com o valor especificado. Caso contrário, pode especificar outra saída se não houver correspondência.<br/>Por exemplo, se quiser emitir uma reclamação em que o valor é o ID do funcionário do utilizador se o país/região começar com "EUA", caso contrário, pretender obter um atributo de extensão. Para tal, configurar os seguintes valores:<br/>*Parâmetro 1(entrada)*: user.country<br/>*Valor*: "EUA"<br/>Parâmetro 2 (saída): user.employeeid<br/>Parâmetro 3 (saída se não houver correspondência): user.extensionattribute1 |
| **Extrato() - Após a correspondência** | Devolve o sub-adc de sub-2 depois de corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon", o valor correspondente é "Finance_", então a produção da reclamação é "BSimon". |
| **Extrato() - Antes de combinar** | Devolve o sub-adc de sub-adcões até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "BSimon_US", o valor correspondente é "_US", então a produção da reclamação é "BSimon". |
| **Extrato() - Entre a correspondência** | Devolve o sub-adc de sub-adcões até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon_US", o primeiro valor correspondente é \_ "Finanças", o segundo valor correspondente é " \_ EUA", então a produção da reclamação é "BSimon". |
| **ExtractAlpha() - Prefixo** | Devolve a parte alfabética do prefixo da corda.<br/>Por exemplo, se o valor da entrada for "BSimon_123", então devolve "BSimon". |
| **ExtractAlpha() - Sufixo** | Devolve a parte alfabética do sufixo da corda.<br/>Por exemplo, se o valor da entrada for "123_Simon", então devolve "Simão". |
| **Extractnumeric() - Prefixo** | Devolve a parte numérica prefixo da corda.<br/>Por exemplo, se o valor da entrada for "123_BSimon", então devolve "123". |
| **ExtractoNúrico() - Sufixo** | Devolve a parte numérica do sufixo da corda.<br/>Por exemplo, se o valor da entrada for "BSimon_123", então devolve "123". |
| **IfEmpty()** | Produz um atributo ou constante se a entrada for nula ou vazia.<br/>Por exemplo, se pretender desativar um atributo armazenado numa extensão, se o ID do empregado para um determinado utilizador estiver vazio. Para tal, configurar os seguintes valores:<br/>Parâmetro 1 (entrada): user.employeeid<br/>Parâmetro 2 (saída): user.extensionattribute1<br/>Parâmetro 3 (saída se não houver correspondência): user.employeeid |
| **IfNotEmpty()** | Produz um atributo ou constante se a entrada não for nula ou vazia.<br/>Por exemplo, se pretender desativar um atributo armazenado numa extensão, se o ID do empregado para um determinado utilizador não estiver vazio. Para tal, configurar os seguintes valores:<br/>Parâmetro 1 (entrada): user.employeeid<br/>Parâmetro 2 (saída): user.extensionattribute1 |

Se precisar de transformações adicionais, submeta a sua ideia no [fórum de feedback em Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) na categoria de *aplicação SaaS.*

## <a name="emitting-claims-based-on-conditions"></a>Emissão de reclamações com base em condições

Pode especificar a origem de uma reclamação com base no tipo de utilizador e no grupo a que o utilizador pertence. 

O tipo de utilizador pode ser:
- **Any**: Todos os utilizadores estão autorizados a aceder à aplicação.
- **Membros**: Membro nativo do inquilino
- **Todos os hóspedes**: O utilizador é trazido de uma organização externa com ou sem Azure AD.
- **Hóspedes AAD**: O utilizador convidado pertence a outra organização que utiliza o Azure AD.
- **Hóspedes externos**: O utilizador convidado pertence a uma organização externa que não tem Azure AD.


Um dos cenários em que isso é útil é quando a origem de uma reclamação é diferente para um hóspede e um funcionário que acede a uma aplicação. Pode querer especificar que, se o utilizador for um empregado, o NameID é proveniente de user.email, mas se o utilizador for um hóspede, o NameID é proveniente do utilizador.extensionattribute1.

Para adicionar uma condição de reclamação:

1. In **Manage claim**, expandir as condições de Reclamação.
2. Selecione o tipo de utilizador.
3. Selecione o(s) grupo(s) a que o utilizador deve pertencer. Pode selecionar até 50 grupos únicos em todas as reclamações para uma determinada aplicação. 
4. Selecione a **Fonte** onde a reclamação vai recuperar o seu valor. Pode selecionar um atributo de utilizador a partir do atributo de origem ou aplicar uma transformação no atributo do utilizador antes de emissão como uma reclamação.

A ordem pela qual se adiciona as condições são importantes. A Azure AD avalia as condições de cima para baixo para decidir qual o valor a emitir na reclamação. O último valor que corresponda à expressão será emitido na reclamação.

Por exemplo, Britta Simon é uma utilizadora convidada do inquilino contoso. Ela pertence a outra organização que também usa Azure AD. Dada a configuração abaixo para a aplicação Fabrikam, quando Britta tentar entrar em Fabrikam, a plataforma de identidade da Microsoft avaliará as condições da seguinte forma.

Em primeiro lugar, a plataforma de identidade da Microsoft verifica se o tipo de utilizador da Britta é `All guests` . Uma vez que, isto é verdade, então a plataforma de identidade da Microsoft atribui a fonte para a reivindicação a `user.extensionattribute1` . Em segundo lugar, a plataforma de identidade da Microsoft verifica se o tipo de utilizador da Britta é `AAD guests` , uma vez que isso também é verdade, então a plataforma de identidade da Microsoft atribui a fonte para a alegação de `user.mail` . Finalmente, a reclamação é emitida com valor `user.mail` para a Britta.

![Configuração condicional de reclamações](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Passos seguintes

* [Gestão de aplicações em Azure AD](../manage-apps/what-is-application-management.md)
* [Configurar aplicações únicas de inscrição que não constam da galeria de candidaturas Azure AD](../manage-apps/configure-saml-single-sign-on.md)
* [Resolver problemas de início de sessão único baseado em SAML](../manage-apps/debug-saml-sso-issues.md)
