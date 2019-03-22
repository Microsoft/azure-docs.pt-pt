---
title: Problema ao configurar o início de sessão único federado para uma aplicação de externas à Galeria | Documentos da Microsoft
description: Abordar alguns dos problemas comuns que poderá encontrar ao configurar o início de sessão único federado para a sua aplicação SAML personalizada que não esteja listado na Galeria de aplicações do Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ecbb097dd3cb3e3fdd6b365b059f7703668f07e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092604"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problema ao configurar o início de sessão único federado para uma aplicação de externas à Galeria

Se ocorrer um problema ao configurar uma aplicação. Certifique-se de que seguiu todos os passos no artigo [configurar início de sessão único para aplicações que não estão na Galeria de aplicações do Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Não é possível adicionar outra instância da aplicação

Para adicionar uma segunda instância de uma aplicação, terá de ser capaz de:

-   Configure um identificador exclusivo para a segunda instância. Não é possível configurar o mesmo identificador utilizado para a primeira instância.

-   Configure um certificado diferente daquele usado para a primeira instância.

Se a aplicação não suportar qualquer um dos precedente, não é possível configurar uma segunda instância.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde posso definir o formato de EntityID (identificador de utilizador)

Não é possível selecionar o formato de EntityID (identificador de usuário) do Azure AD envia para o aplicativo na resposta após a autenticação de utilizador.

O Azure AD seleciona o formato para o atributo NameID (identificador de utilizador) com base no valor selecionado ou o formato de pedido com o aplicativo em AuthRequest o SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Onde posso obter os metadados da aplicação ou o certificado do Azure AD

Para transferir os metadados da aplicação ou o certificado do Azure AD, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação tiver configurado o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Aceda a **certificado de assinatura SAML** secção, em seguida, clique em **transferir** valor da coluna. Consoante o que o aplicativo requer configurar início de sessão único, verá a opção para transferir o XML de metadados ou o certificado.

O Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser obtidos como um arquivo XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Não sei como personalizar afirmações SAML enviadas para uma aplicação

Para saber como personalizar as afirmações de atributo SAML enviadas para a sua aplicação, veja [afirmações mapeamento no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes
[Managing Applications with Azure Active Directory](what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
