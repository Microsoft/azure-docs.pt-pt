---
title: 'Tutorial: Integração do Active Directory do Azure com Supermood | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Supermood.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60efdd5b509045ce2398720d650894983c2cfc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639825"
---
# <a name="tutorial-azure-active-directory-integration-with-supermood"></a>Tutorial: Integração do Active Directory do Azure com Supermood

Neste tutorial, saiba como integrar Supermood com o Azure Active Directory (Azure AD).

Integrar Supermood no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Supermood.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Supermood (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Supermood, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Supermood logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Supermood da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-supermood-from-the-gallery"></a>Adicionando Supermood da Galeria
Para configurar a integração do Supermood com o Azure AD, terá de adicionar Supermood a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Supermood a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Supermood**, selecione **Supermood** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Supermood na lista de resultados](./media/supermood-tutorial/tutorial_supermood_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Supermood com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Supermood a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Supermood deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Supermood, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Supermood](#create-a-supermood-test-user)**  - para ter um equivalente da Eduarda Almeida na Supermood que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Supermood.

**Para configurar o Azure AD início de sessão único com Supermood, execute os seguintes passos:**

1. No portal do Azure, sobre o **Supermood** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/supermood-tutorial/tutorial_supermood_samlbase.png)

1. Sobre o **Supermood domínio e URLs** secção, execute os seguintes passos:

    ![Supermood domínio e URLs únicas início de sessão em informações](./media/supermood-tutorial/tutorial_supermood_url.png)

    a. Verifique **Mostrar definições de URL avançadas**.

    b. Se desejar configurar a aplicação no **IDP** iniciadas pelo modo, na **estado de reencaminhamento** caixa de texto, escreva um URL: `https://supermood.co/auth/sso/saml20`

    c. Se desejar configurar a aplicação no **SP** iniciadas pelo modo, na **URL de início de sessão** caixa de texto, escreva um URL: `https://supermood.co/app/#!/loginv2`

1. Aplicação de supermood espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Captura de ecrã seguinte mostra um exemplo disso.
    
    ![Configurar o início de sessão único](./media/supermood-tutorial/tutorial_supermood_attribute.png)

1. No **atributos de utilizador** secção sobre o **início de sessão único** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome do Atributo | Valor do Atributo |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/supermood-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/supermood-tutorial/tutorial_attribute_05.png)
    
    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.

    d. Deixe o **espaço de nomes** em branco.
    
    d. Clique em **Ok**

1. Sobre o **certificado de assinatura SAML** secção, clique no botão de cópia para copiar **Url de metadados de Federação de aplicação** e cole-o no bloco de notas.

    ![O link de download de certificado](./media/supermood-tutorial/tutorial_supermood_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/supermood-tutorial/tutorial_general_400.png)

1. Aceda ao seu painel de administração Supermood.co em como o administrador de segurança.

1. Clique em **minha conta** (canto inferior esquerdo) e **único início de sessão (SSO)**.

    ![O certificado único](./media/supermood-tutorial/tutorial_supermood_single.png)
1. No **Your SAML 2.0 configurações**, clique em **adicionar uma configuração de SAML 2.0 para um domínio de e-mail**.

    ![Adicionar o certificado](./media/supermood-tutorial/tutorial_supermood_add.png)

1. No **adicionar uma configuração de SAML 2.0 para um domínio de e-mail**. secção, execute os seguintes passos:

    ![O certificado saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Na **domínio de e-mail para este fornecedor de identidade** caixa de texto, escreva o seu domínio.

    b. Na **utilize um URL de metadados** caixa de texto, colar a **Url de metadados de Federação de aplicação** que copiou do portal do Azure.

    c. Clique em **Adicionar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/supermood-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/supermood-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/supermood-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/supermood-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-supermood-test-user"></a>Criar um utilizador de teste Supermood

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Supermood. Supermood suporta o aprovisionamento de just-in-time, que está por predefinição habilitada para usuários cujos e-mails pertencem aos domínios que são adicionados durante a configuração no final de Supermood. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder Supermood se não existir ainda.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de Supermood](mailto:hello@supermood.fr).


### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Supermood.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Supermood, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Supermood**.

    ![A ligação de Supermood na lista de aplicações](./media/supermood-tutorial/tutorial_supermood_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Supermood no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Supermood.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/supermood-tutorial/tutorial_general_01.png
[2]: ./media/supermood-tutorial/tutorial_general_02.png
[3]: ./media/supermood-tutorial/tutorial_general_03.png
[4]: ./media/supermood-tutorial/tutorial_general_04.png

[100]: ./media/supermood-tutorial/tutorial_general_100.png

[200]: ./media/supermood-tutorial/tutorial_general_200.png
[201]: ./media/supermood-tutorial/tutorial_general_201.png
[202]: ./media/supermood-tutorial/tutorial_general_202.png
[203]: ./media/supermood-tutorial/tutorial_general_203.png

