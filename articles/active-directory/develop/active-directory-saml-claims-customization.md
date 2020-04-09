---
title: Personalizar aplicações de ads Azure SAML token claims
titleSuffix: Microsoft identity platform
description: Saiba como personalizar as reclamações emitidas no token SAML para aplicações empresariais em Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 87a9632ec2433b8698e3ae3761ba733aa6bc63a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885689"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Como personalizar as reclamações emitidas no token SAML para aplicações empresariais

Hoje, o Azure Ative Directory (Azure AD) suporta um único sign-on (SSO) com a maioria das aplicações empresariais, incluindo ambas as aplicações pré-integradas na galeria de aplicações Azure AD, bem como aplicações personalizadas. Quando um utilizador autentica uma aplicação através do Azure AD utilizando o protocolo SAML 2.0, a Azure AD envia um sinal para a aplicação (através de um POST HTTP). E, em seguida, a aplicação valida e utiliza o token para iniciar sessão no utilizador em vez de pedir um nome de utilizador e senha. Estas fichas SAML contêm pedaços de informação sobre o utilizador conhecido como *reclamações*.

Uma *alegação* é a informação que um fornecedor de identidade afirma sobre um utilizador dentro do token que emite para esse utilizador. Em [token SAML,](https://en.wikipedia.org/wiki/SAML_2.0)estes dados estão tipicamente contidos na Declaração de Atributo SAML. O ID exclusivo do utilizador está tipicamente representado no Sujeito SAML também chamado de Identificador de Nome.

Por predefinição, a Azure AD emite um `NameIdentifier` token SAML na sua aplicação que contém uma reclamação com um valor do nome de utilizador (também conhecido como nome principal do utilizador) em Azure AD, que pode identificar exclusivamente o utilizador. O token SAML também contém reclamações adicionais que contêm o endereço de e-mail do utilizador, o primeiro nome e o apelido.

Para visualizar ou editar as reclamações emitidas no token SAML para a aplicação, abra a aplicação no portal Azure. Em seguida, abra a secção **de Atributos do Utilizador & Reivindicações.**

![Abra a secção de Atributos de Utilizador & Reclamações no portal Azure](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Existem duas razões possíveis para que possa ter de editar as reclamações emitidas no token SAML:

* A aplicação `NameIdentifier` requer que a alegação ou nome idesem ser algo diferente do nome de utilizador (ou nome principal do utilizador) armazenado em Azure AD.
* A aplicação foi escrita para exigir um conjunto diferente de URIs de reclamação ou valores de reclamação.

## <a name="editing-nameid"></a>Nome de edição

Para editar o NameID (valor identificador de nome):

1. Abra a página de valor do **identificador nome.**
1. Selecione o atributo ou transformação que pretende aplicar ao atributo. Opcionalmente, pode especificar o formato que pretende que a alegação NameID tenha.

   ![Editar o valor NameID (identificador de nome)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato NameID

Se o pedido SAML contiver o elemento NameIDPolicy com um formato específico, então o Azure AD honrará o formato no pedido.

Se o pedido SAML não contiver um elemento para nameIDPolicy, então o Azure AD emitirá o NameID com o formato que especifica. Se nenhum formato for especificado, o Azure AD utilizará o formato de origem predefinido associado à fonte de reclamação selecionada.

A partir do formato de identificação de **nome Escolha,** pode selecionar uma das seguintes opções.

| Formato NameID | Descrição |
|---------------|-------------|
| **Padrão** | A Azure AD utilizará o formato de origem predefinido. |
| **Persistente** | O Azure AD utilizará o Persistent como formato NameID. |
| **Endereço de e-mail** | O Azure AD utilizará o EmailAddress como formato NameID. |
| **Não especificado** | A Azure AD utilizará o formato NameID não especificado. |
| **Nome qualificado do domínio do Windows** | O Azure AD utilizará o Nome Do Domínio do WindowsComo formato NameID. |

O Nome Transitório também é suportado, mas não está disponível no dropdown e não pode ser configurado do lado do Azure. Para saber mais sobre o atributo NameIDPolicy, consulte o [protocolo SAML de Entrada Única](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributos

Selecione a fonte `NameIdentifier` desejada para a alegação (ou NameID). Pode selecionar entre as seguintes opções.

| Nome | Descrição |
|------|-------------|
| Email | Endereço de e-mail do utilizador |
| userprincipalName | Nome principal do utilizador (UPN) do utilizador |
| onpremisessamaccount | Nome da conta SAM que foi sincronizado a partir do local Azure AD |
| objectide | Objectid do utilizador em Azure AD |
| funcionário | Identificação do empregado do utilizador |
| Extensões de diretórios | Extensões de [diretório sincronizadas a partir de diretório ativo no local utilizando O Sincronizado de Ligação AD Azure](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributos de extensão 1-15 | Atributos de extensão no local utilizados para estender o esquema Azure AD |

Para mais informações, consulte [tabela 3: Valores de IDENTIFICAção válidos por fonte](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Também pode atribuir qualquer valor constante (estático) a quaisquer reclamações que defina em Azure AD. Por favor, siga os passos abaixo para atribuir um valor constante:

1. No [portal Azure](https://portal.azure.com/), na secção **Atributos de Utilizador & Reivindicações,** clique no ícone **Editar** para editar as reclamações.

1. Clique na reclamação necessária que pretende modificar.

1. Introduza o valor constante sem cotações no **atributo Fonte** de acordo com a sua organização e clique em **Guardar**.

    ![Abra a secção de Atributos de Utilizador & Reclamações no portal Azure](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. O valor constante será apresentado como abaixo.

    ![Abra a secção de Atributos de Utilizador & Reclamações no portal Azure](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Reivindicações especiais - transformações

Também pode utilizar as funções de transformação de sinistros.

| Função | Descrição |
|----------|-------------|
| **ExtractmailPrefix()** | Remove o sufixo de domínio do endereço de e-mail ou do nome principal do utilizador. Isto extrai apenas a primeira parte do nome de utilizador a joe_smith@contoso.comser transmitida (por exemplo, "joe_smith" em vez de ). |
| **Aderir** | Junta-se a um atributo com um domínio verificado. Se o valor do identificador de utilizador selecionado tiver um domínio, extrairá o nome de utilizador para anexar o domínio verificado selecionado. Por exemplo, se selecionarjoe_smith@contoso.como e-mail ( ) como o valor do identificador joe_smith@contoso.onmicrosoft.comdo utilizador e selecionar contoso.onmicrosoft.com como domínio verificado, isto resultará em . |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **Toupper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |

## <a name="adding-application-specific-claims"></a>Adição de reclamações específicas para aplicações

Para adicionar reclamações específicas da aplicação:

1. Nos **Atributos de utilizador & Reclamações**, selecione **Adicionar nova alegação** para abrir a página de reclamações do **utilizador.**
1. Insira o **nome** das reclamações. O valor não precisa rigorosamente de seguir um padrão URI, de acordo com a especificação SAML. Se precisar de um padrão URI, pode colocá-lo no campo **Namespace.**
1. Selecione a **Fonte** onde a reclamação vai recuperar o seu valor. Pode selecionar um atributo do utilizador a partir do atributo de origem ou aplicar uma transformação ao atributo do utilizador antes de o emitir como uma reclamação.

### <a name="claim-transformations"></a>Transformações de reivindicações

Aplicar uma transformação a um atributo do utilizador:

1. Na **reivindicação de Gestão,** selecione *Transformation* como fonte de reclamação para abrir a página de **transformação Gerir.**
2. Selecione a função a partir do dropdown de transformação. Dependendo da função selecionada, terá de fornecer parâmetros e um valor constante para avaliar na transformação. Consulte a tabela abaixo para obter mais informações sobre as funções disponíveis.
3. Para aplicar a transformação múltipla, clique em **Adicionar transformação**. Pode aplicar um máximo de duas transformações a uma reclamação. Por exemplo, pode primeiro extrair o `user.mail`prefixo de e-mail do . Em seguida, faça a caixa superior da corda.

   ![Editar o valor NameID (identificador de nome)](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Pode utilizar as seguintes funções para transformar reclamações.

| Função | Descrição |
|----------|-------------|
| **ExtractmailPrefix()** | Remove o sufixo de domínio do endereço de e-mail ou do nome principal do utilizador. Isto extrai apenas a primeira parte do nome de utilizador a joe_smith@contoso.comser transmitida (por exemplo, "joe_smith" em vez de ). |
| **Aderir** | Cria um novo valor juntando dois atributos. Opcionalmente, pode utilizar um separador entre os dois atributos. Para a transformação da reivindicação NameID, a adesão é restrita a um domínio verificado. Se o valor do identificador de utilizador selecionado tiver um domínio, extrairá o nome de utilizador para anexar o domínio verificado selecionado. Por exemplo, se selecionarjoe_smith@contoso.como e-mail ( ) como o valor do identificador joe_smith@contoso.onmicrosoft.comdo utilizador e selecionar contoso.onmicrosoft.com como domínio verificado, isto resultará em . |
| **ToLower()** | Converte os caracteres do atributo selecionado em caracteres minúsculos. |
| **Toupper()** | Converte os caracteres do atributo selecionado em caracteres maiúsculos. |
| **Contém()** | Saídas um atributo ou constante se a entrada corresponder ao valor especificado. Caso contrário, pode especificar outra saída se não houver correspondência.<br/>Por exemplo, se pretender emitir uma reclamação onde o valor é o endereço@contoso.comde e-mail do utilizador se contiver o domínio " ", caso contrário, pretende-se obter o nome principal do utilizador. Para tal, configuraria os seguintes valores:<br/>*Parâmetro 1(entrada)*: user.email<br/>*Valor*:@contoso.com"<br/>Parâmetro 2 (saída): user.email<br/>Parâmetro 3 (saída se não houver correspondência): user.userprincipalname |
| **EndWith()** | Produz um atributo ou constante se a entrada terminar com o valor especificado. Caso contrário, pode especificar outra saída se não houver correspondência.<br/>Por exemplo, se pretender emitir uma reclamação em que o valor é o ID do empregado do utilizador se o ID do empregado terminar com "000", caso contrário, pretende-se obter um atributo de extensão. Para tal, configuraria os seguintes valores:<br/>*Parâmetro 1(entrada)*: user.employeeid<br/>*Valor*: "000"<br/>Parâmetro 2 (saída): user.employeeid<br/>Parâmetro 3 (saída se não houver correspondência): user.extensionattribute1 |
| **Início Com** | Produz um atributo ou constante se a entrada começar com o valor especificado. Caso contrário, pode especificar outra saída se não houver correspondência.<br/>Por exemplo, se quiser emitir uma reclamação em que o valor é o ID do utilizador se o país/região começar com "EUA", caso contrário, pretende obter um atributo de extensão. Para tal, configuraria os seguintes valores:<br/>*Parâmetro 1(entrada)*: user.country<br/>*Valor*: "NÓS"<br/>Parâmetro 2 (saída): user.employeeid<br/>Parâmetro 3 (saída se não houver correspondência): user.extensionattribute1 |
| **Extrato() - Após a correspondência** | Devolve o substring depois de corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon", o valor correspondente é "Finance_", então a saída da reclamação é "BSimon". |
| **Extrato() - Antes de combinar** | Devolve o substring até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "BSimon_US", o valor correspondente é "_US", então a saída da reclamação é "BSimon". |
| **Extrato() - Entre a correspondência** | Devolve o substring até corresponder ao valor especificado.<br/>Por exemplo, se o valor da entrada for "Finance_BSimon_US", o primeiro valor correspondente é "Finance_", o segundo valor correspondente é "_US", então a saída da reclamação é "BSimon". |
| **ExtractoAlpha() - Prefixo** | Devolve a parte alfabética prefixo da corda.<br/>Por exemplo, se o valor da entrada for "BSimon_123", então devolve "BSimon". |
| **ExtractoAlpha() - Sufixo** | Devolve a parte alfabética sufixo da corda.<br/>Por exemplo, se o valor da entrada for "123_Simon", então devolve "Simão". |
| **ExtractoNumérico() - Prefixo** | Devolve a parte numérica prefixo da corda.<br/>Por exemplo, se o valor da entrada for "123_BSimon", então devolve "123". |
| **ExtractoNumérico() - Sufixo** | Devolve a parte numérica do sufixo da corda.<br/>Por exemplo, se o valor da entrada for "BSimon_123", então devolve "123". |
| **IfEmpty()** | Saídas um atributo ou constante se a entrada for nula ou vazia.<br/>Por exemplo, se pretender obter um atributo armazenado num atributo extensão se o ID do empregado para um determinado utilizador estiver vazio. Para tal, configuraria os seguintes valores:<br/>Parâmetro 1(entrada): user.employeeid<br/>Parâmetro 2 (saída): user.extensionattribute1<br/>Parâmetro 3 (saída se não houver correspondência): user.employeeid |
| **IfnotEmpty()** | Saídas um atributo ou constante se a entrada não for nula ou vazia.<br/>Por exemplo, se pretender obter um atributo armazenado num atributo extensão se o ID do empregado para um determinado utilizador não estiver vazio. Para tal, configuraria os seguintes valores:<br/>Parâmetro 1(entrada): user.employeeid<br/>Parâmetro 2 (saída): user.extensionattribute1 |

Se precisar de transformações adicionais, submeta a sua ideia no fórum de [feedback em Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) na categoria de *aplicação SaaS.*

## <a name="emitting-claims-based-on-conditions"></a>Emissão de reclamações com base em condições

Pode especificar a origem de uma reclamação com base no tipo de utilizador e no grupo a que o utilizador pertence. 

O tipo de utilizador pode ser:
- **Qualquer**: Todos os utilizadores estão autorizados a aceder à aplicação.
- **Membros**: Membro nativo do inquilino
- **Todos os hóspedes**: O utilizador é trazido de uma organização externa com ou sem Anúncio Azure.
- **Hóspedes AAD**: O utilizador convidado pertence a outra organização usando o Azure AD.
- **Hóspedes externos**: O utilizador convidado pertence a uma organização externa que não tem Anúncio Azure.


Um dos cenários em que isso é útil é quando a origem de uma reclamação é diferente para um hóspede e um funcionário que acede mato. Pode especificar que se o utilizador é um funcionário, o NameID é proveniente do utilizador.email, mas se o utilizador for um hóspede, então o NameID é proveniente do utilizador.extensionattribute1.

Para adicionar uma condição de reclamação:

1. Na **reivindicação de Gestão,** aumente as condições de Reclamação.
2. Selecione o tipo de utilizador.
3. Selecione o(s) grupo(s) a que o utilizador deve pertencer. Pode selecionar até 10 grupos únicos em todas as reclamações para uma determinada aplicação. 
4. Selecione a **Fonte** onde a reclamação vai recuperar o seu valor. Pode selecionar um atributo do utilizador a partir do atributo de origem ou aplicar uma transformação ao atributo do utilizador antes de o emitir como uma reclamação.

A ordem em que se adiciona as condições são importantes. A Azure AD avalia as condições de cima para baixo para decidir qual o valor a emitir na reclamação. 

Por exemplo, Brita Simon é uma utilizadora convidada no inquilino contoso. Pertence a outra organização que também usa a AD Azure. Dada a configuração abaixo para a aplicação Fabrikam, quando Brita tentar assinar com Fabrikam, a Azure AD avaliará as condições seguintes.

Primeiro, a Azure AD verifica se o `All guests`tipo de utilizador da Brita é . Uma vez que, isto é verdade, então a `user.extensionattribute1`AD Azure atribui a fonte para a reivindicação de . Em segundo lugar, a Azure AD verifica `AAD guests`se o tipo de utilizador da Brita é, `user.mail`uma vez que isso também é verdade, então a Azure AD atribui a fonte para a alegação de . Finalmente, a alegação é `user.email` emitida com valor para brita.

![Configuração condicional de sinistros](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Passos seguintes

* [Gestão de aplicações em Azure AD](../manage-apps/what-is-application-management.md)
* [Configure um único sinal sobre aplicações que não estejam na galeria de aplicações da AD Azure](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Srams de problemas saml único sign-on](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
