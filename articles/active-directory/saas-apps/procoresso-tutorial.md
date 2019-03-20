---
title: 'Tutorial: Integração do Active Directory do Azure com o Procore SSO | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36e1c8d6cae79147c5cd4b5a46f5e1c330811ab8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57877654"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Integração do Active Directory do Azure com o Procore SSO

Neste tutorial, saiba como integrar o Procore SSO com o Azure Active Directory (Azure AD).

Integrar o Procore SSO no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Procore SSO.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Procore SSO (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Procore SSO, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Procore SSO logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Procore SSO da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-procore-sso-from-the-gallery"></a>Adicionando Procore SSO da Galeria

Para configurar a integração do Procore SSO para o Azure AD, terá de adicionar Procore SSO partir da galeria, à sua lista de aplicações de SaaS geridas.

**Para adicionar Procore SSO a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Procore SSO**, selecione **Procore SSO** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![SSO procore na lista de resultados](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Procore SSO com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Procore SSO a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Procore SSO tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o Procore SSO, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste de Procore SSO](#creating-a-procore-sso-test-user)**  - para ter um equivalente da Eduarda Almeida na SSO Procore que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação Procore SSO.

**Para configurar o Azure AD início de sessão único com o Procore SSO, execute os seguintes passos:**

1. No portal do Azure, sobre o **Procore SSO** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](common/tutorial_general_301.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](common/editconfigure.png)

4. Sobre o **configuração básica de SAML** secção, o utilizador não tem de efetuar outros passos de como a aplicação já está pré-integrada com o Azure.

    ![Informações de início de sessão de único procore SSO domínio e URLs](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. No **certificado de assinatura SAML** página, além da **certificado de assinatura SAML** secção, clique em **transferir** para transferir **XML de metadados de Federação** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. Sobre o **configurar o Procore SSO** secção, copie o URL adequado, de acordo com seus requisitos.

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    ![Configuração de procore SSO](common/configuresection.png)

7. Para configurar o início de sessão único num **Procore SSO** lado, o início de sessão para o site da sua empresa procore como administrador.

8. No menu de caixa de ferramentas para baixo, clique em **administrador** para abrir a página de definições de SSO.

    ![Configurar o início de sessão único](./media/procoresso-tutorial/procore_tool_admin.png)

9. Cole os valores nas caixas, conforme descrito abaixo-

    ![Configurar o início de sessão único](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Na **único início de sessão no URL do emissor** texto caixa, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.

    b. Na **início de sessão SAML no URL de destino** caixa, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Agora, abra a **XML de metadados de Federação** baixado anteriormente no portal do Azure e copiar o certificado na marca com o nome **X509Certificate**. Cole o valor copiado para o **Single Sign On x509 certificado** caixa.

10. Clique em **Guardar Alterações**.

11. Depois destas definições, precisa enviar o **nome de domínio** (por exemplo, de **contoso.com**) através do qual está a iniciar sessão em Procore para o [equipa de suporte Procore](https://support.procore.com/) e eles serão Ative o SSO federado para esse domínio.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![Criar utilizador do Azure AD][100]

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Criar um utilizador de teste do Azure AD](common/create_aaduser_01.png) 

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![Criar um utilizador de teste do Azure AD](common/create_aaduser_02.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon\@yourcompanydomain.extension**  
       Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.

### <a name="creating-a-procore-sso-test-user"></a>Criar um utilizador de teste Procore SSO

Siga os passos abaixo para criar um utilizador de teste Procore Procore SSOc lado.

1. Início de sessão para o site da sua empresa procore como administrador.  

2. No menu de caixa de ferramentas para baixo, clique em **Directory** para abrir a página do diretório da empresa.

    ![Configurar o início de sessão único](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Clique em **adicionar uma pessoa** executar a opção para abrir o formulário e introduza seguindo as opções,

    ![Configurar o início de sessão único](./media/procoresso-tutorial/Procore_user_add.png)

    a. Na **nome próprio** caixa de texto, nome do usuário do tipo como **Eduarda**.

    b. Na **Apelido** textbox, o apelido do utilizador de tipo, como **Simon**.

    c. Na **endereço de E-Mail** caixa de texto, como o endereço de e-mail do utilizador do tipo **BrittaSimon\@contoso.com**.

    d. Selecione **modelo de permissão** como **aplicar o modelo de permissão mais tarde**.

    e. Clique em **Criar**.

4. Verificar e atualizar os detalhes do contacto da recém-adicionada.

    ![Configurar o início de sessão único](./media/procoresso-tutorial/Procore_user_check.png)

5. Clique em **guardar e enviar convite** (se um convite através de correio é necessário) ou **guardar** (guardar diretamente) para concluir o registo de utilizador.
    
    ![Configurar o início de sessão único](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Procore SSO.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **Procore SSO**.

    ![Configurar o início de sessão único](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Procore SSO no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação Procore SSO.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
