---
title: Problema ao configurar o início de sessão único federado para uma aplicação da galeria do Azure AD | Documentos da Microsoft
description: Abordar alguns dos problemas comuns que poderá encontrar ao configurar federado único início de sessão através de SAML para aplicações que estão listadas na Galeria de aplicações do Azure AD
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
ms.openlocfilehash: 31e9746c0739a2ddd6267428f428e977151077b6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099785"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema ao configurar o início de sessão único federado para uma aplicação da galeria do Azure AD

Se ocorrer um problema ao configurar uma aplicação. Certifique-se de que seguiu todos os passos no tutorial para a aplicação. A configuração da aplicação, terá a documentação inline sobre como configurar a aplicação. Além disso, pode aceder a [lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo de detalhe.

## <a name="cant-add-another-instance-of-the-application"></a>Não é possível adicionar outra instância da aplicação

Para adicionar uma segunda instância de uma aplicação, terá de ser capaz de:

-   Configure um identificador exclusivo para a segunda instância. Não será possível configurar o mesmo identificador utilizado para a primeira instância.

-   Configure um certificado diferente daquele usado para a primeira instância.

Se a aplicação não suportar qualquer um dos acima. Em seguida, não será possível configurar uma segunda instância.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Não é possível adicionar o identificador ou o URL de resposta

Se não pode configurar o identificador ou o URL de resposta, certifique-se que os valores de identificador e o URL de resposta correspondam aos padrões previamente configurados para a aplicação.

Para saber os padrões de pré-configurados para a aplicação:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.** Vá para o passo 7. Se já está no painel de configuração de aplicação no Azure AD.

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Selecione **baseado em SAML logon** da **modo** lista pendente.

9. Vá para o **identificador** ou **URL de resposta** caixa de texto, no **secção de domínio e URLs.**

10. Existem três formas de saber os padrões suportados para a aplicação:

    * Na caixa de texto, vê o pattern(s) suportado como um marcador de posição *exemplo:* <https://contoso.com>.

    * Se o padrão não é suportado, verá um ponto de exclamação vermelho quando tenta introduzir o valor na caixa de texto. Se passar o rato sobre o ponto de exclamação vermelho, verá os padrões suportados.

    * O tutorial para a aplicação, também pode obter informações sobre os padrões suportados. Sob o **configurar o Azure AD início de sessão único** secção. Ir para o passo para configurar os valores sob o **domínio e URLs** secção.

Se os valores não coincidem com os padrões de pré-configurados no Azure AD. Pode:

-   Trabalhar com o fornecedor do aplicativo para obter valores que correspondam ao padrão pré-configuradas no Azure AD

-   Em alternativa, pode contactar a equipa do Azure AD em <aadapprequest@microsoft.com> ou deixe um comentário no tutorial para pedir a atualização dos padrões suportados para a aplicação

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde posso definir o formato de EntityID (identificador de utilizador)

Não será possível selecionar o formato de EntityID (identificador de usuário) do Azure AD envia para o aplicativo na resposta após a autenticação de utilizador.

O Azure AD selecionar o formato para o atributo NameID (identificador de utilizador) com base no valor selecionado ou o formato de pedido com o aplicativo em AuthRequest o SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Não é possível encontrar os metadados do Azure AD para concluir a configuração com a aplicação

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
