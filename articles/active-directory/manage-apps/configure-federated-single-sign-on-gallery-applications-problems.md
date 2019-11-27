---
title: Problemas ao configurar o SSO Federado para aplicativos da galeria do Azure AD
description: Resolva alguns dos problemas comuns que você pode encontrar ao configurar o logon único federado usando o SAML para aplicativos listados na Galeria de aplicativos do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c2497a781b0d46b3b2f1e281a3d7b327b60952
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274643"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema ao configurar o logon único federado para um aplicativo da galeria do Azure AD

Se você encontrar um problema ao configurar um aplicativo. Verifique se você seguiu todas as etapas no tutorial para o aplicativo. Na configuração do aplicativo, você tem a documentação embutida sobre como configurar o aplicativo. Além disso, você pode acessar a [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para obter uma orientação passo a passo detalhada.

## <a name="cant-add-another-instance-of-the-application"></a>Não é possível adicionar outra instância do aplicativo

Para adicionar uma segunda instância de um aplicativo, você precisa ser capaz de:

-   Configure um identificador exclusivo para a segunda instância. Você não conseguirá configurar o mesmo identificador usado para a primeira instância.

-   Configure um certificado diferente daquele usado para a primeira instância.

Se o aplicativo não oferecer suporte a nenhum dos itens acima. Em seguida, você não conseguirá configurar uma segunda instância.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Não é possível adicionar o identificador ou a URL de resposta

Se não for possível configurar o identificador ou a URL de resposta, confirme se os valores do identificador e da URL de resposta correspondem aos padrões pré-configurados para o aplicativo.

Para saber os padrões pré-configurados para o aplicativo:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.** Vá para a etapa 7. Se você já estiver na folha de configuração de aplicativo no Azure AD.

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. clique em **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione o aplicativo para o qual você deseja configurar o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Selecione **logon baseado em SAML** na lista suspensa **modo** .

9. Vá para a caixa de texto **identificador** ou **URL de resposta** , na **seção domínio e URLs.**

10. Há três maneiras de saber os padrões com suporte para o aplicativo:

    * Na caixa de texto, você vê os padrões com suporte como um espaço reservado *exemplo:* <https://contoso.com>.

    * Se não houver suporte para o padrão, você verá um ponto de exclamação vermelho quando tentar inserir o valor na caixa de texto. Se você passar o mouse sobre o ponto de exclamação vermelho, verá os padrões com suporte.

    * No tutorial do aplicativo, você também pode obter informações sobre os padrões com suporte. Na seção **Configurar logon único do Azure ad** . Vá para a etapa para configurar os valores na seção **domínio e URLs** .

Se os valores não corresponderem com os padrões pré-configurados no Azure AD. Pode:

-   Trabalhar com o fornecedor do aplicativo para obter valores que correspondam ao padrão pré-configurado no Azure AD

-   Ou, você pode entrar em contato com a equipe do Azure AD em <aadapprequest@microsoft.com> ou deixar um comentário no tutorial para solicitar a atualização dos padrões com suporte para o aplicativo

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde faço para definir o formato EntityId (identificador de usuário)

Você não poderá selecionar o formato EntityId (identificador de usuário) que o Azure AD envia para o aplicativo na resposta após a autenticação do usuário.

Azure AD selecione o formato para o atributo NameID (identificador de usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, visite o artigo [protocolo SAML de logon único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na seção NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Não é possível localizar os metadados do Azure AD para concluir a configuração com o aplicativo

Para baixar os metadados do aplicativo ou o certificado do Azure AD, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**

2. Abra a **extensão Azure Active Directory** clicando em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .

4. clique em **aplicativos empresariais** no Azure Active Directory menu de navegação à esquerda.

5. clique em **todos os aplicativos** para exibir uma lista de todos os seus aplicativos.

   * Se você não vir o aplicativo que deseja exibir aqui, use o controle de **filtro** na parte superior da **lista todos os aplicativos** e defina a opção **Mostrar** como **todos os aplicativos.**

6. Selecione o aplicativo que você configurou o logon único.

7. Depois que o aplicativo for carregado, clique no **logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **certificado de autenticação SAML** e clique em **baixar** valor da coluna. Dependendo do que o aplicativo requer para configurar o logon único, você verá a opção para baixar o XML de metadados ou o certificado.

O Azure AD não fornece uma URL para obter os metadados. Os metadados só podem ser recuperados como um arquivo XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Não sabe como personalizar as declarações SAML enviadas a um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas ao seu aplicativo, consulte [mapeamento de declarações em Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
[Managing Applications with Azure Active Directory](what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
