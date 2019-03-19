---
title: 'Tutorial: Integração do Active Directory do Azure com a Sandbox da Salesforce | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6794d7eaccb488bb345227161f0bca02f14bc518
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852581"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Active Directory do Azure com a Sandbox de Salesforce

Neste tutorial, saiba como integrar o Sandbox de Salesforce com o Azure Active Directory (Azure AD).

Áreas de segurança dão-lhe a capacidade de criar várias cópias da sua organização em ambientes separados para diversas finalidades, como o desenvolvimento, teste e treinamento, sem comprometer os dados e aplicativos em sua produção de Salesforce organização.
Para obter mais detalhes, consulte [descrição geral de Sandbox](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrar o Salesforce Sandbox no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Sandbox do Salesforce.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a área de segurança do Salesforce (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a Sandbox da Salesforce, terá dos seguintes itens:

- Uma subscrição do Azure
- Uma área de segurança do Salesforce logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Sandbox do Salesforce a partir da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionar Sandbox do Salesforce a partir da Galeria

Para configurar a integração de Salesforce Sandbox para o Azure AD, terá de adicionar área de segurança do Salesforce a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar área de segurança do Salesforce a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Salesforce Sandbox**, selecione **Sandbox de Salesforce** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Sandbox de Salesforce na lista de resultados](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com a Sandbox da Salesforce com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Salesforce Sandbox para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Sandbox do Salesforce tem de ser estabelecida.

Na área de segurança do Salesforce, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com a Sandbox da Salesforce, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste de área de segurança do Salesforce](#create-a-salesforce-sandbox-test-user)**  - para ter um equivalente da Eduarda Almeida na Sandbox do Salesforce que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de área de segurança do Salesforce.

**Para configurar o Azure AD início de sessão único com a Sandbox da Salesforce, execute os seguintes passos:**

1. No portal do Azure, sobre o **Salesforce Sandbox** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Clique em **modo de início de sessão único de alteração** na parte superior do ecrã para selecionar o **SAML** modo.

      ![Configurar a ligação de início de sessão única](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar a ligação de início de sessão única](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **configuração básica de SAML** caixa de diálogo.
   
    ![Configurar a ligação de início de sessão única](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. Sobre o **configuração básica de SAML** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar ficheiro de metadados](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Escolha o ficheiro de metadados](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > Irá obter o fornecedor de serviços de ficheiro de metadados do portal de administração do Salesforce Sandbox, que é explicado mais tarde no tutorial.

    c. Depois do ficheiro de metadados é carregado com êxito, o **URL de resposta** valor irá obter automaticamente preenchido **URL de resposta** caixa de texto.

    ![Domínio de Sandbox do Salesforce e URLs únicas início de sessão em informações](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. Sobre o **certificado de assinatura SAML** secção, clique em **transferir** para transferir **XML de metadados de Federação** e, em seguida, guarde o ficheiro xml em seu computador.

    ![O link de download de certificado](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Abra um novo separador no seu browser e inicie sessão sua conta de administrador da área de segurança do Salesforce.

8. Clique nas **programa de configuração** sob **ícone de definições** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure1.png)

9. Desloque para baixo para o **configurações** no painel de navegação esquerdo, clique em **identidade** para expandir a secção relacionada. Em seguida, clique em **definições de início de sessão único**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Sobre o **definições de início de sessão único** página, clique no **editar** botão.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure3.png)

11. Selecione **ativada SAML**e, em seguida, clique em **guardar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Para configurar o seu únicas início de sessão em definições de SAML, clique em **novo a partir do ficheiro de metadados**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Clique em **Escolher ficheiro** para carregar o ficheiro XML de metadados que transferiu a partir do portal do Azure e clique em **criar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. Sobre o **SAML único configurações de logon** página, campos preencher automaticamente em clique em Guardar.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. Sobre o **definições de início de sessão único** página, clique no **transferir metadados** botão para transferir o ficheiro de metadados do fornecedor de serviço. Utilize este ficheiro na **configuração básica de SAML** secção no portal do Azure para configurar os URLs necessários, tal como explicado anteriormente.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure4.png)

16. Se desejar configurar a aplicação no **SP** modo iniciado, a seguir são os pré-requisitos para isso:

    a. Deve ter um domínio verificado.

    b. Terá de configurar e ativar o seu domínio na Sandbox do Salesforce, passos para isso são explicados posteriormente neste tutorial.

    c. No portal do Azure, sobre o **configuração básica de SAML** secção, clique em **definir URLs adicionais** e executar o passo seguinte:
  
    ![Domínio de Sandbox do Salesforce e URLs únicas início de sessão em informações](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    Na **URL de início de sessão** caixa de texto, digite o valor usando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Este valor deve ser copiado a partir do portal do Salesforce Sandbox assim que tiver ativado o domínio.

17. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados de Federação** e, em seguida, guarde o ficheiro xml em seu computador.

    ![O link de download de certificado](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Abra um novo separador no seu browser e inicie sessão sua conta de administrador da área de segurança do Salesforce.

19. Clique nas **programa de configuração** sob **ícone de definições** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure1.png)

20. Desloque para baixo para o **configurações** no painel de navegação esquerdo, clique em **identidade** para expandir a secção relacionada. Em seguida, clique em **definições de início de sessão único**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. Sobre o **definições de início de sessão único** página, clique no **editar** botão.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure3.png)

22. Selecione **ativada SAML**e, em seguida, clique em **guardar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Para configurar o seu únicas início de sessão em definições de SAML, clique em **novo a partir do ficheiro de metadados**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Clique em **Escolher ficheiro** para carregar o ficheiro XML de metadados e clique em **criar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. Sobre o **SAML único configurações de logon** página, campos preencher automaticamente, escreva o nome da configuração (por exemplo: *SPSSOWAAD_Test*) no **nome** caixa de texto e clique em Guardar.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Para ativar o seu domínio na Sandbox do Salesforce, execute os seguintes passos:

    > [!NOTE]
    > Antes de ativar o domínio tem de criar o mesmo na área de segurança do Salesforce. Para obter mais informações, consulte [definir seu nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Depois de criar o domínio, certifique-se de que está configurado corretamente.

    * No painel de navegação à esquerda na Sandbox do Salesforce, clique em **definições da empresa** para expandir a secção relacionada e, em seguida, clique em **meu domínio**.

         ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * Na **configuração da autenticação** secção, clique em **editar**.

        ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * Na **configuração da autenticação** secção, como **serviço de autenticação**, selecione o nome SAML único início de sessão da definição de que definiu durante a configuração de SSO na Sandbox do Salesforce e Clique em **guardar**.

        ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![Criar utilizador do Azure AD][100]

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Criar um utilizador de teste do Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![Criar um utilizador de teste do Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Criar um utilizador de teste de Sandbox de Salesforce

Nesta secção, um usuário chamado Eduarda Almeida é criado na área de segurança do Salesforce. Sandbox de Salesforce suporta o aprovisionamento de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir na área de segurança do Salesforce, uma nova é criada quando tentar acessar a área de segurança do Salesforce. Sandbox de Salesforce também suporta o aprovisionamento automático de utilizadores, pode encontrar mais detalhes [aqui](salesforce-sandbox-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida a utilizar do Azure-início de sessão único, conceder acesso à área de segurança do Salesforce.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida a área de segurança do Salesforce, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Salesforce Sandbox**.

    ![A ligação de área de segurança do Salesforce na lista de aplicações](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar utilizador** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de área de segurança do Salesforce no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo de área de segurança do Salesforce.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o aprovisionamento do utilizador](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
