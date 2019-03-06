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
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: luleon, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23ce02bd35d9cd4afd881ec276fabb0720b61c09
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444043"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Como: Personalizar afirmações emitidas no token SAML para aplicações empresariais

Hoje em dia o Azure Active Directory (Azure AD) suporta início de sessão único com a maioria das aplicações empresariais, incluindo as duas aplicações pré-integradas na Galeria de aplicações do Azure AD, bem como as aplicações personalizadas. Quando um utilizador efetua a autenticação para uma aplicação através do Azure AD através do protocolo SAML 2.0, o Azure AD envia um token para a aplicação (através de um HTTP POST). E, em seguida, o aplicativo valida e utiliza o token para registar o utilizador em vez de solicitar um nome de utilizador e palavra-passe. Estes tokens SAML contêm informações sobre o utilizador conhecido como "afirmações".

R *afirmação* são informações que indica um fornecedor de identidade sobre um usuário dentro do token que emitir para esse utilizador. Na [SAML token](https://en.wikipedia.org/wiki/SAML_2.0), esses dados normalmente estão contidos na declaração de atributo de SAML. ID exclusivo do utilizador, normalmente é representado no requerente do SAML, também denominado como um identificador de nome.

Por predefinição, o Azure AD emite um token SAML a sua aplicação que contém uma afirmação NameIdentifier, com um valor de nome de utilizador (nome da principal de utilizador também conhecidas como) no Azure AD. Este valor pode identificar exclusivamente o utilizador. O token SAML também contém as afirmações adicionais que contém o endereço de e-mail do utilizador, nome próprio e apelido.

Para ver ou editar as declarações emitidas no SAML token para a aplicação, abra a aplicação no portal do Azure. Em seguida, selecione o **vista e editar todos os outros atributos de utilizador** caixa de seleção o **atributos de utilizador** seção do aplicativo.

![Secção de atributos de utilizador][1]

Existem duas razões possíveis porque poderá ter de editar as declarações emitidas no SAML token:
* O aplicativo foi escrito para solicitar a um conjunto diferente de URIs de declaração ou valores de afirmação.
* A aplicação foi implementada de forma que requer a afirmação NameIdentifier ser algo que não seja o nome de utilizador (nome da principal de utilizador também conhecidas como) armazenado no Azure AD.

Pode editar qualquer um dos valores de afirmação padrão. Selecione a linha de afirmação na tabela de atributos de token SAML. Esta ação abre o **Editar atributo** secção e, em seguida, pode editar a afirmação de nome, valor e associada com a declaração de espaço de nomes.

![Editar atributo de utilizador][2]

Também pode remover declarações (que não seja NameIdentifier) utilizando o menu de contexto, que abre-se ao clicar no **...**  ícone. Também pode adicionar nova afirmações utilizando o **adicionar atributo** botão.

![Editar atributo de utilizador][3]

## <a name="editing-the-nameidentifier-claim"></a>Editar a afirmação NameIdentifier

Para resolver o problema em que o aplicativo tiver sido implementado com um nome de utilizador diferente, selecione o **identificador de utilizador** menu pendente **atributos de utilizador** secção. Esta ação fornece uma caixa de diálogo com várias opções diferentes:

![Editar atributo de utilizador][4]

### <a name="attributes"></a>Atributos

Selecione a origem pretendida para o `NameIdentifier` (ou NameID) de afirmação. Pode selecionar entre as seguintes opções.

| Name | Descrição |
|------|-------------|
| Email | O endereço de e-mail do utilizador |
| userprincipalName | O nome principal de utilizador (UPN) do utilizador |
| onpremisessamaccount | Nome da conta SAM que tenha sido sincronizado a partir do Azure AD no local |
| objectID | O objectID do utilizador no Azure AD |
| employeeID | O campo IDdeEmpregado do utilizador |
| Extensões de diretórios | Extensões de diretório [sincronizados a partir do Active Directory no local com o Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributos de extensão 1-15 | Atributos de extensão usados para estender o esquema do Azure AD no local |

### <a name="transformations"></a>Transformações

Também pode utilizar as funções de transformações de afirmações especial.

| Função | Descrição |
|----------|-------------|
| **ExtractMailPrefix()** | Remove o sufixo de domínio do endereço de e-mail, nome de conta SAM ou o nome principal de utilizador. Extrai apenas a primeira parte do nome de utilizador que está sendo passada por meio de (por exemplo, "joe_smith" em vez de joe_smith@contoso.com). |
| **join()** | Associa um atributo com um domínio verificado. Se o valor do identificador de utilizador selecionado tem um domínio, ele irá extrair o nome de utilizador para acrescentar o domínio verificado selecionado. Por exemplo, se selecionar a mensagem de e-mail (joe_smith@contoso.com) como o valor do identificador de utilizador e selecione contoso.onmicrosoft.com como o domínio verificado, tal resultará em joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Converte os caracteres do atributo selecionado em carateres em minúsculas. |
| **ToUpper()** | Converte os caracteres do atributo selecionado em carateres maiúsculos. |

## <a name="adding-claims"></a>Adicionar afirmações

Quando adicionar uma afirmação, pode especificar o nome de atributo (o que não seja estritamente necessário seguem um padrão URI de acordo com a especificação SAML). Defina o valor para qualquer atributo de utilizador que é armazenado no diretório ou utilize um valor constante como uma entrada de estática para todos os utilizadores na sua organização.

![Adicionar o atributo de utilizador][7]

Por exemplo, terá de enviar o departamento de que o utilizador pertence na sua organização como uma afirmação (como, vendas). Introduza o nome de afirmação, conforme o esperado pela aplicação e, em seguida, selecione **user.department** como o valor.

> [!NOTE]
> Se para um determinado usuário, não existe nenhum valor armazenado para um atributo selecionado, essa afirmação não está a ser emitida no token.

> [!TIP]
> O **user.onpremisesecurityidentifier** e **user.onpremisesamaccountname** só são suportadas quando a sincronização de dados de utilizador no local do Active Directory com o [do Azure AD Ligar a ferramenta](../hybrid/whatis-hybrid-identity.md).

## <a name="restricted-claims"></a>Afirmações restritas

Existem alguns restritas afirmações no SAML. Se adicionar essas declarações, Azure AD não irá enviar essas declarações. Seguem-se o conjunto de afirmações SAML restringida:

    | Tipo de afirmação (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Passos Seguintes

* [Gestão de aplicações no Azure AD](../manage-apps/what-is-application-management.md)
* [Configurar início de sessão único em aplicações que não estão na Galeria de aplicações do Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Resolver problemas com base em SAML início de sessão único](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
