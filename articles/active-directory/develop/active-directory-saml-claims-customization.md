---
title: Personalizar afirmações emitidas no token SAML para aplicações empresariais no Azure AD | Documentos da Microsoft
description: Saiba como personalizar afirmações emitidas no token SAML para aplicações empresariais no Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: celested
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6fe74852824c10d24729f785e5e33a17b793161
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878575"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Como: Personalizar afirmações emitidas no token SAML para aplicações empresariais

Hoje em dia, o Azure Active Directory (Azure AD) suporta início de sessão único (SSO) com a maioria das aplicações empresariais, incluindo as duas aplicações pré-integradas na Galeria de aplicações do Azure AD, bem como as aplicações personalizadas. Quando um utilizador efetua a autenticação para uma aplicação através do Azure AD através do protocolo SAML 2.0, o Azure AD envia um token para a aplicação (através de um HTTP POST). E, em seguida, o aplicativo valida e utiliza o token para registar o utilizador em vez de solicitar um nome de utilizador e palavra-passe. Estes tokens SAML contêm informações sobre o utilizador conhecido como *afirmações*.

R *afirmação* são informações que indica um fornecedor de identidade sobre um usuário dentro do token que emitir para esse utilizador. Na [SAML token](https://en.wikipedia.org/wiki/SAML_2.0), esses dados normalmente estão contidos na declaração de atributo de SAML. ID exclusivo do utilizador, normalmente é representado no requerente do SAML, também denominado como um identificador de nome.

Por predefinição, o Azure AD emite um token SAML para a sua aplicação que contém um `NameIdentifier` a afirmação com um valor de nome de utilizador (também conhecido como o nome principal de utilizador) no Azure AD, pode identificar exclusivamente o utilizador. O token SAML também contém as afirmações adicionais que contém o endereço de e-mail do utilizador, nome próprio e apelido.

Para ver ou editar as declarações emitidas no SAML token para a aplicação, abra a aplicação no portal do Azure. Em seguida, abra a **atributos de utilizador e afirmações** secção.

![Secção atributos de utilizador e afirmações](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Existem duas razões possíveis porque poderá ter de editar as declarações emitidas no SAML token:

* A aplicação requer o `NameIdentifier` ou NameID alegam ser algo que não o nome de utilizador (ou nome principal de utilizador) armazenados no Azure AD.
* O aplicativo foi escrito para solicitar a um conjunto diferente de URIs de declaração ou valores de afirmação.

## <a name="editing-nameid"></a>Editar NameID

Para editar o NameID (valor de identificador de nome):

1. Abra o **nomear o valor do identificador** página.
1. Selecione o atributo ou transformação que pretende aplicar o atributo. Opcionalmente, pode especificar o formato ele mesmo da afirmação NameID ter.

   ![Edite o valor de NameID (identificador de nome)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Formato NameID

Se o pedido SAML contém o elemento NameIDPolicy com um formato específico, o Azure AD respeitará o formato no pedido.

Se o pedido SAML não contém um elemento para NameIDPolicy, do Azure AD irá emitir o NameID com o formato que especificar. Não se for especificado nenhum formato Azure AD irá utilizar o formato de origem do padrão associado com a origem de afirmação selecionada.

Partir do **formato de identificador de nome de escolha** lista pendente, pode selecionar uma das seguintes opções.

| Formato NameID | Descrição |
|---------------|-------------|
| **Predefinição** | Azure AD irá utilizar o formato de origem do padrão. |
| **Persistente** | Azure AD irá utilizar persistente, como o formato NameID. |
| **EmailAddress** | Azure AD irá utilizar o endereço de correio eletrónico como o formato NameID. |
| **Não especificado** | Azure AD irá utilizar não especificado como o formato NameID. |
| **Transitório** | Azure AD irá utilizar transitório como o formato NameID. |

Para saber mais sobre o atributo NameIDPolicy, veja [protocolo SAML de início de sessão único](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Atributos

Selecione a origem pretendida para o `NameIdentifier` (ou NameID) de afirmação. Pode selecionar entre as seguintes opções.

| Name | Descrição |
|------|-------------|
| Email | Endereço de e-mail do utilizador |
| userprincipalName | Nome principal de utilizador (UPN) do utilizador |
| onpremisessamaccount | Nome da conta SAM que tenha sido sincronizado a partir do Azure AD no local |
| ObjectId | ObjectId do utilizador no Azure AD |
| employeeid | employeeid do utilizador |
| Extensões de diretórios | Extensões de diretório [sincronizados a partir do Active Directory no local com o Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributos de extensão 1-15 | Atributos de extensão usados para estender o esquema do Azure AD no local |

Para obter mais informações, consulte [a tabela 3: Valores de ID válidos por origem](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Afirmações especiais - transformações

Também pode utilizar as funções de transformações de afirmações.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo de domínio do endereço de e-mail ou o nome principal de utilizador. Extrai apenas a primeira parte do nome de utilizador que está sendo passada por meio de (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Join()** | Associa um atributo com um domínio verificado. Se o valor do identificador de utilizador selecionado tem um domínio, ele irá extrair o nome de utilizador para acrescentar o domínio verificado selecionado. Por exemplo, se selecionar a mensagem de e-mail (joe_smith@contoso.com) como o valor do identificador de utilizador e selecione contoso.onmicrosoft.com como o domínio verificado, tal resultará em joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte os caracteres do atributo selecionado em carateres em minúsculas. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em carateres maiúsculos. |

## <a name="adding-application-specific-claims"></a>Adicionar declarações específicas da aplicação

Para adicionar afirmações específicas da aplicação:

1. Na **atributos de utilizador e afirmações**, selecione **Adicionar nova afirmação** para abrir o **gerir as afirmações de utilizador** página.
1. Introduza o **nome** das afirmações. O valor não precisa exatamente seguem um padrão URI, pela especificação SAML. Se precisar de um padrão URI, pode inserir que o **espaço de nomes** campo.
1. Selecione o **origem** onde vai a afirmação para obter seu valor. Pode selecionar um atributo de utilizador a partir da lista pendente de atributo de origem ou aplicar uma transformação para o atributo de utilizador antes de emiti-lo como uma afirmação.

### <a name="application-specific-claims---transformations"></a>Declarações específicas da aplicação - transformações

Também pode utilizar as funções de transformações de afirmações.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo de domínio do endereço de e-mail ou o nome principal de utilizador. Extrai apenas a primeira parte do nome de utilizador que está sendo passada por meio de (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **Join()** | Cria um novo valor ao associar dois atributos. Opcionalmente, pode utilizar um separador de entre os dois atributos. |
| **ToLower()** | Converte os caracteres do atributo selecionado em carateres em minúsculas. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em carateres maiúsculos. |
| **Contains()** | Produz um atributo ou constante se a entrada corresponde ao valor especificado. Caso contrário, pode especificar a saída de outro se não houver nenhuma correspondência.<br/>Por exemplo, se pretender emitir uma afirmação onde o valor é o endereço de e-mail do utilizador, se ele contém o domínio "@contoso.com", caso contrário, queira produzir o nome principal de utilizador. Para fazer isso, poderia configurar os seguintes valores:<br/>*Parâmetro 1(input)*: user.email<br/>*Valor*: "@contoso.com"<br/>O parâmetro 2 (saída): user.email<br/>O parâmetro 3 (se não houver nenhuma correspondência de saída): user.userprincipalname |
| **EndWith()** | Produz um atributo ou constante se a entrada terminar com o valor especificado. Caso contrário, pode especificar a saída de outro se não houver nenhuma correspondência.<br/>Por exemplo, se pretender emitir uma afirmação onde o valor é employeeid do utilizador, se o campo IDdeEmpregado terminar com "000", caso contrário, queira produzir um atributo de extensão. Para fazer isso, poderia configurar os seguintes valores:<br/>*Parâmetro 1(input)*: user.employeeid<br/>*Valor*: "000"<br/>O parâmetro 2 (saída): user.employeeid<br/>O parâmetro 3 (se não houver nenhuma correspondência de saída): user.extensionattribute1 |
| **StartWith()** | Produz um atributo ou constante se a entrada for iniciado com o valor especificado. Caso contrário, pode especificar a saída de outro se não houver nenhuma correspondência.<br/>Por exemplo, se pretender emitir uma afirmação onde o valor é employeeid do utilizador, se o país começa com "US", caso contrário, queira produzir um atributo de extensão. Para fazer isso, poderia configurar os seguintes valores:<br/>*Parâmetro 1(input)*: User. Country<br/>*Valor*: "US"<br/>O parâmetro 2 (saída): user.employeeid<br/>O parâmetro 3 (se não houver nenhuma correspondência de saída): user.extensionattribute1 |
| **Extract() - depois de correspondência** | Devolve a subcadeia após ele corresponde ao valor especificado.<br/>Por exemplo, se o valor da entrada é "Finance_BSimon", o valor correspondente é "Finance_", em seguida, é de saída da afirmação "BSimon". |
| **Extract() - antes de correspondência** | Devolve a subcadeia até que ele corresponde ao valor especificado.<br/>Por exemplo, se o valor da entrada é "BSimon_US", o valor correspondente é "_US", em seguida, é de saída da afirmação "BSimon". |
| **Extract() - entre correspondentes** | Devolve a subcadeia até que ele corresponde ao valor especificado.<br/>Por exemplo, se o valor da entrada é "Finance_BSimon_US", o primeiro valor correspondente é "Finance_", o segundo valor correspondente é "_US", então é de saída da afirmação "BSimon". |
| **ExtractAlpha() - prefixo** | Devolve a parte alfabética de prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada é "BSimon_123", em seguida, devolve "BSimon". |
| **ExtractAlpha() - sufixo** | Devolve a parte alfabética de sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada é "123_Simon", em seguida, devolve "BSimon". |
| **ExtractNumeric() - prefixo** | Devolve a parte numérica de prefixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada é "123_BSimon", em seguida, devolve "123". |
| **ExtractNumeric() - sufixo** | Devolve a parte numérica de sufixo da cadeia de caracteres.<br/>Por exemplo, se o valor da entrada é "BSimon_123", em seguida, devolve "123". |
| **IfEmpty()** | Produz um atributo ou constante se a entrada é nulo ou estar vazio.<br/>Por exemplo, se queira produzir um atributo armazenado num extensionattribute se o campo IDdeEmpregado para um determinado usuário está vazio. Para fazer isso, poderia configurar os seguintes valores:<br/>Parâmetro 1(input): user.employeeid<br/>Parameter 2 (output): user.extensionattribute1<br/>O parâmetro 3 (se não houver nenhuma correspondência de saída): user.employeeid |
| **IfNotEmpty()** | Produz um atributo ou constante se a entrada não é nulo ou estar vazio.<br/>Por exemplo, se queira produzir um atributo armazenado num extensionattribute se o campo IDdeEmpregado para um determinado usuário não está vazio. Para fazer isso, poderia configurar os seguintes valores:<br/>Parâmetro 1(input): user.employeeid<br/>Parameter 2 (output): user.extensionattribute1 |

Se precisar de transformações adicionais, submeta a sua ideia na [fórum de comentários no Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) sob a *aplicação SaaS* categoria.

## <a name="next-steps"></a>Passos Seguintes

* [Gestão de aplicações no Azure AD](../manage-apps/what-is-application-management.md)
* [Configurar início de sessão único em aplicações que não estão na Galeria de aplicações do Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Resolver problemas com base em SAML início de sessão único](howto-v1-debug-saml-sso-issues.md)
